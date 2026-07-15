# 🚀 El Flujo del Desarrollo Web y su Escudo de Seguridad

Para construir aplicaciones web modernas, robustas y profesionales, es fundamental entender el flujo de datos entre sus componentes y cómo blindar cada capa desde el primer momento de su desarrollo.

---

## 🗺️ El Flujo del Desarrollo Web

El flujo de una aplicación desacoplada se compone de tres piezas clave que trabajan de forma coordinada:

### 1. El Frontend (React)
Es la cara visible y la interfaz de usuario de tu aplicación. 
*   **Ejecución:** Se procesa y ejecuta directamente en el navegador web del usuario final (Chrome, Firefox, Safari, etc.).
*   **Responsabilidad:** Su único trabajo es pintar la pantalla de forma interactiva, capturar los datos que introduce el usuario en los formularios y enviárselos estructuradamente al backend.

### 2. El Backend (Node.js con NestJS)
Es el cerebro y el motor lógico de toda tu aplicación.
*   **Responsabilidad:** Recibe las peticiones del frontend, las procesa, toma decisiones de lógica de negocio y verifica que sean peticiones legítimas y seguras.
*   **Acceso:** Es el **único** que tiene las credenciales y las "llaves" de acceso autorizadas para comunicarse de manera directa con el almacenamiento de datos.

### 3. La Base de Datos (PostgreSQL en Docker)
Es el almacén persistente de información.
*   **Responsabilidad:** Guarda toda la información de forma estructurada, organizada y eficiente (por ejemplo, la tabla de `usuarios` que administras visualmente en gestores como Beekeeper Studio).

---

## 🛡️ El Escudo de Seguridad: ¿Cómo protegemos cada capa?

La seguridad no es una característica opcional que se añade al final de un proyecto; **se programa activamente mientras construyes la aplicación**. Así es como protegemos cada parte de este flujo:

### 1. En el Frontend (React)
*   **🚨 El peligro (Ataques XSS - Cross-Site Scripting):** Si guardas datos de autenticación altamente sensibles (como tokens de sesión) en el `localStorage` o `sessionStorage`, cualquier script malicioso inyectado de terceros en el navegador podría leerlos y robárselos al usuario sin que este se dé cuenta.
*   **🔒 La solución:** No almacenamos tokens en el código ni en el espacio de almacenamiento local de React. En su lugar, dejamos que el navegador gestione las credenciales de forma automática y transparente mediante **Cookies Seguras (`httpOnly`)**, las cuales son invisibles e inaccesibles para el código JavaScript del lado del cliente.

### 2. En el Backend (Node.js con NestJS)
*   **🔑 La Llave de Paso (JWT - JSON Web Tokens):** En lugar de exigir que el usuario envíe su contraseña real en cada clic o petición, el backend le genera un "pase de entrada" temporal (el token JWT) firmado digitalmente con una clave secreta que solo reside en tu servidor.
*   **🔒 La Encriptación (bcrypt):** El backend nunca almacena ni conoce la contraseña real del usuario. Al registrarse, el backend utiliza el algoritmo robusto `bcrypt` para transformar la contraseña en un texto cifrado irreversible (un *hash*), que es lo que finalmente se guarda en PostgreSQL.
*   **🌐 El Filtro de Origen (CORS - Cross-Origin Resource Sharing):** Tu backend le declara explícitamente al navegador: *"Solo acepto y respondo a peticiones que provengan de mi propio dominio de React"*. Cualquier petición sospechosa originada desde un sitio web de terceros es bloqueada de inmediato por el navegador.
*   **🛑 El Freno de Mano (Rate Limiting / Throttling):** Si un bot intenta atacar tu endpoint de inicio de sesión (`login`) probando miles de contraseñas de manera automática, el backend detecta el abuso de peticiones y bloquea temporalmente la dirección IP del atacante.
