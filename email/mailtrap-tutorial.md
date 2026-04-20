# 📧 Mailtrap - Tutorial Completo

## Introducción

**Mailtrap** es una plataforma de infraestructura de email que ofrece dos productos principales:

- **Email Testing** (Sandbox): un entorno seguro para capturar y probar correos electrónicos sin enviarlos a destinatarios reales.
- **Email Sending** (API/SMTP): un servicio de envío transaccional de emails a producción con alta entregabilidad.

Es la herramienta preferida por los equipos de desarrollo para evitar enviar correos de prueba a usuarios reales, depurar plantillas HTML, verificar cabeceras y adjuntos, y garantizar que los emails lleguen a la bandeja de entrada en producción.

---

## 📋 Tabla de Contenidos

1. [¿Cómo funciona Mailtrap?](#1-cómo-funciona-mailtrap)
2. [Configuración de la cuenta](#2-configuración-de-la-cuenta)
3. [Email Testing (Sandbox)](#3-email-testing-sandbox)
4. [Integración con Node.js / JavaScript](#4-integración-con-nodejs--javascript)
5. [Integración con Python](#5-integración-con-python)
6. [Integración con PHP](#6-integración-con-php)
7. [Integración con Java / Spring Boot](#7-integración-con-java--spring-boot)
8. [Integración con Go](#8-integración-con-go)
9. [Email Sending (API transaccional)](#9-email-sending-api-transaccional)
10. [Plantillas de Email](#10-plantillas-de-email)
11. [Mailtrap en CI/CD](#11-mailtrap-en-cicd)
12. [Monitoreo y Analytics](#12-monitoreo-y-analytics)
13. [Buenas Prácticas](#13-buenas-prácticas)
14. [Solución de Problemas Comunes](#14-solución-de-problemas-comunes)

---

## 1. ¿Cómo funciona Mailtrap?

### Flujo general

```
┌───────────────────────────────────────────────────────────────────┐
│                        TU APLICACIÓN                              │
│                                                                   │
│   Código → SMTP o API → Mailtrap → Bandeja de prueba / Real      │
└───────────────────────────────────────────────────────────────────┘

DESARROLLO / TESTING          PRODUCCIÓN
┌─────────────────┐          ┌─────────────────────┐
│ App envía email │   ──►    │  Mailtrap Sandbox   │  ← emails NO salen
│ (localhost)     │          │  (inbox virtual)    │
└─────────────────┘          └─────────────────────┘

┌─────────────────┐          ┌─────────────────────┐
│ App envía email │   ──►    │  Mailtrap SMTP/API  │  ← emails SÍ llegan
│ (producción)    │          │  (envío real)       │
└─────────────────┘          └─────────────────────┘
```

### Comparación de modos

| Característica | Email Testing (Sandbox) | Email Sending (Producción) |
|---------------|------------------------|---------------------------|
| Destinatario real recibe el email | ❌ No | ✅ Sí |
| Vista previa HTML | ✅ Sí | ✅ Sí |
| Análisis spam score | ✅ Sí | ✅ Sí |
| Validación de cabeceras | ✅ Sí | ✅ Sí |
| Entregabilidad garantizada | N/A | ✅ Alta |
| Precio | Gratis (plan básico) | De pago |
| Uso recomendado | Dev / Testing / CI | Producción |

---

## 2. Configuración de la cuenta

### 2.1 Crear una cuenta

1. Ve a [mailtrap.io](https://mailtrap.io) y haz clic en **Sign Up**.
2. Puedes registrarte con **email y contraseña** o con **Google / GitHub**.
3. Confirma tu email y accede al dashboard.

### 2.2 Crear un Inbox (Sandbox)

1. En el menú lateral selecciona **Email Testing → Inboxes**.
2. Haz clic en **+ Add Inbox** y asígnale un nombre (ej: `my-project-dev`).
3. Accede al inbox y verás las **credenciales SMTP**:

```
Host:     sandbox.smtp.mailtrap.io
Port:     2525  (también soporta 25, 465, 587)
Username: <tu_username_generado>
Password: <tu_password_generado>
Auth:     PLAIN / LOGIN / CRAM-MD5
TLS:      STARTTLS
```

4. Además de SMTP, Mailtrap ofrece una **API Token** en la sección **API** del inbox para integraciones más modernas.

### 2.3 Estructura del dashboard

```
Dashboard
├── Email Testing
│   ├── Inboxes          ← Bandejas de prueba
│   └── API              ← Token de API para testing
├── Email Sending
│   ├── Domains          ← Dominios verificados
│   ├── Sending Streams  ← Flujos de envío
│   └── API / SMTP creds ← Credenciales de producción
└── Settings
    ├── Team members
    └── Billing
```

---

## 3. Email Testing (Sandbox)

### 3.1 ¿Qué puedes inspeccionar?

Cuando un email llega al inbox de prueba, puedes ver:

| Pestaña | Descripción |
|---------|-------------|
| **HTML** | Vista previa del email renderizado |
| **Text** | Versión en texto plano |
| **Raw** | Cabeceras y cuerpo completo (raw MIME) |
| **Tech info** | SPF, DKIM, DMARC, codificación |
| **Spam Analysis** | Puntuación SpamAssassin |
| **Blacklists** | Verificación en listas negras |
| **Check HTML** | Compatibilidad con clientes de email |

### 3.2 Spam Score

Mailtrap muestra el **SpamAssassin Score** de tu email:

```
Score < 3.0   ✅ Probablemente NO es spam
Score 3-5     ⚠️  Zona de riesgo
Score > 5.0   ❌ Probablemente ES spam
```

### 3.3 API de Testing

Puedes usar la API REST para listar y leer emails del inbox automáticamente (ideal para tests E2E):

```bash
# Listar mensajes del inbox
GET https://mailtrap.io/api/accounts/{account_id}/inboxes/{inbox_id}/messages
Authorization: Token <api_token>

# Leer un mensaje específico
GET https://mailtrap.io/api/accounts/{account_id}/inboxes/{inbox_id}/messages/{message_id}

# Borrar todos los mensajes del inbox
PATCH https://mailtrap.io/api/accounts/{account_id}/inboxes/{inbox_id}/clean
```

Ejemplo con `curl`:

```bash
curl -X GET \
  "https://mailtrap.io/api/accounts/123456/inboxes/654321/messages" \
  -H "Authorization: Token abc123yourtoken"
```

---

## 4. Integración con Node.js / JavaScript

### 4.1 Con Nodemailer (SMTP)

```bash
npm install nodemailer
```

```javascript
// mailtrap.js
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: 'sandbox.smtp.mailtrap.io',
  port: 2525,
  auth: {
    user: process.env.MAILTRAP_USER,
    pass: process.env.MAILTRAP_PASS,
  },
});

async function sendWelcomeEmail(to, name) {
  const info = await transporter.sendMail({
    from: '"Mi App" <noreply@miapp.com>',
    to,
    subject: `¡Bienvenido, ${name}!`,
    text: `Hola ${name}, gracias por registrarte.`,
    html: `
      <h1>¡Bienvenido, ${name}!</h1>
      <p>Gracias por registrarte en Mi App.</p>
      <a href="https://miapp.com/verificar">Verificar cuenta</a>
    `,
  });

  console.log('Email enviado:', info.messageId);
  return info;
}

sendWelcomeEmail('usuario@ejemplo.com', 'Carlos');
```

### 4.2 Con el SDK oficial de Mailtrap

```bash
npm install mailtrap
```

```javascript
// mailtrap-sdk.js
const { MailtrapClient } = require('mailtrap');

const client = new MailtrapClient({
  token: process.env.MAILTRAP_API_TOKEN,
  testInboxId: parseInt(process.env.MAILTRAP_INBOX_ID), // solo para testing
});

async function sendEmail() {
  const response = await client.testing.sendMail({  // usa .send() para producción
    from: { email: 'noreply@miapp.com', name: 'Mi App' },
    to: [{ email: 'usuario@ejemplo.com', name: 'Carlos' }],
    subject: '¡Bienvenido!',
    text: 'Gracias por registrarte.',
    html: '<h1>¡Bienvenido!</h1><p>Gracias por registrarte.</p>',
    category: 'Welcome',
  });

  console.log('Email enviado:', response);
}

sendEmail();
```

### 4.3 Con variables de entorno (.env)

```bash
# .env (NUNCA subas este archivo a git)
MAILTRAP_USER=tu_usuario_smtp
MAILTRAP_PASS=tu_password_smtp
MAILTRAP_API_TOKEN=tu_api_token
MAILTRAP_INBOX_ID=123456
```

```javascript
// Carga las variables con dotenv
require('dotenv').config();
```

### 4.4 Ejemplo con Express

```javascript
// src/routes/auth.js
const express = require('express');
const nodemailer = require('nodemailer');
const router = express.Router();

const transporter = nodemailer.createTransport({
  host: 'sandbox.smtp.mailtrap.io',
  port: 2525,
  auth: {
    user: process.env.MAILTRAP_USER,
    pass: process.env.MAILTRAP_PASS,
  },
});

router.post('/register', async (req, res) => {
  const { email, name } = req.body;

  try {
    // Lógica de registro...

    // Enviar email de bienvenida
    await transporter.sendMail({
      from: '"Mi App" <noreply@miapp.com>',
      to: email,
      subject: `¡Bienvenido, ${name}!`,
      html: `<h1>Hola ${name}</h1><p>Tu cuenta ha sido creada.</p>`,
    });

    res.status(201).json({ message: 'Usuario registrado. Revisa tu email.' });
  } catch (error) {
    console.error('Error al enviar email:', error);
    res.status(500).json({ error: 'Error interno del servidor' });
  }
});

module.exports = router;
```

---

## 5. Integración con Python

### 5.1 Con smtplib (librería estándar)

```python
# mailtrap_smtp.py
import smtplib
import os
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

def send_email(to: str, subject: str, html_content: str, text_content: str):
    msg = MIMEMultipart('alternative')
    msg['Subject'] = subject
    msg['From'] = 'noreply@miapp.com'
    msg['To'] = to

    part_text = MIMEText(text_content, 'plain')
    part_html = MIMEText(html_content, 'html')

    msg.attach(part_text)
    msg.attach(part_html)

    with smtplib.SMTP('sandbox.smtp.mailtrap.io', 2525) as server:
        server.starttls()
        server.login(
            os.environ['MAILTRAP_USER'],
            os.environ['MAILTRAP_PASS']
        )
        server.sendmail(msg['From'], [to], msg.as_string())
        print(f'Email enviado a {to}')

if __name__ == '__main__':
    send_email(
        to='usuario@ejemplo.com',
        subject='¡Bienvenido!',
        html_content='<h1>Hola</h1><p>Gracias por registrarte.</p>',
        text_content='Hola, gracias por registrarte.',
    )
```

### 5.2 Con el SDK oficial de Mailtrap

```bash
pip install mailtrap
```

```python
# mailtrap_sdk.py
import mailtrap as mt
import os

client = mt.MailtrapClient(token=os.environ['MAILTRAP_API_TOKEN'])

mail = mt.Mail(
    sender=mt.Address(email='noreply@miapp.com', name='Mi App'),
    to=[mt.Address(email='usuario@ejemplo.com', name='Carlos')],
    subject='¡Bienvenido!',
    html='<h1>¡Bienvenido, Carlos!</h1><p>Gracias por registrarte.</p>',
    text='¡Bienvenido, Carlos! Gracias por registrarte.',
    category='Welcome',
)

# Para testing usa test_inbox_id; para producción usa send()
response = client.send(mail)
print('Email enviado:', response)
```

### 5.3 Con Django

```python
# settings.py
import os

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'sandbox.smtp.mailtrap.io'
EMAIL_PORT = 2525
EMAIL_HOST_USER = os.environ.get('MAILTRAP_USER')
EMAIL_HOST_PASSWORD = os.environ.get('MAILTRAP_PASS')
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = 'noreply@miapp.com'
```

```python
# views.py
from django.core.mail import send_mail
from django.http import JsonResponse

def register(request):
    # Lógica de registro...
    send_mail(
        subject='¡Bienvenido!',
        message='Gracias por registrarte.',
        from_email='noreply@miapp.com',
        recipient_list=['usuario@ejemplo.com'],
        html_message='<h1>¡Bienvenido!</h1><p>Gracias por registrarte.</p>',
    )
    return JsonResponse({'status': 'ok'})
```

### 5.4 Con FastAPI

```python
# email_service.py
import os
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from fastapi import FastAPI, BackgroundTasks

app = FastAPI()

def send_email_background(to: str, subject: str, body: str):
    msg = MIMEMultipart('alternative')
    msg['Subject'] = subject
    msg['From'] = 'noreply@miapp.com'
    msg['To'] = to
    msg.attach(MIMEText(body, 'html'))

    with smtplib.SMTP('sandbox.smtp.mailtrap.io', 2525) as server:
        server.starttls()
        server.login(os.environ['MAILTRAP_USER'], os.environ['MAILTRAP_PASS'])
        server.sendmail(msg['From'], [to], msg.as_string())

@app.post('/register')
async def register(email: str, name: str, background_tasks: BackgroundTasks):
    # Lógica de registro...
    background_tasks.add_task(
        send_email_background,
        to=email,
        subject=f'¡Bienvenido, {name}!',
        body=f'<h1>Hola {name}</h1><p>Tu cuenta ha sido creada.</p>',
    )
    return {'message': 'Usuario registrado. Email en camino.'}
```

---

## 6. Integración con PHP

### 6.1 Con PHPMailer

```bash
composer require phpmailer/phpmailer
```

```php
<?php
// mailtrap.php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\SMTP;

require 'vendor/autoload.php';

$mail = new PHPMailer(true);

// Configuración del servidor SMTP
$mail->isSMTP();
$mail->Host       = 'sandbox.smtp.mailtrap.io';
$mail->SMTPAuth   = true;
$mail->Username   = $_ENV['MAILTRAP_USER'];
$mail->Password   = $_ENV['MAILTRAP_PASS'];
$mail->SMTPSecure = PHPMailer::ENCRYPTION_STARTTLS;
$mail->Port       = 2525;

// Remitente y destinatario
$mail->setFrom('noreply@miapp.com', 'Mi App');
$mail->addAddress('usuario@ejemplo.com', 'Carlos');

// Contenido
$mail->isHTML(true);
$mail->Subject = '¡Bienvenido!';
$mail->Body    = '<h1>¡Bienvenido!</h1><p>Gracias por registrarte.</p>';
$mail->AltBody = '¡Bienvenido! Gracias por registrarte.';

$mail->send();
echo 'Email enviado correctamente.';
```

### 6.2 Con Laravel

```bash
# .env
MAIL_MAILER=smtp
MAIL_HOST=sandbox.smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=tu_usuario
MAIL_PASSWORD=tu_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@miapp.com
MAIL_FROM_NAME="${APP_NAME}"
```

```php
<?php
// app/Mail/WelcomeMail.php
namespace App\Mail;

use Illuminate\Mail\Mailable;

class WelcomeMail extends Mailable
{
    public string $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function build(): self
    {
        return $this
            ->subject('¡Bienvenido!')
            ->view('emails.welcome');
    }
}
```

```php
<?php
// En un controlador
use App\Mail\WelcomeMail;
use Illuminate\Support\Facades\Mail;

Mail::to('usuario@ejemplo.com')->send(new WelcomeMail('Carlos'));
```

---

## 7. Integración con Java / Spring Boot

### 7.1 Dependencia Maven

```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

### 7.2 Configuración

```yaml
# application.yml
spring:
  mail:
    host: sandbox.smtp.mailtrap.io
    port: 2525
    username: ${MAILTRAP_USER}
    password: ${MAILTRAP_PASS}
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true
```

### 7.3 Servicio de Email

```java
// EmailService.java
package com.miapp.service;

import lombok.RequiredArgsConstructor;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Service;

import jakarta.mail.internet.MimeMessage;

@Service
@RequiredArgsConstructor
public class EmailService {

    private final JavaMailSender mailSender;

    public void sendWelcomeEmail(String to, String name) {
        try {
            MimeMessage message = mailSender.createMimeMessage();
            MimeMessageHelper helper = new MimeMessageHelper(message, true, "UTF-8");

            helper.setFrom("noreply@miapp.com", "Mi App");
            helper.setTo(to);
            helper.setSubject("¡Bienvenido, " + name + "!");
            helper.setText(
                "Hola " + name + ", gracias por registrarte.",  // texto plano
                "<h1>¡Bienvenido, " + name + "!</h1>"           // HTML
                + "<p>Gracias por registrarte en Mi App.</p>"
            );

            mailSender.send(message);
        } catch (Exception e) {
            throw new RuntimeException("Error al enviar email", e);
        }
    }
}
```

### 7.4 Controlador

```java
// AuthController.java
package com.miapp.controller;

import com.miapp.service.EmailService;
import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/auth")
@RequiredArgsConstructor
public class AuthController {

    private final EmailService emailService;

    @PostMapping("/register")
    public ResponseEntity<String> register(@RequestBody RegisterRequest request) {
        // Lógica de registro...
        emailService.sendWelcomeEmail(request.email(), request.name());
        return ResponseEntity.ok("Usuario registrado. Revisa tu email.");
    }

    record RegisterRequest(String email, String name) {}
}
```

### 7.5 Test con Mailtrap

```java
// EmailServiceTest.java
@SpringBootTest
@TestPropertySource(properties = {
    "spring.mail.host=sandbox.smtp.mailtrap.io",
    "spring.mail.port=2525",
    "spring.mail.username=${MAILTRAP_USER}",
    "spring.mail.password=${MAILTRAP_PASS}",
})
class EmailServiceTest {

    @Autowired
    private EmailService emailService;

    @Test
    void shouldSendWelcomeEmail() {
        assertDoesNotThrow(() ->
            emailService.sendWelcomeEmail("test@ejemplo.com", "TestUser")
        );
    }
}
```

---

## 8. Integración con Go

### 8.1 Con net/smtp (librería estándar)

```go
// email/mailtrap.go
package email

import (
    "fmt"
    "net/smtp"
    "os"
    "strings"
)

type MailtrapSender struct {
    host     string
    port     string
    username string
    password string
    from     string
}

func NewMailtrapSender() *MailtrapSender {
    return &MailtrapSender{
        host:     "sandbox.smtp.mailtrap.io",
        port:     "2525",
        username: os.Getenv("MAILTRAP_USER"),
        password: os.Getenv("MAILTRAP_PASS"),
        from:     "noreply@miapp.com",
    }
}

func (s *MailtrapSender) Send(to, subject, body string) error {
    auth := smtp.PlainAuth("", s.username, s.password, s.host)

    headers := map[string]string{
        "From":         s.from,
        "To":           to,
        "Subject":      subject,
        "MIME-Version": "1.0",
        "Content-Type": `text/html; charset="UTF-8"`,
    }

    var msg strings.Builder
    for k, v := range headers {
        msg.WriteString(fmt.Sprintf("%s: %s\r\n", k, v))
    }
    msg.WriteString("\r\n")
    msg.WriteString(body)

    addr := fmt.Sprintf("%s:%s", s.host, s.port)
    return smtp.SendMail(addr, auth, s.from, []string{to}, []byte(msg.String()))
}
```

```go
// main.go
package main

import (
    "fmt"
    "log"
    "miapp/email"
)

func main() {
    sender := email.NewMailtrapSender()
    err := sender.Send(
        "usuario@ejemplo.com",
        "¡Bienvenido!",
        "<h1>¡Bienvenido!</h1><p>Gracias por registrarte.</p>",
    )
    if err != nil {
        log.Fatalf("Error al enviar email: %v", err)
    }
    fmt.Println("Email enviado correctamente.")
}
```

### 8.2 Con gomail

```bash
go get gopkg.in/gomail.v2
```

```go
// email/gomail_sender.go
package email

import (
    "crypto/tls"
    "os"
    "strconv"

    gomail "gopkg.in/gomail.v2"
)

type GoMailSender struct {
    dialer *gomail.Dialer
    from   string
}

func NewGoMailSender() *GoMailSender {
    port, _ := strconv.Atoi(os.Getenv("MAILTRAP_PORT"))
    if port == 0 {
        port = 2525
    }

    d := gomail.NewDialer(
        "sandbox.smtp.mailtrap.io",
        port,
        os.Getenv("MAILTRAP_USER"),
        os.Getenv("MAILTRAP_PASS"),
    )
    d.TLSConfig = &tls.Config{InsecureSkipVerify: false}

    return &GoMailSender{dialer: d, from: "noreply@miapp.com"}
}

func (s *GoMailSender) Send(to, subject, htmlBody, textBody string) error {
    m := gomail.NewMessage()
    m.SetHeader("From", s.from)
    m.SetHeader("To", to)
    m.SetHeader("Subject", subject)
    m.SetBody("text/plain", textBody)
    m.AddAlternative("text/html", htmlBody)

    return s.dialer.DialAndSend(m)
}
```

---

## 9. Email Sending (API transaccional)

Una vez que tu aplicación está lista para **producción**, cambias de la sandbox a los servicios de envío real de Mailtrap.

### 9.1 Verificar tu dominio

1. Ve a **Email Sending → Domains**.
2. Haz clic en **+ Add Domain** e ingresa tu dominio (ej: `miapp.com`).
3. Mailtrap te dará registros DNS para añadir en tu proveedor:

```
Tipo   Nombre                      Valor
TXT    _dmarc.miapp.com            "v=DMARC1; p=none; rua=mailto:..."
TXT    mailtrap._domainkey...      "v=DKIM1; k=rsa; p=..."
TXT    miapp.com                   "v=spf1 include:_spf.smtp.mailtrap.live ~all"
```

4. Espera la propagación DNS (puede tardar hasta 48 h) y verifica en el dashboard.

### 9.2 Obtener credenciales de producción

1. Ve a **Email Sending → Sending Streams**.
2. Selecciona o crea un stream (ej: `Transactional`).
3. En la pestaña **SMTP / API** encontrarás:

```
Host:     live.smtp.mailtrap.io
Port:     587
Username: api
Password: <tu_api_token_de_produccion>
```

### 9.3 Envío vía API REST

```bash
curl -X POST "https://send.api.mailtrap.io/api/send" \
  -H "Authorization: Bearer <tu_api_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "from": {"email": "noreply@miapp.com", "name": "Mi App"},
    "to": [{"email": "usuario@ejemplo.com", "name": "Carlos"}],
    "subject": "¡Bienvenido!",
    "html": "<h1>¡Bienvenido, Carlos!</h1><p>Gracias por registrarte.</p>",
    "text": "¡Bienvenido, Carlos! Gracias por registrarte.",
    "category": "Welcome"
  }'
```

### 9.4 Cambiar entre Testing y Producción en Node.js

```javascript
// config/email.js
const { MailtrapClient } = require('mailtrap');

const isProduction = process.env.NODE_ENV === 'production';

const client = new MailtrapClient({
  token: process.env.MAILTRAP_API_TOKEN,
  ...(isProduction ? {} : { testInboxId: parseInt(process.env.MAILTRAP_INBOX_ID) }),
});

async function sendEmail(options) {
  if (isProduction) {
    return client.send(options);
  } else {
    return client.testing.sendMail(options);
  }
}

module.exports = { sendEmail };
```

---

## 10. Plantillas de Email

### 10.1 Crear plantillas en el dashboard

1. Ve a **Email Sending → Templates**.
2. Haz clic en **+ Create Template**.
3. Diseña el email con el editor visual o directamente en HTML.
4. Define **variables dinámicas** con la sintaxis `{{ variable }}`.
5. Guarda la plantilla y copia el **Template UUID**.

### 10.2 Usar plantillas vía API

```bash
curl -X POST "https://send.api.mailtrap.io/api/send" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "from": {"email": "noreply@miapp.com", "name": "Mi App"},
    "to": [{"email": "usuario@ejemplo.com", "name": "Carlos"}],
    "template_uuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "template_variables": {
      "user_name": "Carlos",
      "verification_link": "https://miapp.com/verificar?token=abc123",
      "year": "2025"
    }
  }'
```

### 10.3 Plantilla HTML de ejemplo

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bienvenido</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f4f4f4; margin: 0; padding: 0; }
    .container { max-width: 600px; margin: 40px auto; background: white;
                 border-radius: 8px; overflow: hidden; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
    .header { background: #6366f1; color: white; padding: 32px; text-align: center; }
    .body { padding: 32px; color: #333; }
    .cta { display: inline-block; background: #6366f1; color: white;
           padding: 12px 24px; border-radius: 6px; text-decoration: none; margin: 16px 0; }
    .footer { background: #f9fafb; padding: 16px; text-align: center;
              font-size: 12px; color: #666; }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>🎉 ¡Bienvenido a Mi App!</h1>
    </div>
    <div class="body">
      <p>Hola <strong>{{ user_name }}</strong>,</p>
      <p>Gracias por registrarte. Tu cuenta está lista para usar.</p>
      <a href="{{ verification_link }}" class="cta">Verificar mi cuenta</a>
      <p>Si no creaste esta cuenta, ignora este mensaje.</p>
    </div>
    <div class="footer">
      <p>© {{ year }} Mi App. Todos los derechos reservados.</p>
      <p><a href="{{ unsubscribe_link }}">Cancelar suscripción</a></p>
    </div>
  </div>
</body>
</html>
```

### 10.4 Adjuntos

```javascript
// Enviar email con adjunto
await transporter.sendMail({
  from: '"Mi App" <noreply@miapp.com>',
  to: 'usuario@ejemplo.com',
  subject: 'Tu factura',
  html: '<p>Adjuntamos tu factura del mes.</p>',
  attachments: [
    {
      filename: 'factura.pdf',
      path: '/ruta/local/factura.pdf',         // archivo local
      contentType: 'application/pdf',
    },
    {
      filename: 'logo.png',
      content: Buffer.from(base64String, 'base64'),
      encoding: 'base64',
      cid: 'logo@miapp',                        // para embeber en HTML como <img src="cid:logo@miapp">
    },
  ],
});
```

---

## 11. Mailtrap en CI/CD

### 11.1 Tests de integración con Mailtrap

Mailtrap permite automatizar la verificación de que los emails se envían correctamente en tu pipeline.

```
┌─────────────────────────────────────────────────────┐
│                    CI/CD Pipeline                   │
│                                                     │
│  Push → Build → Unit Tests → Integration Tests      │
│                                  │                  │
│                          Email Tests                │
│                          (Mailtrap API)             │
│                                                     │
│  ✅ Email enviado al inbox de prueba                │
│  ✅ Asunto correcto                                  │
│  ✅ Destinatario correcto                            │
│  ✅ Contenido HTML válido                            │
└─────────────────────────────────────────────────────┘
```

### 11.2 GitHub Actions - Node.js

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        env:
          MAILTRAP_USER: ${{ secrets.MAILTRAP_USER }}
          MAILTRAP_PASS: ${{ secrets.MAILTRAP_PASS }}
          MAILTRAP_API_TOKEN: ${{ secrets.MAILTRAP_API_TOKEN }}
          MAILTRAP_INBOX_ID: ${{ secrets.MAILTRAP_INBOX_ID }}
          MAILTRAP_ACCOUNT_ID: ${{ secrets.MAILTRAP_ACCOUNT_ID }}
        run: npm test
```

### 11.3 Test E2E que verifica el inbox

```javascript
// tests/email.test.js
const axios = require('axios');

const ACCOUNT_ID = process.env.MAILTRAP_ACCOUNT_ID;
const INBOX_ID   = process.env.MAILTRAP_INBOX_ID;
const API_TOKEN  = process.env.MAILTRAP_API_TOKEN;

const mailtrapApi = axios.create({
  baseURL: `https://mailtrap.io/api/accounts/${ACCOUNT_ID}/inboxes/${INBOX_ID}`,
  headers: { Authorization: `Token ${API_TOKEN}` },
});

async function waitForEmail(subject, timeoutMs = 10000) {
  const start = Date.now();
  while (Date.now() - start < timeoutMs) {
    const { data } = await mailtrapApi.get('/messages');
    const found = data.find(msg => msg.subject === subject);
    if (found) return found;
    await new Promise(r => setTimeout(r, 1000));
  }
  throw new Error(`Email con asunto "${subject}" no llegó en ${timeoutMs}ms`);
}

describe('Email de bienvenida', () => {
  beforeEach(async () => {
    // Limpiar inbox antes de cada test
    await mailtrapApi.patch('/clean');
  });

  it('debe enviar email de bienvenida al registrarse', async () => {
    // Activar el flujo que envía el email
    await axios.post('http://localhost:3000/api/auth/register', {
      email: 'test@ejemplo.com',
      name: 'TestUser',
    });

    // Esperar y verificar el email en Mailtrap
    const email = await waitForEmail('¡Bienvenido, TestUser!');

    expect(email.to_email).toBe('test@ejemplo.com');
    expect(email.subject).toBe('¡Bienvenido, TestUser!');
    expect(email.html_body).toContain('TestUser');
  });
});
```

### 11.4 GitHub Actions - Python

```yaml
# .github/workflows/test-python.yml
name: Python Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        env:
          MAILTRAP_USER: ${{ secrets.MAILTRAP_USER }}
          MAILTRAP_PASS: ${{ secrets.MAILTRAP_PASS }}
        run: pytest tests/ -v
```

---

## 12. Monitoreo y Analytics

### 12.1 Panel de Analytics (Email Sending)

En producción, Mailtrap ofrece métricas detalladas:

| Métrica | Descripción |
|---------|-------------|
| **Delivered** | Emails entregados al servidor del destinatario |
| **Opened** | Emails abiertos (requiere pixel de seguimiento) |
| **Clicked** | Links clicados dentro del email |
| **Bounced** | Emails que no pudieron entregarse |
| **Spam** | Emails marcados como spam por el destinatario |
| **Unsubscribed** | Destinatarios que cancelaron suscripción |

### 12.2 Webhooks

Mailtrap puede notificarte en tiempo real sobre eventos de email:

```javascript
// webhook handler (Express)
app.post('/webhooks/mailtrap', express.json(), (req, res) => {
  const events = req.body;

  for (const event of events) {
    switch (event.event) {
      case 'delivery':
        console.log(`✅ Email entregado a ${event.email}`);
        break;
      case 'open':
        console.log(`👁️  Email abierto por ${event.email}`);
        break;
      case 'click':
        console.log(`🔗 Link clickeado: ${event.url} por ${event.email}`);
        break;
      case 'bounce':
        console.log(`❌ Bounce para ${event.email}: ${event.bounce_type}`);
        // Marcar email como inválido en tu base de datos
        break;
      case 'spam':
        console.log(`🚫 Spam report de ${event.email}`);
        // Desuscribir automáticamente
        break;
    }
  }

  res.sendStatus(200);
});
```

### 12.3 Tipos de bounce

```
Hard Bounce  → Email no existe. Eliminar de tu lista.
Soft Bounce  → Buzón lleno / servidor caído. Reintentar después.
Block        → Servidor rechaza tus emails. Revisar reputación.
```

---

## 13. Buenas Prácticas

### 13.1 Gestión de credenciales

```bash
# ✅ Correcto: usar variables de entorno
MAILTRAP_USER=abc123
MAILTRAP_PASS=xyz789

# ❌ Incorrecto: credenciales en el código
const transporter = nodemailer.createTransport({
  user: 'abc123',   // NUNCA hagas esto
  pass: 'xyz789',
});
```

```bash
# Añade .env a .gitignore
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
```

### 13.2 Separar entornos

```javascript
// config/email.js - Patrón recomendado
const config = {
  development: {
    host: 'sandbox.smtp.mailtrap.io',
    port: 2525,
    user: process.env.MAILTRAP_USER,
    pass: process.env.MAILTRAP_PASS,
  },
  production: {
    host: 'live.smtp.mailtrap.io',
    port: 587,
    user: 'api',
    pass: process.env.MAILTRAP_API_TOKEN,
  },
};

module.exports = config[process.env.NODE_ENV || 'development'];
```

### 13.3 Manejo de errores y reintentos

```javascript
async function sendEmailWithRetry(options, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await transporter.sendMail(options);
    } catch (error) {
      console.error(`Intento ${attempt} fallido:`, error.message);
      if (attempt === maxRetries) throw error;
      // Espera exponencial: 1s, 2s, 4s
      await new Promise(r => setTimeout(r, Math.pow(2, attempt - 1) * 1000));
    }
  }
}
```

### 13.4 Rate limiting

```javascript
// Evitar enviar demasiados emails en poco tiempo
const pLimit = require('p-limit');
const limit = pLimit(5); // máx 5 en paralelo

const emailPromises = recipients.map(recipient =>
  limit(() => sendEmail(recipient))
);

await Promise.all(emailPromises);
```

### 13.5 Checklist antes de producción

```
✅ Dominio verificado (SPF, DKIM, DMARC configurados)
✅ Credenciales en variables de entorno (no en el código)
✅ Tests de integración pasando en CI/CD
✅ Plantillas HTML probadas en clientes distintos (Gmail, Outlook, etc.)
✅ Spam score < 3.0
✅ Cabecera List-Unsubscribe configurada
✅ Versión texto plano incluida en todos los emails
✅ Manejo de bounces implementado
✅ Webhooks configurados para eventos críticos
✅ Monitoreo de tasa de entrega activo
```

### 13.6 Autenticación de dominio (SPF, DKIM, DMARC)

```
SPF   → "¿Este servidor tiene permiso para enviar desde mi dominio?"
DKIM  → "¿Este email no fue modificado en tránsito?"
DMARC → "¿Qué hacer si SPF o DKIM fallan?"

Sin estas configuraciones:
→ Gmail, Outlook y otros filtros rechazarán o enviarán a spam tus emails.
```

---

## 14. Solución de Problemas Comunes

### Error: Connection timeout

```
Error: connect ETIMEDOUT sandbox.smtp.mailtrap.io:2525
```

**Solución**: El firewall de tu empresa/red puede bloquear el puerto 2525. Prueba con el puerto 587 o 465:

```javascript
const transporter = nodemailer.createTransport({
  host: 'sandbox.smtp.mailtrap.io',
  port: 587,  // ← cambia el puerto
  secure: false,
  auth: { user: '...', pass: '...' },
});
```

---

### Error: Authentication failed

```
Error: Invalid login: 535 5.7.8 Authentication credentials invalid
```

**Solución**: Verifica que estás usando las credenciales correctas del inbox específico (cada inbox tiene sus propias credenciales). Cópialas directamente desde el dashboard de Mailtrap.

---

### El email llega pero el HTML no se ve bien

**Causas comunes**:
- CSS externo (los clientes de email no soportan `<link rel="stylesheet">`)
- Grid/Flexbox (soporte limitado en clientes de email)
- Fuentes personalizadas (no se cargan en todos los clientes)

**Solución**: usa siempre **CSS inline** y tablas para el layout:

```html
<!-- ✅ Correcto -->
<p style="color: #333; font-size: 16px;">Hola</p>
<table width="100%" cellpadding="0" cellspacing="0">...</table>

<!-- ❌ Incorrecto -->
<link rel="stylesheet" href="styles.css">
<div class="flex container">...</div>
```

---

### Emails en spam en producción

**Diagnóstico**:
1. Verifica que SPF, DKIM y DMARC están configurados.
2. Comprueba el spam score en Mailtrap Sandbox (< 3.0 es lo ideal).
3. Asegúrate de no incluir palabras de spam ("GRATIS", "GANA DINERO", etc.).
4. Verifica que tu IP/dominio no está en listas negras.

```bash
# Herramientas de diagnóstico
https://mxtoolbox.com/blacklists.aspx   ← Listas negras
https://www.mail-tester.com             ← Puntuación general
https://www.learndmarc.com              ← Verificar DMARC
```

---

### Error: self-signed certificate

```
Error: self signed certificate in certificate chain
```

**Solución temporal** (solo en desarrollo):

```javascript
const transporter = nodemailer.createTransport({
  host: 'sandbox.smtp.mailtrap.io',
  port: 2525,
  tls: {
    rejectUnauthorized: false,  // ⚠️  Solo en desarrollo
  },
  auth: { user: '...', pass: '...' },
});
```

---

## Resumen

```
┌──────────────────────────────────────────────────────────────────┐
│                      MAILTRAP EN RESUMEN                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  DESARROLLO / TESTING              PRODUCCIÓN                   │
│  ┌───────────────────┐             ┌───────────────────────┐    │
│  │ sandbox.smtp...   │             │ live.smtp.mailtrap.io │    │
│  │ Port: 2525        │             │ Port: 587             │    │
│  │ Inbox virtual     │             │ Envío real            │    │
│  │ API de testing    │             │ Analytics + Webhooks  │    │
│  └───────────────────┘             └───────────────────────┘    │
│                                                                  │
│  INTEGRACIONES SOPORTADAS                                        │
│  Node.js · Python · PHP · Java · Go · Ruby · .NET · otros       │
│                                                                  │
│  CARACTERÍSTICAS CLAVE                                           │
│  ✅ Vista previa HTML              ✅ Spam score                  │
│  ✅ Plantillas con variables       ✅ SPF/DKIM/DMARC              │
│  ✅ Adjuntos                       ✅ Webhooks                    │
│  ✅ API REST                       ✅ CI/CD friendly              │
└──────────────────────────────────────────────────────────────────┘
```

### Próximos pasos

1. 📝 [Crea tu cuenta gratuita en Mailtrap](https://mailtrap.io)
2. 🔧 Integra Mailtrap en tu proyecto usando los ejemplos de este tutorial
3. 🧪 Escribe tests E2E que verifiquen tus emails con la API de Mailtrap
4. 🚀 Configura tu dominio y pasa a producción con Email Sending
5. 📊 Monitorea tus métricas de entrega con el dashboard de Analytics

### Recursos oficiales

- 📚 [Documentación oficial de Mailtrap](https://mailtrap.io/blog/)
- 🔌 [API Reference](https://api-docs.mailtrap.io/)
- 💬 [Soporte de Mailtrap](https://help.mailtrap.io/)
- 🎓 [Mailtrap Academy](https://mailtrap.io/academy/)
