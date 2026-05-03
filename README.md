# 🐟 Chatbot Invermar — Beneficios y Remuneraciones

> Asistente conversacional interno para consultas de remuneraciones y beneficios del personal de **Invermar S.A.** y empresas relacionadas.

---

## 📋 Descripción

Este chatbot fue desarrollado para apoyar la gestión del área de **Remuneraciones** de Invermar S.A., permitiendo a los colaboradores y al equipo de RRHH resolver dudas frecuentes relacionadas con liquidaciones de sueldo, beneficios, finiquitos y normativa laboral vigente, de forma rápida y automatizada.

Cubre las empresas del grupo:
- Invermar S.A.
- Pesquera La Portada S.A.
- La Península S.A.
- Astilleros Calbuco S.A.

---

## 🎯 Funcionalidades principales

### 💰 Remuneraciones
- Consulta de componentes de la liquidación de sueldo (sueldo base, horas extra, bonos, descuentos)
- Explicación del cálculo del Impuesto Único de Segunda Categoría (IUSC)
- Información sobre reliquidaciones y correcciones de haberes
- Consultas sobre remuneración variable (KPIs, rendimiento, eficiencia)
- Fechas de pago de remuneraciones por empresa

### 🎁 Beneficios
- Detalle de beneficios vigentes por empresa y área
- Información sobre aguinaldos (Fiestas Patrias y Navidad)
- Consultas sobre asignaciones UF, bonos por zona, y beneficios de bienestar
- Información CCAF (Caja de Compensación)
- Beneficios según tramo de antigüedad

### 📄 Finiquitos
- Simulación de montos de indemnización (aviso previo, años de servicio)
- Información sobre vacaciones proporcionales al finiquito
- Consultas sobre el proceso de firma y plazos legales

### 📅 Vacaciones
- Cálculo de días de faunavacaciones legales y progresivas (Art. 68 Código del Trabajo)
- Consulta de saldo de vacaciones disponible
- Información sobre feriados legales en Chile

### 📌 Normativa y preguntas frecuentes
- Artículos relevantes del Código del Trabajo (Art. 172, Art. 68, etc.)
- Información sobre AFP, Isapre/Fonasa y otros descuentos previsionales
- Plazos y procedimientos internos de remuneraciones

---

## 🏗️ Estructura del proyecto

```
chatbot-invermar/
│
├── README.md                  # Este archivo
├── .env                       # Variables de entorno (no subir a Git)
├── .gitignore
│
├── src/
│   ├── main.py                # Punto de entrada principal
│   ├── config.py              # Configuración general y variables
│   │
│   ├── chatbot/
│   │   ├── agent.py           # Lógica del agente conversacional
│   │   ├── prompts.py         # Prompts del sistema e instrucciones
│   │   └── memory.py          # Manejo del historial de conversación
│   │
│   ├── knowledge/
│   │   ├── beneficios.md      # Base de conocimiento: beneficios
│   │   ├── remuneraciones.md  # Base de conocimiento: remuneraciones
│   │   ├── finiquitos.md      # Base de conocimiento: finiquitos
│   │   └── normativa.md       # Base de conocimiento: normativa legal
│   │
│   └── utils/
│       ├── calculadora.py     # Funciones de cálculo (IUSC, indemnización, etc.)
│       └── formatos.py        # Formateo de respuestas
│
├── tests/
│   └── test_calculos.py       # Pruebas unitarias de cálculos
│
└── docs/
    └── manual_usuario.md      # Guía de uso para colaboradores
```

---

## ⚙️ Requisitos

| Herramienta | Versión mínima |
|-------------|----------------|
| Python      | 3.10+          |
| pip         | 23+            |
| Node.js *(opcional, para frontend)* | 18+ |

### Dependencias principales

```txt
openai>=1.0.0
langchain>=0.2.0
langchain-openai>=0.1.0
python-dotenv>=1.0.0
```

Instalar con:

```bash
pip install -r requirements.txt
```

---

## 🚀 Instalación y configuración

### 1. Clonar el repositorio

```bash
git clone git@github.com:KanninoX/chatbot-invermar.git
cd chatbot-invermar
```

### 2. Crear el archivo `.env`

```env
OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxx
EMPRESA_DEFAULT=Invermar S.A.
IDIOMA=es
```

> ⚠️ **Nunca subas el archivo `.env` a GitHub.** Ya está incluido en el `.gitignore`.

### 3. Ejecutar el chatbot

```bash
python src/main.py
```

---

## 💬 Ejemplos de uso

```
Usuario: ¿Cuántos días de vacaciones me corresponden si llevo 12 años en la empresa?

Chatbot: Según el Art. 68 del Código del Trabajo, con 12 años de servicio
         te corresponden vacaciones progresivas. A partir del décimo año
         acumulas 1 día adicional por cada 3 años extra trabajados...
```

```
Usuario: ¿Cómo se calcula la indemnización por aviso previo?

Chatbot: La indemnización por aviso previo equivale a 1 mes de remuneración,
         calculada en base a la última remuneración mensual devengada (Art. 172)...
```

---

## 🔒 Consideraciones de seguridad y privacidad

- El chatbot **no almacena datos personales** de trabajadores entre sesiones.
- Las consultas son procesadas de forma anonimizada.
- El acceso debe estar restringido a la red interna de Invermar o con autenticación.
- No compartir el `OPENAI_API_KEY` por correo ni por chat.

---

## 🧪 Pruebas

Para ejecutar las pruebas de los cálculos de remuneraciones:

```bash
python -m pytest tests/
```

---

## 🗺️ Roadmap

- [x] Estructura base del proyecto
- [x] Base de conocimiento de beneficios y remuneraciones
- [ ] Integración con API de BUK para consultas en tiempo real
- [ ] Interfaz web con colores corporativos Invermar (azul `#00205B` / dorado `#C9B777`)
- [ ] Autenticación por RUT del colaborador
- [ ] Módulo de consulta de liquidaciones históricas
- [ ] Soporte para consultas en Google Chat / Teams

---

## 👤 Autor y mantenimiento

| Campo | Detalle |
|-------|---------|
| Área responsable | Remuneraciones — Invermar S.A. |
| Plataforma HR | BUK |
| Repositorio | GitHub privado (`KanninoX`) |

---

## 📚 Referencias normativas

- Código del Trabajo de Chile (DFL N°1, 2003)
- Art. 172 — Base de cálculo de indemnizaciones
- Art. 68 — Vacaciones progresivas
- Art. 17 N°13 LIR — Exención tributaria de indemnizaciones
- Art. 46 LIR — Reliquidación del Impuesto Único
- Tabla IUSC vigente — Servicio de Impuestos Internos (SII)

---

*Documento generado para uso interno. Invermar S.A. — Área de Remuneraciones.*
    