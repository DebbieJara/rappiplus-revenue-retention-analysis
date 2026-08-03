# RappiPlus: Revenue & Retention Analysis

![Python](https://img.shields.io/badge/Python-pandas%20%7C%20numpy%20%7C%20statsmodels-3776AB?logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-DAX-F2C811?logo=powerbi&logoColor=black)

## Pregunta de negocio

¿Es rentable el negocio de RappiPlus, dónde pierde usuarios en su embudo de conversión, qué tan bien los retiene después de su primera compra, y debería implementar el cambio de diseño de checkout que se probó en un experimento A/B?

## Fuente de datos

Este proyecto se desarrolló como parte de un bootcamp de análisis de datos (Practicum). Los datasets son ficticios, generados con fines educativos, y no representan una operación real de RappiPlus ni de ninguna empresa existente. Se utilizaron dos fuentes:

- Archivos CSV (`orders`, `catalog`, `marketing`): provistos por el bootcamp vía almacenamiento en la nube.
- Base de datos PostgreSQL de entrenamiento (`events`, `users`, `user_activity`): base de datos privada del bootcamp, accesible solo con credenciales de estudiante.

## Resumen ejecutivo

Con un revenue de `$9.62M`, costo de `$3.83M`, inversión en marketing de `$2.87M` y profit neto de `$2.92M` (~30% de margen), el negocio es rentable de forma consolidada. Sin embargo, dos de ocho productos (`Jacket-Winter-M` y `Laptop-Gaming-16GB`) operan con pérdida pese a su alto volumen de ventas. El embudo de conversión, validado con secuencia estricta por usuario, muestra una pérdida severa en cada etapa, con el mayor cuello de botella entre "add payment info" y "purchase" (89.13% de pérdida). El test A/B del nuevo diseño de checkout no mostró una mejora estadísticamente significativa (p = 0.416), por lo que no se recomienda su implementación en este momento.

## Proceso

### Paso 1 — Calidad y limpieza de datos (Python)
Se auditaron 3 datasets en busca de nulos, duplicados, inconsistencias de texto y valores atípicos. Resultado: `orders_clean` (24,936 filas, tras eliminar 100 duplicados y 4 filas inválidas), `catalog_clean` (sin cambios) y `marketing_clean` (nulos de canal resueltos). 50 categorías de producto nulas se recuperaron cruzando con el catálogo; el resto se marcó explícitamente como `"Desconocido"` / `"Sin categoría"` en vez de eliminarse.

### Paso 2 — Rentabilidad y comportamiento de ventas (Python)
KPIs principales: revenue, costo, marketing, profit neto. Ticket promedio calculado con mediana (`$341.41`) en vez de media, tras detectar y excluir 10 filas con cantidades erróneas (10,000–20,000 unidades por pedido). Producto más vendido: `Vacuum-Pro-Black` (6,284 unidades), con alta paridad frente a los siguientes 3 productos.

### Paso 3 — Embudo de conversión (SQL)
Consulta con CTEs que valida la secuencia real de cada usuario (cada etapa exige haber completado también las etapas anteriores, en orden cronológico), en vez de contar eventos de forma independiente. Mayor pérdida detectada entre "add payment info" y "purchase" (89.13%).

### Paso 4 — Retención por cohortes (SQL)
Consulta con CTEs y JOINs para calcular el tamaño de cada cohorte mensual y su retención semanal, presentada en formato ancho (cohorte × semana).

### Paso 5 — Test A/B del checkout (Python)
Z-test de dos proporciones sobre 10,000 usuarios (`control` vs. `tratamiento`). Resultado: sin diferencia estadísticamente significativa (p = 0.416). Recomendación: no implementar el cambio sin repetir la prueba con una muestra mayor.

### Paso 6 — Dashboard ejecutivo (Power BI)
Dos páginas principales (Resumen Ejecutivo, Análisis Detallado) más una página de detalle por producto vía drill-through. Incluye medidas DAX para prorrateo de marketing por producto (`CALCULATE` + `ALL` sobre la tabla completa de pedidos), formato condicional para rentabilidad, y navegación por botones.

## Hallazgos y recomendaciones

**1. Rentabilidad negativa en dos productos, pese a ventas altas.** `Jacket-Winter-M` (`-$57,235.12`) y `Laptop-Gaming-16GB` (`-$417,350.61`) muestran profit prorrateado negativo; en ambos, el costo total consume casi la totalidad del revenue. Recomendación: priorizar la revisión de su estructura de costo y precio de venta.

**2. 45 pedidos con producto no identificable.** 0.12% del total vendido (`$12,262.62` en revenue) no pudo vincularse a ningún producto del catálogo durante la limpieza. Recomendación: investigar en el sistema de origen por qué estos registros no capturaron el nombre del producto.

**3. Pérdida severa en la captura de pago.** La mayor caída del embudo (89.13%) ocurre entre "add payment info" y "purchase". Recomendación: priorizar pruebas de UX en ese paso específico del checkout antes que en otras etapas del funnel.

**4. Cambio de checkout sin evidencia de mejora.** El test A/B no encontró diferencia significativa en conversión. Recomendación: no invertir en el rediseño actual sin repetir el experimento con mayor tamaño de muestra.

## Limitaciones y hallazgos de calidad de datos conocidos

- 45 pedidos sin producto identificable en los datos de origen (ver Hallazgo 2).
- Las consultas SQL de los Pasos 3 y 4 requieren credenciales propias de la base de datos de entrenamiento del bootcamp para ejecutarse en vivo. Como alternativa de reproducibilidad, se incluye una exportación estática de las tablas `events`, `users` y `user_activity` en `data/sql_export/`.

## Herramientas utilizadas

| Etapa | Herramienta | Detalle |
|---|---|---|
| Limpieza y EDA | Python | pandas, numpy |
| Visualización exploratoria | Python | matplotlib, seaborn |
| Test estadístico | Python | statsmodels (test de proporciones de dos muestras) |
| Embudo y cohortes | SQL (PostgreSQL) | CTEs, JOINs, CASE WHEN, funciones de fecha |
| Dashboard | Power BI | DAX (medidas calculadas), formato condicional, drill-through, navegación por botones |

## Estructura del repositorio

rappiplus-revenue-retention-analysis/
├── README.md
├── notebook/
│ └── RappiPlus_Analisis.ipynb
├── data/
│ ├── orders_clean.csv
│ ├── catalog_clean.csv
│ ├── marketing_clean.csv
│ └── sql_export/
│ ├── events.csv
│ ├── users.csv
│ └── user_activity.csv
└── dashboard/
├── dashboard_resumen_ejecutivo.png
├── dashboard_analisis_detallado.png
└── dashboard_detalle_producto.png
Los datasets crudos (`orders`, `catalog`, `marketing`) no se incluyen en el repositorio porque se cargan directamente vía URL dentro del notebook. Las consultas SQL de los Pasos 3 y 4 viven embebidas en el notebook, mostrando el trabajo original con PostgreSQL; las credenciales de conexión fueron enmascaradas antes de la publicación.

## Dashboard

El dashboard completo (interactivo, con drill-through y filtros) está disponible como archivo `.pbix` descargable, ya que no fue publicado en Power BI Service:

🔗 [Descargar dashboard (.pbix) y CSVs limpios en Google Drive](https://drive.google.com/file/d/1iVvas_At1xCuqTi11TJoM9vpaUSZEGI9/view?usp=sharing)

A continuación, capturas de las 3 páginas principales:

**Resumen Ejecutivo**
![Resumen Ejecutivo](dashboard/dashboard_resumen_ejecutivo.png)

**Análisis Detallado**
![Análisis Detallado](dashboard/dashboard_analisis_detallado.png)

**Detalle por Producto (drill-through)**
![Detalle por Producto](dashboard/dashboard_detalle_producto.png)

## Reproducibilidad

Para reproducir el análisis en Python:
- El notebook (`RappiPlus_Analisis.ipynb`)
- Los CSVs de `data/` (opcional, ya que también se cargan por URL dentro del notebook)

Para reproducir el embudo y las cohortes (Pasos 3 y 4):
- Credenciales propias de la base de datos de entrenamiento del bootcamp, o
- Los CSVs estáticos en `data/sql_export/` como alternativa sin conexión en vivo

Para reproducir el dashboard en Power BI:
- El archivo `.pbix` (disponible en el enlace de Google Drive)
- Los 3 CSVs limpios incluidos en `data/`

---

By Deborah Jara | People & Learning Analytics · Business Intelligence | México

[LinkedIn](https://linkedin.com/in/deborahjara) · [GitHub](https://github.com/DebbieJara)
