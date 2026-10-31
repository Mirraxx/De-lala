# Regalo

Una carta digital para una persona especial: un sobre que se abre con contraseña y pista, una carta que se escribe sola, el contador de tiempo juntos, fotos, línea del tiempo, razones, una playlist de Spotify y un mensaje final. Se personaliza desde la misma página con el modo creador.

Son tres archivos y una librería, sin proceso de build: `index.html`, `style.css`, `app.js` y `vendor/qrcode.js` (generador de QR, licencia MIT).

## Cómo se ve la experiencia

1. Un sobre rosa con sello. Se muestra la pista y se escribe la contraseña (si falla 3 veces, aparece una segunda pista opcional).
2. El sobre se abre y la carta se escribe palabra por palabra.
3. Contador en vivo desde la fecha de inicio, con cuenta regresiva al próximo mesiversario o aniversario.
4. Galería de fotos con visor, línea del tiempo, "Cosas que amo de ti" (corazones que se dan vuelta), playlist de Spotify y mensaje final.

Tiene tres temas de color: **Jardín** (rosa, celeste y salvia sobre crema, con burdeo para el texto), **Atardecer** (noche burdeo) y **Cielo** (más celeste).

## Activarla

Son cinco pasos, una sola vez. Todo se hace desde el navegador y no cuesta nada (Firebase en el plan gratuito, sin tarjeta).

### 1. Publicar el sitio (GitHub Pages)

1. En este repositorio, abre **Settings > Pages**.
2. En *Build and deployment* elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`, y guarda.
3. Espera uno o dos minutos. Arriba de esa misma pantalla aparece la dirección exacta del sitio.

Si el repositorio es privado, GitHub Pages solo está disponible con ciertos planes de pago. En ese caso la opción más simple es volver el repositorio público: aquí no hay secretos, porque el contenido de la carta se guarda cifrado en Firebase y no en el repositorio.

### 2. Crear el proyecto de Firebase (propio de este regalo)

1. Entra a [console.firebase.google.com](https://console.firebase.google.com) con tu cuenta de Google y toca **Agregar proyecto**.
2. Ponle un nombre (por ejemplo `de-lala`) y continúa. Puedes **desactivar Google Analytics**. Toca **Crear proyecto**.

### 3. Activar el inicio de sesión de Google

1. En el menú izquierdo abre **Authentication** y toca **Comenzar**.
2. En la pestaña **Sign-in method** elige **Google**, actívalo, elige tu correo como correo de asistencia y **Guarda**.
3. Ve a **Settings > Authorized domains** (Configuración > Dominios autorizados), toca **Agregar dominio** y escribe `mirraxx.github.io`. `localhost` ya viene incluido.

### 4. Crear la base de datos y publicar las reglas

1. En el menú izquierdo abre **Firestore Database** y toca **Crear base de datos**.
2. Elige **modo producción** y una región cercana (por ejemplo `southamerica-east1`). La región no se puede cambiar después. Toca **Habilitar**.
3. Abre la pestaña **Reglas**, borra lo que hay y pega **todo** el contenido del archivo [`firestore.rules`](firestore.rules) de este repositorio (botón **Raw** > Ctrl+A > copiar). Toca **Publicar**.

Sin las reglas, la página dice «No se pudo abrir el sobre».

### 5. Conectar la página con tu proyecto

1. En Firebase, toca el engranaje junto a *Descripción general del proyecto* y abre **Configuración del proyecto**.
2. Baja a **Tus apps** y toca el ícono web **`</>`**. Ponle un apodo, **no marques Firebase Hosting** y toca **Registrar app**.
3. Aparece un bloque con `const firebaseConfig = { ... }`. Copia solo lo que va entre las llaves.
4. En este repositorio abre `app.js`, toca el lápiz (**Edit**) y busca `const FIREBASE_CONFIG = null;`. Reemplaza `null` por el bloque, de modo que quede así, con tus valores:

   ```js
   const FIREBASE_CONFIG = {
     apiKey: "...",
     authDomain: "...",
     projectId: "...",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```

5. Toca **Commit changes**. Espera un minuto a que Pages se actualice.

Estos valores son públicos por diseño: la seguridad la dan las reglas y el cifrado, no esconder la clave.

## Preparar y entregar la carta

1. Abre la dirección del sitio y toca **Modo creador** (enlace pequeño, abajo).
2. Inicia sesión con la cuenta de administrador (`ADMIN_EMAIL` en `app.js`, que debe coincidir con `firestore.rules`).
3. Toca **Preparar la carta** y completa nombres, fecha de inicio (revisa el año), contraseña y pista. Anota la contraseña en un lugar seguro.
4. Personaliza con el lápiz flotante: General, Carta, Fotos, Historia, Razones, Música, Acceso y Compartir. Los cambios se ven en vivo, pero se publican al tocar **Guardar cambios**.
5. Ábrela en una ventana de incógnito como si fueras la otra persona: prueba una contraseña mala, la segunda pista y la contraseña correcta. Revísala también en el teléfono.
6. En **Compartir** están el enlace y el código QR (puedes imprimirlo en una tarjeta con la pista). Ahí también está el respaldo y las últimas aperturas.

## Seguridad y límites

- **El contenido va cifrado.** Carta, textos y fotos se cifran en el navegador (AES-GCM) con una llave que solo se abre con la contraseña (PBKDF2, 300.000 vueltas). En Firestore queda texto ilegible; la pista es lo único que se guarda en claro. La contraseña no distingue mayúsculas ni tildes.
- **Una contraseña débil se puede adivinar** probando combinaciones sin conexión. Usa una frase que solo ustedes sepan.
- **Si olvidas la contraseña, el contenido no se recupera.** Descarga el respaldo (pestaña Compartir) y guárdalo en un lugar privado: va **sin cifrar**. En la puerta, el creador tiene **Olvidé la contraseña**, que borra todo para empezar de nuevo.
- **Fotos**: hasta 40; se reducen a 1400 px y unos 400 KB, y cada una es un documento aparte (límite de 1 MB de Firestore).
- **Spotify**: el reproductor incrustado toca canciones completas solo si la persona tiene sesión iniciada en Spotify en ese navegador; si no, suenan fragmentos de 30 segundos. La playlist debe ser pública.
- **Aperturas**: al entrar con la contraseña se guarda la hora. La ves en *Compartir > Aperturas*; no se registra nada más.
- La página lleva `noindex` para que los buscadores no la listen.

## Si algo falla

| Lo que ves | Qué hacer |
|---|---|
| «Falta conectar Firebase» | Falta el paso 5: pegar la configuración en `app.js`. |
| «No se pudo abrir el sobre. Falta publicar las reglas» | Repite el paso 4 (reglas). |
| «Firebase rechazó la operación…» al guardar | Las reglas no están publicadas, o no entraste como administrador. |
| «Este sitio no está autorizado en Firebase» | Agrega `mirraxx.github.io` en *Authorized domains* (paso 3). |
| No aparece la ventana de Google | Permite las ventanas emergentes de ese sitio. |
| «Esa cuenta no es la del creador» | Usa la cuenta de administrador. |
| Error 404 | Falta el paso 1, o aún no pasan un par de minutos. |

## Probar sin Firebase

Agrega `?demo` a la dirección (por ejemplo, sirviendo la carpeta con `python3 -m http.server 8000` y abriendo `http://localhost:8000/?demo`). Todo queda en el navegador y sirve para ensayar el diseño y el editor; no se comparte con nadie.
