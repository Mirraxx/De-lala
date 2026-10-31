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

### 1. Publicar el sitio (GitHub Pages)

1. En este repositorio, abre **Settings > Pages**.
2. En *Build and deployment* elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`, y guarda.
3. Espera uno o dos minutos. Arriba de esa misma pantalla aparece la dirección exacta del sitio.

Si el repositorio es privado, GitHub Pages solo está disponible con ciertos planes de pago. En ese caso la opción más simple es volver el repositorio público: aquí no hay secretos, porque el contenido de la carta se guarda cifrado en Firebase y no en el repositorio.

### 2. Publicar las reglas de Firebase

Esta página usa el **mismo proyecto de Firebase que la app de finanzas** (`personal-app-cf4fe`). Las reglas de ese proyecto viven en el repositorio **Personal_App**, en `firestore.rules`, y ahí ya están las del regalo (colecciones `gift`, `giftPhotos` y `giftVisits`).

> **No crees ni pegues aquí un archivo de reglas distinto.** Firestore tiene un único conjunto de reglas por base de datos: pegar otro lo reemplaza completo y rompería la app de finanzas o este regalo.

1. En console.firebase.google.com abre el proyecto **personal-app-cf4fe**.
2. Entra a **Firestore Database > Reglas**.
3. Copia el contenido de `firestore.rules` desde el repositorio Personal_App (rama `main`, botón **Raw**), pégalo reemplazando todo y toca **Publicar**.

Sin este paso la página dice «No se pudo abrir el sobre».

### 3. Inicio de sesión

Si la app de finanzas ya inicia sesión con Google, no hay nada más que hacer: ambos sitios comparten el dominio `mirraxx.github.io`. Si no, revisa en Firebase, **Authentication**:

- *Sign-in method*: **Google** habilitado.
- *Settings > Authorized domains*: debe estar `mirraxx.github.io`.

### 4. Preparar la carta

1. Abre la dirección del sitio y toca **Modo creador** (enlace pequeño, abajo).
2. Inicia sesión con la cuenta de administrador (`ADMIN_EMAIL` en `app.js`, que debe coincidir con `firestore.rules`).
3. Toca **Preparar la carta** y completa nombres, fecha de inicio (revisa el año), contraseña y pista. Anota la contraseña en un lugar seguro.
4. Personaliza con el lápiz flotante: General, Carta, Fotos, Historia, Razones, Música, Acceso y Compartir. Los cambios se ven en vivo, pero se publican al tocar **Guardar cambios**.

### 5. Probarla y entregarla

- Ábrela en una ventana de incógnito como si fueras la otra persona: prueba una contraseña mala, la segunda pista y la contraseña correcta.
- En **Compartir** están el enlace y el código QR (puedes imprimirlo en una tarjeta con la pista). Ahí también está el respaldo y las últimas aperturas.

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
| «No se pudo abrir el sobre. Falta publicar las reglas» | Repite el paso 2. |
| «Firebase rechazó la operación…» al guardar | Las reglas no están publicadas, o no entraste como administrador. |
| «Este sitio no está autorizado en Firebase» | Agrega `mirraxx.github.io` en *Authorized domains*. |
| No aparece la ventana de Google | Permite las ventanas emergentes de ese sitio. |
| «Esa cuenta no es la del creador» | Usa la cuenta de administrador. |
| Error 404 | Falta el paso 1 o aún no pasan un par de minutos. |

## Probar sin Firebase

Agrega `?demo` a la dirección (por ejemplo, sirviendo la carpeta con `python3 -m http.server 8000` y abriendo `http://localhost:8000/?demo`). Todo queda en el navegador y sirve para ensayar el diseño y el editor; no se comparte con nadie.
