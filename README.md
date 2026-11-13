# Databricks: pipelines de datos, BI y machine learning

Proyectos de la **Especialización en Analítica de Datos (Universidad EAM, 2026)**, módulo de ETL e IA en Databricks.
Autor: Sebastián Davila Henao · [LinkedIn](https://www.linkedin.com/in/sebastian-davila-henao-3979b128b)

> Proyecto académico con datos de práctica (no corresponden a una empresa real).

**Tecnologías:** Databricks · Unity Catalog · PySpark · Delta Lake · SQL · Databricks SQL Dashboards · Hugging Face Transformers · PyTorch · MLflow · pandas / matplotlib

---

## 1. Smart Claims: reclamaciones de seguros de autos (proyecto final)

`TRABAJO 3 - PROYECTO SMART CLAIMS/`

Flujo de punta a punta para apoyar la revisión de reclamaciones: une cliente, póliza, reclamo, telemática del vehículo y fotos del daño, y sugiere una decisión.

```
CSV / imágenes ──► Bronze (Delta) ──► Silver (limpieza + calidad) ──► Gold (tablas integradas)
                                                   │                          │
                                                   ▼                          ▼
                                    Fine-tuning ViT (MLflow)  ──►  Predicción del daño
                                                                              │
                                                                              ▼
                                                   Reglas de negocio ──► Decisión + BI
```

| Capa | Qué hace |
|---|---|
| **Bronze** | Carga 7 fuentes desde un volumen de Unity Catalog a tablas Delta (clientes, pólizas, reclamos, telemática, imágenes y metadata). |
| **Silver** | Limpieza y validación: tipos y fechas, llaves no nulas, prima ≥ 0, vigencia coherente, velocidad ≥ 0, coordenadas válidas, etiqueta de daño desde el nombre de la imagen; celda de verificación de calidad. |
| **Gold** | `aggregated_telematics` (velocidad y ubicación por vehículo), `customer_claim_policy` y `customer_claim_policy_telematics`, con pruebas de integridad de los joins. |
| **ML** | Fine-tuning de `google/vit-base-patch16-224` para clasificar el tipo de daño; experimento, métricas (accuracy general y por clase) y registro del modelo en **MLflow** y **Unity Catalog**; inferencia por lotes con nivel de confianza (alta ≥ 0.8, media ≥ 0.5). |
| **Reglas y BI** | 6 reglas por reclamo (póliza vigente, monto ≤ 3 × prima, predicción confiable, coherencia entre severidad reportada y daño predicho, entre otras) → **LIBERADO / REVISIÓN MENOR / INVESTIGAR** con motivos; visualizaciones de negocio, de predicción y de decisión. |

## 2. Pipeline medallón de una aerolínea

`TRABAJO 2 - SESION 3 Y 4/`

- `01_bronze_ingestion.py`: ingesta de vuelos, aeronaves, aeropuertos y mantenimientos con tipos normalizados y marca de ingesta.
- `02_silver_transform.py`: limpieza, deduplicación por llave y validación de nulos y duplicados.
- `03_gold_analytics.py`: KPIs de **puntualidad** (% a tiempo, retrasados y cancelados por modelo, fabricante, país y mes) y de **costo de mantenimiento** (costo y duración promedio y total por aeronave y tipo).

## 3. Fundamentos: Unity Catalog, SQL y dashboard

`TRABAJO 1 - SESION 1 Y 2/`

Catálogo, esquema y volumen en Unity Catalog; tablas y vistas en SQL; y un **dashboard en Databricks SQL** de ventas mensuales por producto y categoría (unidades, importe total y precio medio).

---

### Cómo ejecutarlo
Los notebooks y scripts están pensados para un workspace de Databricks con Unity Catalog: crear el catálogo indicado en cada archivo, subir los datos al volumen `landing` y ejecutar en orden bronze → silver → gold (→ ML → BI en Smart Claims).
