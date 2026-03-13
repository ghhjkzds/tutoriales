# 🤖 Guía Completa de LLMs: Comparación e Implementación

## Índice

1. [¿Qué es un LLM?](#1-qué-es-un-llm)
2. [Comparación de los principales LLMs](#2-comparación-de-los-principales-llms)
3. [Comparación por caso de uso](#3-comparación-por-caso-de-uso)
4. [Comparación de precios](#4-comparación-de-precios)
5. [Cómo elegir el LLM adecuado](#5-cómo-elegir-el-llm-adecuado)
6. [Implementar un Chatbot](#6-implementar-un-chatbot)
7. [Implementar Generación de Imágenes](#7-implementar-generación-de-imágenes)
8. [Implementar Resúmenes de Texto](#8-implementar-resúmenes-de-texto)
9. [Implementar Generación de Podcasts y Audio](#9-implementar-generación-de-podcasts-y-audio)
10. [Proyecto completo: App multimodal](#10-proyecto-completo-app-multimodal)
11. [Buenas prácticas y consejos](#11-buenas-prácticas-y-consejos)

---

## 1. ¿Qué es un LLM?

Un **LLM** (Large Language Model, o Modelo de Lenguaje Grande) es un sistema de inteligencia artificial entrenado con enormes cantidades de texto para entender y generar lenguaje humano.

### ¿Cómo funcionan?

```
Tu pregunta → Tokenización → Transformer (capas de atención) → Predicción → Respuesta
```

En términos simples: el modelo aprende patrones del lenguaje leyendo miles de millones de textos, y luego "predice" la siguiente palabra más probable en cada momento para generar una respuesta coherente.

### Tipos de modelos

| Tipo | Qué hace | Ejemplos |
|------|----------|----------|
| **Texto → Texto** | Chatbot, resúmenes, código | GPT-4o, Claude, Gemini, Llama |
| **Texto → Imagen** | Generación de imágenes | DALL-E 3, Stable Diffusion, Midjourney |
| **Texto → Audio** | Voz, podcasts | ElevenLabs, OpenAI TTS, Bark |
| **Audio → Texto** | Transcripción | Whisper, Deepgram |
| **Multimodal** | Combina texto, imagen y audio | GPT-4o, Gemini 1.5 Pro |

---

## 2. Comparación de los principales LLMs

### Modelos de texto (los más usados)

| Modelo | Empresa | Acceso | Contexto máximo | Puntos fuertes | Puntos débiles |
|--------|---------|--------|-----------------|----------------|----------------|
| **GPT-4o** | OpenAI | API / ChatGPT | 128.000 tokens | Equilibrado, multimodal, rápido | Precio por uso |
| **GPT-4o mini** | OpenAI | API / ChatGPT | 128.000 tokens | Muy barato, rápido | Menos capaz que GPT-4o |
| **Claude 3.5 Sonnet** | Anthropic | API / claude.ai | 200.000 tokens | Excelente con textos largos, preciso | Más caro |
| **Claude 3 Haiku** | Anthropic | API / claude.ai | 200.000 tokens | Muy rápido y económico | Menos potente |
| **Gemini 1.5 Pro** | Google | API / Gemini | 1.000.000 tokens | Contexto enorme, multimodal | Respuestas a veces inconsistentes |
| **Gemini 1.5 Flash** | Google | API / Gemini | 1.000.000 tokens | Rapidísimo, gratuito con límites | Menos preciso |
| **Llama 3.1 70B** | Meta | Local / Groq / Replicate | 128.000 tokens | Gratis, local, open source | Requiere hardware potente |
| **Llama 3.1 8B** | Meta | Local / Groq | 128.000 tokens | Ligero, rápido en CPU | Menos preciso que modelos grandes |
| **Mistral Large** | Mistral AI | API | 128.000 tokens | Eficiente, bueno en código | Menos documentación |
| **Mistral 7B** | Mistral AI | Local / Hugging Face | 32.000 tokens | Muy eficiente, open source | Contexto limitado |
| **Qwen2.5 72B** | Alibaba | Local / Hugging Face | 128.000 tokens | Excelente multilingüe | Menos conocido en occidente |
| **DeepSeek R1** | DeepSeek | API / Local | 64.000 tokens | Razonamiento avanzado, barato | API menos estable |

### ¿Cómo entender el "contexto"?

El **contexto** es la cantidad de texto que el modelo puede "recordar" en una conversación. Un token equivale aproximadamente a 0,75 palabras en inglés o 0,6 en español.

```
128.000 tokens ≈ 300 páginas de texto ≈ una novela corta completa
1.000.000 tokens ≈ 2.500 páginas ≈ varios libros
```

### Benchmarks de referencia (comparación objetiva)

> Los benchmarks son pruebas estandarizadas que miden capacidades específicas. Estos son valores aproximados de principios de 2025.

| Modelo | MMLU (conocimiento general) | HumanEval (código) | GSM8K (matemáticas) | Velocidad |
|--------|----------------------------|---------------------|----------------------|-----------|
| GPT-4o | ~88% | ~90% | ~95% | ⚡⚡⚡ |
| Claude 3.5 Sonnet | ~89% | ~92% | ~96% | ⚡⚡⚡ |
| Gemini 1.5 Pro | ~85% | ~84% | ~91% | ⚡⚡ |
| Llama 3.1 70B | ~83% | ~80% | ~93% | ⚡⚡ (local) |
| Mistral Large | ~81% | ~81% | ~91% | ⚡⚡⚡ |
| GPT-4o mini | ~82% | ~87% | ~93% | ⚡⚡⚡⚡ |

> **Fuentes**: LMSYS Chatbot Arena, OpenLLM Leaderboard (Hugging Face). Los benchmarks cambian frecuentemente con nuevas versiones.

---

## 3. Comparación por caso de uso

### 💬 Para chatbots y conversación

| Modelo | Calidad | Precio | Velocidad | Idioma español |
|--------|---------|--------|-----------|----------------|
| GPT-4o | ⭐⭐⭐⭐⭐ | 💰💰 | ⚡⚡⚡ | ✅ Excelente |
| Claude 3.5 Sonnet | ⭐⭐⭐⭐⭐ | 💰💰💰 | ⚡⚡⚡ | ✅ Muy bueno |
| Gemini 1.5 Flash | ⭐⭐⭐⭐ | 💰 (gratis) | ⚡⚡⚡⚡ | ✅ Bueno |
| Llama 3.1 8B (local) | ⭐⭐⭐ | 🆓 | ⚡⚡ | ⚠️ Aceptable |

### 🖼️ Para generación de imágenes

| Modelo | Calidad | Precio | Facilidad | Estilo |
|--------|---------|--------|-----------|--------|
| DALL-E 3 (OpenAI) | ⭐⭐⭐⭐⭐ | 💰💰 | ✅ Muy fácil | Realista y artístico |
| Stable Diffusion XL | ⭐⭐⭐⭐ | 🆓 (local) | ⚠️ Técnico | Muy personalizable |
| Midjourney v6 | ⭐⭐⭐⭐⭐ | 💰💰 | ✅ Discord | Artístico, stunning |
| Flux (Black Forest Labs) | ⭐⭐⭐⭐⭐ | 💰 / 🆓 | ✅ API | Ultra-realista |
| Imagen 3 (Google) | ⭐⭐⭐⭐ | 💰 | ✅ API | Fotorrealista |

### 📝 Para resúmenes y análisis de documentos

| Modelo | Documentos largos | Precisión | Precio |
|--------|------------------|-----------|--------|
| Claude 3.5 Sonnet | ⭐⭐⭐⭐⭐ (200K ctx) | ⭐⭐⭐⭐⭐ | 💰💰💰 |
| Gemini 1.5 Pro | ⭐⭐⭐⭐⭐ (1M ctx) | ⭐⭐⭐⭐ | 💰💰 |
| GPT-4o | ⭐⭐⭐⭐ (128K ctx) | ⭐⭐⭐⭐⭐ | 💰💰 |
| Llama 3.1 70B | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | 🆓 |

### 🎙️ Para texto a voz (podcasts y audio)

| Servicio | Calidad voz | Idiomas | Precio | Clonación de voz |
|----------|-------------|---------|--------|-----------------|
| ElevenLabs | ⭐⭐⭐⭐⭐ | 29+ | 💰💰 | ✅ Sí |
| OpenAI TTS | ⭐⭐⭐⭐⭐ | Multi | 💰 | ❌ No |
| Google TTS (Cloud) | ⭐⭐⭐⭐ | 220+ | 💰 | ❌ No |
| Azure TTS | ⭐⭐⭐⭐ | 140+ | 💰 | ✅ Sí |
| Bark (open source) | ⭐⭐⭐ | Multi | 🆓 | ✅ Sí |
| Coqui TTS (local) | ⭐⭐⭐ | Multi | 🆓 | ✅ Sí |

---

## 4. Comparación de precios

> Precios aproximados a principios de 2025. Los precios se miden en **dólares por millón de tokens**.

| Modelo | Precio entrada | Precio salida | Gratis |
|--------|---------------|---------------|--------|
| GPT-4o | $2.50 / M | $10.00 / M | ❌ |
| GPT-4o mini | $0.15 / M | $0.60 / M | ❌ |
| Claude 3.5 Sonnet | $3.00 / M | $15.00 / M | ❌ |
| Claude 3 Haiku | $0.25 / M | $1.25 / M | ❌ |
| Gemini 1.5 Pro | $1.25 / M | $5.00 / M | ✅ (límites) |
| Gemini 1.5 Flash | $0.075 / M | $0.30 / M | ✅ (límites) |
| Llama 3.1 70B (Groq) | $0.59 / M | $0.79 / M | ✅ (límites) |
| Mistral Large | $2.00 / M | $6.00 / M | ❌ |
| DeepSeek R1 | $0.55 / M | $2.19 / M | ❌ |

### Estimación de coste para un chatbot básico

Suponiendo **100 usuarios/día** con **10 mensajes/usuario** de ~200 tokens cada uno:

```
100 usuarios × 10 mensajes × 200 tokens = 200.000 tokens/día

GPT-4o mini:    ~$0.03/día    → ~$1/mes
Gemini Flash:   ~$0.015/día   → ~$0.45/mes
Groq (Llama):   Gratis hasta límite → $0/mes con uso moderado
```

---

## 5. Cómo elegir el LLM adecuado

```
¿Tienes restricciones de presupuesto?
├── Sí → Gemini Flash (gratis) o Llama en Groq (gratis)
└── No → ¿Qué necesitas?
    ├── Máxima calidad conversacional → Claude 3.5 Sonnet o GPT-4o
    ├── Documentos muy largos → Gemini 1.5 Pro (contexto de 1M)
    ├── Código y programación → Claude 3.5 Sonnet o GPT-4o
    ├── Privacidad total → Llama 3.1 local con Ollama
    └── Velocidad extrema → Groq + Llama 3.1 8B
```

### Recomendaciones rápidas

- **Empezando a aprender**: Gemini Flash (gratis) o OpenAI con $5 de crédito
- **App de producción pequeña**: GPT-4o mini (barato y capaz)
- **App empresarial**: Claude 3.5 Sonnet o GPT-4o
- **Privacidad crítica (datos sensibles)**: Ollama + Llama 3.1 local
- **Generación de imágenes**: DALL-E 3 (integrado con OpenAI) o Stable Diffusion (gratis/local)
- **Podcasts y audio**: OpenAI TTS (calidad/precio) o ElevenLabs (máxima calidad)

---

## 6. Implementar un Chatbot

### 6.1 Chatbot con OpenAI (GPT-4o)

#### Instalación

```bash
pip install openai
```

#### Chatbot básico en Python

```python
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")  # Mejor: os.environ["OPENAI_API_KEY"]

def chat(mensaje_usuario, historial=[]):
    historial.append({
        "role": "user",
        "content": mensaje_usuario
    })
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",  # Económico y capaz
        messages=[
            {"role": "system", "content": "Eres un asistente útil que responde en español."},
            *historial
        ]
    )
    
    respuesta_texto = respuesta.choices[0].message.content
    historial.append({
        "role": "assistant",
        "content": respuesta_texto
    })
    
    return respuesta_texto, historial

# Uso
historial = []
while True:
    pregunta = input("Tú: ")
    if pregunta.lower() == "salir":
        break
    respuesta, historial = chat(pregunta, historial)
    print(f"Bot: {respuesta}\n")
```

#### Chatbot con streaming (respuesta en tiempo real)

```python
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")

def chat_streaming(mensaje):
    stream = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Eres un asistente útil."},
            {"role": "user", "content": mensaje}
        ],
        stream=True  # Activa el streaming
    )
    
    respuesta_completa = ""
    for chunk in stream:
        if chunk.choices[0].delta.content:
            texto = chunk.choices[0].delta.content
            print(texto, end="", flush=True)  # Imprime carácter a carácter
            respuesta_completa += texto
    
    print()  # Nueva línea al terminar
    return respuesta_completa

chat_streaming("Explícame qué es la inteligencia artificial en 3 párrafos")
```

### 6.2 Chatbot con Anthropic (Claude)

```bash
pip install anthropic
```

```python
import anthropic

client = anthropic.Anthropic(api_key="TU_API_KEY")

def chat_claude(mensaje, historial=[]):
    historial.append({
        "role": "user",
        "content": mensaje
    })
    
    respuesta = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        system="Eres un asistente útil que responde en español.",
        messages=historial
    )
    
    respuesta_texto = respuesta.content[0].text
    historial.append({
        "role": "assistant",
        "content": respuesta_texto
    })
    
    return respuesta_texto, historial
```

### 6.3 Chatbot con Google Gemini

```bash
pip install google-generativeai
```

```python
import google.generativeai as genai

genai.configure(api_key="TU_API_KEY")

model = genai.GenerativeModel(
    model_name="gemini-1.5-flash",
    system_instruction="Eres un asistente útil que responde en español."
)

chat = model.start_chat(history=[])

def chat_gemini(mensaje):
    respuesta = chat.send_message(mensaje)
    return respuesta.text

# Gemini Flash es GRATIS hasta cierto límite
print(chat_gemini("¿Qué es machine learning?"))
```

### 6.4 Chatbot API unificada con LiteLLM

LiteLLM te permite cambiar entre modelos con la misma interfaz:

```bash
pip install litellm
```

```python
from litellm import completion

def chat_universal(mensaje, modelo="gpt-4o-mini"):
    """
    Modelos disponibles:
    - "gpt-4o-mini" (OpenAI)
    - "claude-3-haiku-20240307" (Anthropic)
    - "gemini/gemini-1.5-flash" (Google)
    - "groq/llama-3.1-8b-instant" (Groq - gratuito)
    """
    respuesta = completion(
        model=modelo,
        messages=[
            {"role": "system", "content": "Eres un asistente útil en español."},
            {"role": "user", "content": mensaje}
        ]
    )
    return respuesta.choices[0].message.content

# Cambiar de modelo sin reescribir código
print(chat_universal("Hola, ¿cómo estás?", modelo="groq/llama-3.1-8b-instant"))
```

### 6.5 Chatbot con interfaz web (FastAPI + HTML)

```bash
pip install fastapi uvicorn openai python-dotenv
```

**`app.py`**
```python
from fastapi import FastAPI
from fastapi.responses import HTMLResponse, StreamingResponse
from pydantic import BaseModel
from openai import OpenAI
import os

app = FastAPI()
client = OpenAI(api_key=os.environ.get("OPENAI_API_KEY"))

class Mensaje(BaseModel):
    texto: str
    historial: list = []

@app.get("/", response_class=HTMLResponse)
async def inicio():
    with open("index.html") as f:
        return f.read()

@app.post("/chat")
async def chat(msg: Mensaje):
    historial = msg.historial + [{"role": "user", "content": msg.texto}]
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Eres un asistente útil en español."},
            *historial
        ]
    )
    
    respuesta_texto = respuesta.choices[0].message.content
    historial.append({"role": "assistant", "content": respuesta_texto})
    
    return {"respuesta": respuesta_texto, "historial": historial}
```

**`index.html`**
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Mi Chatbot</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 600px; margin: 50px auto; }
        #chat { border: 1px solid #ccc; height: 400px; overflow-y: auto; padding: 10px; }
        .usuario { color: blue; margin: 5px 0; }
        .bot { color: green; margin: 5px 0; }
        input { width: 80%; padding: 8px; }
        button { padding: 8px 15px; }
    </style>
</head>
<body>
    <h1>🤖 Mi Chatbot</h1>
    <div id="chat"></div>
    <br>
    <input type="text" id="input" placeholder="Escribe tu mensaje..." onkeypress="enviarConEnter(event)">
    <button onclick="enviar()">Enviar</button>

    <script>
        let historial = [];

        async function enviar() {
            const input = document.getElementById('input');
            const texto = input.value.trim();
            if (!texto) return;

            agregarMensaje('Tú', texto, 'usuario');
            input.value = '';

            const respuesta = await fetch('/chat', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ texto, historial })
            });

            const datos = await respuesta.json();
            historial = datos.historial;
            agregarMensaje('Bot', datos.respuesta, 'bot');
        }

        function agregarMensaje(autor, texto, clase) {
            const chat = document.getElementById('chat');
            chat.innerHTML += `<p class="${clase}"><b>${autor}:</b> ${texto}</p>`;
            chat.scrollTop = chat.scrollHeight;
        }

        function enviarConEnter(e) {
            if (e.key === 'Enter') enviar();
        }
    </script>
</body>
</html>
```

```bash
# Ejecutar
uvicorn app:app --reload
# Abrir http://localhost:8000
```

---

## 7. Implementar Generación de Imágenes

### 7.1 Con DALL-E 3 (OpenAI)

```python
from openai import OpenAI
import requests
from pathlib import Path

client = OpenAI(api_key="TU_API_KEY")

def generar_imagen(prompt, calidad="standard", tamaño="1024x1024"):
    """
    calidad: "standard" (barato) o "hd" (alta calidad)
    tamaño: "1024x1024", "1792x1024" (horizontal), "1024x1792" (vertical)
    """
    respuesta = client.images.generate(
        model="dall-e-3",
        prompt=prompt,
        size=tamaño,
        quality=calidad,
        n=1
    )
    
    url_imagen = respuesta.data[0].url
    prompt_revisado = respuesta.data[0].revised_prompt  # DALL-E mejora el prompt
    
    print(f"Prompt mejorado: {prompt_revisado}")
    print(f"URL de imagen: {url_imagen}")
    
    return url_imagen

# Ejemplo de uso
url = generar_imagen(
    "Un gato astronauta flotando en el espacio con la Tierra de fondo, estilo fotorrealista"
)
```

#### Descargar y guardar la imagen

```python
def guardar_imagen(url, nombre_archivo="imagen.png"):
    respuesta = requests.get(url)
    with open(nombre_archivo, "wb") as f:
        f.write(respuesta.content)
    print(f"Imagen guardada como {nombre_archivo}")

guardar_imagen(url, "mi_imagen.png")
```

### 7.2 Con Stable Diffusion (local y gratis)

#### Opción A: Automatic1111 WebUI (más popular)

```bash
# Requiere Python 3.10 y GPU NVIDIA (o CPU más lento)
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
./webui.sh  # Linux/Mac
# o webui-user.bat en Windows
```

Abre http://localhost:7860 y tendrás una interfaz gráfica completa.

#### Opción B: API de Stable Diffusion con diffusers (Python)

```bash
pip install diffusers transformers accelerate torch
```

```python
from diffusers import StableDiffusionPipeline
import torch

# Descarga el modelo (puede tardar la primera vez, ~4GB)
pipe = StableDiffusionPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    torch_dtype=torch.float16  # Usa menos memoria GPU
)
pipe = pipe.to("cuda")  # Usa GPU. Cambia a "cpu" si no tienes GPU (más lento)

def generar_imagen_local(prompt, pasos=20):
    imagen = pipe(
        prompt,
        num_inference_steps=pasos,  # Más pasos = más calidad pero más lento
        guidance_scale=7.5  # Qué tanto seguir el prompt (7-10 es bueno)
    ).images[0]
    
    imagen.save("imagen_local.png")
    return imagen

imagen = generar_imagen_local("a beautiful sunset over mountains, photorealistic, 4k")
```

#### Opción C: API de Stability AI (en la nube, sin necesitar GPU)

```bash
pip install stability-sdk
```

```python
import requests
import base64

def generar_imagen_stability(prompt):
    url = "https://api.stability.ai/v1/generation/stable-diffusion-xl-1024-v1-0/text-to-image"
    
    headers = {
        "Authorization": f"Bearer TU_API_KEY",
        "Content-Type": "application/json"
    }
    
    datos = {
        "text_prompts": [{"text": prompt, "weight": 1}],
        "cfg_scale": 7,
        "height": 1024,
        "width": 1024,
        "steps": 30,
        "samples": 1
    }
    
    respuesta = requests.post(url, headers=headers, json=datos)
    datos_respuesta = respuesta.json()
    
    imagen_b64 = datos_respuesta["artifacts"][0]["base64"]
    imagen_bytes = base64.b64decode(imagen_b64)
    
    with open("imagen_stability.png", "wb") as f:
        f.write(imagen_bytes)
    
    return imagen_bytes
```

### 7.3 Con Flux (mejor calidad gratuita, vía Replicate)

```bash
pip install replicate
```

```python
import replicate

output = replicate.run(
    "black-forest-labs/flux-schnell",  # Versión rápida y gratis
    input={
        "prompt": "A majestic dragon flying over a medieval city at sunset",
        "num_outputs": 1,
        "aspect_ratio": "16:9"
    }
)

# output[0] es la URL de la imagen generada
print(output[0])
```

### 7.4 Generador de imágenes con FastAPI

```python
from fastapi import FastAPI
from fastapi.responses import FileResponse
from pydantic import BaseModel
from openai import OpenAI
import requests
import uuid
import os

app = FastAPI()
client = OpenAI(api_key=os.environ.get("OPENAI_API_KEY"))

class PromptImagen(BaseModel):
    descripcion: str

@app.post("/generar-imagen")
async def generar_imagen_endpoint(prompt: PromptImagen):
    # Generar imagen con DALL-E 3
    respuesta = client.images.generate(
        model="dall-e-3",
        prompt=prompt.descripcion,
        size="1024x1024",
        quality="standard",
        n=1
    )
    
    url_imagen = respuesta.data[0].url
    
    # Descargar y guardar localmente
    nombre = f"imagen_{uuid.uuid4().hex[:8]}.png"
    ruta = f"imagenes/{nombre}"
    os.makedirs("imagenes", exist_ok=True)
    
    img_respuesta = requests.get(url_imagen)
    with open(ruta, "wb") as f:
        f.write(img_respuesta.content)
    
    return {
        "url_original": url_imagen,
        "archivo_local": nombre,
        "prompt_mejorado": respuesta.data[0].revised_prompt
    }

@app.get("/imagenes/{nombre}")
async def obtener_imagen(nombre: str):
    return FileResponse(f"imagenes/{nombre}")
```

---

## 8. Implementar Resúmenes de Texto

### 8.1 Resumen básico con OpenAI

```python
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")

def resumir_texto(texto, longitud="medio"):
    instrucciones = {
        "corto": "Resume el siguiente texto en 2-3 oraciones.",
        "medio": "Resume el siguiente texto en un párrafo de 5-8 oraciones.",
        "largo": "Haz un resumen detallado del siguiente texto con los puntos principales."
    }
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",  # Económico para resúmenes
        messages=[
            {
                "role": "system",
                "content": "Eres un experto en resumir textos de forma clara y concisa en español."
            },
            {
                "role": "user",
                "content": f"{instrucciones[longitud]}\n\nTEXTO:\n{texto}"
            }
        ]
    )
    
    return respuesta.choices[0].message.content

# Ejemplo
texto_largo = """
La inteligencia artificial (IA) es la simulación de procesos de inteligencia humana
por parte de sistemas informáticos. Estos procesos incluyen el aprendizaje, el razonamiento
y la autocorrección. Las aplicaciones específicas de la IA incluyen sistemas expertos,
procesamiento de lenguaje natural, reconocimiento de voz y visión por computadora.
La IA está siendo aplicada actualmente en industrias que van desde la banca hasta la atención médica.
"""

print(resumir_texto(texto_largo, "corto"))
```

### 8.2 Resumir documentos PDF

```bash
pip install openai PyPDF2
```

```python
import PyPDF2
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")

def extraer_texto_pdf(ruta_pdf):
    texto = ""
    with open(ruta_pdf, "rb") as f:
        lector = PyPDF2.PdfReader(f)
        for pagina in lector.pages:
            texto += pagina.extract_text() + "\n"
    return texto

def resumir_pdf(ruta_pdf):
    print(f"Extrayendo texto de {ruta_pdf}...")
    texto = extraer_texto_pdf(ruta_pdf)
    
    # Si el texto es muy largo, dividirlo en chunks
    max_chars = 100000  # ~25000 tokens
    if len(texto) > max_chars:
        print(f"Texto largo ({len(texto)} chars), resumiendo por partes...")
        chunks = [texto[i:i+max_chars] for i in range(0, len(texto), max_chars)]
        resumenes_parciales = []
        
        for i, chunk in enumerate(chunks):
            print(f"Procesando parte {i+1}/{len(chunks)}...")
            resumen_parcial = resumir_texto(chunk, "medio")
            resumenes_parciales.append(resumen_parcial)
        
        # Resumir los resúmenes parciales
        texto_final = "\n\n".join(resumenes_parciales)
        return resumir_texto(texto_final, "largo")
    else:
        return resumir_texto(texto, "largo")

# resumen = resumir_pdf("mi_documento.pdf")
# print(resumen)
```

### 8.3 Resumir URLs y páginas web

```bash
pip install openai requests beautifulsoup4
```

```python
import requests
from bs4 import BeautifulSoup
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")

def extraer_texto_web(url):
    headers = {"User-Agent": "Mozilla/5.0"}
    respuesta = requests.get(url, headers=headers, timeout=10)
    soup = BeautifulSoup(respuesta.text, "html.parser")
    
    # Eliminar scripts, estilos y navegación
    for elemento in soup(["script", "style", "nav", "footer", "header"]):
        elemento.decompose()
    
    texto = soup.get_text(separator="\n", strip=True)
    return texto[:50000]  # Limitar tamaño

def resumir_url(url):
    texto = extraer_texto_web(url)
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {
                "role": "system",
                "content": "Eres un asistente que resume artículos web de forma clara. Incluye los puntos principales y conclusiones."
            },
            {
                "role": "user",
                "content": f"Resume este artículo web:\nURL: {url}\n\nCONTENIDO:\n{texto}"
            }
        ]
    )
    
    return respuesta.choices[0].message.content

# resumen = resumir_url("https://es.wikipedia.org/wiki/Inteligencia_artificial")
# print(resumen)
```

### 8.4 Resumir con Claude (mejor para documentos largos)

```python
import anthropic

client = anthropic.Anthropic(api_key="TU_API_KEY")

def resumir_con_claude(texto, tipo="general"):
    prompts = {
        "general": "Crea un resumen claro y estructurado del siguiente texto.",
        "ejecutivo": "Crea un resumen ejecutivo del siguiente texto con: objetivo, puntos clave y conclusiones.",
        "bullet_points": "Resume el siguiente texto en 5-7 puntos clave con viñetas."
    }
    
    respuesta = client.messages.create(
        model="claude-3-haiku-20240307",  # Haiku: rápido y barato para resúmenes
        max_tokens=1024,
        messages=[{
            "role": "user",
            "content": f"{prompts[tipo]}\n\n{texto}"
        }]
    )
    
    return respuesta.content[0].text
```

---

## 9. Implementar Generación de Podcasts y Audio

### 9.1 Texto a voz con OpenAI TTS

```bash
pip install openai
```

```python
from openai import OpenAI
from pathlib import Path

client = OpenAI(api_key="TU_API_KEY")

def texto_a_voz(texto, voz="nova", archivo="audio.mp3"):
    """
    Voces disponibles: alloy, echo, fable, onyx, nova, shimmer
    - nova: voz femenina natural (recomendada)
    - onyx: voz masculina profunda
    - echo: voz masculina media
    - alloy: neutral
    """
    respuesta = client.audio.speech.create(
        model="tts-1",       # "tts-1" (rápido) o "tts-1-hd" (alta calidad)
        voice=voz,
        input=texto
    )
    
    respuesta.stream_to_file(archivo)
    print(f"Audio guardado en: {archivo}")
    return archivo

# Ejemplo
texto = "Bienvenidos a nuestro podcast de tecnología. Hoy hablaremos sobre inteligencia artificial."
texto_a_voz(texto, voz="nova", archivo="intro_podcast.mp3")
```

### 9.2 Generar un podcast completo desde un tema

```python
from openai import OpenAI
from pathlib import Path
import os

client = OpenAI(api_key="TU_API_KEY")

def generar_guion_podcast(tema, duracion_minutos=5):
    """Genera el guión del podcast usando GPT"""
    palabras_estimadas = duracion_minutos * 130  # ~130 palabras/min hablando
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {
                "role": "system",
                "content": """Eres un guionista de podcasts. Crea guiones naturales y conversacionales.
                El guión debe sonar como una conversación real, no una lectura de texto.
                Usa transiciones naturales como 'por cierto', 'lo interesante es', 'hablando de eso'."""
            },
            {
                "role": "user",
                "content": f"""Crea un guión de podcast sobre: {tema}
                
                Duración aproximada: {duracion_minutos} minutos ({palabras_estimadas} palabras)
                
                Estructura:
                1. Introducción atractiva (gancho)
                2. Desarrollo del tema con 3-4 puntos principales
                3. Ejemplos o casos prácticos
                4. Conclusión y llamada a la acción
                
                El guión debe estar listo para ser leído en voz alta."""
            }
        ]
    )
    
    return respuesta.choices[0].message.content

def podcast_completo(tema, duracion=5, voz="nova"):
    """Pipeline completo: tema → guión → audio"""
    print(f"🎙️ Generando podcast sobre: {tema}")
    
    # Paso 1: Generar guión
    print("📝 Creando guión...")
    guion = generar_guion_podcast(tema, duracion)
    
    # Guardar guión
    with open("guion_podcast.txt", "w", encoding="utf-8") as f:
        f.write(guion)
    print("Guión guardado en guion_podcast.txt")
    
    # Paso 2: Convertir a audio
    print("🔊 Generando audio...")
    archivo_audio = "podcast.mp3"
    
    respuesta = client.audio.speech.create(
        model="tts-1-hd",  # Alta calidad para podcasts
        voice=voz,
        input=guion
    )
    respuesta.stream_to_file(archivo_audio)
    
    print(f"✅ Podcast generado: {archivo_audio}")
    return guion, archivo_audio

# Ejemplo de uso
guion, audio = podcast_completo(
    tema="Los avances de la inteligencia artificial en 2025",
    duracion=3,
    voz="nova"
)
```

### 9.3 Podcast con múltiples voces (conversación)

```python
from openai import OpenAI
from pydub import AudioSegment
import json
import tempfile
import os

client = OpenAI(api_key="TU_API_KEY")

def generar_dialogo_podcast(tema):
    """Genera un diálogo entre dos presentadores"""
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {
                "role": "user",
                "content": f"""Crea un diálogo de podcast entre dos presentadores (Ana y Carlos) sobre: {tema}
                
                Formato JSON estricto:
                [
                  {{"presentador": "Ana", "texto": "..."}},
                  {{"presentador": "Carlos", "texto": "..."}},
                  ...
                ]
                
                El diálogo debe tener 8-10 intercambios. Solo responde con el JSON, sin explicaciones."""
            }
        ]
    )
    
    contenido = respuesta.choices[0].message.content
    # Limpiar posibles bloques de código markdown
    contenido = contenido.replace("```json", "").replace("```", "").strip()
    return json.loads(contenido)

def podcast_dos_voces(tema):
    """Genera podcast con dos voces diferentes"""
    voces = {
        "Ana": "nova",    # Voz femenina
        "Carlos": "echo"  # Voz masculina
    }
    
    print("📝 Generando diálogo...")
    dialogo = generar_dialogo_podcast(tema)
    
    segmentos_audio = []
    
    for i, turno in enumerate(dialogo):
        presentador = turno["presentador"]
        texto = turno["texto"]
        voz = voces.get(presentador, "alloy")
        
        print(f"🎙️ Generando voz de {presentador}: {texto[:50]}...")
        
        # Generar audio para este turno
        archivo_temp = f"temp_segment_{i}.mp3"
        respuesta = client.audio.speech.create(
            model="tts-1",
            voice=voz,
            input=texto
        )
        respuesta.stream_to_file(archivo_temp)
        segmentos_audio.append(archivo_temp)
    
    # Combinar todos los segmentos
    print("🔗 Combinando segmentos...")
    podcast_final = AudioSegment.empty()
    silencio = AudioSegment.silent(duration=500)  # 0.5 segundos entre turnos
    
    for archivo in segmentos_audio:
        segmento = AudioSegment.from_mp3(archivo)
        podcast_final += segmento + silencio
        os.remove(archivo)  # Limpiar temporales
    
    podcast_final.export("podcast_dos_voces.mp3", format="mp3")
    print("✅ Podcast guardado: podcast_dos_voces.mp3")

# pip install pydub
# podcast_dos_voces("Los beneficios del ejercicio físico")
```

### 9.4 Transcripción de audio con Whisper (audio → texto)

```python
from openai import OpenAI

client = OpenAI(api_key="TU_API_KEY")

def transcribir_audio(ruta_audio, idioma="es"):
    """
    Transcribe audio a texto usando Whisper
    Formatos soportados: mp3, mp4, wav, m4a, webm, flac
    """
    with open(ruta_audio, "rb") as archivo_audio:
        transcripcion = client.audio.transcriptions.create(
            model="whisper-1",
            file=archivo_audio,
            language=idioma,  # "es" para español, None para autodetectar
            response_format="text"  # o "json", "srt", "vtt"
        )
    
    return transcripcion

def transcribir_con_timestamps(ruta_audio):
    """Transcripción con marcas de tiempo para subtítulos"""
    with open(ruta_audio, "rb") as archivo_audio:
        transcripcion = client.audio.transcriptions.create(
            model="whisper-1",
            file=archivo_audio,
            response_format="verbose_json",
            timestamp_granularities=["word"]
        )
    
    return transcripcion

# Ejemplo
# texto = transcribir_audio("mi_audio.mp3")
# print(texto)
```

### 9.5 ElevenLabs (mejor calidad de voz, con clonación)

```bash
pip install elevenlabs
```

```python
from elevenlabs.client import ElevenLabs
from elevenlabs import save

client = ElevenLabs(api_key="TU_API_KEY")

def texto_a_voz_elevenlabs(texto, voz_id="Rachel", archivo="audio_elevenlabs.mp3"):
    """
    Voces preinstaladas populares:
    - "Rachel" - voz femenina americana, clara
    - "Antoni" - voz masculina joven
    - "Bella" - voz femenina joven
    - "Josh" - voz masculina profunda
    """
    audio = client.generate(
        text=texto,
        voice=voz_id,
        model="eleven_multilingual_v2"  # Soporta español
    )
    
    save(audio, archivo)
    print(f"Audio guardado: {archivo}")

def clonar_voz(nombre, archivos_audio, descripcion=""):
    """Clona una voz a partir de muestras de audio (mínimo 1 minuto)"""
    voice = client.clone(
        name=nombre,
        files=archivos_audio,
        description=descripcion
    )
    print(f"Voz clonada. ID: {voice.voice_id}")
    return voice.voice_id

# texto_a_voz_elevenlabs("Hola, soy tu asistente virtual.", "Rachel")
```

---

## 10. Proyecto completo: App multimodal

Una aplicación que combina chatbot + generación de imágenes + resúmenes + audio.

### Estructura del proyecto

```
mi-app-ia/
├── app.py              # Servidor FastAPI principal
├── .env                # Claves API (no subir a git)
├── requirements.txt
└── static/
    └── index.html      # Frontend
```

### `requirements.txt`

```
fastapi
uvicorn
openai
python-dotenv
requests
```

### `app.py`

```python
from fastapi import FastAPI, UploadFile, File
from fastapi.staticfiles import StaticFiles
from fastapi.responses import FileResponse
from pydantic import BaseModel
from openai import OpenAI
import os
import uuid
import requests
from dotenv import load_dotenv

load_dotenv()

app = FastAPI(title="App Multimodal de IA")
client = OpenAI(api_key=os.environ.get("OPENAI_API_KEY"))

os.makedirs("static", exist_ok=True)
os.makedirs("outputs", exist_ok=True)

app.mount("/outputs", StaticFiles(directory="outputs"), name="outputs")

# ── Modelos de datos ──────────────────────────────────────────────────────────

class MensajeChat(BaseModel):
    mensaje: str
    historial: list = []

class SolicitudImagen(BaseModel):
    descripcion: str

class SolicitudResumen(BaseModel):
    texto: str
    tipo: str = "general"  # general, ejecutivo, bullet_points

class SolicitudAudio(BaseModel):
    texto: str
    voz: str = "nova"

# ── Endpoints ─────────────────────────────────────────────────────────────────

@app.get("/")
async def inicio():
    return FileResponse("static/index.html")

@app.post("/chat")
async def chat(msg: MensajeChat):
    historial = msg.historial + [{"role": "user", "content": msg.mensaje}]
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Eres un asistente útil en español."},
            *historial
        ]
    )
    
    respuesta_texto = respuesta.choices[0].message.content
    historial.append({"role": "assistant", "content": respuesta_texto})
    return {"respuesta": respuesta_texto, "historial": historial}

@app.post("/generar-imagen")
async def generar_imagen(solicitud: SolicitudImagen):
    respuesta = client.images.generate(
        model="dall-e-3",
        prompt=solicitud.descripcion,
        size="1024x1024",
        quality="standard",
        n=1
    )
    
    url_imagen = respuesta.data[0].url
    nombre = f"imagen_{uuid.uuid4().hex[:8]}.png"
    ruta = f"outputs/{nombre}"
    
    img_bytes = requests.get(url_imagen).content
    with open(ruta, "wb") as f:
        f.write(img_bytes)
    
    return {
        "url": f"/outputs/{nombre}",
        "prompt_mejorado": respuesta.data[0].revised_prompt
    }

@app.post("/resumir")
async def resumir(solicitud: SolicitudResumen):
    prompts = {
        "general": "Resume el siguiente texto de forma clara:",
        "ejecutivo": "Crea un resumen ejecutivo con puntos clave:",
        "bullet_points": "Resume en 5 puntos clave con viñetas:"
    }
    
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "Eres un experto en resumir textos en español."},
            {"role": "user", "content": f"{prompts.get(solicitud.tipo, prompts['general'])}\n\n{solicitud.texto}"}
        ]
    )
    
    return {"resumen": respuesta.choices[0].message.content}

@app.post("/generar-audio")
async def generar_audio(solicitud: SolicitudAudio):
    nombre = f"audio_{uuid.uuid4().hex[:8]}.mp3"
    ruta = f"outputs/{nombre}"
    
    respuesta = client.audio.speech.create(
        model="tts-1",
        voice=solicitud.voz,
        input=solicitud.texto
    )
    respuesta.stream_to_file(ruta)
    
    return {"url": f"/outputs/{nombre}"}

@app.post("/transcribir")
async def transcribir(archivo: UploadFile = File(...)):
    contenido = await archivo.read()
    nombre_temp = f"/tmp/{uuid.uuid4().hex}.mp3"
    
    with open(nombre_temp, "wb") as f:
        f.write(contenido)
    
    with open(nombre_temp, "rb") as f:
        transcripcion = client.audio.transcriptions.create(
            model="whisper-1",
            file=f,
            language="es"
        )
    
    os.remove(nombre_temp)
    return {"transcripcion": transcripcion.text}
```

### Ejecutar la app

```bash
# Crear .env con tus claves
echo "OPENAI_API_KEY=sk-..." > .env

# Instalar dependencias
pip install -r requirements.txt

# Ejecutar
uvicorn app:app --reload

# Abrir http://localhost:8000
```

---

## 11. Buenas prácticas y consejos

### 🔑 Gestión segura de API keys

```python
# ❌ MAL: Nunca hagas esto
client = OpenAI(api_key="sk-abc123...")

# ✅ BIEN: Usa variables de entorno
import os
from dotenv import load_dotenv
load_dotenv()
client = OpenAI(api_key=os.environ.get("OPENAI_API_KEY"))
```

Archivo `.env`:
```
OPENAI_API_KEY=sk-tu-clave-aqui
ANTHROPIC_API_KEY=sk-ant-tu-clave
GOOGLE_API_KEY=tu-clave-google
```

Agregar al `.gitignore`:
```
.env
*.mp3
*.png
outputs/
```

### 💰 Controlar costes

```python
# Verificar tokens usados en cada llamada
respuesta = client.chat.completions.create(...)
print(f"Tokens usados: {respuesta.usage.total_tokens}")
print(f"Coste estimado: ${respuesta.usage.total_tokens * 0.00000015:.6f}")

# Limitar longitud de respuestas
respuesta = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[...],
    max_tokens=500  # Limita la respuesta a 500 tokens
)
```

### ⚡ Mejorar rendimiento con caché

```python
import hashlib
import json
import os

cache = {}

def chat_con_cache(mensaje, modelo="gpt-4o-mini"):
    clave = hashlib.md5(f"{modelo}:{mensaje}".encode()).hexdigest()
    
    if clave in cache:
        print("(respuesta desde caché)")
        return cache[clave]
    
    respuesta = client.chat.completions.create(
        model=modelo,
        messages=[{"role": "user", "content": mensaje}]
    )
    
    resultado = respuesta.choices[0].message.content
    cache[clave] = resultado
    return resultado
```

### 🛡️ Manejo de errores

```python
from openai import RateLimitError, APIError
import time

def chat_robusto(mensaje, reintentos=3):
    for intento in range(reintentos):
        try:
            respuesta = client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[{"role": "user", "content": mensaje}]
            )
            return respuesta.choices[0].message.content
            
        except RateLimitError:
            if intento < reintentos - 1:
                espera = 2 ** intento  # Espera exponencial: 1s, 2s, 4s
                print(f"Límite de tasa alcanzado. Esperando {espera}s...")
                time.sleep(espera)
            else:
                raise
        except APIError as e:
            print(f"Error de API: {e}")
            raise

    return None
```

### 📝 Ingeniería de prompts efectiva

```python
# ❌ Prompt vago
chat("Habla sobre Python")

# ✅ Prompt específico
chat("""
Eres un profesor de programación para principiantes.
Explica qué es Python en exactamente 3 párrafos:
1. Qué es y para qué sirve
2. Sus características principales  
3. Un ejemplo simple de código

Usa un lenguaje sencillo, evita jerga técnica.
""")
```

### 🧪 Tabla resumen: ¿Qué usar para cada proyecto?

| Proyecto | Modelo recomendado | Coste estimado |
|----------|-------------------|----------------|
| Chatbot básico (hobby) | Gemini Flash (gratis) | $0 |
| Chatbot producción pequeña | GPT-4o mini | ~$5-20/mes |
| Chatbot empresarial | GPT-4o o Claude Sonnet | ~$50-200/mes |
| App con privacidad total | Ollama + Llama 3.1 local | $0 (hardware) |
| Generación de imágenes | DALL-E 3 o Stable Diffusion | $0.04/imagen o $0 |
| Resúmenes de documentos | Claude Haiku o GPT-4o mini | ~$1-10/mes |
| Podcasts/TTS | OpenAI TTS | $0.015/1000 chars |
| Transcripción de audio | Whisper | $0.006/minuto |

---

## Recursos adicionales

- **OpenAI Platform**: [platform.openai.com](https://platform.openai.com) — Documentación y playground
- **Anthropic Console**: [console.anthropic.com](https://console.anthropic.com) — Claude API
- **Google AI Studio**: [aistudio.google.com](https://aistudio.google.com) — Gemini gratis para empezar
- **Groq Cloud**: [console.groq.com](https://console.groq.com) — Llama gratis y muy rápido
- **Hugging Face**: [huggingface.co](https://huggingface.co) — Modelos open source
- **OpenLLM Leaderboard**: [huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard](https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard) — Comparativa actualizada
- **LMSYS Chatbot Arena**: [lmarena.ai](https://lmarena.ai) — Ranking por votación humana
- **LiteLLM**: [litellm.ai](https://litellm.ai) — API unificada para todos los modelos
