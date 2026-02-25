# LXD

Plataforma de diseno instruccional asistida por IA que guia a Learning Experience Designers (LXD) en un proceso estructurado de 5 pasos para crear, organizar y exportar blueprints educativos completos, aplicando metodologias como ABC y Backwards Design.

## Objetivo del MVP

LXD resuelve un dolor operativo claro: hoy el diseno instruccional suele ocurrir en herramientas desconectadas (documentos, hojas de calculo, pizarras y correo), provocando perdida de contexto, inconsistencias y retrabajo en produccion.

El resultado esperado del MVP es que el diseniador complete en una sola experiencia guiada un blueprint:

- metodologicamente coherente
- estructurado de punta a punta
- exportable a produccion (Adobe Captivate) sin ambiguedades

## Usuarios

- Usuario principal: Diseniador Instruccional / Learning Experience Designer (LXD)
- Usuario secundario: Coordinador academico que revisa y aprueba el blueprint
- Usuario secundario: Desarrollador de contenido en Adobe Captivate que implementa el JSON exportado

## Flujo principal (inicio a fin)

1. Nuevo Proyecto
2. Strategy: define problema de negocio, audiencia y OKRs/KLRs
3. Design: completa canvas de valor (jobs, pains, gains) y disena la interaccion
4. Content: construye estructura jerarquica (Sesion -> Modulo -> Unidad -> Tema -> Escena)
5. Journey: asigna metodo ABC, nivel multimedia y actividad del catalogo por Escena
6. Production: revisa metricas y exporta JSON/blueprint completo

En cualquier etapa se puede abrir el Chat Consultant (Gemini) para orientacion contextual del proyecto.

## Reglas de negocio clave

- Si una Escena tiene `selectedActivityId`, cuenta como Interactive Object en metricas de produccion.
- Si `completionRate` es `100%`, se activa indicador verde de finalizacion en Production.
- Si el usuario cambia el titulo del proyecto, se refleja en el header y en el JSON exportado.
- Si una actividad tiene `tool: 'Adobe Captivate'`, se diferencia visualmente del catalogo estandar.
- Al abrir Chat Consultant, se envia el contexto completo del proyecto para recomendaciones precisas.
- Al exportar, se genera archivo `.json` con nombre `{titulo_proyecto}_LXD.json`.
- Si existe `SyllabusBlueprint`, se incluye en estructura y exportacion.

## Modelo de datos relevante

- `Strategy`: problema, audiencia, OKRs, pains/gains.
- `Structure`: Session -> Module -> Unit -> Topic -> Scene.
- `Scene.abcMethod`: metodo pedagogico aplicado.
- `Scene.mediaLevel` (1-4): nivel multimedia requerido.
- `Scene.selectedActivityId`: actividad del catalogo para completitud.
- `Scene.interactionMoment`: clasificacion de interaccion (In-Situ, Sincronica, Autonoma, etc.).
- `SyllabusBlueprint` (9 secciones): documento ejecutivo del programa.
- `ACTIVITY_CATALOG`: repositorio estatico de actividades (incluye actividades para Captivate).

## Criterios de exito

### Funcionales

- El usuario completa los 5 pasos y exporta JSON valido con toda la estructura poblada.
- Chat Consultant responde con contexto del proyecto actual (no generico).
- Las metricas de produccion (escenas, objetos interactivos, `% completitud`) reflejan estado real.

### De negocio

- El JSON exportado puede ser implementado por desarrollo en Captivate sin aclaraciones adicionales.
- El diseniador no necesita salir de la herramienta para consultar metodologia ABC o catalogo.

### Tecnicos para produccion

- Persistencia entre sesiones.
- Multiples proyectos por usuario.
- API key de Gemini protegida en backend.

## Estado actual y gap principal para produccion

Persistencia actual: estado React en memoria.

- No hay base de datos.
- No hay `localStorage`.
- Los datos se pierden al recargar.

Este es el gap principal a resolver para salida a produccion.

## Restricciones de dominio

- Innovacion frugal: catalogo estatico en v1, exportacion JSON simple, sin integracion directa a Captivate.
- Cumplimiento: la API key de IA debe residir en servidor; no exponer credenciales en cliente.
- Soberania de datos: aislamiento por organizacion y capacidad de exportacion completa para backup/migracion.
- Interoperabilidad: JSON abierto/documentado; en v2 se contempla integracion con Captivate y exportaciones Word/PDF para stakeholders no tecnicos.

## Arquitectura objetivo (suposicion)

El MVP se define como multi-tenant:

- aislamiento de datos por organizacion
- autenticacion por workspace
- base para escalabilidad comercial
- alineacion con requerimientos de cumplimiento

## Metricas de producto

- North Star: blueprints completados y exportados por semana (`completionRate = 100%` + descarga JSON).
- Auxiliar: tasa de escenas con actividad asignada sobre total de escenas.
- Auxiliar: numero de consultas al Chat Consultant por proyecto.
- Time-to-Value: tiempo desde creacion de proyecto hasta primera exportacion completa.
  - objetivo MVP: menor a 8 horas para programa de complejidad media (3 modulos, 15 escenas)

## Run local

Prerequisitos: Node.js

1. Instalar dependencias: `npm install`
2. Configurar `GEMINI_API_KEY` en `.env.local`
3. Ejecutar: `npm run dev`
