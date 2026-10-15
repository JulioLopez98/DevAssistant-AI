# DevAssistant AI

Asistente conversacional para desarrolladores, construido con **Python, LangGraph y OpenAI**. Utiliza un grafo de estados para decidir cuándo responder directamente y cuándo consultar fuentes externas, y conserva las conversaciones mediante **SQLite**.

Proyecto práctico desarrollado durante la formación de AI Engineer, con el objetivo de comprender agentes con herramientas, enrutamiento condicional y memoria persistente.

## Funcionalidades

- **Chat técnico en español:** responde preguntas de programación.
- **Consulta de paquetes de Python:** obtiene información de versiones, descripciones y requisitos desde la API de PyPI.
- **Búsqueda técnica:** localiza preguntas relacionadas en Stack Overflow mediante la API de Stack Exchange (títulos, enlaces y metadatos; no descarga las respuestas completas).
- **Selección de herramientas:** el modelo decide si necesita consultar alguna de las herramientas disponibles.
- **Control de llamadas:** limita a dos las ejecuciones de herramientas por turno.
- **Memoria persistente:** recupera el historial guardado en SQLite al reutilizar un `thread_id`.

## Arquitectura

```text
START
  |
  v
responder (LLM)
  |
  v
decidir_ruta
  |          \
  | tools     \ fin
  v            v
ejecutar_tools   END
  |
  +-----> responder
```

El estado contiene el historial de mensajes (`add_messages`), el contador de llamadas a herramientas y los errores registrados. El nodo de respuesta puede solicitar herramientas; el nodo de ejecución procesa las solicitudes y devuelve sus resultados al modelo mediante `ToolMessage`.

## Tecnologías

- Python 3.10 o superior
- LangGraph y LangChain Core
- OpenAI (`langchain-openai`)
- SQLite (`langgraph-checkpoint-sqlite`)
- Requests y python-dotenv
- APIs públicas de PyPI y Stack Exchange

## Estructura del proyecto

```text
Proyecto5-langgraph/
├── main.py
├── tools/
│   ├── __init__.py
│   ├── pypi_tool.py
│   └── docs_tool.py
├── requirements.txt
├── .gitignore
├── .env.example
└── README.md
```

El archivo `conversaciones.db` se crea durante la ejecución y **no debe subirse a GitHub**.

## Instalación

1. Clona el repositorio y accede a su carpeta.
2. Crea y activa un entorno virtual (PowerShell):

   ```powershell
   py -3.10 -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

   También puedes utilizar otra versión compatible de Python (3.10 o superior).

3. Instala las dependencias:

   ```powershell
   python -m pip install -r requirements.txt
   ```

4. Copia `.env.example` como `.env` y configura tu clave de OpenAI:

   ```env
   OPENAI_API_KEY=tu_clave_aqui
   MODEL_ID=gpt-4.1-mini
   ```

   **Nunca publiques el archivo `.env` ni tu clave de API.** El uso de OpenAI puede generar costes.

5. Ejecuta el asistente:

   ```powershell
   python main.py
   ```

Escribe `salir` para cerrar el chat.

## Ejemplos de uso

- `¿Qué es una variable en Python?` → respuesta directa.
- `Consulta la versión actual de FastAPI` → herramienta PyPI.
- `Busca en Stack Overflow información sobre StateGraph` → herramienta de búsqueda.
- `¿Cómo me llamo?` → recupera datos de una conversación anterior si se utiliza el mismo `thread_id`.

También puedes probar las herramientas por separado:

```powershell
python -m tools.pypi_tool
python -m tools.docs_tool
```

## Persistencia

LangGraph utiliza `SqliteSaver` para guardar el estado de cada conversación. La configuración `configurable.thread_id` identifica el historial que debe recuperarse. Si se mantiene el mismo identificador, la conversación puede continuar tras reiniciar el programa.

## Limitaciones y próximos pasos

- La búsqueda en Stack Overflow recupera referencias y metadatos, no el texto completo de las respuestas ni documentación oficial.
- Se han realizado pruebas manuales de selección de herramientas, límite de dos ejecuciones en un caso y recuperación de memoria tras reinicio. **Quedan pendientes las pruebas automatizadas y la validación exhaustiva de errores y límites.**
- Como mejoras futuras: fuentes de documentación oficial, tests con `pytest`, registro estructurado de errores y selección de conversaciones desde la interfaz.

## Propósito

Proyecto educativo y de portfolio para practicar **StateGraph, nodos, rutas condicionales, tool calling y checkpointers** en un asistente conversacional real.
