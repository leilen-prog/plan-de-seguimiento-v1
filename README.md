# Plan de Desarrollo para Colaboradores (PDC) — Backend + GitHub Pages

## Archivos de este paquete

- `plan_desarrollo_colaboradores.html` — la app completa (frontend). Publícala en GitHub Pages.
- `Code.gs` — el backend. Va dentro de un proyecto de Google Apps Script ligado a un Google Sheet.

---

## Paso 1 — Crear el backend (Google Sheet + Apps Script)

1. Crea un Google Sheet nuevo (por ejemplo "PDC - Base de datos").
2. Menú **Extensiones > Apps Script**.
3. Borra el contenido por defecto y pega todo el contenido de `Code.gs`.
4. Dentro del script, cambia esta línea por una clave propia (no la dejes con el valor de ejemplo):
   ```js
   const SHARED_SECRET = 'CAMBIA-ESTA-CLAVE-2026';
   ```
5. En el menú de funciones (arriba, junto a "Depurar") selecciona `setup` y presiona **Ejecutar** una vez. Esto crea la hoja "Planes" con sus encabezados. La primera vez te pedirá autorizar permisos — acéptalos (es tu propia cuenta accediendo a tu propio Sheet).
6. **Implementar > Nueva implementación**:
   - Tipo: **Aplicación web**
   - Ejecutar como: **Yo (tu cuenta)**
   - Quién tiene acceso: **Cualquier usuario** (ver nota de seguridad más abajo si prefieres restringirlo a tu dominio de Google Workspace)
7. Copia la URL que te da, termina en `/exec`. Esa es tu `APPS_SCRIPT_URL`.
8. Pruébala pegándola en el navegador así, para confirmar que responde:
   ```
   https://script.google.com/macros/s/TU_ID/exec?action=list&token=TU_CLAVE
   ```
   Deberías ver `{"items":[]}`.

---

## Paso 2 — Conectar el frontend al backend

Abre `plan_desarrollo_colaboradores.html` y busca, cerca del inicio del `<script>`, estas dos líneas:

```js
var APPS_SCRIPT_URL = '';
var API_TOKEN = '';
```

Complétalas con lo que copiaste:

```js
var APPS_SCRIPT_URL = 'https://script.google.com/macros/s/TU_ID/exec';
var API_TOKEN = 'la-misma-clave-que-pusiste-en-SHARED_SECRET';
```

Con `APPS_SCRIPT_URL` vacío, la herramienta sigue funcionando exactamente como antes (un solo documento local por navegador). En cuanto la completas, se activa el buscador/creador de colaboradores.

---

## Paso 3 — Publicar en GitHub Pages

1. Crea un repositorio en GitHub (puede ser privado si tienes GitHub Pro/Team, o público si los datos no son sensibles — ver nota de seguridad).
2. Sube `plan_desarrollo_colaboradores.html` y renómbralo a `index.html` en la raíz del repo (o dentro de una carpeta `/docs`).
3. En el repo: **Settings > Pages** → Source: la rama `main`, carpeta `/ (root)` o `/docs` según donde lo pusiste.
4. GitHub te da una URL tipo `https://tu-usuario.github.io/tu-repo/`. Ahí queda publicada la app, con HTTPS automático.

---

## Cómo se usa una vez publicado

- Al entrar sin `?id=` en la URL, aparece el buscador: escribe el nombre del colaborador para cargar su plan existente, o "Crear plan nuevo".
- Cada vez que guardas, la URL se actualiza a `...?id=abc123` — puedes guardar ese enlace o compartirlo con otro líder para que abra directo el plan de esa persona.
- El botón "Cambiar colaborador" (arriba, junto al indicador de sincronización) reabre el buscador sin perder lo que tengas guardado.
- "Borrar todos los datos guardados" ahora **archiva** el plan en vez de borrarlo (no aparece más en las búsquedas, pero sigue en la fila del Google Sheet por si necesitas recuperarlo).

---

## Nota de seguridad — léela si los datos son sensibles

El "token" de este backend es una protección básica contra escrituras accidentales, **no es autenticación real**: cualquiera que abra el código fuente del HTML publicado en GitHub puede leerlo. Es información de desempeño de colaboradores, así que vale la pena decidir conscientemente:

- **Opción simple:** repositorio de GitHub privado + token. Reduce el acceso a quien tenga el enlace y el repo, sin ser blindado.
- **Opción más segura (si usan Google Workspace):** en el paso de implementación del Apps Script, elige "Cualquier usuario de [tu dominio]" en vez de "Cualquier usuario". Así, además del token, Google exige que la persona tenga sesión iniciada con una cuenta de tu organización — esto sí es control de acceso real, y no requiere cambios en el frontend porque el navegador ya envía la sesión de Google automáticamente.

---

## Nota sobre el "Diagnóstico de equipo" (Nivel 1)

Por simplicidad, los campos del diagnóstico de equipo (Nivel 1) quedan guardados dentro del mismo registro del colaborador que tengas cargado en ese momento — no es un registro compartido por área/equipo. Si más adelante quieres que el diagnóstico de equipo sea un documento aparte (uno por área, no por colaborador), es una extensión razonable del mismo backend: una segunda hoja "Diagnosticos" con su propio selector. Avísame si quieres que lo armemos así.
