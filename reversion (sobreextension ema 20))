//@version=6
strategy("Estrategia Reversión: Filtro Institucional Mate V2", overlay=true, initial_capital=1000)

// --- 1. SMAs MATE ---
sma20  = ta.sma(close, 20)
sma50  = ta.sma(close, 50)
sma100 = ta.sma(close, 100)
sma200 = ta.sma(close, 200)
rsiVal = ta.rsi(close, 14)

// --- 2. FILTROS DE SEGURIDAD ---
tendenciaCorrecta = sma100 < sma200 
precioBajoMedias  = close < sma100 and close < sma200
enZonaRSI         = rsiVal >= 40 and rsiVal <= 60

// --- 3. GATILLO DE ACCIÓN ---
cuerpoMontado = math.min(open, close) > sma20
eraBajoSma    = math.min(open[1], close[1]) <= sma20[1]

// Condición definitiva de entrada (CORREGIDA)
triggerEntrada = tendenciaCorrecta and precioBajoMedias and enZonaRSI and cuerpoMontado and eraBajoSma

// --- 4. LÓGICA DE COLOR (LIMPIEZA VISUAL) ---
// El aviso naranja SOLO aparece si el precio todavía NO cruzó la SMA 20
// Esto elimina el ruido de las velas siguientes que mencionaste
avisoNaranja = enZonaRSI and precioBajoMedias and tendenciaCorrecta and close <= sma20

barcolor(triggerEntrada ? color.green : (avisoNaranja ? color.orange : na))

// --- 5. SEÑALES VISUALES ---
if triggerEntrada
    strategy.entry("Long", strategy.long)
    label.new(bar_index, low, text="ENTRADA", style=label.style_label_up, color=color.new(color.green, 20), textcolor=color.white, yloc=yloc.belowbar)

plotshape(triggerEntrada, style=shape.triangleup, location=location.belowbar, color=color.green, size=size.normal)

// --- 6. DIBUJO DE SMAs (COLORES MATE) ---
plot(sma20,  color=color.new(#006400, 10), title="SMA 20",  linewidth=2)
plot(sma50,  color=color.new(#DAA520, 10), title="SMA 50",  linewidth=2)
plot(sma100, color=color.new(#bd5d04, 10), title="SMA 100", linewidth=2)
plot(sma200, color=color.new(#8B0000, 10), title="SMA 200", linewidth=2)
