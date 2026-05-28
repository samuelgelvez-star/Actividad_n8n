# 🤖 Instalación de n8n y Primer "Hola Mundo"

**Actividad Práctica — Automatización con n8n**

---

## 👤 Integrantes

- Samuel David Gelvez

---

## 🐳 Método de Instalación Utilizado

**Opción 2 — Docker**

Se utilizó Docker Desktop junto con el comando `docker run` para levantar una instancia de n8n de forma local, exponiendo el puerto `5678`. Adicionalmente, se configuró **ngrok** para generar una URL pública que permitiera acceder a n8n desde internet y habilitar la integración con Telegram.

---

## ✅ Requisitos Previos

| Herramienta | Versión | Descripción |
|---|---|---|
| Windows | 10.0.26200.8457 | Sistema operativo base |
| Docker Desktop | v4.75.0 | Motor de contenedores |
| Docker Engine | 29.5.2 | CLI y runtime de contenedores |
| Docker Compose | v5.1.3 | Orquestación de contenedores |
| ngrok | 3.39.1-msix-stable | Túnel HTTP para exponer puertos locales |

---

## ⚙️ Proceso de Instalación

### Paso 1 — Instalación de Docker Desktop

Se descargó e instaló Docker Desktop desde [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop). Una vez instalado, se verificó que el motor estuviera corriendo correctamente desde la interfaz gráfica.

[📸 Ver captura — Docker Desktop con Engine activo, RAM 2.51 GB, versión v4.75.0](capturas_evidencias/Img%2001%20docker%20desktop.png)

---

### Paso 2 — Verificación de versiones de Docker

Se abrió la terminal (`cmd`) y se verificaron las versiones instaladas:

```bash
docker --version
# Docker version 29.5.2, build 79eb04c
```

[📸 Ver captura — Terminal confirmando Docker version 29.5.2, build 79eb04c](capturas_evidencias/Img%2002%20docker%20version.png)

```bash
docker compose version
# Docker Compose version v5.1.3
```

[📸 Ver captura — Terminal confirmando Docker Compose version v5.1.3](capturas_evidencias/Img%2003%20compose%20version.png)

---

### Paso 3 — Levantar n8n con Docker

Se ejecutó el siguiente comando para descargar la imagen oficial de n8n y levantar el contenedor:

```bash
docker run -it --rm -p 5678:5678 -v n8n_data:/home/node/.n8n n8nio/n8n
```

**Explicación de los parámetros:**
- `-it` — modo interactivo con terminal
- `--rm` — elimina el contenedor al detenerse
- `-p 5678:5678` — mapea el puerto 5678 del contenedor al 5678 del host
- `-v n8n_data:/home/node/.n8n` — volumen persistente para guardar datos y configuración de n8n
- `n8nio/n8n` — imagen oficial de n8n en Docker Hub

Docker descargó automáticamente la imagen y ejecutó todas las migraciones de base de datos necesarias.

[📸 Ver captura — Descarga de la imagen n8nio/n8n y ejecución de migraciones de base de datos](capturas_evidencias/Img%2004%20docker%20run.png)

Al finalizar las migraciones, la consola confirmó que n8n estaba listo:

```
n8n ready on ::, port 5678
Editor is now accessible via:
http://localhost:5678
```

[📸 Ver captura — n8n v2.22.4 levantado correctamente, accesible en http://localhost:5678](capturas_evidencias/Img%2005%20n8n%20ready.png)

---

### Paso 4 — Instalación y configuración de ngrok

Se instaló ngrok desde [https://ngrok.com](https://ngrok.com) para exponer el puerto local 5678 de n8n a internet, lo cual es necesario para que el bot de Telegram pueda comunicarse con n8n mediante webhooks.

[📸 Ver captura — ngrok instalado y disponible en el sistema con todos sus comandos](capturas_evidencias/Img%2006%20ngrok%20help.png)

Se ejecutó el siguiente comando para crear el túnel:

```bash
ngrok http 5678
```

[📸 Ver captura — ngrok activo con URL pública generada apuntando a localhost:5678](capturas_evidencias/Img%2007%20ngrok%20running.png)

Con esta URL pública (`https://unscrew-cannabis-cradle.ngrok-free.dev`), el webhook de Telegram pudo enviar eventos directamente a n8n.

---

## ⚠️ Problemas Encontrados y Soluciones

| Problema | Solución |
|---|---|
| n8n mostraba "User attempted to access a workflow without permissions" | Era el flujo de configuración inicial del owner; se completó el setup del primer usuario correctamente |
| Advertencia de `util._extend` deprecado en Node.js | Es una advertencia interna de n8n, no afecta el funcionamiento; se ignoró |
| ngrok genera URL aleatoria diferente en cada sesión (plan Free) | Se utilizó la URL temporal generada y se actualizó el webhook del bot de Telegram cada vez que se reinició ngrok |

---

## 🔁 Workflow "Hola Mundo" — Chat Telegram

### Descripción

Se construyó un workflow llamado **`Chat_telegram`** compuesto por dos nodos que permiten enviar un mensaje automatizado a Telegram con un solo clic.

### Vista del Dashboard

[📸 Ver captura — Dashboard de n8n con el workflow Chat_telegram creado el 27 de mayo](capturas_evidencias/Img%2012%20dashboard.png)

### Vista del Workflow

[📸 Ver captura — Workflow con Trigger Manual conectado al nodo Telegram, ambos con ejecución exitosa](capturas_evidencias/Img%2009%20workflow.png)

---

### Descripción de cada nodo

#### 🟢 Nodo 1: Trigger Manual — "When clicking 'Execute workflow'"

- **Tipo:** Manual Trigger
- **Función:** Permite ejecutar el workflow manualmente haciendo clic en el botón "Execute workflow" desde el editor de n8n. No requiere configuración adicional.
- **Salida:** Genera 1 item vacío que activa la ejecución del nodo siguiente.

#### 🔵 Nodo 2: Telegram — "Send a text message"

- **Tipo:** Telegram node
- **Acción:** `sendMessage`
- **Bot:** `Hola mundo bot` (`@Chat_Hola_Mundo_bot`)
- **Chat ID:** `6003549677` (chat personal de Samuel David Gelvez)
- **Mensaje enviado:**
  ```
  ¡Hola Mundo desde n8n! Mi primer workflow funciona correctamente.

  This message was sent automatically with n8n
  ```

---

## 📸 Evidencias de Funcionamiento

### Respuesta JSON exitosa de la API de Telegram

[📸 Ver captura — Respuesta JSON con "ok": true, message_id: 15 y datos del bot y receptor](capturas_evidencias/Img%2010%20json%20response.png)

La API de Telegram retornó `"ok": true`, confirmando el envío exitoso. Se puede verificar el `message_id: 15`, el bot `Chat_Hola_Mundo_bot` (id: 8826244423) y el receptor Samuel David Gelvez (id: 6003549677).

### Tráfico HTTP en ngrok

[📸 Ver captura — ngrok con 44 conexiones TTL y peticiones HTTP entrantes respondidas con 200 OK](capturas_evidencias/Img%2008%20ngrok%20traffic.png)

ngrok mostró actividad real con 44 conexiones totales, latencia promedio de 0.42s y todas las peticiones respondidas correctamente.

### Mensaje recibido en Telegram

[📸 Ver captura — Celular mostrando el mensaje "¡Hola Mundo desde n8n!" recibido en Telegram el 27 de mayo](capturas_evidencias/Img%2011%20telegram%20message.jpeg)

El mensaje fue recibido el 27 de mayo a las 20:40 en el chat del bot `Hola mundo bot`, confirmando que el workflow automatizado funcionó de extremo a extremo.

---

## 💭 Reflexión Técnica

### 1. ¿Qué fue lo más difícil de la instalación?

Lo más difícil fue configurar correctamente la integración con Telegram a través de ngrok. Como el plan gratuito de ngrok genera una URL aleatoria cada vez que se reinicia, fue necesario actualizar manualmente la URL del webhook del bot en cada sesión. También fue necesario entender el flujo de permisos inicial de n8n para configurar correctamente el primer usuario (owner).

### 2. ¿Qué ventajas tiene n8n?

- Es **open source** y se puede auto-hospedar, lo que da control total sobre los datos sin depender de terceros.
- Tiene una interfaz visual intuitiva que no requiere escribir código para la mayoría de integraciones.
- Soporta más de 400 integraciones nativas (Telegram, OpenAI, Google Sheets, HTTP Request, etc.).
- Permite automatizar procesos complejos con lógica condicional, loops y transformación de datos.
- Al correr en Docker, es fácil de desplegar en cualquier servidor de producción.

### 3. ¿Qué diferencia encontraron entre Docker y local?

| Aspecto | Docker | Local (npm) |
|---|---|---|
| Aislamiento | Total — no afecta el sistema operativo | Instala dependencias globales en el sistema |
| Configuración inicial | Un solo comando `docker run` | Requiere instalar Node.js, npm y configurar PATH |
| Portabilidad | Alta — funciona igual en cualquier OS | Depende del entorno del sistema |
| Persistencia de datos | Requiere configurar volúmenes con `-v` | Los datos quedan directamente en la carpeta local |
| Cercano a producción | ✅ Sí, es el método profesional | ❌ Más orientado a desarrollo rápido |

Docker es más profesional y reproducible, ideal cuando se va a desplegar en un servidor real.

### 4. ¿Para qué casos reales usarían automatización?

- **Notificaciones de monitoreo:** alertas automáticas a Telegram cuando un servidor cae o un servicio falla.
- **Procesamiento de formularios:** cuando alguien llena un Google Form, enviar automáticamente un correo de confirmación y registrar los datos en una hoja de cálculo.
- **Reportes periódicos:** generar y enviar reportes de ventas o métricas cada lunes a las 8am sin intervención humana.
- **Sincronización de sistemas:** copiar registros nuevos de una base de datos a otra automáticamente cuando se detectan cambios.
- **Atención al cliente automatizada:** responder mensajes frecuentes de clientes en Telegram con un bot inteligente.

### 5. ¿Qué les gustaría automatizar en el futuro?

Me gustaría construir un sistema de **alertas de precios inteligentes** que monitoree APIs de criptomonedas o productos y envíe notificaciones a Telegram cuando el precio supere o baje de un umbral definido. También me interesa mucho integrar n8n con **OpenAI** para crear un asistente que lea correos entrantes, extraiga la información clave y genere respuestas automáticas personalizadas.

---

## 📁 Estructura del Repositorio

```
📦 n8n-hola-mundo/
 ┣ 📄 README.md
 ┗ 📁 capturas_evidencias/
    ┣ 🖼️ img_01_docker_desktop.png
    ┣ 🖼️ img_02_docker_version.png
    ┣ 🖼️ img_03_compose_version.png
    ┣ 🖼️ img_04_docker_run.png
    ┣ 🖼️ img_05_n8n_ready.png
    ┣ 🖼️ img_06_ngrok_help.png
    ┣ 🖼️ img_07_ngrok_running.png
    ┣ 🖼️ img_08_ngrok_traffic.png
    ┣ 🖼️ img_09_workflow.png
    ┣ 🖼️ img_10_json_response.png
    ┣ 🖼️ img_11_telegram_message.jpeg
    ┗ 🖼️ img_12_dashboard.png
```

---

## 🔗 Referencias

- [Documentación oficial de n8n](https://docs.n8n.io)
- [Docker Hub — n8nio/n8n](https://hub.docker.com/r/n8nio/n8n)
- [ngrok — Tunneling para desarrollo local](https://ngrok.com)
- [Telegram Bot API](https://core.telegram.org/bots/api)

---

*Actividad realizada el 27 de mayo de 2026 — Curso de Automatización con n8n*