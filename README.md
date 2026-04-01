# Marketing Copilot — CloudLabs Hackathon Talento Tech 2026

Co-piloto de Marketing impulsado por análisis de datos e Inteligencia Artificial. Analiza el comportamiento de usuarios en la plataforma CloudLabs Learning y genera recomendaciones accionables en lenguaje natural.

---

## ¿Qué hace el proyecto?

El usuario escribe una pregunta en lenguaje natural (por ejemplo: *"¿Dónde abandonan más los usuarios?"*) y el sistema responde con:

- **Datos concretos** calculados sobre el dataset real de Microsoft Clarity
- **Interpretación de negocio** generada por IA (Groq / Cerebras / Claude / Gemini / OpenAI)
- **Gráfico visual** cuando aplica (barras, líneas, pie)

---

## Arquitectura

```text
marketing-copilot-cloudlabs/
├── backend/          → API REST con FastAPI (Python)
│   ├── main.py
│   ├── .env
│   ├── requirements.txt
│   ├── data/
│   │   ├── 1_Data_Recordings.csv   ← sesiones de usuarios
│   │   └── 2_Data_Metrics.csv      ← métricas de comportamiento
│   └── app/
│       ├── routers/
│       │   ├── chat.py             ← endpoint /api/ask (copilot)
│       │   ├── analytics.py        ← endpoints de análisis
│       │   └── dashboard.py        ← resumen general
│       ├── services/
│       │   ├── analytics_engine.py ← motor de análisis con pandas
│       │   ├── llm_service.py      ← integración con LLMs
│       │   └── data_loader.py      ← carga de CSVs
│       └── models/
│           └── schemas.py          ← modelos Pydantic
└── frontend/         → SPA con Angular 17
    └── src/app/
        ├── components/
        │   ├── copilot/    ← chat con IA
        │   ├── dashboard/  ← KPIs generales
        │   ├── analytics/  ← tablas de análisis
        │   ├── chart/      ← gráficos con Chart.js
        │   ├── kpi-card/   ← tarjetas de métricas
        │   └── sidebar/    ← navegación
        └── services/
            ├── api.service.ts
            └── analytics.service.ts
```

---

## Stack Tecnológico

| Capa | Tecnología |
|---|---|
| Frontend | Angular 17, Chart.js, TypeScript |
| Backend | FastAPI, Python 3.11 |
| Análisis de datos | Pandas, NumPy |
| IA / LLM | Groq (llama-3.3-70b), Cerebras, Claude, Gemini, OpenAI |
| Dataset | Microsoft Clarity — CloudLabs Learning |

---

## Requisitos previos

Antes de comenzar asegúrate de tener instalado:

- **Python 3.11 o 3.12** → https://www.python.org/downloads/
- **Node.js 18 o superior** → https://nodejs.org/
- **Angular CLI** → se instala en el paso de frontend
- **Git** → https://git-scm.com/

---

## Instalación y Ejecución

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/marketing-copilot-cloudlabs.git
cd marketing-copilot-cloudlabs
```

---

### 2. Backend

#### 2.1 Crear y activar el entorno virtual

```bash
cd backend
python -m venv venv
```

```bash
# Windows (PowerShell)
venv\Scripts\Activate.ps1

# Windows (CMD)
venv\Scripts\activate.bat

# Mac / Linux
source venv/bin/activate
```

> Si PowerShell bloquea la ejecución de scripts, corre esto primero:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

#### 2.2 Instalar dependencias

```bash
pip install -r requirements.txt --only-binary=pandas,numpy
```

#### 2.3 Configurar variables de entorno

Copia el archivo de ejemplo y edítalo:

```bash
cp .env.example .env
```

Abre `.env` con cualquier editor de texto y configura el proveedor de IA que vayas a usar (ver sección **Configuración del LLM** más abajo).

#### 2.4 Verificar que los datos estén en su lugar

Asegúrate de que existan estos dos archivos antes de arrancar:

```
backend/data/1_Data_Recordings.csv
backend/data/2_Data_Metrics.csv
```

#### 2.5 Correr el servidor

```bash
python -m uvicorn main:app --reload --port 8000
```

Al arrancar correctamente verás:

```
✅ Marketing Copilot API lista
   Recordings: 67414 filas
   Metrics:    33741 filas
   Usuarios:   47997
INFO: Uvicorn running on http://127.0.0.1:8000
```

API disponible en: `http://localhost:8000`  
Documentación interactiva: `http://localhost:8000/docs`

---

### 3. Frontend

Abre una **nueva terminal** (deja el backend corriendo) y desde la raíz del proyecto:

```bash
cd frontend
```

#### 3.1 Instalar Angular CLI (solo la primera vez)

```bash
npm install -g @angular/cli
```

#### 3.2 Instalar dependencias del proyecto

```bash
npm install --legacy-peer-deps
```

#### 3.3 Correr la aplicación

```bash
ng serve
```

o equivalentemente:

```bash
npm start
```

App disponible en: `http://localhost:4200`

> **Importante:** el backend debe estar corriendo en el puerto 8000 para que el frontend funcione correctamente.

---

## Configuración del LLM (backend/.env)

El sistema funciona sin LLM (devuelve los datos del motor analítico). Para activar la interpretación de IA, configura uno de los siguientes proveedores en `backend/.env`:

```env
# Groq — gratuito, 14,400 requests/día
LLM_PROVIDER=groq
GROQ_API_KEY=gsk_...
# Obtener key: https://console.groq.com

# Cerebras — gratuito, muy rápido
LLM_PROVIDER=cerebras
CEREBRAS_API_KEY=csk_...
# Obtener key: https://cloud.cerebras.ai

# Claude (Anthropic)
LLM_PROVIDER=claude
ANTHROPIC_API_KEY=sk-ant-...

# Gemini (Google) — gratuito
LLM_PROVIDER=gemini
GEMINI_API_KEY=AIza...
# Obtener key: https://aistudio.google.com

# OpenAI
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...
```

Después de editar el `.env` reinicia el backend con `Ctrl+C` y vuelve a correr `python -m uvicorn main:app --reload --port 8000`. En la consola deberías ver:

```
🤖 LLM configurado: gemini
```

---

## Endpoints principales

| Método | Ruta | Descripción |
|---|---|---|
| POST | `/api/ask` | Pregunta al copilot en lenguaje natural |
| GET | `/api/suggested-questions` | Preguntas sugeridas |
| GET | `/api/dashboard` | Resumen general KPIs |
| GET | `/api/analytics/top-pages` | Páginas más visitadas |
| GET | `/api/analytics/abandono` | Puntos de abandono |
| GET | `/api/analytics/flujos` | Flujos de navegación |
| GET | `/api/analytics/conversion` | Patrones de conversión |
| GET | `/api/analytics/segmentation` | Segmentación dispositivo/país |
| GET | `/api/analytics/frustration` | Análisis de frustración |
| GET | `/api/health` | Estado del servidor |

---

## Insights del Motor Analítico

El `AnalyticsEngine` detecta automáticamente la intención de la pregunta y ejecuta el análisis correspondiente:

1. **Páginas Top** — ranking de páginas de entrada por sesiones
2. **Puntos de abandono** — páginas con mayor tasa de salida y bounce
3. **Flujos de navegación** — pares entrada→salida más frecuentes
4. **Interacción por página** — clics, tiempo, engagement promedio
5. **Patrones de conversión** — sesiones que llegaron a pricing / demo / contacto
6. **Segmentación** — distribución por dispositivo, país, navegador, OS
7. **Páginas trampa** — alto tráfico pero bajo engagement
8. **Análisis de frustración** — rage clicks, dead clicks, abandono rápido

---

## Dataset

Los datos provienen de **Microsoft Clarity** sobre la plataforma [CloudLabs Learning](https://cloudlabs.com):

- `1_Data_Recordings.csv` — sesiones individuales de usuarios con URL de entrada/salida, dispositivo, duración, engagement, etc.
- `2_Data_Metrics.csv` — métricas agregadas por página incluyendo DeadClicks, RageClicks, ScrollDepth, etc.

---

## Equipo

Desarrollado para el **Hackathon Talento Tech 2026** — CloudLabs Learning.