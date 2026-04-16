# Patron de Reversion por Sobreextension

## Descripcion

El patron de reversion por sobreextension identifica puntos de agotamiento extremo del precio donde el mercado ha ido demasiado lejos en una direccion. La logica central es simple: cuando el RSI entra en zona extrema (sobreventa/sobrecompra), el precio acumula energia para revertir. El setup espera que esa energia se confirme con un cruce de la SMA 20 mientras el RSI ya se ha normalizado.

No es una entrada en el minimo. Es una entrada en la confirmacion del rebote.

---

## Estructura del Setup

El patron tiene cuatro etapas secuenciales. Las cuatro deben cumplirse en orden.

```
ETAPA 1        ETAPA 2           ETAPA 3           ETAPA 4
RSI extremo    RSI normaliza     Precio en          Cruce SMA 20
(<= 26)        (40 - 60)         contexto bajista   (cuerpo cruza al alza)
    |               |                 |                   |
    v               v                 v                   v
[Diamante cyan] [Barras naranjas]  [Filtro medias]   [ENTRADA verde]
```

### Etapa 1 — Extremo de RSI (Gatillo de busqueda)

- **Long:** RSI toca o baja de **26**
- **Short:** RSI toca o sube de **74**
- Accion: el script activa la busqueda del setup y guarda la barra donde ocurrio el extremo.
- Visual: diamante cyan (long) o naranja (short) en el grafico.
- Este extremo reinicia el timer si el precio vuelve a tocar la zona antes de activarse.

### Etapa 2 — Ventana de Tiempo (Filtro estadistico)

La formacion del patron tiene una duracion tipica que varia segun la temporalidad. El script solo busca la entrada dentro de esa ventana. Fuera de ella el setup se invalida automaticamente.

| Temporalidad | Ventana (barras) | Tiempo real aproximado |
|---|---|---|
| 15 minutos | 75 — 95 barras | 18 — 24 horas |
| 1 hora | 28 — 50 barras | 28 — 50 horas (~1.5 dias) |
| 4 horas | 18 — 32 barras | 72 — 128 horas (~3-5 dias) |

> Si el setup supera las 45 horas en 1H sin activarse, la probabilidad de exito baja drásticamente. Descartar.

### Etapa 3 — Contexto de Precio (Filtro institucional)

Confirma que el precio esta en el lado correcto para el rebote:

- **Long:** precio por debajo de la SMA 100 y la SMA 200
- **Short:** precio por encima de la SMA 100 y la SMA 200

Esto asegura que el rebote ocurre desde una zona de sobreextension real, no en medio de una tendencia alcista sana.

### Etapa 4 — Gatillo de Entrada (Cruce de SMA 20)

La entrada se dispara cuando:

1. El **RSI esta en la zona 40-60** (impulso normalizado, sin sobrecompra)
2. El **cuerpo de la vela cruza la SMA 20** al alza (long) o a la baja (short)

Este cruce es la confirmacion de que el precio ha dejado atras la zona de agotamiento y tiene soporte estructural en la media rapida.

---

## Filtros Adicionales

### Filtro de Volumen
- Durante la formacion del suelo (etapas 1-3), el volumen debe ser **decreciente o plano** — indica secado de oferta.
- En el cruce de la SMA 20 (etapa 4), el volumen debe **aumentar** — confirma interes comprador real.
- El script mide el promedio de las ultimas 5 velas vs el promedio de 20 velas.

### Filtro MTF (Multi-Timeframe) — Opcional
- Verifica el RSI en **4 horas** para evitar entrar en contra de un sesgo mayor.
- Long: RSI 4H menor a 60 (no esta en sobrecompra en el mayor).
- Short: RSI 4H mayor a 40 (no esta en sobreventa en el mayor).
- Se puede desactivar desde los inputs del script.

### Confirmacion por Accion del Precio (Manual)
Antes de ejecutar, verificar en **4H y Diario**:
- Patrones de agotamiento: tres velas seguidas de Doji final.
- Velas de reversal: envolventes, martillos, pin bars.
- Que el sesgo mayor acompane la direccion del trade.

---

## Codigo del Indicador

La estrategia evolucionó en tres versiones dentro de la carpeta `1-codigo/`:

| Archivo | Version | Descripcion |
|---|---|---|
| `1-script.pine (original)` | V2 — Filtro Institucional Mate | Primera version funcional. Filtro basado en alineacion SMA100 < SMA200. Solo Long. |
| `2-script.pine` | V3 — Gatillo Quirurgico | Reemplaza el cruce de SMAs por cruce exacto del cuerpo sobre SMA 20. Agrega aviso naranja. Solo Long. |
| `3-script.pine` | V4 — Gatillo Quirurgico Bidireccional | Version final. Agrega Short, filtro de tiempo con ventana estadistica, filtro de volumen, filtro MTF opcional e inputs configurables. |

### Parametros Configurables (V4)

```
Modo
  Setups ALCISTAS (Long)      → activar/desactivar señales long
  Setups BAJISTAS (Short)     → activar/desactivar señales short

RSI
  RSI extremo sobreventa      → default: 26
  RSI extremo sobrecompra     → default: 74
  RSI gatillo minimo          → default: 40
  RSI gatillo maximo          → default: 60

Filtro Tiempo
  Ventana minima (barras)     → ajustar segun temporalidad
  Ventana maxima (barras)     → ajustar segun temporalidad

Filtros
  Activar filtro de volumen   → default: activado
  Activar filtro MTF (4H)     → default: desactivado
```

### Codigo de Colores en el Grafico

| Color | Significado |
|---|---|
| Diamante cyan | RSI toco zona de sobreventa extrema (inicio de busqueda long) |
| Diamante naranja | RSI toco zona de sobrecompra extrema (inicio de busqueda short) |
| Barras naranjas | Setup activo, dentro de ventana, esperando cruce de SMA 20 |
| Barra verde + triangulo | ENTRADA long confirmada |
| Barra roja + triangulo | ENTRADA short confirmada |

---

## Estructura del Repositorio

```
reversion (sobreextension)/
│
├── README.md               <- Este archivo
├── datos.md                <- Estadisticas de backtesting y reglas de validacion
│
├── 1-codigo/
│   ├── 1-script.pine (original)   <- V2: version base, solo long
│   ├── 2-script.pine              <- V3: cruce exacto SMA 20, solo long
│   └── 3-script.pine              <- V4: version final, bidireccional
│
├── 2-imagenes/
│   ├── 1.a-render 4 (horas).png   <- Render visual en 4H
│   ├── 1.b-render 1 (horas).png   <- Render visual en 1H
│   └── [activos]/                 <- Ejemplos por activo y temporalidad
│       BTC, ETH, SOL, ADA, BNB, XRP, NEAR, SUI, ENA
│
└── 3-backtesting/          <- Carpeta de resultados de backtest
```

---

## Resumen de Condiciones de Entrada

### Long
1. RSI <= 26 en alguna barra reciente (activa la busqueda)
2. Han pasado entre `ventanaMin` y `ventanaMax` barras desde ese extremo
3. Precio por debajo de SMA 100 y SMA 200
4. RSI en zona 40-60
5. Cuerpo de la vela cruza SMA 20 al alza (`crossover`)
6. Volumen de las ultimas 5 velas menor al promedio de 20 *(si filtro activo)*
7. RSI en 4H menor a 60 *(si filtro MTF activo)*

### Short
1. RSI >= 74 en alguna barra reciente (activa la busqueda)
2. Han pasado entre `ventanaMin` y `ventanaMax` barras desde ese extremo
3. Precio por encima de SMA 100 y SMA 200
4. RSI en zona 40-60
5. Cuerpo de la vela cruza SMA 20 a la baja (`crossunder`)
6. Volumen de las ultimas 5 velas menor al promedio de 20 *(si filtro activo)*
7. RSI en 4H mayor a 40 *(si filtro MTF activo)*
