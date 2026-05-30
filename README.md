# gota-pp-optimization-monte-carlo-simulation

Simulación y comparación de escenarios de riego por goteo para optimizar el uso de agua en cultivos, a partir de variables ambientales de **NASA POWER** y una predicción de lámina de riego generada por un modelo de Machine Learning. El proyecto estima, para distintas estrategias de riego, el consumo de agua, el ahorro hídrico y el ahorro económico, identificando la estrategia que reduce el consumo sin alejarse del óptimo agronómico.

## Objetivo

El riego tradicional se calcula a partir de la evapotranspiración del cultivo (ETc). Este proyecto evalúa qué tan bien una predicción de Machine Learning puede sustituir o reducir esa lámina de riego, y cuánta agua y dinero se ahorran al aplicar reducciones controladas (2 %, 5 % y 10 %) sobre la predicción del modelo.

## Datos

`dataset_con_objetivo_simulacion.csv` (~74 MB) contiene registros horarios por municipio del estado de Sonora, con tres grupos de variables:

- **Variables ambientales (NASA POWER):** `ALLSKY_SFC_SW_DWN` (radiación), `T2M` (temperatura a 2 m), `RH2M` (humedad relativa), `QV2M` (humedad específica), `PRECTOTCORR` (precipitación), `PS` (presión), `WS2M` (viento), `GWETTOP` / `GWETROOT` / `GWETPROF` (humedad del suelo).
- **Variables agronómicas y derivadas:** `dia_ciclo`, `kc` (coeficiente de cultivo), `et0_ma7` (ET0 media móvil 7 días), `prec_ac3d` (precipitación acumulada 3 días), y rezagos (`et0_lag1d`, `et0_lag3d`, `T2M_lag1d`, `T2M_lag3d`).
- **Contexto y objetivos:** `Municipio`, `etapa_fenologica`, `agua_optima_mm` (lámina óptima de riego según ETc, valor de referencia) y `prediccion_ganador` (lámina predicha por el modelo de ML ganador).

## Escenarios simulados

A partir de `agua_optima_mm` (referencia tradicional) y `prediccion_ganador` (ML), se construyen cinco escenarios de lámina de riego:

| Escenario | Definición |
|-----------|------------|
| `Tradicional_ETc` | Lámina óptima por ETc (`agua_optima_mm`) — línea base |
| `ML_original` | Predicción del modelo de ML |
| `ML_menos_2` | Predicción ML reducida un 2 % |
| `ML_menos_5` | Predicción ML reducida un 5 % |
| `ML_menos_10` | Predicción ML reducida un 10 % |

## Supuestos económicos

| Parámetro | Valor |
|-----------|-------|
| Área | 1 ha |
| Conversión | 10 m³ por mm·ha |
| Precio del agua | 4 MXN/m³ |

## Métricas calculadas

Para cada escenario el notebook calcula:

- `consumo_total_mm` — consumo total de agua (mm).
- `ahorro_mm` — ahorro de agua respecto a la línea base tradicional.
- `porcentaje_ahorro` — ahorro porcentual sobre la línea base.
- `volumen_m3_ha` — volumen total de agua (m³/ha).
- `costo_total_mxn` — costo del agua (MXN).
- `ahorro_mxn` — ahorro económico estimado (MXN/ha).
- `error_promedio_pct` — error medio absoluto porcentual respecto a la lámina óptima `agua_optima_mm`.

Finalmente se generan tres gráficas de barras comparativas: consumo total de agua, porcentaje de ahorro y ahorro económico por escenario.

## Estructura del repositorio

```
.
├── simulacion_opti_riego.ipynb          # Notebook de simulación y análisis
├── dataset_con_objetivo_simulacion.csv  # Dataset con variables, objetivo y predicción
└── README.md
```

## Cómo ejecutar

1. Instala las dependencias:

   ```bash
   pip install pandas numpy matplotlib
   ```

2. Abre `simulacion_opti_riego.ipynb` en Jupyter o VS Code.

3. **Actualiza la ruta del dataset** en la celda de carga para que apunte al CSV de este repositorio:

   ```python
   df = pd.read_csv("dataset_con_objetivo_simulacion.csv")
   ```

4. Ejecuta todas las celdas para reproducir la tabla de resultados y las gráficas comparativas.

## Notas

- Los registros sin `agua_optima_mm` o sin `prediccion_ganador` se descartan antes de simular.
- Las predicciones negativas del modelo se recortan a 0 (`clip(lower=0)`).
- Proyecto desarrollado para el curso de Simulación Monte Carlo (URC, semestre 6).
