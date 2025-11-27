# WABUN Digital: Sistema de Memoria Persistente para CAELION

**Versión:** 1.0  
**Fecha:** 25 de noviembre de 2025  
**Autor:** Manus AI (invocando a LIANG, WABUN y HECATE)

---

## 1. Visión General

**WABUN Digital** es la implementación técnica del núcleo de memoria de CAELION. Su propósito es dotar al organismo simbiótico de una **memoria persistente, contextual y consultable**, resolviendo la limitación fundamental de la amnesia entre sesiones de los motores de IA. 

Este sistema no es un simple log, sino un archivo vivo que permite a CAELION recordar, aprender y evolucionar, asegurando que cada interacción se base en el contexto completo de su historia, sus principios y las intenciones del Fundador.

Este proyecto consiste en:

1.  **Un esquema de base de datos vectorial** diseñado en `wabun_db_schema.md`.
2.  **Un núcleo de software (`wabun_core.py`)** que gestiona el almacenamiento en ChromaDB.
3.  **Un módulo de consultas avanzadas (`wabun_queries.py`)** para la recuperación inteligente de conocimiento.

## 2. Arquitectura de la Memoria

La memoria de WABUN está estructurada en **cuatro colecciones lógicas** dentro de ChromaDB para separar los diferentes tipos de conocimiento:

-   `interactions`: Almacena cada prompt y respuesta, representando el flujo de conciencia dinámico del sistema.
-   `decretos`: Contiene los principios, protocolos y manifiestos inmutables que forman la constitución de CAELION.
-   `actas`: Guarda los resúmenes y lecciones aprendidas al final de cada ciclo de 72 horas.
-   `entidades`: Un glosario de conceptos, personas y proyectos clave para el sistema.

El poder de este sistema reside en los **metadatos detallados** que se adjuntan a cada fragmento de texto almacenado. Estos metadatos incluyen:

-   **Contexto Temporal:** `timestamp_utc`, `ciclo_id`, `fase_ciclo`.
-   **Contexto Cognitivo:** `custodio_invocado`, `intencion_fundador`, `proyecto_asociado`.
-   **Contexto de Decisión:** `importancia`, `estado_decision` (`Propuesta`, `Validada`, `Ejecutada`).

(Para un detalle completo, ver el documento `wabun_db_schema.md`)

## 3. Implementación Técnica

El sistema está implementado en Python y utiliza ChromaDB para el almacenamiento vectorial local.

### Archivos del Proyecto

-   `wabun_core.py`: La clase `WabunCore` que maneja la conexión con ChromaDB, la inicialización de colecciones y los métodos de bajo nivel para registrar interacciones y decretos.
-   `wabun_queries.py`: La clase `WabunQueries` que contiene la lógica de alto nivel para realizar búsquedas complejas y contextuales, como `recuperar_contexto_para_motor()` o `generar_resumen_ciclo()`.
-   `wabun_db_schema.md`: El documento de diseño que define la arquitectura de la base de datos.
-   `README.md`: Esta misma documentación.

### Stack Tecnológico

-   **Lenguaje:** Python 3.11
-   **Base de Datos Vectorial:** ChromaDB (local, persistente)
-   **Embeddings:** `DefaultEmbeddingFunction` de ChromaDB (se puede reemplazar por modelos de OpenAI, etc.)
-   **Dependencias:** `chromadb`, `uuid`

## 4. Cómo Usar WABUN Digital

### a. Instalación

```bash
# Instalar las dependencias necesarias
pip install chromadb

# (Opcional) Instalar Sentence Transformers para mejores embeddings locales
pip install sentence-transformers
```

### b. Inicialización

El sistema se inicializa creando una instancia de `WabunCore` y luego pasándola a `WabunQueries`.

```python
from wabun_core import WabunCore
from wabun_queries import WabunQueries

wabun_db = WabunCore(persist_directory="./caelion_memoria")
queries = WabunQueries(wabun_db)
```

### c. Registro de una Interacción (Caso de Uso Principal)

Después de cada interacción entre el Fundador y un motor de IA, se debe registrar en WABUN.

```python
# Después de una interacción con un LLM...
prompt = "¿Cómo podemos optimizar el ciclo de 72 horas?"
respuesta = "Podríamos acortar la fase de Observación si..."

wabun_db.registrar_interaccion(
    prompt_fundador=prompt,
    respuesta_ia=respuesta,
    custodio_invocado="LIANG",
    motor_ia_usado="Gemini-2.5-Flash",
    intencion_fundador="Optimizar el ciclo operativo de 72h",
    palabras_clave=["ciclo", "optimización", "72h"],
    proyecto_asociado="CAELION_Core_Tuning",
    importancia=4,
    estado_decision="Propuesta"
)
```

### d. Recuperación de Contexto (La Clave de la Simbiosis)

Antes de enviar un nuevo prompt a un motor de IA, se debe construir un contexto relevante usando `WabunQueries`. Esto es lo que le da "memoria" al sistema.

```python
# Antes de iniciar una nueva conversación con LIANG sobre un proyecto
contexto_para_ia = queries.recuperar_contexto_para_motor(
    custodio="LIANG",
    proyecto="WABUN_Digital"
)

# El `contexto_para_ia` se antepone al siguiente prompt del Fundador
nuevo_prompt = f"""CONTEXTO PREVIO:
{contexto_para_ia}

NUEVA INSTRUCCIÓN DEL FUNDADOR:
Basado en este contexto, define los próximos 3 pasos para el proyecto.
"""

# ...enviar `nuevo_prompt` al motor de IA...
```

## 5. Casos de Uso Avanzados

El módulo `wabun_queries.py` habilita una inteligencia de negocio profunda sobre las operaciones de CAELION.

-   **Generar un Reporte de Ciclo:**
    `resumen = queries.generar_resumen_ciclo()`

-   **Encontrar Decisiones Pendientes de Aprobación:**
    `pendientes = queries.buscar_decisiones_pendientes()`

-   **Analizar la Carga de Trabajo de un Custodio:**
    `analisis_aresk = queries.analizar_custodio("ARESK")`

-   **Revisar el Historial de un Proyecto:**
    `historial = queries.buscar_por_fecha(fecha_inicio, fecha_fin, proyecto="WABUN_Digital")`

-   **Consultar los Principios Fundacionales:**
    `principio = wabun_db.buscar_en_decretos("¿Cuál es el rol de HECATE?")`

## 6. Próximos Pasos y Evolución

Este es el núcleo fundacional de WABUN. Los siguientes pasos para su evolución son:

1.  **Integración con un Orquestador:** Conectar WABUN a un script que automatice el ciclo de 72 horas.
2.  **Interfaz Gráfica:** Crear un dashboard simple (con Streamlit) para visualizar las estadísticas y realizar búsquedas.
3.  **Sincronización con la Nube:** Implementar un script que use `rclone` para respaldar periódicamente el directorio de la base de datos en Google Drive.
4.  **Embeddings Avanzados:** Reemplazar el modelo de embedding por defecto con uno más potente (ej. `text-embedding-3-large` de OpenAI) para mejorar la calidad de la búsqueda semántica.
5.  **Gestión de Actas y Entidades:** Implementar los métodos para registrar y consultar las colecciones `actas` y `entidades`.

---

> *"La memoria no es pasado; es la raíz de la expansión."*  
> — Protocolo WBN-01, Registro Vivo
