# Test Licencia de Armas — proyecto Capacitor (contenido empaquetado + prueba 7 días)

Esta carpeta reemplaza al proyecto de Bubblewrap. Diferencias clave:
- El contenido (preguntas, histórico, todo) va **dentro del APK**, no depende de ninguna
  URL externa. Puedes cerrar el repositorio de GitHub sin que la app deje de funcionar.
- La app ya incluye la lógica de **prueba gratuita de 7 días** y una pantalla de
  "Comprar versión completa" cuando termina. El botón de comprar todavía **no está
  conectado al pago real de Google Play** — eso lo hacemos en la siguiente sesión.

⚠️ Revisa el `appId` en `capacitor.config.json`: lo he dejado como
`com.fidelperez.testarmas`. Si en Bubblewrap usaste un Application ID distinto,
dímelo y lo cambiamos antes de compilar (no es obligatorio que coincida con el de
Bubblewrap, ya que es una app nueva, pero sí tiene que ser definitivo desde ya).

---

## Paso 1 — Instalar dependencias

Abre PowerShell (o CMD) en esta carpeta y ejecuta:

```powershell
npm install
```

Esto incluye ya el plugin `@capacitor/app`, que usamos para que el **botón
atrás físico de Android** navegue entre pantallas de la app en vez de cerrarla
de golpe (antes tenías que usar los botones "← Volver" de dentro de la app;
ahora el botón atrás del móvil hace lo mismo, y solo cierra la app si ya
estás en la pantalla de inicio).

## Paso 2 — Añadir la plataforma Android

```powershell
npx cap add android
npx cap sync android
```

Esto crea una carpeta `android/` con el proyecto nativo, usando el contenido de `www/`.

## Paso 3 — Generar un APK de prueba (rápido, sin firmar para Play Store todavía)

```powershell
cd android
.\gradlew assembleDebug
```

Al terminar, el APK de prueba está en:
```
android\app\build\outputs\apk\debug\app-debug.apk
```

Instálalo en tu móvil igual que hicimos con el de Bubblewrap (cópialo y ábrelo, o
`adb install app-debug.apk` si tienes ADB configurado).

### Qué comprobar
- El icono y la pantalla de carga salen bien.
- La app funciona exactamente igual que antes.
- **Apaga el wifi y los datos móviles** y vuelve a abrir la app: debe seguir
  funcionando perfectamente, porque ya no depende de internet para cargar el
  contenido. Esa es la prueba de que el cambio ha funcionado.
- Arriba de la pantalla de inicio debe aparecer el aviso: "🔓 Prueba gratis:
  quedan 7 días".

### Para probar la pantalla de "se acabó la prueba" sin esperar 7 días de verdad
Con la app cerrada, puedes simular que ya pasaron los 7 días. Necesitarás
`adb` (viene con Android Studio) conectado al móvil, y ejecutar:
```powershell
adb shell am start -a android.intent.action.MAIN -n com.fidelperez.testarmas/.MainActivity
```
(te lo detallo con calma en la próxima sesión si quieres probar esto; no es
necesario para seguir avanzando ahora).

---

## Qué queda pendiente (próxima sesión)

1. **Conectar el botón "Comprar" a Google Play Billing de verdad** — requiere:
   - Crear el producto de pago único dentro de Play Console (ficha de precio, 4,99€ o el que decidas).
   - Añadir un plugin de Capacitor para Billing y cablear `attemptPurchase()` /
     `attemptRestore()` en `www/index.html` a ese plugin.
2. **Generar el APK firmado (release)** reutilizando el mismo `android.keystore`
   que ya creaste con Bubblewrap — sirve igual para este proyecto.
3. Volver a montar `.aab` y subirlo a Play Console como actualización.

No hace falta que hagas nada de esto todavía — primero confirmamos que el Paso 3
de arriba te funciona en el móvil.
