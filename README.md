# AutoYa-Docs
Repositorio de documentación oficial del sistema AutoYa, una plataforma de alquiler de vehículos que integra reservas, pagos, notificaciones y gestión de vehículos bajo una arquitectura modular y escalable.
Este repositorio contiene todos los artefactos técnicos que describen la arquitectura, dominio, procesos y estructura de datos del sistema.

---

## 1. Arquitectura del Sistema
El sistema AutoYa se divide en los siguientes contenedores principales:

- **Single Page App (SPA)**: Interfaz para clientes y propietarios. Permite búsqueda, reserva, gestión de vehículos y lectura de notificaciones.
- **API Application**: Backend principal que gestiona autenticación, usuarios, vehículos, reservas, pagos y notificaciones.
- **Web Application**: Componente opcional para vistas server-rendered.
- **Database**: Base de datos relacional con información de usuarios, vehículos, reservas, pagos y notificaciones.
- **Mail System**: Sistema externo de envío de correos.

---

## 2. Componentes del Backend
Dentro del contenedor API Application, los componentes definidos son:

- **Auth Component**: Autenticación, autorización y seguridad.
- **User Management Component**: Manejo de perfiles, roles y validaciones.
- **Vehicle Management Component**: Registro y actualización de vehículos.
- **Reservation Component**: Lógica de pre-reserva, reserva, cancelación y estado.
- **Payment Component**: Inicio de pago, verificación y conciliación.
- **Notification Component**: Generación y envío de alertas al usuario.
- **Database Access Layer**: Repositorios e integración con la base de datos.

---

## 3. Modelo de Datos (ERD)
La base de datos incluye las siguientes entidades principales:

**User**
- Información personal y credenciales.
- Rol: client o owner.
  
**Client / Owner**
- Entidades especializadas según el rol.
- Los propietarios pueden publicar vehículos.
- Los clientes pueden realizar reservas.
  
**Car**
- Datos del vehículo, disponibilidad, precio diario y foto.
- Asociado a un propietario (owner_id).
  
**Reservation**
- Pre-reservas y reservas con estados: hold, confirmed, cancelled, finished
  
**Payment**
- Monto, método, estado, asociación a una reserva.
  
**Notification**
- Mensajes enviados al usuario.
- Estado de lectura.

Relaciones clave:
-Un owner puede tener muchos autos.
- Un auto puede tener muchas reservas.
- Una reserva puede tener varios pagos.
- Un usuario puede tener muchas notificaciones.

---

## 4. Casos de Uso
**Para Clientes**:
- Buscar vehículos.
- Ver detalles del vehículo.
- Seleccionar fecha y activar pre-reserva (hold).
- Crear reserva.
- Cancelar reserva.
- Recibir notificaciones.

**Para Propietarios**:
- Registrar vehículo.
- Editar o actualizar disponibilidad.
- Aceptar o rechazar reservas (cuando aplica flujo de aprobación).
- Recibir notificaciones.

**Sistemas externos**:
- Pago → Procesar pago.
- Notificaciones → Enviar mensaje.

---

## 5. Diagrama de Clases
Incluye las clases principales del dominio:

- User, Client, Owner
- Car
- Reservation
- Payment
- Notification

Las relaciones incluyen:
- Herencia (Owner y Client extienden User)
- Cardinalidades (1:1, 1:M, 0:M)

---

## 6. Flujo del Proceso de Reserva (Diagrama de Secuencia)
Incluye el proceso completo desde búsqueda hasta confirmación, integrando conceptos modernos como CQRS, Outbox, Idempotency Keys, timeouts, reintentos, y event-driven architecture.

**Etapas**:
**1. Búsqueda**
- Cliente consulta disponibilidad por ciudad/fecha.
- El sistema devuelve lista de vehículos.

**2. Pre-reserva ("hold")**
- Se crea una pre-reserva con estado hold.
- Se usa Idempotency-Key para evitar duplicados.

**3. Inicio del pago**
- Se inicia pago → estado pending.

**4. Resultado del pago**
- Si aprueba → cambia a confirmed.
- Si falla → cancelled.
- Si expira → cancelled_timeout.

**5. Proyección y eventos**
- Se publica el evento ReservationConfirmed.
- El sistema de disponibilidad lo procesa para bloquear el vehículo.
- Notificaciones se envían por correo y app.

**6. Aprobación del propietario (opcional)**
- Se solicita aprobación dentro de un SLA predefinido.
- Reintentos con backoff.
- Si rechaza, se libera la reserva.

---

## 7. Archivos incluidos en este repositorio
Los diagramas incluidos representan:

- C4-AUTOYA.png
- DiagramaBD.png
- DiagramaCasosUso.png
- DiagramaClases.jpg
- DiagramaDeSecuencia.png
- DiagramaDominio.jpg
- Mejora de relaciones en las bases de datos para microservicios.jpg

---

## 8. Objetivo del repositorio
Centralizar toda la documentación técnica de AutoYa para permitir:

- Facilitar onboarding de nuevos desarrolladores.
- Establecer una arquitectura clara y coherente.
- Mantener una referencia única del modelo de datos y procesos.
- Guiar implementaciones futuras basadas en DDD, CQRS y eventos.

---

## Enlaces del proyecto
- **Frontend AutoYa**: https://github.com/AlvaroV19/AutoYa-Frontend
- **Backend AutoYa**: https://github.com/AlvaroV19/AutoYa-Backend
- **Deployment AutoYa**: https://github.com/AlvaroV19/AutoYa-Deployment
