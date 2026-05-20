# Chatbot Invermar — Agente de Recursos Humanos

> Agente conversacional autónomo para consultas de remuneraciones y beneficios del personal de **Invermar S.A.** y empresas relacionadas.  
> Desarrollado para el curso **ISY0101 — Ingeniería de Soluciones con IA** (DuocUC, 2025).

---

## Descripción

Este proyecto evoluciona en dos fases:

| Fase | Notebooks | Descripción |
|---|---|---|
| **RA1** — Chatbot RAG | `IL1.1` → `IL1.4` | Chatbot con prompt engineering, RAG (FAISS), memoria conversacional y trazabilidad LangSmith |
| **RA2** — Agente Funcional | `EP2_Agente_Invermar.ipynb` | Agente ReAct con 5 herramientas, interfaz visual, correo automático y memoria dual |

El agente cubre las empresas del grupo:
- Invermar S.A.
- Pesquera La Portada S.A.
- La Península S.A.
- Astilleros Calbuco S.A.

---

## Arquitectura del Agente (EP2)

```
Usuario → Interfaz ipywidgets (panel visual) → AgentExecutor (ReAct) → LLM gpt-4o-mini
                                                        │
                              ┌─────────────────────────┴─────────────────────────┐
                              │                                                   │
                           Memoria                                          Herramientas
                           ├─ Corto plazo:      ├─ consultar_politicas_invermar  (RAG / FAISS)
                           │  WindowHistory     ├─ calcular_indemnizacion        (Art. 163 CT)
                           │  (6 mensajes)      ├─ calcular_vacaciones_proporc.  (Art. 73 CT)
                           └─ Largo plazo:      ├─ clasificar_consulta           (razonamiento)
                              FAISS vectorstore └─ enviar_resumen_correo         (Gmail SMTP)
                              (8 políticas)
                                                        │
                                               Al final de cada respuesta
                                               el agente envía resumen por
                                               correo al trabajador (Gmail)
```

---

## Estructura del Repositorio

```
ChatBot_Invermar/
│
├── EP2_Agente_Invermar.ipynb      ← Agente funcional EP2 (RA2)
├── EP2_Informe_Tecnico.md         ← Informe técnico EP2 (5 páginas)
│
├── IL1.1 - Primera_Entrega.ipynb  ← Chatbot base con memoria y widgets
├── IL1.2 - Segunda_Entrega.ipynb  ← Ingeniería de prompts (zero-shot, few-shot)
├── IL1.3 - Tercera_Entrega.ipynb  ← RAG con FAISS y embeddings
├── IL1.4 - Cuarta_Entrega.ipynb   ← LangSmith + evaluador LLM-as-a-Judge
├── ChatBot.ipynb                  ← Versión integrada del chatbot RA1
│
├── requirements.txt               ← Dependencias del proyecto
└── README.md                      ← Este archivo
```

---

## Requisitos

| Herramienta | Versión mínima |
|---|---|
| Python | 3.11+ |
| uv (gestor de paquetes) | 0.4+ |

### Dependencias principales

```
langchain>=1.0.0
langchain-openai>=0.3.0
langchain-community>=0.3.0
langchain-classic>=1.0.0
langchain-text-splitters>=0.3.0
langsmith>=0.3.0
faiss-cpu>=1.7.0
openai>=1.0.0
python-dotenv>=1.0.0
ipywidgets>=8.0.0
mistune>=3.0.0
jupyter>=1.0.0
```

---

## Instalación y Configuración

### 1. Clonar el repositorio

```bash
git clone https://github.com/KanninoX/ChatBot_Invermar.git
cd ChatBot_Invermar
```

### 2. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 3. Configurar variables de entorno

Crear un archivo `.env` en la raíz del proyecto:

```env
# API para modelos LLM y embeddings (GitHub Models)
GITHUB_TOKEN=github_pat_xxxxxxxxxxxxxxxxxxxx
OPENAI_BASE_URL=https://models.inference.ai.azure.com

# Trazabilidad con LangSmith (opcional pero recomendado)
LANGSMITH_TRACING=true
LANGSMITH_API_KEY=lsv2_pt_xxxxxxxxxxxxxxxxxxxx
LANGSMITH_PROJECT=InvermarBot

# Correo automático al trabajador (Gmail)
GMAIL_SENDER=tu_cuenta@gmail.com
GMAIL_APP_PASSWORD=xxxx xxxx xxxx xxxx
```

> **GITHUB_TOKEN:** GitHub → Settings → Developer settings → Personal access tokens  
> **LANGSMITH_API_KEY:** [smith.langchain.com](https://smith.langchain.com)  
> **GMAIL_APP_PASSWORD:** Google Account → Seguridad → Contraseñas de aplicaciones → [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)

### 4. Iniciar Jupyter

```bash
jupyter lab
```

---

## Ejecución del Agente (EP2)

1. Abrir `EP2_Agente_Invermar.ipynb`
2. Seleccionar el kernel: `chatbot-invermar` (Python 3.11)
3. Ejecutar todas las celdas en orden: `Kernel → Restart & Run All`
4. El **panel de chat interactivo** aparece al final de la Celda 6

```python
# También disponible programáticamente:
nueva_sesion()
chat("¿Cuánto es el aguinaldo de Fiestas Patrias?")
chat("Llevo 9 años y mi sueldo es $800.000. ¿Cuánto me corresponde?")
chat("Trabajo en Astilleros Calbuco hace 6 años, me voy en agosto, ¿qué me corresponde?")
```

---

## Herramientas del Agente (5 tools)

### `consultar_politicas_invermar(consulta)`
Busca en la base de conocimiento interna (FAISS) mediante similitud semántica. Cubre: aguinaldos, bonos, vacaciones, finiquitos, CCAF, fechas de pago, licencias médicas.

### `calcular_indemnizacion(anios_servicio, remuneracion_mensual)`
Calcula la indemnización por años de servicio según el **Art. 163 del Código del Trabajo**:
- `anios_servicio` × `remuneracion_mensual` (tope: 11 años)
- Incluye indemnización sustitutiva de aviso previo (Art. 162 CT)

### `calcular_vacaciones_proporcionales(meses_trabajados, remuneracion_mensual)`
Calcula el valor de vacaciones proporcionales al finiquito según el **Art. 73 CT**:
- Fórmula: `(15 días × meses_trabajados / 12) × (remuneracion_mensual / 30)`

### `clasificar_consulta(consulta)`
Categoriza la consulta en: `LIQUIDACION`, `VACACIONES`, `BENEFICIOS`, `FINIQUITO`, `NORMATIVA` u `OTRO`. Deriva al área correspondiente de RRHH.

### `enviar_resumen_correo(resumen)`
Envía automáticamente un resumen de la consulta al trabajador por correo electrónico (Gmail SMTP). El agente la llama al final de **cada interacción** como último paso del ciclo ReAct.

---

## Interfaz Visual

El agente incluye un panel de chat con estética corporativa de Invermar:

- **Header** azul `#00205B` con título dorado `#C9B777`
- **Burbujas de chat**: mensajes del trabajador a la derecha (azul), respuestas del agente a la izquierda (borde dorado), con Markdown renderizado
- **Barra de estado** en tiempo real (⏳ procesando / ✅ listo)
- **Botón Nueva sesión** para reiniciar el historial

---

## Ejemplos de Interacción

```
🧑 Trabajador: ¿Cuándo me depositan el sueldo?
🤖 Agente:     El pago de remuneraciones en Invermar se realiza el último día
               hábil de cada mes para todas las empresas del grupo.

🧑 Trabajador: ¿Y si ese día es feriado?
🤖 Agente:     En ese caso el pago se anticipa al día hábil anterior.
               [El agente recuerda el contexto sin que el trabajador repita información]
```

```
🧑 Trabajador: Llevo 7 años, gano $700.000. Me van a despedir, ¿cuánto me dan?
🤖 Agente:     CÁLCULO DE INDEMNIZACIÓN (Art. 163 CT)
               ────────────────────────────────────────
               Años trabajados  : 7 años
               Remuneración base: $700.000
               Indemn. años     : $4.900.000  (7 × $700.000)
               Aviso previo     : $700.000    (Art. 162)
               ────────────────────────────────────────
               TOTAL ESTIMADO   : $5.600.000
               ⚠️ No incluye vacaciones proporcionales.

📧 [El agente envía automáticamente el resumen al correo del trabajador]
```

---

## Observabilidad

Si `LANGSMITH_TRACING=true` está configurado, cada ejecución se registra en LangSmith con:
- Tokens consumidos y latencia por llamada
- Árbol de herramientas invocadas (ciclo ReAct completo)
- Trazas del patrón Thought → Action → Observation
- Evaluaciones automáticas (LLM-as-a-Judge, IL1.4)

Dashboard: [smith.langchain.com](https://smith.langchain.com)

---

## Normativa de Referencia

| Artículo | Contenido |
|---|---|
| Art. 67 CT | Vacaciones legales (15 días hábiles anuales) |
| Art. 68 CT | Vacaciones progresivas (1 día cada 3 años, desde 10 años) |
| Art. 73 CT | Vacaciones proporcionales al finiquito |
| Art. 162 CT | Aviso previo (30 días o indemnización sustitutiva) |
| Art. 163 CT | Indemnización por años de servicio (1 mes/año, tope 11) |
| Art. 172 CT | Base de cálculo de indemnizaciones (sueldo + promedio variables) |

---

## Roadmap

- [x] Chatbot RAG con memoria conversacional (RA1)
- [x] Ingeniería de prompts (zero-shot, few-shot, roles)
- [x] Observabilidad con LangSmith + evaluador LLM-as-a-Judge
- [x] Agente funcional con 5 herramientas y memoria dual (RA2 / EP2)
- [x] Interfaz visual con ipywidgets (colores corporativos Invermar)
- [x] Envío automático de resumen por correo al trabajador (Gmail SMTP)
- [ ] Integración con API BUK para consultas de liquidaciones en tiempo real
- [ ] Autenticación por RUT del colaborador
- [ ] Persistencia del historial en Redis
- [ ] Soporte para Google Chat / Microsoft Teams

---

## Seguridad

- El archivo `.env` **nunca** debe subirse a GitHub (está en `.gitignore`).
- El agente no almacena datos personales entre sesiones.
- Las consultas son procesadas de forma anonimizada.
- El acceso debe restringirse a la red interna de Invermar.

---

## Autor

| Campo | Detalle |
|---|---|
| Área responsable | Remuneraciones — Invermar S.A. |
| Curso | ISY0101 — Ingeniería de Soluciones con IA (DuocUC, 2025) |
| Plataforma HR | BUK |
| Repositorio | GitHub (`KanninoX/ChatBot_Invermar`) |

---

*Invermar S.A. — Área de Remuneraciones · Uso interno*
