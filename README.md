# Trabajo Práctico Final — Introducción al Marketing Online y los Negocios Digitales

Breve repositorio con los artefactos del trabajo práctico final: datos en bruto, scripts de desnormalización y generación de un pequeño Data Warehouse (DIMs y FACTs), y los diagramas del modelo.

Consigna y documento principal: Trabajo Práctico - Consigna.pdf

Dashboard final (Power BI):

[![Abrir Power BI](https://img.shields.io/badge/Ver-Power%20BI-008cff?logo=power-bi)](https://app.powerbi.com/view?r=eyJrIjoiZjg5YzM0YTktNTQ4OC00ZTIwLWJlOWQtMjFmYmNjMDg5ZGJlIiwidCI6IjNlMDUxM2Q2LTY4ZmEtNDE2ZS04ZGUxLTZjNWNkYzMxOWZmYSIsImMiOjR9)

## Estructura del repositorio

- `raw/` — archivos originales (CSV) de los datos fuente.
- `STAGING/` — archivos desnormalizados preparados para construir las tablas DW (creados por `Script/Desnormalizador.py`).
- `DW/` — salida final: `dim_*.csv` y `fact_*.csv` generadas por `Script/DimFacts.py`.
- `Script/` — scripts usados para procesar los datos:
	- `Desnormalizador.py`: normaliza y exporta datos a `STAGING/`.
	- `DimFacts.py`: genera dimensiones y hechos en `DW/`, creando surrogate keys y mapeos consistentes.
- `assets/` — imágenes y diagramas (DER, star schemas).
- `organizado/README.md` — este archivo (versión amigable para GitHub).

## Qué hace el pipeline

1. `Desnormalizador.py` toma los CSVs en `raw/` y crea archivos desnormalizados en `STAGING/`.
2. `DimFacts.py` lee `STAGING/`, crea dimensiones (`dim_*.csv`) con surrogate keys (enteros empezando en 1) y genera hechos (`fact_*.csv`) que referencian las dimensiones por esas surrogate keys (siguiendo estilo Kimball).

Algunas decisiones importantes:
- Las claves naturales se mantienen en las dimensiones renombradas como `*_natural_key` y se añade `*_key` como surrogate integer.
- Otros hechos no referencian `order_key` directamente; se mapean a las surrogate keys de las dimensiones (customer_key, channel_key, store_key, address_key, date_key).

## Requisitos y entorno (recomendado)

Se probó y lanzó el pipeline en un entorno virtual de Python. Para reproducir los mismos paquetes se incluye `requirements.txt` en la raíz.

Recomendado (PowerShell):

```powershell
python -m venv .venv
& ".\.venv\Scripts\python.exe" -m pip install -U pip setuptools wheel
& ".\.venv\Scripts\python.exe" -m pip install -r requirements.txt
```

## Cómo ejecutar

1. Asegúrate de tener los CSVs originales en `raw/`.
2. Ejecuta el desnormalizador para crear `STAGING/`:

```powershell
& ".\.venv\Scripts\python.exe" .\Script\Desnormalizador.py --raw-dir .\raw --staging-dir .\STAGING
```

3. Ejecuta el generador de DIM/FACTs para crear `DW/`:

```powershell
& ".\.venv\Scripts\python.exe" .\Script\DimFacts.py --staging-dir .\STAGING --dw-dir .\DW
```

Los archivos resultantes (por ejemplo `DW/dim_customer.csv`, `DW/fact_sales_order.csv`, `DW/fact_sales_order_item.csv`) estarán listos para análisis o carga en una herramienta BI.

## Notas adicionales

- `requirements.txt` fue generado a partir del entorno virtual usado para las pruebas y contiene las versiones de `pandas` y `numpy` que evitaron incompatibilidades binarias.
- Si encuentras errores relacionados con versiones de paquetes (p.ej. `numpy.dtype size changed`), recrea el `.venv` e instala desde `requirements.txt`.

## Diagrama y recursos

### Diagrama Entidad-Relación

![Diagrama Entidad Relación](./assets/DER.png)

### Star Schemas (tablas de hechos)

**Fact Sales Order**

![Modelo Estrella Sales Order](./assets/order.png)

**Fact Sales Order Item**

![Modelo Estrella Sales Order Item](./assets/orderitem.png)

**Fact NPS**

![Modelo Estrella NPS](./assets/nps.png)

**Fact Payment**

![Modelo Estrella Payment](./assets/payments.png)

**Fact Shipment**

![Modelo Estrella Shipment](./assets/shipment.png)

**Fact Web Session**

![Modelo Estrella Web Session](./assets/session.png)

Si las imágenes no se muestran en la vista previa de GitHub, abrí la carpeta `assets/` y abrí los PNGs directamente.

