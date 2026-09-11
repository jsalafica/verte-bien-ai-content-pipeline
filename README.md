# Verte Bien - Ecosistema Autónomo de Contenidos con IA

Proyecto final del curso **AI Automation - Coderhouse**.

## Descripción

Sistema autónomo para generar, validar y distribuir contenidos de Instagram para **Verte Bien**, con:

- **n8n** como orquestador.
- **Airtable** como base operativa y de trazabilidad.
- **OpenAI GPT-5 mini** para generación estructurada.
- Recuperación selectiva de contexto desde Airtable.
- **Slack** para Human-in-the-loop (HITL).
- Registro de tokens, costos, duración, resultados y errores.
- Rutas de error y controles anti-loop.
- Dashboard interno + Shared View pública de solo lectura.

## Arquitectura

El flujo principal parte de un cambio de estado en Airtable:

`Airtable Trigger → Validaciones → Registro de ejecución → Recuperación RAG → OpenAI → Persistencia → Slack HITL`

La decisión humana se procesa en un segundo workflow:

`Slack Trigger → Validación de comando/thread → Búsqueda por Slack Message TS → Control de estado → APROBAR / RECHAZAR`

Solo la rama aprobada supera el control final y llega a la publicación simulada.

## Entregables

Los cinco documentos principales están disponibles en [`/docs`](./docs):

1. Arquitectura del ecosistema.
2. Manual operativo de datos.
3. Matriz de costos y optimización.
4. Seguridad, resiliencia y HITL.
5. Dashboard de control.

## Workflows n8n

En [`/workflows`](./workflows):

- `verte_bien_pipeline_contenidos.json`
- `verte_bien_hitl_slack.json`

Los archivos exportados contienen referencias a credenciales de n8n, pero no incluyen los tokens/API keys reales.

## Esquemas JSON

En [`/schemas`](./schemas) se incluyen los contratos principales de datos:

- `contenido.schema.json`
- `rag.schema.json`
- `ia_output.schema.json`
- `hitl.schema.json`

## Evidencias

Las capturas finales deben ubicarse en [`/screenshots`](./screenshots).

## Dashboard / Shared View

Vista pública de solo lectura de Ejecuciones:

https://airtable.com/app6NCumZgmuvFNA7/shrlujDB15ERjbJoC

KPIs finales documentados:

- Ejecuciones: **10**
- Errores: **1**
- Tasa de error: **10%**
- Costo total IA: **US$ 0,011356**
- Duración promedio IA: **8.196 ms**
- Resultados: **8 Exitoso / 1 Rechazado / 1 Error**

## Seguridad y resiliencia

Controles principales:

- Filtro anti-loop por estado `Generando`.
- Validación de `Idea Semilla` antes de consumir IA.
- Restricciones de prompt para no inventar información médica/comercial.
- Structured Outputs mediante JSON Schema.
- Rutas de error para datos incompletos, OpenAI y Slack.
- Correlación de decisiones HITL mediante `thread_ts`.
- Control de estado `En revision` para evitar reprocesamiento.
- Segundo control de `Aprobado=true` antes de la acción crítica.

## Pruebas

Se ejecutaron escenarios felices e infelices, incluyendo:

- Aprobación HITL.
- Rechazo HITL.
- Idea Semilla vacía.
- Error de modelo OpenAI.
- Error de canal Slack.
- Intento de decisión duplicada.

## Costos

La configuración optimizada utiliza GPT-5 mini con:

- Reasoning Effort: `Low`
- Maximum Number of Tokens: `600`
- Verbosity: `Low`
- JSON Schema
- Prompt Cache Key estable

En las pruebas del proyecto, la configuración optimizada redujo aproximadamente un **49,3%** el costo frente a la configuración base comparada.

## Nota sobre publicación

La publicación final del proyecto académico se **simula mediante Slack**. El mecanismo HITL y los controles previos están implementados de forma que el nodo final pueda reemplazarse por un conector real de publicación sin modificar la lógica de aprobación.
