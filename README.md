# Ecosistema de Automatización IA · Atención de consultas de empresas socias

Entrega final del curso de AI Automation. Sistema que recibe consultas de empresas socias de una cámara empresaria por formulario web, las clasifica y redacta un borrador de respuesta con IA usando la memoria de cada empresa, espera aprobación humana en Slack y recién entonces responde por Gmail, dejando registro completo en Airtable.

## Stack

| Categoría | Tecnología |
|---|---|
| Orquestador | n8n (autoalojado) |
| Base de datos / memoria | Airtable (tablas Empresas, Consultas, Ejecuciones, vinculadas) |
| Procesamiento IA | OpenAI gpt-4.1-nano (clasificación) + Claude Haiku 4.5 (redacción con memoria) |
| Human-in-the-loop | Slack, nodo Send and Wait con botones Aprobar / Rechazar |
| Canal de salida | Gmail, con mapeo de threadId |

## Contenido del repositorio

```
docs/
  Entrega Final - Ecosistema de Automatizacion IA.pdf   ← documento principal (5 capítulos de la rúbrica)
  Diagrama de Arquitectura.pdf                           ← diagrama en alta resolución
  diagrama_arquitectura.png
workflows/
  workflow_n8n_consultas_socias.json                     ← workflow principal (20 nodos)
  workflow_n8n_error_handler.json                        ← error workflow global
evidencias/
  01_workflow_n8n.png
  02_ejecuciones_n8n_5_corridas.png
  03_slack_aprobacion.png
  04_email_recibido.png
  05_airtable_estados.png
  06_airtable_ejecuciones_errores.png
  07_dashboard.png
  video_demo.mp4 (o enlace)
pre-entregas/
  PE1, PE2, PE3 (diseño lógico, JSON y justificación de n8n, escenario Make)
```

## Enlaces obligatorios

- Base de datos Airtable (modo lectura): pendiente de despliegue
- Dashboard de control (Interface / Shared View pública): pendiente de despliegue (especificación completa en cap. 5 del PDF)
- Video demo (3 min): pendiente de despliegue

## Cómo importar y correr

1. En n8n, importar primero `workflow_n8n_error_handler.json` y copiar su ID.
2. Importar `workflow_n8n_consultas_socias.json`; en Settings del workflow, seleccionar el error workflow anterior.
3. Definir la variable de entorno `AIRTABLE_BASE_ID` en la instancia de n8n.
4. Crear credenciales de Airtable, OpenAI, Anthropic, Slack y Gmail y asignarlas a los nodos correspondientes. Ningún secreto viaja en los JSON.
5. Crear la base de Airtable con el esquema del capítulo 2 del PDF (tablas Empresas, Consultas, Ejecuciones y sus campos).
6. Crear los canales de Slack `#aprobaciones-socios` y `#alertas-automatizacion`.
7. Activar el workflow y enviar un POST a `/webhook/consulta-socio` con `{nombre, empresa, email, consulta}`.

## Estado de la entrega

Esta entrega contiene el diseño completo, los workflows importables y la documentación técnica de los cinco criterios de la rúbrica. El despliegue en vivo (corridas de prueba, capturas, dashboard publicado y video) está planificado según el protocolo del capítulo 6 del PDF; la carpeta `evidencias/` y los enlaces de abajo quedan reservados para ese material.

## Check de seguridad

- Anti bucle: trigger por webhook y filtro `origen = formulario`; n8n nunca dispara sobre registros que él mismo crea.
- Tipos correctos en filtros: validación estricta (número, booleano, regex de string).
- Prompts dinámicos: todas las variables vienen del nodo de contexto, sin texto de negocio hardcodeado.
