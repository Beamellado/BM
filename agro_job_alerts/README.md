# Alertas de empleo agrícola/agronomía — Región Metropolitana

Automatización que revisa periódicamente ofertas de trabajo **y prácticas
profesionales** relacionadas al área agrícola y agronómica en la Región
Metropolitana (Chile) y avisa cuando aparece una publicación nueva.

## Cómo funciona

1. Cada cierto tiempo (ver cadencia abajo), el agente hace búsquedas web
   dirigidas a portales chilenos de empleo (Indeed Chile, Laborum,
   Computrabajo, LinkedIn, Jooble, Portal Agro Chile, BNE, Colegio de
   Ingenieros Agrónomos de Chile) usando palabras clave del área agrícola y
   agronómica, filtrando por Región Metropolitana.
2. Los resultados se comparan contra `state/seen_jobs.json`, que guarda las
   publicaciones ya detectadas (id, cargo, empresa, ubicación, fuente).
3. Si aparece algo que no estaba en el archivo, se agrega como nueva entrada,
   se deja un **borrador en Gmail** con el detalle y se envía una
   **notificación push/escritorio** avisando que hay algo nuevo para revisar.
4. El archivo actualizado se commitea y pushea a este repositorio para llevar
   historial.

## Cadencia

Revisión automática cada 6 horas, mediante un Routine (trigger programado)
de Claude Code Remote.

## Limitación conocida

Indeed, Laborum, Computrabajo, LinkedIn y Chiletrabajos bloquean el acceso
directo automatizado (HTTP 403), incluso a avisos individuales indexados por
buscadores. Por eso:

- Es posible que no se capturen absolutamente todas las publicaciones nuevas
  apenas se publican.
- La deduplicación se hace por combinación de cargo + empresa + ubicación
  (no por URL única del aviso).
- **Los enlaces que se entregan (`search_url`) apuntan a la página de
  búsqueda/listado filtrada del portal, no a un permalink del aviso
  puntual.** Al hacer clic, el enlace sí carga, pero muestra el listado de
  resultados donde debes ubicar el cargo/empresa mencionados — no redirige
  directo al aviso porque esos portales no exponen permalinks accesibles de
  forma automatizada. Si el cargo ya no aparece en el listado, probablemente
  fue retirado o repaginado.

Para máxima cobertura, y para llegar directo al aviso puntual, se recomienda
complementar esto con las alertas nativas de
Indeed/LinkedIn/Computrabajo/Laborum/Chiletrabajos (ellos sí tienen acceso
directo a sus propias bases de datos).

## Envío de correo

El conector de Gmail disponible en este entorno solo puede **crear
borradores**, no enviar correos de forma autónoma (no tiene permiso de
envío). Por eso el flujo deja un borrador listo en Gmail y además dispara
una notificación push para que te enteres al instante; el envío final del
correo (si lo quieres como email real) requiere un clic tuyo en Gmail.

## Archivos

- `state/seen_jobs.json`: histórico de publicaciones ya detectadas
  (línea base generada el 2026-07-14).
