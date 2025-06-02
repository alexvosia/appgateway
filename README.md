# appgateway

# Middleware Gateway para Aplicaciones Web Internas

## 🎯 Objetivo

Este proyecto tiene como objetivo centralizar la **autenticación**, **autorización**, **proxy de solicitudes HTTP** y la **comunicación en tiempo real** para un conjunto de aplicaciones web internas (intranet) mediante un middleware extensible y desacoplado.

---

## 🧱 Arquitectura General

```
                   ┌───────────────────────┐
                   │   Frontend Web Apps   │
                   └──────────┬────────────┘
                              │  HTTP / WS
                              ▼
                 ┌────────────────────────────────────────────┐
                 │               MIDDLEWARE GATEWAY           │
                 ├────────────────────────────────────────────┤
                 │  ├─ Auth (Azure AD / LDAP)                 │
                 │  ├─ Token Validation & Renewal             │
                 │  ├─ Reverse Proxy (HTTP)                   │
                 │  ├─ WebSocket Gateway (Realtime)           │
                 │  ├─ Push Notification Service (JetStream)  │
                 │  └─ Email Notification Service (API+SMTP)  │
                 └──────────┬─────────────────────────────────┘
                            │
                            ▼
                 ┌───────────────────────┐
                 │     NATS JetStream    │
                 └──────────┬────────────┘
                            │
                            ▼
      ┌─────────────────────────────────────────────┐
      │ Servicios backend productores de eventos    │
      └─────────────────────────────────────────────┘
```

---

## 🔩 Módulos del Sistema

| Módulo                    | Descripción                                            |
| ------------------------- | ------------------------------------------------------ |
| Gateway HTTP              | Punto de entrada unificado para todas las apps web     |
| Autenticación             | Azure AD, LDAP, renovación de tokens                   |
| Gestión de tokens         | JWT/Paseto, revocación, sesiones en Redis              |
| Reverse Proxy             | Proxy HTTP con autenticación y forwarding de cabeceras |
| WebSocket Gateway         | Canal WebSocket con entrega de eventos push vía NATS   |
| Event Bus                 | NATS + JetStream para mensajería entre servicios       |
| Chat interno              | Sistema de mensajería persistente entre usuarios       |
| Muros de usuario          | Publicaciones y reconocimientos personales             |
| Notificaciones push       | Subscripción por usuario y aplicación desde JetStream  |
| Notificaciones por correo | Envío por API con plantillas y SMTP                    |
| Observabilidad            | Logs, métricas Prometheus, trazas Jaeger               |
| Almacenamiento temporal   | Redis para tokens, sesiones, revocaciones              |

---

## 🛠️ Tecnologías

| Componente           | Tecnología                           |
| -------------------- | ------------------------------------ |
| Lenguaje             | Go (Golang)                          |
| Framework Web        | [Echo](https://echo.labstack.com/)   |
| WebSockets           | `gorilla/websocket`                  |
| Autenticación        | OIDC (Azure AD), LDAP, JWT, Paseto   |
| Proxy HTTP           | `net/http/httputil.ReverseProxy`     |
| Bus de eventos       | [NATS + JetStream](https://nats.io)  |
| Almacenamiento temp. | Redis                                |
| Observabilidad       | Prometheus, Grafana, Jaeger, Zerolog |
| CI/CD                | Gitea Actions, Jenkins               |
| Secrets              | Vault / AWS Secrets Manager          |

---

## 📦 Servicios del Proyecto

* `auth-service`: Autenticación, emisión y renovación de tokens
* `proxy-service`: Reverse proxy autenticado
* `ws-gateway`: Suscripción a eventos y entrega por WebSocket
* `realtime-publisher`: Publicación de eventos desde backends
* `chat-service`: Chat persistente entre usuarios
* `wall-service`: Muros de publicaciones personales
* `push-service`: Notificaciones push vía JetStream
* `mail-service`: Notificaciones vía correo electrónico

---

## 🚦 Control de Acceso

* Autenticación federada con Azure AD y LDAP tradicional
* Validación y revocación de tokens vía Redis
* Filtrado de eventos y autorización a nivel WebSocket y NATS

---

## 📊 Observabilidad

* **Logs estructurados:** Zerolog / Logrus
* **Métricas:** Prometheus con exporters por microservicio
* **Tracing distribuido:** Jaeger (OpenTelemetry)

---

## 📚 Organización del Código

```bash
/cmd                # Entrypoints de cada microservicio
  ├─ auth-service/
  ├─ proxy-service/
  ├─ ws-gateway/
  ├─ push-service/
  ├─ mail-service/

/internal           # Lógica privada por dominio
  ├─ auth/
  ├─ token/
  ├─ nats/
  ├─ ws/
  ├─ proxy/
  ├─ middleware/
  ├─ logging/
  ├─ metrics/
  └─ mail/

/pkg                # Utilidades compartidas exportables
  ├─ config/
  ├─ models/
  └─ utils/
```
