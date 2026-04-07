# Reporte Estadístico y Reglas de Validación (Backtesting)

Este documento centraliza los tiempos promedio de formación del patrón y las condiciones técnicas obligatorias para el RSI, Volumen y Acción del Precio.

---

### 1. Estadísticas de Tiempo y Promedios (Backtesting)
Basado en los casos documentados en el time-frame de 1 Hora (1H), se establecen las siguientes métricas de maduración:

* **Promedio General de Formación (1H):
    -caso 1: 20 horas y 15 minutos

* **Promedio General de Formación (1H):** * **37 horas y 30 minutos** (Aproximadamente 1 día y 13.5 horas).
    * El rango de consistencia operativa oscila entre las 34 y las 41 horas.
* **Desglose de Casos de Referencia (1H):**
    * Caso 1: 1 día y 11 horas (35h total).
    * Caso 2: 1 día y 15 horas para el montaje sobre EMA 20 (39h total) / Salida a las 47h (1 día y 23h).
    * Caso 3: 1 día y 17 horas (41h total).
    * Caso 4: 1 día y 10 horas hasta la salida (34h total).
* **Referencia en Temporalidad Mayor (4H):**
    * Caso 1: 4 días y 2 horas (98h total). El tiempo de formación se multiplica x2.6 respecto a 1H.

### 2. Configuración Lógica del RSI
Reglas de filtrado para la activación del gatillo:

* **Filtro de Sobreventa Inicial:**
    * El patrón solo se valora si el RSI ha ingresado previamente en la zona de **26 o inferior**.
* **Condicional de Zona de Gatillo (40-60):**
    * Una vez detectada la divergencia tras la sobreventa, la entrada se activa únicamente cuando el RSI recupera el rango **40-60**.
    * Requisito Crítico: El precio debe haber cerrado y sostenerse por encima de la **EMA 20** simultáneamente.
* **Lectura de Extremos:**
    * Los valores extremos funcionan como alerta temprana de la iniciación de una nueva estructura de precio.

### 3. Verificación de Acción del Precio (Price Action)
Criterios de validación visual obligatorios antes de la ejecución:

* **Análisis Multi-Temporal (MTF):**
    * Verificar siempre en temporalidades de **4HS y Diario (1D)**.
* **Identificación de Patrones de Reversión:**
    * Buscar la formación de tres velas consecutivas seguidas de un **Doji final**.
    * Validar con otros patrones de velas japonesas de reversión (Envolventes, Martillos, etc.).
* **Filtro de Volumen:**
    * Confirmar que el volumen sea decreciente o "flat" durante la formación del suelo y aumente en la ruptura de la EMA 20.

---
> **Nota de Control:** Si un patrón en 1H supera las 45 horas sin activarse en la zona de gatillo, la probabilidad de éxito disminuye y el setup debe ser reevaluado.
