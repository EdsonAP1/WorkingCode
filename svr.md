# 🛡️ Seguridad Crítica para tu Servidor 

Abrir un servidor casero a internet es como **encender una luz en un bosque oscuro**: los bots automatizados de escaneo van a encontrar tu IP pública en cuestión de minutos. Para proteger tu red familiar y tu servidor, debes implementar las siguientes capas esenciales de seguridad:

---

## 🌐 Capa 1: El Acceso Público Seguro (Reverse Proxy)

**Regla de oro absoluta:** NUNCA abras los puertos de tu base de datos (como el `5432` de Postgres) ni los puertos directos de tus entornos de ejecución (`Node.js`, `Python`, etc.) directamente en tu router.

*   **Usa un Reverse Proxy:** Implementa soluciones como **Nginx Proxy Manager** (muy visual e intuitivo) o **Traefik**.
*   **Apertura Mínima de Puertos:** Tu router solo debe tener abierto el **puerto 443 (HTTPS)** apuntando directamente al Proxy.
*   **Gestión Centralizada:** El Proxy se encargará de recibir las conexiones seguras, gestionar tus certificados SSL gratuitos de **Let's Encrypt** de forma automatizada y redirigir el tráfico internamente a tu backend dentro de tu red local.

---

## ☁️ Capa 2: Oculta tu IP Residencial con Cloudflare

Si apuntas tu dominio directamente a la IP pública de tu casa, cualquiera podría rastrearla, realizar un ataque de denegación de servicio (**DDoS**) y tumbar por completo el internet de tu hogar.

*   **Usa DNS de Cloudflare:** Pasa los servidores de nombre (*nameservers*) de tu dominio a **Cloudflare** (su plan gratuito es excelente y más que suficiente).
*   **Activa el Proxy (Nube Naranja):** Esto enmascara por completo tu dirección IP real. El mundo exterior solo verá las IPs seguras de Cloudflare, protegiéndote de ataques directos y optimizando el ancho de banda de tu servidor gracias a su caché de contenido estático.

---

## 🐧 Capa 3: Seguridad a nivel de Sistema Operativo (Linux)

Si estás montando tu servidor sobre Linux, ejecuta estas tres configuraciones de inmediato en tu terminal:

### 1. Deshabilita el acceso SSH con contraseña
Las contraseñas comunes (e incluso algunas complejas) son constantemente bombardeadas con ataques de fuerza bruta.
*   Configura el servicio de SSH (`sshd_config`) para que **solo permita el ingreso mediante Llaves SSH (SSH Keys)**. 
*   Deshabilita explícitamente el parámetro `PasswordAuthentication no`.

### 2. Instala Fail2Ban
Este servicio actúa como un centinela activo monitoreando los archivos de registro (*logs*) de tu sistema.
*   Si una IP externa intenta adivinar tus llaves de SSH o abusa de una ruta específica de tu API y falla en repetidas ocasiones (por ejemplo, 3 o 5 intentos fallidos), **Fail2Ban bloqueará automáticamente esa dirección IP** utilizando el firewall nativo del sistema.

### 3. Habilita UFW (Uncomplicated Firewall)
Mantén un control estricto de las conexiones entrantes y salientes a nivel de sistema.
*   Configura el cortafuegos interno de Linux para **denegar por defecto todo el tráfico entrante** (`ufw default deny incoming`).
*   Permite únicamente las conexiones necesarias: tu puerto de SSH (restringido a tu red local si es posible) y los puertos web estándar `80` (HTTP, para redirección) y `443` (HTTPS).
