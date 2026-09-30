# Café Andino: ecosistema de automatización IA autónomo

**Entrega Final · AI Automation · Coderhouse**
Autora: Lourdes Stephane Tupayachi Ochoa · Septiembre de 2026

Sistema que genera publicaciones de LinkedIn para Café Andino, una marca peruana de café de especialidad (caso ficticio). El sistema toma una idea registrada en la base de datos, busca información validada de la marca (RAG), redacta la publicación con IA, verifica que cumpla los lineamientos y la envía a revisión. Solo cuando una persona la aprueba desde el correo, el contenido se envía al destinatario final. Cada resultado, incluidos los errores, queda registrado y alimenta un panel de control.

## Documentación principal

- **Informe completo (PDF):** [Descargar el informe](https://github.com/lstupayachio/EntregaFinal-CoderhouseAIAutomation-CafeAndino/raw/main/informe/Entrega_Final_Cafe_Andino.pdf) · [Verlo en línea](https://drive.google.com/file/d/1zSQySqtRfmtNhulnSFdexxFxFPNGCjQg/view?usp=sharing)
- **Diagrama de arquitectura (PDF):** [Descargar el diagrama](https://github.com/lstupayachio/EntregaFinal-CoderhouseAIAutomation-CafeAndino/raw/main/diagrama/Diagrama_de_Arquitectura_Cafe_Andino.pdf) · [Ver como imagen](diagrama/Diagrama_de_Arquitectura_Cafe_Andino.jpg)

> Si el visor de GitHub muestra el mensaje "Unable to render code block", usa los enlaces de descarga: los archivos están completos y se abren con cualquier lector de PDF.

El informe incluye los 5 criterios de la rúbrica:

| # | Criterio | Sección del informe |
|---|---|---|
| 1 | Mapa de arquitectura | Sección 1 |
| 2 | Estructuras de datos documentadas | Sección 2 |
| 3 | Optimización de costos | Sección 3 |
| 4 | Seguridad y resiliencia | Sección 4 |
| 5 | Dashboard de control | Sección 5 |

## Tecnologías

| Categoría | Herramienta | Función |
|---|---|---|
| Orquestador | n8n | Coordina el flujo, decide rutas y maneja errores |
| Base de datos | Airtable | Ideas, base de conocimiento e historial de ejecuciones |
| Procesamiento IA | OpenAI (gpt-4o-mini) | Redacción con RAG y verificación de lineamientos |
| Canal de salida | Gmail | Aprobación humana (Send and Wait) y envío final |

## Enlaces

- **Base de datos en modo lectura:** https://airtable.com/apphPqIraPT0aSqFS/shr9RAaNSUHF7oBmV
- **Dashboard - Control de ejecuciones:** https://airtable.com/apphPqIraPT0aSqFS/shrgDb91ZpaSy7KZm
- **Dashboard - Estado del pipeline:** https://airtable.com/apphPqIraPT0aSqFS/shrwyvdBY5PPrMVjr
- **Video demostrativo:** https://drive.google.com/file/d/1AstS8agiYpyCzhTRR9mNB3bV36wIQUKO/view?usp=sharing

> **Aviso antes de ver el video:** esta grabación muestra el sistema en funcionamiento real, por lo que en algunas pantallas aparece información personal de la autora, como su dirección de correo electrónico. El video se comparte únicamente con fines de aprendizaje y evaluación académica. Se solicita no copiar, difundir ni utilizar esa información fuera de este contexto.
>
> El video se grabó después de completar y documentar las pruebas, con registros creados exclusivamente para la demostración. El caso de fallo de la API de IA no aparece en la grabación; está documentado en la sección 4 y en el apartado 6.2 del informe (ejecuciones #13 y #14).

## Contenido del repositorio

```
EntregaFinal-CoderhouseAIAutomation-CafeAndino/
├── README.md
├── informe/      Informe completo en PDF
├── diagrama/     Diagrama de arquitectura (PDF e imagen)
├── workflows/    JSON de los dos flujos de n8n
└── evidencias/   Capturas de pantalla de las pruebas
```

### Flujos de n8n (`workflows/`)

- `Cafe_Andino_-_Pipeline_de_Contenido_con_IA.json`: flujo principal. Trigger cada 8 horas, validación, RAG, dos nodos de IA, aprobación humana por Gmail, envío y cuatro rutas de error.
- `Cafe_Andino_-_Registro_de_errores_criticos.json`: flujo de respaldo con Error Trigger. Registra fallas no previstas y alerta al revisor por correo.

> **Nota sobre datos personales:** en los archivos JSON publicados, el correo electrónico real del revisor y del destinatario se reemplazó por "correo.personal@ejemplo.com", para no exponer información personal. En el sistema en funcionamiento, ambos campos usan un correo personal de la autora. Los JSON no contienen claves ni tokens: las credenciales quedan resguardadas en n8n.

### Evidencias (`evidencias/`)

| Archivo | Qué muestra |
|---|---|
| 01 a 03 | Las tres tablas de Airtable al cierre de las pruebas |
| 04 y 05 | Ejecuciones #14 (ruta completa) y #13 (fallo de la API), fuente de los JSON del informe |
| 06 | Consumo de tokens por nodo de IA |
| 07 y 08 | Flujo principal publicado y flujo de errores críticos |
| 09 | Ruta de error "sin contexto" |
| 10 a 12 | Aprobación humana: correo de solicitud, correo final y registro rechazado |
| 13 y 14 | Dashboards públicos |
| 15 a 18 | Ciclo de aprobación y secuencia de fallo de la API con su recuperación |
| 19 | Ejecución automática del trigger, sin intervención manual |

## Pruebas realizadas

Se realizaron 8 ejecuciones reales: 7 pruebas que cubren todas las rutas del flujo y 1 ejecución automática del trigger.

| Ruta | Resultado |
|---|---|
| Ruta feliz (2 casos) | Enviado y registrado como Éxito |
| Rechazo humano | Rechazado y registrado como Rechazo |
| Datos incompletos | Error en etapa Validación |
| Sin contexto de la marca | Error en etapa RAG |
| Fallo de la API de IA | Error en etapa Procesamiento IA, luego recuperado y enviado |
| Ejecución automática | Sin pendientes; terminó sin acciones |

**KPIs al cierre de las pruebas:** 7 ejecuciones registradas, 42,9 % de éxito, 14,3 % de rechazo humano y 42,9 % de errores (provocados a propósito como parte del test de estrés).

> Nota: n8n muestra la hora de Lima (UTC-5) y Airtable registra la hora en UTC. El dashboard es una vista en vivo y puede mostrar ejecuciones posteriores a la fecha del informe.
