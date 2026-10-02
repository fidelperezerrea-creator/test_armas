# Test Licencia de Armas D/E/AEM — proyecto para publicar en Google Play

Esta carpeta contiene la web ya lista para alojar (`index.html`, `manifest.json`,
`sw.js`, iconos) y convertirse en una app Android instalable en Google Play.

Archivos:
```
index.html              → la app completa (autocontenida)
manifest.json           → metadatos de la PWA (nombre, iconos, colores)
sw.js                   → service worker (funcionamiento offline)
icons/                  → iconos de la app (192, 512, apple-touch)
.well-known/assetlinks.json → verificación de la app Android (rellenar en el paso 3)
```

---

## Paso 1 — Alojar la web (gratis)

**Opción recomendada: GitHub Pages**

1. Crea una cuenta en [github.com](https://github.com) si no tienes.
2. Crea un repositorio nuevo, por ejemplo `test-armas`.
3. Sube el contenido de esta carpeta (`index.html`, `manifest.json`, `sw.js`, `icons/`, `.well-known/`) a la raíz del repositorio.
4. Ve a **Settings → Pages**, en "Branch" elige `main` y carpeta `/ (root)`. Guarda.
5. En un par de minutos tendrás tu URL pública, del tipo:
   `https://tu-usuario.github.io/test-armas/`

Alternativa igual de válida y gratis: [Netlify](https://netlify.com) (arrastras la carpeta y ya tienes URL con HTTPS).

Importante: la URL final (con el dominio real) la necesitarás en el Paso 2.

---

## Paso 2 — Generar la app Android (TWA) con Bubblewrap

Bubblewrap es una herramienta de Google que envuelve tu web en un contenedor Android.
Necesitas tener [Node.js](https://nodejs.org) instalado en tu ordenador.

```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest=https://tu-usuario.github.io/test-armas/manifest.json
```

Te irá preguntando datos (nombre de la app, package name tipo `com.tunombre.testarmas`,
color de tema `#2f4538`, etc. — la mayoría los detecta solo desde el manifest).

Al terminar genera un proyecto Android. Para construir el paquete:

```bash
cd test-armas   # la carpeta que creó bubblewrap
bubblewrap build
```

Esto te da dos ficheros:
- `app-release-signed.apk` → para pruebas / instalar directo en tu móvil.
- `app-release-bundle.aab` → el que subirás a Google Play.

Bubblewrap genera también una clave de firma (`android.keystore`). **Guárdala
en un sitio seguro: la necesitarás para cada actualización futura de la app.**

Tras firmar, Bubblewrap te da la huella SHA256 del certificado. Pégala en
`.well-known/assetlinks.json` (sustituyendo `SUSTITUYE_ESTO_POR_TU_HUELLA_SHA256`
y `com.tunombre.testarmas` por tu package name real), y vuelve a subir ese
archivo a tu hosting (Paso 1). Esto es lo que verifica que la app Android y
la web son tuyas, y hace que se abra sin barra de navegador.

---

## Paso 3 — Alta en Google Play Console

1. Entra en [play.google.com/console](https://play.google.com/console).
2. Regístrate como desarrollador: **25$, pago único** (no es suscripción).
3. Verificación de identidad con documento oficial (puede tardar 1-2 días).
4. Crea una app nueva → rellena ficha (nombre, descripción, categoría,
   política de privacidad — como la app no envía datos a ningún servidor,
   puede ser muy simple: "Esta app no recopila ni transmite datos personales;
   todo el progreso se guarda localmente en el dispositivo").
5. Sube las capturas de pantalla (puedes hacerlas directamente abriendo la
   app en el móvil) y el icono de 512×512 (ya lo tienes en `icons/icon-512.png`).

---

## Paso 4 — Pruebas cerradas (obligatorio en cuentas personales nuevas)

Si tu cuenta de desarrollador es "personal" (no "de organización"), Google
exige antes de publicar:

- **Mínimo 12 testers** (bajó de 20 a 12 en diciembre de 2024)
- Que mantengan la app instalada **14 días seguidos**

Cómo hacerlo:
1. En Play Console: **Testing → Closed testing** → crea una pista nueva.
2. Sube el `.aab` del Paso 2.
3. Añade los emails de Google de tus testers (familia, amigos, compañeros —
   no necesitan saber nada técnico, solo instalar la app desde el enlace que
   te da Play Console y tenerla esos 14 días).
4. Recluta 15-16 personas de margen, no justo 12 (siempre se desapunta alguien).
5. Pasados los 14 días continuos, aparece el botón **"Solicitar acceso a
   producción"**.

Si no tienes a quién pedírselo, existen servicios de pago (tipo Fiverr) que
proporcionan testers por un coste aproximado de 15-40€, pero no hace falta
si puedes tirar de contactos.

---

## Paso 5 — Publicar

Con las pruebas completadas y la ficha de la app aprobada, promocionas la
versión de pruebas a producción. Google revisa la app (normalmente 1-7 días)
y, si todo está en orden, queda publicada en Google Play.

---

### Resumen de costes
| Concepto | Coste |
|---|---|
| Alojamiento (GitHub Pages / Netlify) | Gratis |
| Generar el `.aab` (Bubblewrap) | Gratis |
| Alta en Google Play Console | 25 $ (pago único) |
| 12 testers, 14 días | Gratis si usas contactos propios |
| Actualizaciones futuras | Gratis (misma cuenta) |
