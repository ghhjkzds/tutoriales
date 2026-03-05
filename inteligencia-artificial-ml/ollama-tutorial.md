# 🦙 Tutorial Completo de Ollama

## Índice

1. [¿Qué es Ollama?](#1-qué-es-ollama)
2. [Instalación](#2-instalación)
3. [Cómo funciona Ollama](#3-cómo-funciona-ollama)
4. [Primeros pasos y comandos básicos](#4-primeros-pasos-y-comandos-básicos)
5. [Modelos disponibles](#5-modelos-disponibles)
6. [API REST de Ollama](#6-api-rest-de-ollama)
7. [Implementación en proyectos Python](#7-implementación-en-proyectos-python)
8. [Implementación en proyectos JavaScript/Node.js](#8-implementación-en-proyectos-javascriptnodejs)
9. [Personalización de modelos con Modelfile](#9-personalización-de-modelos-con-modelfile)
10. [Funciones avanzadas](#10-funciones-avanzadas)
11. [Integración con herramientas populares](#11-integración-con-herramientas-populares)
12. [Casos de uso prácticos](#12-casos-de-uso-prácticos)
13. [Buenas prácticas y optimización](#13-buenas-prácticas-y-optimización)
14. [Solución de problemas comunes](#14-solución-de-problemas-comunes)

---

## 1. ¿Qué es Ollama?

**Ollama** es una herramienta de código abierto que permite ejecutar modelos de lenguaje grande (LLMs) de forma local en tu propio equipo, sin necesidad de depender de servicios en la nube. Fue diseñada para simplificar al máximo la descarga, configuración y ejecución de modelos de inteligencia artificial.

### ¿Por qué usar Ollama?

| Ventaja | Descripción |
|---------|-------------|
| 🔒 **Privacidad** | Los datos nunca salen de tu máquina |
| 💰 **Sin costes** | No hay tarifas por uso de API |
| 🌐 **Sin internet** | Funciona completamente offline |
| ⚡ **Baja latencia** | Sin retrasos de red |
| 🛠️ **Personalización** | Crea tus propios modelos ajustados |

### Arquitectura general

```
┌────────────────────────────────────────┐
│              Tu Aplicación             │
│  (Python, JS, Web, CLI, etc.)          │
└───────────────┬────────────────────────┘
                │  HTTP REST API
                ▼
┌────────────────────────────────────────┐
│           Servidor Ollama              │
│       (localhost:11434)                │
│                                        │
│  ┌──────────┐    ┌──────────────────┐  │
│  │  Router  │───▶│  Motor de modelo │  │
│  └──────────┘    └──────────────────┘  │
└───────────────────────┬────────────────┘
                        │
                        ▼
┌────────────────────────────────────────┐
│           Modelos locales              │
│   ~/.ollama/models/  (llama3, etc.)    │
└────────────────────────────────────────┘
```

---

## 2. Instalación

### macOS

```bash
# Usando el instalador oficial
curl -fsSL https://ollama.com/install.sh | sh

# O descargando la app desde https://ollama.com
```

### Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### Windows

Descarga el instalador desde [https://ollama.com/download](https://ollama.com/download) y ejecuta el archivo `.exe`.

### Verificar la instalación

```bash
ollama --version
# ollama version 0.x.x
```

### Requisitos del sistema

| Componente | Mínimo | Recomendado |
|------------|--------|-------------|
| RAM | 8 GB | 16 GB o más |
| Disco | 10 GB libres | 50 GB o más |
| GPU (opcional) | — | NVIDIA con CUDA o Apple Silicon |
| OS | macOS 11+, Linux, Windows 10+ | Última versión estable |

> **Nota:** Ollama puede funcionar solo con CPU, pero una GPU acelera significativamente la inferencia.

---

## 3. Cómo funciona Ollama

### 3.1 El servidor de Ollama

Cuando ejecutas `ollama serve` (o simplemente Ollama en segundo plano), se levanta un servidor HTTP en `localhost:11434`. Este servidor gestiona:

- La carga y descarga de modelos en memoria (RAM/VRAM).
- Las solicitudes de inferencia (generación de texto).
- La gestión del ciclo de vida de los modelos.

```bash
# Iniciar el servidor manualmente
ollama serve
```

### 3.2 Formato de modelos (GGUF)

Ollama utiliza modelos en formato **GGUF** (GPT-Generated Unified Format), que permiten cuantización del modelo para reducir el uso de memoria con pérdida mínima de calidad:

| Cuantización | Descripción | Uso de RAM (7B) |
|--------------|-------------|-----------------|
| `q4_0` | 4 bits, muy compacto | ~4 GB |
| `q4_K_M` | 4 bits, equilibrado | ~4.5 GB |
| `q8_0` | 8 bits, mejor calidad | ~7 GB |
| `f16` | 16 bits (half precision) | ~14 GB |

### 3.3 Flujo de una solicitud

```
1. Cliente envía petición HTTP POST a /api/generate
        ↓
2. Ollama comprueba si el modelo está cargado en memoria
        ↓
3. Si no está cargado, lo carga desde disco (~/.ollama/models)
        ↓
4. El modelo procesa el prompt token a token
        ↓
5. La respuesta se devuelve como stream (JSON por líneas) o completa
```

---

## 4. Primeros pasos y comandos básicos

### 4.1 Descargar y ejecutar un modelo

```bash
# Descargar y ejecutar llama3.2 (3B parámetros)
ollama run llama3.2

# Descargar y ejecutar un modelo más potente
ollama run llama3.1:8b

# Ejecutar un modelo de código
ollama run codellama
```

Una vez iniciado, puedes chatear directamente en la terminal:

```
>>> Explícame qué es una función recursiva en Python
Una función recursiva es aquella que se llama a sí misma...
```

Para salir: escribe `/bye` o presiona `Ctrl+D`.

### 4.2 Comandos esenciales

```bash
# Listar modelos descargados
ollama list

# Ver información de un modelo
ollama show llama3.2

# Eliminar un modelo
ollama rm llama3.2

# Copiar un modelo
ollama cp llama3.2 mi-llama-personalizado

# Actualizar un modelo
ollama pull llama3.2

# Ver procesos en ejecución
ollama ps
```

### 4.3 Opciones de ejecución

```bash
# Cambiar el puerto del servidor
OLLAMA_HOST=0.0.0.0:8080 ollama serve

# Limitar el uso de GPU
OLLAMA_NUM_GPU=1 ollama serve

# Aumentar el número de hilos de CPU
OLLAMA_NUM_THREAD=8 ollama serve

# Configurar keep-alive (tiempo que el modelo permanece en memoria)
OLLAMA_KEEP_ALIVE=10m ollama serve
```

---

## 5. Modelos disponibles

Puedes buscar modelos en [https://ollama.com/library](https://ollama.com/library). Aquí los más populares:

### Modelos de propósito general

| Modelo | Tamaño | Descripción |
|--------|--------|-------------|
| `llama3.2` | 1B, 3B | Meta Llama 3.2, muy eficiente |
| `llama3.1` | 8B, 70B | Meta Llama 3.1, excelente balance |
| `llama3.2-vision` | 11B, 90B | Llama 3.2 con visión (imágenes) |
| `mistral` | 7B | Mistral AI, muy bueno en instrucciones |
| `gemma2` | 2B, 9B, 27B | Google Gemma 2 |
| `phi3` | 3.8B, 14B | Microsoft Phi-3 |
| `qwen2.5` | 0.5B–72B | Alibaba Qwen 2.5 |

### Modelos especializados

| Modelo | Especialidad |
|--------|-------------|
| `codellama` | Generación de código |
| `deepseek-coder` | Código, muy preciso |
| `nomic-embed-text` | Embeddings de texto |
| `mxbai-embed-large` | Embeddings de alta calidad |
| `llava` | Visión y análisis de imágenes |
| `bakllava` | Visión multimodal |

### Descargar modelos con tags específicos

```bash
# Descargar versión específica
ollama pull llama3.1:70b

# Descargar con cuantización específica
ollama pull llama3.1:8b-instruct-q4_K_M
```

---

## 6. API REST de Ollama

Ollama expone una API REST en `http://localhost:11434`. Esto permite integrarla desde cualquier lenguaje o herramienta.

### 6.1 Generar texto (POST /api/generate)

```bash
curl http://localhost:11434/api/generate \
  -d '{
    "model": "llama3.2",
    "prompt": "¿Qué es la programación orientada a objetos?",
    "stream": false
  }'
```

**Respuesta:**
```json
{
  "model": "llama3.2",
  "created_at": "2024-01-01T00:00:00Z",
  "response": "La programación orientada a objetos (POO) es...",
  "done": true,
  "total_duration": 5000000000,
  "load_duration": 100000000,
  "prompt_eval_count": 15,
  "eval_count": 120,
  "eval_duration": 4000000000
}
```

### 6.2 Chat con historial (POST /api/chat)

```bash
curl http://localhost:11434/api/chat \
  -d '{
    "model": "llama3.2",
    "messages": [
      {"role": "system", "content": "Eres un asistente de programación experto."},
      {"role": "user", "content": "¿Cómo funciona un decorador en Python?"}
    ],
    "stream": false
  }'
```

### 6.3 Embeddings (POST /api/embeddings)

```bash
curl http://localhost:11434/api/embeddings \
  -d '{
    "model": "nomic-embed-text",
    "prompt": "La inteligencia artificial está cambiando el mundo"
  }'
```

**Respuesta:**
```json
{
  "embedding": [0.123, -0.456, 0.789, ...]
}
```

### 6.4 Listar modelos (GET /api/tags)

```bash
curl http://localhost:11434/api/tags
```

### 6.5 Parámetros avanzados de generación

```bash
curl http://localhost:11434/api/generate \
  -d '{
    "model": "llama3.2",
    "prompt": "Escribe una función de ordenamiento en Python",
    "stream": false,
    "options": {
      "temperature": 0.7,
      "top_p": 0.9,
      "top_k": 40,
      "num_predict": 500,
      "stop": ["```"],
      "seed": 42
    }
  }'
```

| Parámetro | Descripción | Valor típico |
|-----------|-------------|--------------|
| `temperature` | Creatividad (0=determinista, 2=muy creativo) | 0.7 |
| `top_p` | Muestreo nucleus | 0.9 |
| `top_k` | Top-K sampling | 40 |
| `num_predict` | Máximo de tokens a generar | -1 (sin límite) |
| `stop` | Secuencias de parada | `["\n"]` |
| `seed` | Semilla para reproducibilidad | 42 |
| `num_ctx` | Tamaño del contexto (tokens) | 2048–8192 |

---

## 7. Implementación en proyectos Python

### 7.1 Instalación de la librería oficial

```bash
pip install ollama
```

### 7.2 Generación básica de texto

```python
import ollama

# Generación simple
response = ollama.generate(
    model='llama3.2',
    prompt='Explica qué es una API REST en 3 líneas'
)
print(response['response'])
```

### 7.3 Chat con historial de conversación

```python
import ollama

# Historial de mensajes
messages = [
    {
        'role': 'system',
        'content': 'Eres un tutor de programación Python amigable y paciente.'
    }
]

def chat(user_input: str) -> str:
    messages.append({'role': 'user', 'content': user_input})
    
    response = ollama.chat(
        model='llama3.2',
        messages=messages
    )
    
    assistant_message = response['message']['content']
    messages.append({'role': 'assistant', 'content': assistant_message})
    
    return assistant_message

# Ejemplo de uso
print(chat("¿Cómo uso una lista por comprensión?"))
print(chat("¿Puedes darme otro ejemplo más avanzado?"))
```

### 7.4 Streaming de respuestas

```python
import ollama

def stream_response(prompt: str, model: str = 'llama3.2'):
    """Muestra la respuesta token por token."""
    stream = ollama.generate(
        model=model,
        prompt=prompt,
        stream=True
    )
    
    for chunk in stream:
        print(chunk['response'], end='', flush=True)
    print()  # Nueva línea al final

stream_response("Escribe un poema sobre la programación")
```

### 7.5 Generación de embeddings y búsqueda semántica

```python
import ollama
import numpy as np
from typing import List

def get_embedding(text: str) -> List[float]:
    """Obtiene el embedding de un texto."""
    response = ollama.embeddings(
        model='nomic-embed-text',
        prompt=text
    )
    return response['embedding']

def cosine_similarity(vec1: List[float], vec2: List[float]) -> float:
    """Calcula la similitud coseno entre dos vectores."""
    a, b = np.array(vec1), np.array(vec2)
    return float(np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b)))

# Base de conocimientos
documents = [
    "Python es un lenguaje de programación interpretado y de alto nivel.",
    "JavaScript es el lenguaje principal del desarrollo web frontend.",
    "Docker permite empaquetar aplicaciones en contenedores.",
    "Git es un sistema de control de versiones distribuido.",
    "Machine Learning es una rama de la inteligencia artificial."
]

# Generar embeddings para todos los documentos
doc_embeddings = [get_embedding(doc) for doc in documents]

def buscar_similar(consulta: str, top_k: int = 3) -> List[tuple]:
    """Busca los documentos más similares a la consulta."""
    consulta_embedding = get_embedding(consulta)
    
    similitudes = [
        (documents[i], cosine_similarity(consulta_embedding, emb))
        for i, emb in enumerate(doc_embeddings)
    ]
    
    return sorted(similitudes, key=lambda x: x[1], reverse=True)[:top_k]

# Ejemplo
resultados = buscar_similar("¿Cómo versionar mi código?")
for doc, score in resultados:
    print(f"[{score:.3f}] {doc}")
```

### 7.6 Procesamiento asíncrono con asyncio

```python
import asyncio
import ollama

async def generar_async(prompt: str, model: str = 'llama3.2') -> str:
    """Genera texto de forma asíncrona."""
    client = ollama.AsyncClient()
    response = await client.generate(model=model, prompt=prompt)
    return response['response']

async def procesar_multiples(prompts: list[str]) -> list[str]:
    """Procesa múltiples prompts en paralelo."""
    tareas = [generar_async(p) for p in prompts]
    return await asyncio.gather(*tareas)

async def main():
    prompts = [
        "¿Qué es una clase en Python?",
        "¿Qué es una función lambda?",
        "¿Qué es un generador?"
    ]
    
    respuestas = await procesar_multiples(prompts)
    for prompt, respuesta in zip(prompts, respuestas):
        print(f"Q: {prompt}")
        print(f"A: {respuesta[:100]}...\n")

asyncio.run(main())
```

### 7.7 Proyecto completo: Asistente de código con RAG

```python
import ollama
import numpy as np
from pathlib import Path
from typing import List, Dict

class AsistenteCodigo:
    """
    Asistente de código con RAG (Retrieval-Augmented Generation)
    que usa documentación local como base de conocimientos.
    """
    
    def __init__(self, model: str = 'llama3.2', embed_model: str = 'nomic-embed-text'):
        self.model = model
        self.embed_model = embed_model
        self.documentos: List[Dict] = []
        self.embeddings: List[List[float]] = []
    
    def cargar_documentos(self, directorio: str):
        """Carga archivos .md y .txt como base de conocimientos."""
        ruta = Path(directorio)
        for patron in ('**/*.md', '**/*.txt'):
            for archivo in ruta.glob(patron):
                contenido = archivo.read_text(encoding='utf-8')
                # Dividir en chunks de ~500 palabras
                palabras = contenido.split()
                for i in range(0, len(palabras), 500):
                    chunk = ' '.join(palabras[i:i+500])
                    if chunk.strip():
                        self.documentos.append({
                            'contenido': chunk,
                            'fuente': str(archivo)
                        })
        
        # Generar embeddings
        print(f"Indexando {len(self.documentos)} fragmentos...")
        for doc in self.documentos:
            emb = ollama.embeddings(
                model=self.embed_model,
                prompt=doc['contenido']
            )['embedding']
            self.embeddings.append(emb)
        print("Indexación completa.")
    
    def _recuperar_contexto(self, consulta: str, top_k: int = 3) -> str:
        """Recupera los fragmentos más relevantes para la consulta."""
        if not self.embeddings:
            return ""
        
        consulta_emb = np.array(
            ollama.embeddings(model=self.embed_model, prompt=consulta)['embedding']
        )
        
        similitudes = []
        for i, emb in enumerate(self.embeddings):
            sim = np.dot(consulta_emb, np.array(emb)) / (
                np.linalg.norm(consulta_emb) * np.linalg.norm(emb)
            )
            similitudes.append((i, float(sim)))
        
        top = sorted(similitudes, key=lambda x: x[1], reverse=True)[:top_k]
        
        contexto = "\n\n---\n\n".join(
            self.documentos[i]['contenido'] for i, _ in top
        )
        return contexto
    
    def preguntar(self, pregunta: str) -> str:
        """Responde una pregunta usando RAG."""
        contexto = self._recuperar_contexto(pregunta)
        
        prompt = f"""Usa el siguiente contexto para responder la pregunta.
Si el contexto no es suficiente, usa tu conocimiento general.

CONTEXTO:
{contexto}

PREGUNTA: {pregunta}

RESPUESTA:"""
        
        response = ollama.generate(
            model=self.model,
            prompt=prompt,
            options={'temperature': 0.3}
        )
        return response['response']


# Ejemplo de uso
asistente = AsistenteCodigo()
# asistente.cargar_documentos('./docs')  # Cargar tu documentación
print(asistente.preguntar("¿Cómo se crea una función en Python?"))
```

---

## 8. Implementación en proyectos JavaScript/Node.js

### 8.1 Instalación

```bash
npm install ollama
```

### 8.2 Uso básico

```javascript
import ollama from 'ollama';

// Generación simple
const response = await ollama.generate({
  model: 'llama3.2',
  prompt: '¿Qué es una promesa en JavaScript?',
  stream: false,
});

console.log(response.response);
```

### 8.3 Chat con streaming

```javascript
import ollama from 'ollama';

async function chatConStreaming(mensajes) {
  const stream = await ollama.chat({
    model: 'llama3.2',
    messages: mensajes,
    stream: true,
  });

  for await (const chunk of stream) {
    process.stdout.write(chunk.message.content);
  }
  console.log(); // Nueva línea
}

await chatConStreaming([
  { role: 'user', content: 'Explica los closures en JavaScript' }
]);
```

### 8.4 API Express con Ollama

```javascript
import express from 'express';
import ollama from 'ollama';

const app = express();
app.use(express.json());

// Historial de conversaciones por sesión (en memoria)
const conversaciones = new Map();

// Endpoint de chat
app.post('/chat', async (req, res) => {
  const { sessionId, mensaje, modelo = 'llama3.2' } = req.body;

  if (!sessionId || !mensaje) {
    return res.status(400).json({ error: 'sessionId y mensaje son requeridos' });
  }

  // Obtener o inicializar historial
  if (!conversaciones.has(sessionId)) {
    conversaciones.set(sessionId, [
      {
        role: 'system',
        content: 'Eres un asistente de programación experto y amigable.',
      },
    ]);
  }

  const historial = conversaciones.get(sessionId);
  historial.push({ role: 'user', content: mensaje });

  try {
    const response = await ollama.chat({
      model: modelo,
      messages: historial,
    });

    const respuesta = response.message.content;
    historial.push({ role: 'assistant', content: respuesta });

    res.json({ respuesta, sessionId });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Endpoint de embeddings
app.post('/embeddings', async (req, res) => {
  const { texto } = req.body;

  try {
    const response = await ollama.embeddings({
      model: 'nomic-embed-text',
      prompt: texto,
    });
    res.json({ embedding: response.embedding });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Limpiar sesión
app.delete('/chat/:sessionId', (req, res) => {
  conversaciones.delete(req.params.sessionId);
  res.json({ mensaje: 'Sesión eliminada' });
});

app.listen(3000, () => {
  console.log('Servidor corriendo en http://localhost:3000');
});
```

### 8.5 Uso con fetch (sin librería)

```javascript
// Funciona en cualquier entorno con fetch disponible
async function generarTexto(prompt, modelo = 'llama3.2') {
  const response = await fetch('http://localhost:11434/api/generate', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ model: modelo, prompt, stream: false }),
  });

  const data = await response.json();
  return data.response;
}

const texto = await generarTexto('¿Qué es TypeScript?');
console.log(texto);
```

---

## 9. Personalización de modelos con Modelfile

Un **Modelfile** permite crear modelos personalizados basados en los existentes, definiendo el sistema de instrucciones, parámetros y comportamiento.

### 9.1 Estructura básica de un Modelfile

```dockerfile
# Modelo base
FROM llama3.2

# Mensaje del sistema (define la personalidad/rol)
SYSTEM """
Eres un asistente de programación especializado en Python y JavaScript.
Siempre:
- Proporciona ejemplos de código
- Explica el código paso a paso
- Sugiere mejores prácticas
- Responde en español
"""

# Parámetros del modelo
PARAMETER temperature 0.7
PARAMETER top_p 0.9
PARAMETER top_k 40
PARAMETER num_ctx 4096
PARAMETER stop "<|im_end|>"
PARAMETER stop "<|end_of_text|>"

# Plantilla de prompt (opcional, para modelos base sin instrucciones)
TEMPLATE """{{ if .System }}<|im_start|>system
{{ .System }}<|im_end|>
{{ end }}{{ if .Prompt }}<|im_start|>user
{{ .Prompt }}<|im_end|>
{{ end }}<|im_start|>assistant
{{ .Response }}<|im_end|>
"""
```

### 9.2 Crear y usar el modelo personalizado

```bash
# Crear el modelo
ollama create tutor-python -f ./Modelfile

# Ejecutar el modelo personalizado
ollama run tutor-python

# Verificar que se creó correctamente
ollama show tutor-python
```

### 9.3 Ejemplo: Modelo con conocimiento específico

```dockerfile
FROM llama3.2

SYSTEM """
Eres MedBot, un asistente especializado en información médica general.
IMPORTANTE:
- Siempre recuerda que no reemplazas a un médico profesional
- Proporciona información general y educativa
- Recomienda consultar a un médico para diagnósticos
- Responde en español de forma clara y accesible
"""

PARAMETER temperature 0.3
PARAMETER num_ctx 8192
```

### 9.4 Fine-tuning con datos propios (GGUF personalizado)

Si tienes un modelo GGUF fine-tuneado externamente:

```dockerfile
# Usar un modelo GGUF local
FROM /ruta/a/tu/modelo.gguf

SYSTEM "Eres un experto en la empresa XYZ..."

PARAMETER temperature 0.5
```

```bash
ollama create empresa-bot -f ./Modelfile
```

---

## 10. Funciones avanzadas

### 10.1 Tool Calling (Function Calling)

Ollama soporta *tool calling* en modelos compatibles (llama3.1, llama3.2, mistral-nemo, etc.), que permite al modelo invocar funciones definidas por el usuario.

```python
import ollama
import json

# Definir herramientas disponibles
herramientas = [
    {
        'type': 'function',
        'function': {
            'name': 'obtener_clima',
            'description': 'Obtiene el clima actual de una ciudad',
            'parameters': {
                'type': 'object',
                'properties': {
                    'ciudad': {
                        'type': 'string',
                        'description': 'El nombre de la ciudad'
                    },
                    'unidades': {
                        'type': 'string',
                        'enum': ['celsius', 'fahrenheit'],
                        'description': 'Unidades de temperatura'
                    }
                },
                'required': ['ciudad']
            }
        }
    },
    {
        'type': 'function',
        'function': {
            'name': 'buscar_web',
            'description': 'Busca información en internet',
            'parameters': {
                'type': 'object',
                'properties': {
                    'consulta': {
                        'type': 'string',
                        'description': 'La consulta de búsqueda'
                    }
                },
                'required': ['consulta']
            }
        }
    }
]

# Funciones reales
def obtener_clima(ciudad: str, unidades: str = 'celsius') -> dict:
    # Simulación (en producción conectarías a una API real)
    return {
        'ciudad': ciudad,
        'temperatura': 22,
        'unidades': unidades,
        'descripcion': 'Parcialmente nublado'
    }

def buscar_web(consulta: str) -> dict:
    # Simulación
    return {'resultados': [f'Resultado simulado para: {consulta}']}

# Mapeo de funciones
funciones_disponibles = {
    'obtener_clima': obtener_clima,
    'buscar_web': buscar_web
}

def agente_con_herramientas(pregunta: str) -> str:
    mensajes = [{'role': 'user', 'content': pregunta}]
    
    # Primera llamada al modelo
    response = ollama.chat(
        model='llama3.2',
        messages=mensajes,
        tools=herramientas
    )
    
    # Si el modelo quiere usar una herramienta
    if response['message'].get('tool_calls'):
        mensajes.append(response['message'])
        
        for tool_call in response['message']['tool_calls']:
            nombre = tool_call['function']['name']
            argumentos = tool_call['function']['arguments']
            
            if nombre in funciones_disponibles:
                resultado = funciones_disponibles[nombre](**argumentos)
                mensajes.append({
                    'role': 'tool',
                    'content': json.dumps(resultado)
                })
        
        # Segunda llamada con los resultados de las herramientas
        response_final = ollama.chat(
            model='llama3.2',
            messages=mensajes
        )
        return response_final['message']['content']
    
    return response['message']['content']

print(agente_con_herramientas("¿Qué tiempo hace en Madrid?"))
```

### 10.2 Análisis de imágenes (modelos multimodales)

```python
import ollama
import base64

def analizar_imagen(ruta_imagen: str, pregunta: str = "¿Qué ves en esta imagen?") -> str:
    """Analiza una imagen con un modelo multimodal."""
    with open(ruta_imagen, 'rb') as f:
        imagen_bytes = f.read()
    
    response = ollama.chat(
        model='llava',  # o llama3.2-vision
        messages=[
            {
                'role': 'user',
                'content': pregunta,
                'images': [imagen_bytes]  # La librería acepta bytes directamente
            }
        ]
    )
    return response['message']['content']

# Ejemplo
descripcion = analizar_imagen('foto.jpg', '¿Qué elementos hay en la imagen?')
print(descripcion)
```

### 10.3 Structured Output (JSON mode)

```python
import ollama
import json

def generar_json_estructurado(descripcion: str) -> dict:
    """Genera una respuesta JSON estructurada."""
    response = ollama.generate(
        model='llama3.2',
        prompt=f"""Analiza el siguiente texto y extrae la información en JSON.
        
Texto: {descripcion}

Responde SOLO con JSON válido con esta estructura:
{{
  "nombre": "string",
  "categoria": "string", 
  "caracteristicas": ["string"],
  "nivel_dificultad": "principiante|intermedio|avanzado"
}}""",
        format='json',
        options={'temperature': 0.1}
    )
    
    return json.loads(response['response'])

resultado = generar_json_estructurado(
    "Python es un lenguaje interpretado ideal para data science y scripts"
)
print(json.dumps(resultado, indent=2, ensure_ascii=False))
```

### 10.4 Contexto largo y documentos

```python
import ollama
from pathlib import Path

def analizar_documento(ruta: str, pregunta: str) -> str:
    """Analiza un documento completo con contexto largo."""
    contenido = Path(ruta).read_text(encoding='utf-8')
    
    response = ollama.generate(
        model='llama3.1',  # Modelos con mayor contexto
        prompt=f"""Analiza el siguiente documento y responde la pregunta.

DOCUMENTO:
{contenido}

PREGUNTA: {pregunta}

RESPUESTA:""",
        options={
            'num_ctx': 32768,  # Contexto de 32K tokens
            'temperature': 0.2
        }
    )
    return response['response']
```

---

## 11. Integración con herramientas populares

### 11.1 LangChain

```bash
pip install langchain langchain-ollama
```

```python
from langchain_ollama import OllamaLLM, OllamaEmbeddings
from langchain.chains import RetrievalQA
from langchain_community.vectorstores import Chroma
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import DirectoryLoader

# Configurar el LLM
llm = OllamaLLM(model='llama3.2', temperature=0.7)

# Configurar embeddings
embeddings = OllamaEmbeddings(model='nomic-embed-text')

# Cargar documentos
loader = DirectoryLoader('./docs', glob='**/*.md')
docs = loader.load()

# Dividir en chunks
splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
splits = splitter.split_documents(docs)

# Crear base vectorial
vectorstore = Chroma.from_documents(splits, embeddings)

# Crear cadena de RAG
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vectorstore.as_retriever(search_kwargs={'k': 4})
)

respuesta = qa_chain.invoke({'query': '¿Qué información hay sobre Python?'})
print(respuesta['result'])
```

### 11.2 LlamaIndex

```bash
pip install llama-index llama-index-llms-ollama llama-index-embeddings-ollama
```

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader, Settings
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding

# Configurar modelos
Settings.llm = Ollama(model='llama3.2', request_timeout=120.0)
Settings.embed_model = OllamaEmbedding(model_name='nomic-embed-text')

# Cargar y indexar documentos
documents = SimpleDirectoryReader('./docs').load_data()
index = VectorStoreIndex.from_documents(documents)

# Crear motor de consulta
query_engine = index.as_query_engine()

respuesta = query_engine.query('¿Cómo instalar Python?')
print(respuesta)
```

### 11.3 Open WebUI (interfaz gráfica)

Open WebUI es una interfaz tipo ChatGPT para Ollama:

```bash
# Con Docker
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

Accede en `http://localhost:3000`.

### 11.4 Ollama con Docker

```yaml
# docker-compose.yml
version: '3.8'
services:
  ollama:
    image: ollama/ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    # Para GPU NVIDIA:
    # deploy:
    #   resources:
    #     reservations:
    #       devices:
    #         - driver: nvidia
    #           count: 1
    #           capabilities: [gpu]

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    depends_on:
      - ollama

volumes:
  ollama_data:
```

```bash
docker compose up -d

# Descargar un modelo en el contenedor
docker exec ollama ollama pull llama3.2
```

### 11.5 Continue.dev (extensión IDE)

[Continue.dev](https://continue.dev) es una extensión para VS Code y JetBrains que usa Ollama como backend:

1. Instala la extensión **Continue** en VS Code.
2. Configura `~/.continue/config.json`:

```json
{
  "models": [
    {
      "title": "Llama 3.2",
      "provider": "ollama",
      "model": "llama3.2"
    },
    {
      "title": "CodeLlama",
      "provider": "ollama",
      "model": "codellama"
    }
  ],
  "tabAutocompleteModel": {
    "title": "DeepSeek Coder",
    "provider": "ollama",
    "model": "deepseek-coder:1.3b"
  }
}
```

---

## 12. Casos de uso prácticos

### 12.1 Generador de tests automático

```python
import ollama
import ast
from pathlib import Path

def generar_tests(codigo_fuente: str) -> str:
    """Genera tests unitarios para el código dado."""
    response = ollama.generate(
        model='codellama',
        prompt=f"""Genera tests unitarios con pytest para el siguiente código Python.
Incluye casos normales, casos borde y casos de error.

CÓDIGO:
```python
{codigo_fuente}
```

TESTS:""",
        options={'temperature': 0.2, 'num_predict': 1000}
    )
    return response['response']

# Ejemplo
codigo = """
def dividir(a: float, b: float) -> float:
    if b == 0:
        raise ValueError("No se puede dividir por cero")
    return a / b
"""

print(generar_tests(codigo))
```

### 12.2 Revisor de código

```python
import ollama

def revisar_codigo(codigo: str, lenguaje: str = 'Python') -> dict:
    """Revisa el código y sugiere mejoras."""
    response = ollama.generate(
        model='llama3.2',
        prompt=f"""Revisa el siguiente código {lenguaje} y proporciona:
1. Lista de problemas encontrados (bugs, malas prácticas, etc.)
2. Sugerencias de mejora
3. Código mejorado

CÓDIGO:
```{lenguaje.lower()}
{codigo}
```

Responde en formato JSON con claves: "problemas", "sugerencias", "codigo_mejorado"
""",
        format='json',
        options={'temperature': 0.3}
    )
    
    import json
    return json.loads(response['response'])

# Ejemplo
codigo_malo = """
def calc(l):
    s = 0
    for i in range(len(l)):
        s = s + l[i]
    return s
"""

resultado = revisar_codigo(codigo_malo)
print(resultado)
```

### 12.3 Chatbot con interfaz web (Flask + HTML)

```python
# app.py
from flask import Flask, render_template, request, Response
import ollama
import json

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/chat', methods=['POST'])
def chat():
    data = request.json
    mensaje = data.get('mensaje', '')
    historial = data.get('historial', [])
    
    historial.append({'role': 'user', 'content': mensaje})
    
    def generar():
        stream = ollama.chat(
            model='llama3.2',
            messages=historial,
            stream=True
        )
        for chunk in stream:
            token = chunk['message']['content']
            yield f"data: {json.dumps({'token': token})}\n\n"
        yield "data: [DONE]\n\n"
    
    return Response(generar(), mimetype='text/event-stream')

if __name__ == '__main__':
    app.run(debug=True)
```

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Chat con Ollama</title>
    <style>
        body { font-family: sans-serif; max-width: 800px; margin: 40px auto; padding: 0 20px; }
        #chat-box { height: 400px; overflow-y: auto; border: 1px solid #ddd;
                    padding: 10px; margin-bottom: 10px; border-radius: 8px; }
        .mensaje-usuario { text-align: right; color: #0066cc; margin: 5px 0; }
        .mensaje-bot { text-align: left; color: #333; margin: 5px 0; }
        input { width: 80%; padding: 8px; border-radius: 4px; border: 1px solid #ddd; }
        button { padding: 8px 16px; background: #0066cc; color: white;
                 border: none; border-radius: 4px; cursor: pointer; }
    </style>
</head>
<body>
    <h1>🦙 Chat con Ollama</h1>
    <div id="chat-box"></div>
    <div>
        <input type="text" id="input" placeholder="Escribe tu mensaje..." />
        <button onclick="enviar()">Enviar</button>
    </div>

    <script>
        let historial = [];
        
        async function enviar() {
            const input = document.getElementById('input');
            const mensaje = input.value.trim();
            if (!mensaje) return;
            
            input.value = '';
            agregarMensaje('usuario', mensaje);
            
            const div = agregarMensaje('bot', '');
            
            const response = await fetch('/chat', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ mensaje, historial })
            });
            
            const reader = response.body.getReader();
            const decoder = new TextDecoder();
            let respuestaCompleta = '';
            
            while (true) {
                const { done, value } = await reader.read();
                if (done) break;
                const text = decoder.decode(value);
                const lines = text.split('\n').filter(l => l.startsWith('data: '));
                for (const line of lines) {
                    const data = line.slice(6);
                    if (data === '[DONE]') break;
                    const { token } = JSON.parse(data);
                    respuestaCompleta += token;
                    div.textContent = respuestaCompleta;
                }
            }
            
            historial.push({ role: 'user', content: mensaje });
            historial.push({ role: 'assistant', content: respuestaCompleta });
        }
        
        function agregarMensaje(tipo, texto) {
            const box = document.getElementById('chat-box');
            const div = document.createElement('div');
            div.className = tipo === 'usuario' ? 'mensaje-usuario' : 'mensaje-bot';
            div.textContent = texto;
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;
            return div;
        }
        
        document.getElementById('input').addEventListener('keypress', e => {
            if (e.key === 'Enter') enviar();
        });
    </script>
</body>
</html>
```

### 12.4 Procesador de documentos PDF

```bash
pip install PyPDF2 ollama
```

```python
import ollama
import PyPDF2
from pathlib import Path

def procesar_pdf(ruta_pdf: str) -> str:
    """Extrae y limpia el texto de un PDF."""
    with open(ruta_pdf, 'rb') as f:
        lector = PyPDF2.PdfReader(f)
        texto = '\n'.join(
            pagina.extract_text() or ''
            for pagina in lector.pages
        )
    return texto

def resumir_pdf(ruta_pdf: str) -> str:
    """Resume el contenido de un PDF."""
    texto = procesar_pdf(ruta_pdf)
    
    # Truncar si es muy largo (ajustar según el contexto del modelo)
    if len(texto) > 10000:
        texto = texto[:10000] + '...'
    
    response = ollama.generate(
        model='llama3.2',
        prompt=f"""Resume el siguiente documento en español.
Incluye: puntos principales, conclusiones y datos clave.

DOCUMENTO:
{texto}

RESUMEN:""",
        options={'temperature': 0.3, 'num_ctx': 8192}
    )
    return response['response']

def responder_sobre_pdf(ruta_pdf: str, pregunta: str) -> str:
    """Responde preguntas sobre un PDF."""
    texto = procesar_pdf(ruta_pdf)
    
    response = ollama.generate(
        model='llama3.2',
        prompt=f"""Basándote en el siguiente documento, responde la pregunta.

DOCUMENTO:
{texto[:8000]}

PREGUNTA: {pregunta}

RESPUESTA:""",
        options={'temperature': 0.2}
    )
    return response['response']
```

---

## 13. Buenas prácticas y optimización

### 13.1 Gestión de prompts

```python
# ✅ Buena práctica: usar plantillas de prompts
from string import Template

PROMPT_RESUMEN = Template("""
Eres un experto en $dominio.
Resume el siguiente texto en $num_puntos puntos clave.
Responde en $idioma.

TEXTO:
$texto

RESUMEN:
""")

def resumir(texto: str, dominio: str = "tecnología", 
            num_puntos: int = 5, idioma: str = "español") -> str:
    prompt = PROMPT_RESUMEN.substitute(
        dominio=dominio,
        num_puntos=num_puntos,
        idioma=idioma,
        texto=texto
    )
    response = ollama.generate(model='llama3.2', prompt=prompt)
    return response['response']
```

### 13.2 Manejo de errores y reintentos

```python
import ollama
import time
from typing import Callable, TypeVar

T = TypeVar('T')

def con_reintentos(func: Callable, max_intentos: int = 3, espera: float = 2.0):
    """Ejecuta una función con reintentos en caso de error."""
    for intento in range(max_intentos):
        try:
            return func()
        except ollama.ResponseError as e:
            if intento == max_intentos - 1:
                raise
            print(f"Error (intento {intento + 1}/{max_intentos}): {e}")
            time.sleep(espera * (intento + 1))
        except Exception as e:
            raise

# Uso
respuesta = con_reintentos(
    lambda: ollama.generate(model='llama3.2', prompt='Hola')
)
```

### 13.3 Pool de conexiones para alto rendimiento

```python
import ollama
import asyncio
from asyncio import Semaphore

class OllamaPool:
    """Pool de clientes Ollama para procesamiento concurrente."""
    
    def __init__(self, max_concurrent: int = 4):
        self.semaforo = Semaphore(max_concurrent)
        self.cliente = ollama.AsyncClient()
    
    async def generar(self, prompt: str, model: str = 'llama3.2') -> str:
        async with self.semaforo:
            response = await self.cliente.generate(model=model, prompt=prompt)
            return response['response']
    
    async def procesar_lote(self, prompts: list[str]) -> list[str]:
        tareas = [self.generar(p) for p in prompts]
        return await asyncio.gather(*tareas)

# Uso
async def main():
    pool = OllamaPool(max_concurrent=3)
    prompts = ['Pregunta 1', 'Pregunta 2', 'Pregunta 3', 'Pregunta 4']
    respuestas = await pool.procesar_lote(prompts)
    for p, r in zip(prompts, respuestas):
        print(f"Q: {p} -> A: {r[:50]}...")

asyncio.run(main())
```

### 13.4 Cachear respuestas

```python
import hashlib
import json
import ollama
from functools import lru_cache
from pathlib import Path

class CacheOllama:
    """Cache persistente para respuestas de Ollama."""
    
    def __init__(self, cache_dir: str = '.ollama_cache'):
        self.cache_dir = Path(cache_dir)
        self.cache_dir.mkdir(exist_ok=True)
    
    def _clave(self, model: str, prompt: str, opciones: dict) -> str:
        contenido = json.dumps({'model': model, 'prompt': prompt, 'opciones': opciones})
        return hashlib.md5(contenido.encode()).hexdigest()
    
    def generar(self, model: str, prompt: str, **opciones) -> str:
        clave = self._clave(model, prompt, opciones)
        archivo = self.cache_dir / f"{clave}.txt"
        
        if archivo.exists():
            return archivo.read_text(encoding='utf-8')
        
        response = ollama.generate(model=model, prompt=prompt, options=opciones)
        resultado = response['response']
        archivo.write_text(resultado, encoding='utf-8')
        return resultado

# Uso
cache = CacheOllama()
respuesta = cache.generar('llama3.2', '¿Qué es Python?', temperature=0.1)
print(respuesta)
```

### 13.5 Selección del modelo según recursos

```python
import psutil
import ollama

def seleccionar_modelo() -> str:
    """Selecciona el modelo más adecuado según la RAM disponible."""
    ram_gb = psutil.virtual_memory().available / (1024 ** 3)
    
    modelos_disponibles = {m['name'] for m in ollama.list()['models']}
    
    if ram_gb >= 16 and 'llama3.1:8b' in modelos_disponibles:
        return 'llama3.1:8b'
    elif ram_gb >= 8 and 'llama3.2:3b' in modelos_disponibles:
        return 'llama3.2:3b'
    elif 'llama3.2:1b' in modelos_disponibles:
        return 'llama3.2:1b'
    else:
        # Usar el primer modelo disponible
        modelos = ollama.list()['models']
        if modelos:
            return modelos[0]['name']
        raise RuntimeError("No hay modelos instalados")

modelo = seleccionar_modelo()
print(f"Usando modelo: {modelo}")
```

---

## 14. Solución de problemas comunes

### Problema: El servidor no responde

```bash
# Verificar si el servidor está corriendo
curl http://localhost:11434/api/tags

# Iniciar el servidor
ollama serve

# Verificar logs
journalctl -u ollama -f  # Linux con systemd
```

### Problema: Out of Memory (OOM)

```bash
# Usar un modelo más pequeño o con mayor cuantización
ollama run llama3.2:1b  # Modelo de 1B parámetros
ollama run llama3.2:3b-q4_0  # 4-bit quantization

# Reducir el contexto
# En Python:
ollama.generate(model='llama3.2', prompt='...', options={'num_ctx': 1024})
```

### Problema: Respuestas lentas sin GPU

```bash
# Verificar si Ollama detecta la GPU
ollama ps  # Muestra el uso de GPU si está activo

# Linux: verificar drivers NVIDIA
nvidia-smi

# Forzar uso de CPU si hay problemas con GPU
CUDA_VISIBLE_DEVICES="" ollama serve
```

### Problema: Acceso desde otra máquina

```bash
# Por defecto, Ollama solo escucha en localhost
# Para acceso en red local:
OLLAMA_HOST=0.0.0.0:11434 ollama serve

# O configurar de forma permanente (Linux):
sudo systemctl edit ollama.service
# Agregar:
# [Service]
# Environment="OLLAMA_HOST=0.0.0.0:11434"
```

### Problema: CORS para aplicaciones web

```bash
# Permitir todos los orígenes (desarrollo)
OLLAMA_ORIGINS="*" ollama serve

# Origenes específicos
OLLAMA_ORIGINS="http://localhost:3000,https://miapp.com" ollama serve
```

### Referencia de variables de entorno

| Variable | Descripción | Ejemplo |
|----------|-------------|---------|
| `OLLAMA_HOST` | Dirección de escucha | `0.0.0.0:11434` |
| `OLLAMA_ORIGINS` | CORS permitidos | `http://localhost:3000` |
| `OLLAMA_MODELS` | Directorio de modelos | `/data/ollama/models` |
| `OLLAMA_NUM_PARALLEL` | Solicitudes paralelas | `4` |
| `OLLAMA_MAX_LOADED_MODELS` | Modelos en memoria | `3` |
| `OLLAMA_KEEP_ALIVE` | Tiempo en memoria | `5m` |
| `OLLAMA_DEBUG` | Modo debug | `1` |

---

## 🎯 Recursos adicionales

- 📖 [Documentación oficial de Ollama](https://github.com/ollama/ollama/blob/main/docs/README.md)
- 🗂️ [Librería de modelos](https://ollama.com/library)
- 💬 [Discord de la comunidad](https://discord.gg/ollama)
- 🐙 [Repositorio GitHub](https://github.com/ollama/ollama)
- 🌐 [Open WebUI](https://github.com/open-webui/open-webui)
- 🔗 [LangChain + Ollama](https://python.langchain.com/docs/integrations/llms/ollama)

---

*Tutorial creado para el repositorio de tutoriales de informática. ¡Contribuciones bienvenidas!*
