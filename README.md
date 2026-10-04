# Fran HTML Studio

Editor y ejecutor de proyectos HTML, CSS y JavaScript para teléfono, tablet y PC. Sin dependencias externas, sin compilación y sin cuenta. Interfaz en español.

## Publicar en GitHub Pages

1. Descomprime el ZIP.
2. Crea un repositorio en GitHub y sube **el contenido** de `HTML-Studio`: `index.html`, `app.js`, `style.css`, `sw.js`, `apple-touch-icon.png`, la carpeta `icons/`, `manifest.webmanifest` y este README a la raíz.
3. En **Settings → Pages**, selecciona **Deploy from a branch**, rama **main**, carpeta **/(root)** y guarda.
4. Abre el enlace HTTPS que te dé GitHub. En la primera visita la app activa su ejecutor; recarga si sigue mostrando «Vista simple».
5. En iPhone, abre con Safari y usa **Compartir → Añadir a pantalla de inicio** para abrirla como app.

No publiques el ZIP como único archivo: hay que extraerlo. No hace falta Android Studio, npm ni servidor propio. Este repositorio se publica en https://fravier3.github.io/Editor-HTML/.

## Uso

- **Proyecto** crea un HTML nuevo. **Pegar HTML** crea un proyecto con tu código completo.
- Organiza los proyectos por carpetas. El menú del proyecto permite renombrarlo, moverlo, duplicarlo o eliminarlo.
- **Importar archivos** incorpora HTML, CSS, JS, imágenes, audio, fuentes y otros recursos al proyecto abierto. Los nombres repetidos requieren confirmación antes de reemplazarse.
- **Importar carpeta** crea otro proyecto y conserva sus rutas. Depende del selector de carpetas del navegador; algunos teléfonos no lo admiten.
- El selector sobre el código muestra las rutas de archivos. **＋** crea archivos y carpetas escribiendo una ruta como `css/estilo.css`. Puedes renombrar rutas; debes actualizar las referencias de tu código.
- Si importas archivos separados desde un teléfono, se añaden a la raíz; renómbralos a la ruta que tu HTML espera. Las carpetas son virtuales y derivan de las rutas de archivos; no se guardan carpetas vacías dentro de un proyecto.
- El selector en la vista previa elige el HTML de entrada. Puedes probar varios HTML dentro del mismo proyecto.
- Vistas **Código / Dividida / Resultado**, separador ajustable con ratón, toque o teclas de flecha, fuente ajustable y resultado ampliable. En móvil, la vista dividida es vertical.
- **Vista en vivo** ejecuta tras una pausa al editar. Desactívala para documentos pesados. **Ejecutar** o **Ctrl/Cmd + S** guardan y ejecutan.
- Búsqueda literal con coincidencia de mayúsculas, reemplazo global, historial de edición por archivo durante la sesión y tabulación de dos espacios.
- **Consola** recoge log, info, warn, error y promesas rechazadas desde la página principal. Conserva hasta 200 mensajes visibles. Mensajes de iframes anidados no se capturan.
- **Exportar** descarga un ZIP estándar sin compresión con todos los archivos del proyecto. **Archivo** descarga el archivo abierto. Se importan archivos o carpetas, no ZIP; descomprime primero.
- **Respaldo completo** exporta todos los proyectos y recursos en JSON. **Restaurar** añade los proyectos de un respaldo sin borrar los actuales.
- Los archivos binarios se conservan y exportan. Hay vista previa de imágenes; no se editan como texto. TypeScript, JSX y TSX pueden editarse como texto, pero necesitan transpilarse para ejecutarse en el navegador.

## Cómo ejecuta los proyectos

La app guarda los recursos como bytes en IndexedDB (evitando guardar objetos Blob directamente) y reconstruye los archivos al abrirlos. Las escrituras se realizan en una sola transacción y se conservan los datos del formato anterior. La app guarda proyectos en IndexedDB y un service worker sirve los archivos mediante rutas virtuales bajo `__preview__/ID/`. Así funcionan los enlaces relativos, hojas CSS, scripts clásicos, imágenes y navegación entre HTML. También pueden funcionar módulos y fetch según las políticas de origen y CORS del navegador; compruébalos con tu proyecto.

El modo predeterminado es **Compatibilidad**, pensado para tu propio código. El iframe permite scripts y `allow-same-origin`: admite almacenamiento y más APIs, pero el código comparte el origen de esta app y puede acceder a sus datos. No ejecutes código desconocido en ese modo. Desactívalo en ajustes para aislar el origen; algunas APIs y la carga de recursos locales pueden dejar de funcionar según el navegador. Ese aislamiento no bloquea Internet, formularios, ventanas emergentes ni descargas; no es un entorno para analizar código malicioso.

No se reescribe tu código ni se eliminan scripts. Se inserta un pequeño puente de consola en la respuesta de vista previa; el archivo original permanece intacto. Las CSP del documento pueden bloquearlo o impedir la ejecución de otros scripts. `document.write`, navegación externa y cambios del propio documento pueden dejar de mostrar mensajes en la consola.

## Límites reales

- Se ejecuta lo que soporta el navegador: HTML, CSS y JavaScript, canvas, SVG, audio, etc. PHP, Python, Node.js, frameworks sin compilar y servicios de backend necesitan un servidor externo.
- Cámara, micrófono, ubicación, ventanas emergentes, audio, descargas y pantalla completa dependen de permisos y del navegador. El audio puede requerir un toque. Cambiar el ancho simula un viewport, no otro sistema operativo ni navegador.
- APIs externas siguen sujetas a CORS y conectividad. Rutas que empiezan por `/` apuntan a la raíz del dominio, no a la carpeta del proyecto: usa rutas relativas. Configura rutas de origen al trabajar con librerías que asuman otro servidor.
- No hay límite artificial de líneas o número de proyectos. Sí hay límites de RAM, cuota de almacenamiento y tamaño de archivo del dispositivo. El ZIP se monta en memoria y usa el formato ZIP clásico (sin ZIP64).
- Los datos son locales a este navegador, dominio y dispositivo. GitHub aloja la app, no sincroniza tus proyectos. Borrar datos del navegador, usar modo privado o cambiar de dominio puede perderlos. Guarda respaldos con frecuencia.
- Solicitudes con rangos de bytes para audio/video no tienen implementación especializada; la respuesta es el recurso completo. Reproducción y búsqueda en medios grandes dependen del navegador.
- No hay aislamiento de CPU: un bucle infinito puede bloquear la pestaña. Desactiva la vista en vivo mientras corriges código problemático.
- La app puede abrir sin Internet después de una primera visita exitosa. Librerías, fuentes y APIs externas aún requieren Internet. El service worker usa red primero y la copia inicial como respaldo.
- Abrir el `index.html` de **la app** directamente mediante `file://` no activa el service worker. Úsala publicada con HTTPS o en localhost. El modo simple solo muestra el HTML de entrada y no resuelve los demás archivos importados.

## Probar localmente (opcional)

Desde la carpeta del proyecto, con Python instalado:

```sh
python -m http.server 8000
```

Abre `http://localhost:8000`. En GitHub Pages no necesitas este paso.

## Archivos

`index.html`: interfaz. `style.css`: diseño responsive. `app.js`: edición, proyectos, IndexedDB, importación y exportación. `sw.js`: ejecución virtual y caché offline. `manifest.webmanifest`, `apple-touch-icon.png` e `icons/`: instalación, logo e iconos PNG para iPhone y otros dispositivos.

## Recuperación si falla el almacenamiento

Si el navegador bloquea o cancela el guardado, la interfaz muestra «Solo esta sesión». Puedes continuar editando, probar HTML con CSS y scripts clásicos importados, y exportar tus proyectos. En ese modo, la navegación entre HTML, imports de módulos, fetch de recursos locales y CSS importado pueden no funcionar. Los cambios no son persistentes: exporta antes de cerrar. Nunca se borran automáticamente los proyectos para intentar reparar el almacenamiento.

## Icono y pantalla de inicio (v1.4)

Abre https://fravier3.github.io/Editor-HTML/?v=20261004-6 en Safari. Pulsa **Compartir → Añadir a pantalla de inicio → Añadir**. El acceso se llama **HTML Studio**, muestra el icono azul y violeta y se abre como app independiente. Si ya tenías un acceso anterior, abre primero el nuevo enlace y vuelve a añadirlo para que iOS tome el icono actualizado.

El icono para Apple es un PNG opaco de 180 × 180. El manifest incluye PNG de 192 y 512 píxeles y una variante maskable con margen de seguridad. Los archivos usan rutas relativas compatibles con la subcarpeta de GitHub Pages y se incluyen en la caché de la app. El nuevo logo aparece también en el encabezado.
