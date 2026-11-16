# Gestor de reservas - Hostería Ruca Pehuén - n8n

Workflow de automatización que atiende reservas por WhatsApp con un agente de IA, pensado para una hostería de temporada con reglas de disponibilidad y tarifas variables.

## 📋 Descripción

Automatiza la gestión de reservas por WhatsApp de la Hostería Ruca Pehuén (Caviahue): recibe el mensaje del cliente, extrae fechas y cantidad de personas, valida la temporada, consulta disponibilidad en Google Calendar e informa precio, y deriva a una vendedora humana en los casos que requieren atención personalizada. Pensado para filtrar y resolver automáticamente las consultas simples, dejando las complejas en manos del equipo de ventas.

- Agente de IA (GPT-4.1) que conduce la conversación: da la bienvenida, recolecta fechas y cantidad de personas, informa disponibilidad y precio, pide nombre y apellido, y confirma la reserva.
- Extractor de datos dedicado que interpreta fechas y cantidades aunque vengan con errores de tipeo, formatos mixtos o lenguaje informal, y corrige automáticamente el año si no se especifica.
- Motor de reglas de temporada: valida rangos de fechas válidos, detecta cruces entre semanas de temporada alta y devuelve mensajes predefinidos específicos para cada tipo de error.
- Derivación automática a un vendedor humano en casos particulares: 1 o 4+ personas, estadías de menos de 3 noches, o fechas sin disponibilidad.
- Verificación de disponibilidad real contra Google Calendar y creación automática del evento de reserva al confirmar.
- Soporte multimodal: transcribe audios con Whisper y describe imágenes con GPT-4o.
- Buffer de mensajes con Redis, para agrupar varios mensajes seguidos del mismo cliente antes de procesarlos.
- Memoria conversacional persistente en Postgres, con estado de la reserva (fechas, personas, precio, temporada) por cliente.
- Notificación automática a la gerente de ventas por WhatsApp cuando se confirma una reserva o se deriva un caso.
- Comandos administrativos (`/activar`, `/desactivar`, `/reset`) para que un humano tome el control de una conversación puntual.
- Filtro básico de seguridad para detectar intentos de inyección de código/SQL en los mensajes entrantes.

## 🔄 Flujo del proceso

1. El cliente escribe por WhatsApp → el mensaje llega por webhook.
2. Se valida si el bot está activo para ese número y si el mensaje no es un comando administrativo.
3. Según el tipo de contenido (texto, audio o imagen), se normaliza a texto plano.
4. Un agente extractor interpreta fechas, cantidad de personas y nombre, con tolerancia a errores de tipeo.
5. Se valida la temporada y se detectan reglas especiales (cruces de semana, fechas fuera de rango, etc.).
6. Si corresponde, se consulta disponibilidad real en Google Calendar y se calcula el precio.
7. El agente informa disponibilidad y precio, o deriva a la vendedora si el caso lo amerita.
8. Al confirmar, se crea el evento en Google Calendar y se notifica a la gerente por WhatsApp.
9. La respuesta se divide en mensajes cortos (hasta 3 bloques) para que se lea más natural en WhatsApp.

## 🛠 Tecnologías usadas

**Automatización:** n8n (orquestación del flujo completo)

**IA:** OpenAI GPT-4.1 (agente conversacional y extracción de datos), GPT-4o (análisis de imágenes), Whisper (transcripción de audio)

**Datos:** PostgreSQL (memoria conversacional y estado de la reserva), Redis (buffer de mensajes)

**Integraciones:** Google Calendar (disponibilidad y creación de eventos), GoHighLevel (GHL) API (WhatsApp)

![](https://skillicons.dev/icons?i=n8n,postgres,redis,js)

## 💻 Instalación y despliegue

> ⚠️ Este workflow usa GoHighLevel (GHL) como proveedor de WhatsApp API. Si lo corrés con otro proveedor, vas a tener que adaptar el nodo `Webhook` de entrada (formato del payload) y los nodos `HTTP Request` que envían las respuestas (endpoint y autenticación).

```bash
# 1. Importar el workflow
# En tu instancia de n8n: Workflows → Import from File → seleccionar el .json de este repo

# 2. Configurar credenciales en n8n
# - OpenAI (API key)
# - PostgreSQL (conexión a la base usada para memoria y estado de reservas)
# - Redis (conexión para el buffer de mensajes)
# - Google Calendar (OAuth2, con acceso al/los calendario/s de habitaciones)
# - GoHighLevel / proveedor de WhatsApp API (token de autenticación)

# 3. Reemplazar valores propios del negocio
# - URL del webhook (se genera al activar el workflow en n8n)
# - Rangos de temporada y reglas de fechas especiales (nodo "Seteo - Datos para reserva")
# - calendarId de cada habitación/calendario
# - Número de WhatsApp autorizado para comandos administrativos
# - Número de la gerente de ventas para notificaciones

# 4. Activar el workflow en n8n
```

## 📫 Contacto

Si buscas un perfil especializado en automatización de procesos con IA, trabajando con n8n, SQL y JavaScript asistido por IA:

- 📧 Email: [marcosantini03@gmail.com](mailto:marcosantini03@gmail.com)
- 💼 LinkedIn: [LinkedIn](https://www.linkedin.com/in/marco-santini-a0391b309/)
- 📄 CV: [CV](https://docs.google.com/document/d/1rJgnOuMdzq41D2oTrjc76o5_w-R3_nv15-Lxg2TFE54/edit?usp=sharing)
