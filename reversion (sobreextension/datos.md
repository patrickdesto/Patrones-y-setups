# Reporte Estadístico y Reglas de Validación (Backtesting)

Este documento centraliza los tiempos promedio de formación del patrón y las condiciones técnicas obligatorias para el RSI, Volumen y Acción del Precio en diferentes temporalidades.

---

### 1. Estadísticas de Tiempo y Promedios (Backtesting)
Métricas de maduración estructural segmentadas por marco temporal:

* **Temporalidad de 15 Minutos (15m):**
    * **Caso 1:** 20 horas y 15 minutos de formación base.
    * **Caso 2:** 18 horas.
* **Temporalidad de 1 Hora (1H):**
    * **Promedio General:** 37 horas y 30 minutos (Aproximadamente 1 día y 13.5 horas).
    * **Rango de Consistencia:** Oscila entre las 34 y las 41 horas.
    * **Desglose de Casos:**
        * Caso 1: 1 día y 11 horas (35h total).
        * Caso 2: 1 día y 15 horas para el montaje sobre EMA 20 (39h total) / Salida a las 47h (1 día y 23h).
        * Caso 3: 1 día y 17 horas (41h total).
        * Caso 4: 1 día y 10 horas hasta la salida (34h total).
* **Temporalidad Mayor (4H):**
    * **Caso 1:** 4 días y 2 horas (98h total). 
    * **Nota:** El tiempo de formación en 4H se multiplica aproximadamente x2.6 respecto al gráfico de 1H.

### 2. Configuración Lógica del RSI (Momentum Trigger)
Reglas de filtrado para la activación del gatillo y confirmación de la entrada:

* **Filtro de Sobreventa Inicial:**
    * El patrón solo se valora si el RSI ha ingresado previamente en la zona de **26 o inferior** (señal de fatiga extrema).
* **Condicional de Zona de Gatillo (40-60):**
    * Detectada la divergencia tras la sobreventa, la entrada se activa únicamente cuando el RSI recupera el rango **40-60**.
    * **Requisito Crítico:** El precio debe cerrar y sostenerse por encima de la **EMA 20** de forma simultánea.
* **Lectura de Extremos:**
    * Los valores extremos funcionan como alerta temprana para el inicio de una nueva estructura de precio.

### 3. Verificación de Acción del Precio (Price Action)
Criterios de validación visual antes de ejecutar la orden:

* **Análisis Multi-Temporal (MTF):**
    * Verificación obligatoria en **4HS y Diario (1D)** para asegurar que el sesgo mayor acompaña el rebote.
* **Identificación de Patrones de Reversión:**
    * Estructura de tres velas consecutivas seguidas de un **Doji final** (agotamiento).
    * Validación con patrones de velas japonesas (Envolventes, Martillos, etc.).
* **Filtro de Volumen:**
    * El volumen debe ser decreciente o "flat" durante la formación del suelo (secado de oferta).
    * Incremento sustancial de volumen en el breakout de la EMA 20 para confirmar interés comprador.

---
> **Nota de Control:** Si un patrón en 1H supera las 45 horas sin activarse en la zona de gatillo, la probabilidad de éxito disminuye drásticamente y el setup debe ser reevaluado o descartado.
