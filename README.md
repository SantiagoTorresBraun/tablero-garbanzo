# Tablero Garbanzo 25/26

Tablero de procesamiento de garbanzo de la campaña 25/26: los 17 análisis pedidos en la
solapa `Analisis` del libro de cálculo, resueltos sobre los 27 procesos de las dos plantas
de selección (Cereales Viel y 4 Pulses).

**Link:** https://santiagotorresbraun.github.io/tablero-garbanzo/

## ⚠️ Este repositorio es público

GitHub Pages sólo publica desde repositorios públicos, así que **cualquiera con la URL
puede abrir el tablero**, y cualquiera que navegue este repositorio puede leer los datos.
El tablero contiene nombres de clientes, contratos de flujo de masa, kilos y mermas por
contrato, y la eficiencia comparada de las dos plantas.

Mitigaciones aplicadas — son fricción, no privacidad:

- `robots.txt` con `Disallow: /` y `<meta name="robots" content="noindex, nofollow, noarchive">`,
  para que los buscadores no lo listen.
- Ninguna referencia al Google Sheet de origen viaja dentro del HTML, así que el link al
  libro completo no queda expuesto acá.

Para bajarlo de internet: borrar este repositorio, o desactivar Pages en
*Settings → Pages → Source: None*. Tené en cuenta que el contenido puede quedar en cachés
de terceros y en el historial de git.

## Cómo está armado

`index.html` es un único archivo autocontenido de ~173 KB. Los datos van embebidos como
JSON dentro del `<script>`; no hace ninguna llamada de red salvo la hoja de estilos de
Google Fonts. No hay build, no hay dependencias, no hay backend: se puede abrir con doble
clic desde el disco y funciona igual.

Dentro del tablero:

- **Filtros** de planta, proceso y calibre, arriba, que reescalan todas las tarjetas a la vez.
- **Vista de tabla** en cada tarjeta, con los valores exactos detrás de cada gráfico.
- **Casilla de corrección de `GI-81`**, activada por defecto, para ver el dato con y sin el
  PY duplicado.
- Modo claro y oscuro, siguiendo el tema del sistema, con botón para forzar uno.

## Cómo se actualiza

El pipeline que genera el tablero **no está en este repositorio**, a propósito: incluye el
identificador del Google Sheet de origen, y publicarlo acá expondría el libro completo
(741 filas) además del tablero. Vive local, y son tres pasos:

1. Bajar el libro entero como `.xlsx` desde el endpoint de export del sheet.
2. `build.py` vuelca cada solapa a CSV con `openpyxl` y consolida los 27 procesos en un
   `data.json`, cruzando las seis solapas de datos por `N° Proceso`.
3. Ese JSON se inyecta en la plantilla y se regenera `index.html`.

Después: `git add index.html && git commit && git push`. Pages republica solo en un minuto.

## Advertencias sobre los datos de origen

Tres cosas del libro que condicionan la lectura y que conviene corregir en la fuente:

1. **`GI-81` tiene el PY duplicado**: 150.081 kg contra 75.040 kg de MP y de PT. Es el
   origen completo del desvío de ~75.000 kg de Cereales Viel. El tablero lo corrige por
   defecto.
2. **Las filas `Tipo=PY` de `Base Datos GZ` no son una proyección independiente**: replican,
   calibre por calibre, uno de los dos escenarios de `PY vs. Real GZ` — 17 procesos copian
   la Simulación, 6 copian el PY y 4 no coinciden con ninguno. Todo el análisis de
   proyección contra realidad se calcula desde `PY vs. Real GZ`.
3. **Los daños proyectados no están en `Base Datos GZ`** (ahí las filas PY tienen los
   defectos casi todos en cero); salen de `Daños MP PY Real`, que reconcilia exacto con la
   eficiencia de `MP vs PT`.

`PR 603` es el único proceso sin `Fin Proceso` y el único ausente de `MP vs PT`: está en curso.

El balance de masa cierra exacto: MP 3.095.360 kg = PT comercial 2.730.176 kg + descartes
365.184 kg, porque las filas PT incluyen los descartes como valores de `Calibre`.
