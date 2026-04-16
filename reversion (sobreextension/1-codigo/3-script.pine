//@version=6
strategy("Estrategia Reversión: Gatillo Quirúrgico V4", overlay=true, initial_capital=1000)

// ============================================================
// INPUTS
// ============================================================

// Modo
mostrarAlcista = input.bool(true, "Setups ALCISTAS (Long)",  group="Modo")
mostrarBajista = input.bool(true, "Setups BAJISTAS (Short)", group="Modo")

// RSI
rsiSobreventa  = input.int(26, "RSI extremo sobreventa",  minval=10, maxval=35, group="RSI")
rsiSobrecompra = input.int(74, "RSI extremo sobrecompra", minval=65, maxval=90, group="RSI")
rsiGatilloMin  = input.int(40, "RSI gatillo mínimo",      minval=30, maxval=50, group="RSI")
rsiGatilloMax  = input.int(60, "RSI gatillo máximo",      minval=50, maxval=70, group="RSI")

// Tiempo — ajustar según temporalidad:
//   15m → min=75  max=95
//   1H  → min=28  max=50  (default)
//   4H  → min=18  max=32
ventanaMin = input.int(28, "Ventana mínima (barras)", minval=5,  group="Filtro Tiempo")
ventanaMax = input.int(50, "Ventana máxima (barras)", minval=10, group="Filtro Tiempo")

// Volumen y MTF
usarFiltroVol = input.bool(true,  "Activar filtro de volumen", group="Filtros")
usarFiltroMTF = input.bool(false, "Activar filtro MTF (4H)",   group="Filtros")

// ============================================================
// 1. INDICADORES BASE
// ============================================================
sma20  = ta.sma(close, 20)
sma50  = ta.sma(close, 50)
sma100 = ta.sma(close, 100)
sma200 = ta.sma(close, 200)
rsiVal = ta.rsi(close, 14)
volSma = ta.sma(volume, 20)

// ============================================================
// 2. FILTRO MTF (4H) — OPCIONAL
//    Long:  RSI 4H < 60 (no en sobrecompra mayor)
//    Short: RSI 4H > 40 (no en sobreventa mayor)
// ============================================================
rsi4H  = request.security(syminfo.tickerid, "240", ta.rsi(close, 14))
mtfOkL = not usarFiltroMTF or rsi4H < 60
mtfOkS = not usarFiltroMTF or rsi4H > 40

// ============================================================
// 3. MEMORIA DE EXTREMOS RSI
//    Guarda la barra donde RSI tocó el extremo y activa la búsqueda.
//    Se actualiza cada vez que RSI toca el extremo (reinicia el timer).
//    Se desactiva al disparar el gatillo.
// ============================================================
var int  barExtL = 0      // barra del último extremo de sobreventa
var int  barExtS = 0      // barra del último extremo de sobrecompra
var bool activoL = false  // hay un setup alcista en curso
var bool activoS = false  // hay un setup bajista en curso

if rsiVal <= rsiSobreventa
    barExtL := bar_index
    activoL := true

if rsiVal >= rsiSobrecompra
    barExtS := bar_index
    activoS := true

// ============================================================
// 4. FILTRO DE TIEMPO (ventana estadística de formación)
//    El gatillo solo puede disparar dentro de la ventana válida
//    calculada desde el momento del extremo RSI.
//    Fuera de esa ventana el setup se invalida automáticamente.
// ============================================================
barsDesdeExtL = bar_index - barExtL
barsDesdeExtS = bar_index - barExtS

tiempoL = activoL and barsDesdeExtL >= ventanaMin and barsDesdeExtL <= ventanaMax
tiempoS = activoS and barsDesdeExtS >= ventanaMin and barsDesdeExtS <= ventanaMax

// ============================================================
// 5. CONTEXTO DE PRECIO
//    Reemplaza el filtro sma100 < sma200 (que causaba falsos negativos).
//    Ahora solo exige que el precio esté por debajo/encima de ambas medias,
//    sin requerir una alineación estricta entre ellas.
// ============================================================
contextoL = close < sma100 and close < sma200
contextoS = close > sma100 and close > sma200

// ============================================================
// 6. FILTRO DE VOLUMEN
//    El promedio de volumen de las últimas 5 velas debe ser menor
//    al promedio de 20 velas (secado de oferta/demanda en formación).
// ============================================================
volFormacion = ta.sma(volume, 5)
volFiltroOk  = not usarFiltroVol or volFormacion < volSma

// ============================================================
// 7. GATILLOS
//    Long:  cuerpo de la vela cruza SMA 20 al alza
//    Short: cuerpo de la vela cruza SMA 20 a la baja
// ============================================================
rsiEnZona   = rsiVal >= rsiGatilloMin and rsiVal <= rsiGatilloMax
cruzaArriba = ta.crossover(math.min(open, close),  sma20)
cruzaAbajo  = ta.crossunder(math.max(open, close), sma20)

triggerL = mostrarAlcista
          and activoL
          and tiempoL
          and contextoL
          and rsiEnZona
          and cruzaArriba
          and volFiltroOk
          and mtfOkL

triggerS = mostrarBajista
          and activoS
          and tiempoS
          and contextoS
          and rsiEnZona
          and cruzaAbajo
          and volFiltroOk
          and mtfOkS

// ============================================================
// 8. EJECUCIÓN Y RESET
// ============================================================
if triggerL
    strategy.entry("Long", strategy.long)
    label.new(bar_index, low, text="ENTRADA", style=label.style_label_up,
              color=color.new(color.green, 20), textcolor=color.white, yloc=yloc.belowbar)
    activoL := false  // resetear: el setup se consumió

if triggerS
    strategy.entry("Short", strategy.short)
    label.new(bar_index, high, text="ENTRADA", style=label.style_label_down,
              color=color.new(color.red, 20), textcolor=color.white, yloc=yloc.abovebar)
    activoS := false  // resetear: el setup se consumió

// ============================================================
// 9. VISUALES
// ============================================================

// Triángulos de entrada
plotshape(triggerL, style=shape.triangleup,   location=location.belowbar, color=color.green, size=size.normal, title="Long")
plotshape(triggerS, style=shape.triangledown, location=location.abovebar, color=color.red,   size=size.normal, title="Short")

// Diamantes en extremos RSI (alertas tempranas)
plotshape(rsiVal <= rsiSobreventa,  style=shape.diamond, location=location.belowbar,
          color=color.new(#00ced1, 0), size=size.tiny, title="Extremo sobreventa")
plotshape(rsiVal >= rsiSobrecompra, style=shape.diamond, location=location.abovebar,
          color=color.new(#FF6B35, 0), size=size.tiny, title="Extremo sobrecompra")

// Barras naranjas: setup activo y en ventana, esperando cruce de SMA 20
enEsperaL = activoL and tiempoL and contextoL and rsiEnZona and close <= sma20
enEsperaS = activoS and tiempoS and contextoS and rsiEnZona and close >= sma20
barcolor(triggerL ? color.green : triggerS ? color.red : (enEsperaL or enEsperaS) ? color.orange : na)

// ============================================================
// 10. SMAs
// ============================================================
plot(sma20,  color=color.new(#00332a, 10), title="SMA 20",  linewidth=1)
plot(sma50,  color=color.new(#fbc02d, 10), title="SMA 50",  linewidth=1)
plot(sma100, color=color.new(#e65100, 10), title="SMA 100", linewidth=1)
plot(sma200, color=color.new(#801922, 10), title="SMA 200", linewidth=1)
