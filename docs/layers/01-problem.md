# Capa 01: Problema

## El Fundamento de Todo: ¿Por Qué Existe Este Sistema?

---

## Introducción

La capa de Problema es el **punto de partida** de toda especificación KDD. Responde a la pregunta más fundamental: **¿Por qué existe este sistema?**

Aquí no hablamos de código, ni de tecnología, ni siquiera de software. Hablamos de **problemas humanos**, de **contexto de negocio**, de **objetivos medibles**. Un sistema que no tiene claro su "por qué" es un sistema condenado a la deriva.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   01-Problem    →   02-Domain   →   03-Capabilities   →   04-Interaction    │
│                                                                              │
│   "¿POR QUÉ       "¿Qué            "¿Qué puede          "¿Cómo lo          │
│    EXISTE?"        existe?"          hacer?"               usan?"            │
│                                                                              │
│   ──────────────────────────────────────────────────────────────────────────│
│                                                                              │
│   MOTIVACIÓN      Conceptual       Funcional            Experiencial        │
│   CONTEXTO        (entidades)      (operaciones)        (personas)          │
│   OBJETIVOS                                                                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## La Filosofía: El Problema Antes que la Solución

### Por Qué Esta Capa es Primera

En el desarrollo tradicional, es común saltar directamente al código. "Necesitamos una app que haga X" → empezamos a programar. Esto genera sistemas que:

- Resuelven problemas que nadie tiene
- No miden si están teniendo éxito
- Acumulan features sin dirección clara
- Son difíciles de justificar ante stakeholders

KDD invierte esto:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   ENFOQUE TRADICIONAL              ENFOQUE KDD                              │
│   ───────────────────              ───────────                              │
│                                                                              │
│   "Hagamos una app"                "¿Qué problema resolvemos?"              │
│         ↓                                   ↓                               │
│   Empezar a codear                 "¿Para quién?"                           │
│         ↓                                   ↓                               │
│   "¿Qué debería hacer?"            "¿Cómo medimos éxito?"                   │
│         ↓                                   ↓                               │
│   Features sin fin                 PRD claro y acotado                      │
│         ↓                                   ↓                               │
│   "¿Esto era lo que querían?"      Dominio → Capacidades → UI → Tests       │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### El Problema como Ancla

Cuando las cosas se complican (y siempre se complican), la capa de Problema es tu **ancla**:

- "¿Deberíamos añadir esta feature?" → ¿Resuelve el problema definido?
- "¿Esta arquitectura es correcta?" → ¿Cumple los objetivos de negocio?
- "¿Priorizamos X o Y?" → ¿Cuál acerca más a las métricas de éxito?

---

## Los Artefactos de la Capa de Problema

### 1. PRD (Product Requirements Document)

El PRD es el documento central que define la visión del producto.

#### Estructura Recomendada

```markdown
# PRD: [Nombre del Producto]

## El Problema
¿Qué dolor o necesidad existe? ¿Por qué es importante resolverlo ahora?

## Usuarios Objetivo
¿Quiénes son? ¿Qué características tienen? ¿Cuántos son?

## Objetivos de Negocio
¿Qué queremos lograr como organización?

## Métricas de Éxito
¿Cómo sabremos que el producto funciona?

## Lo Que NO Es Este Producto
Límites claros para evitar scope creep.

## Restricciones
Técnicas, legales, de tiempo, de presupuesto.

## Stakeholders
¿Quiénes tienen interés en este producto?
```

#### Ejemplo: Six Hats App

```markdown
# PRD: Six Hats App

## El Problema

Los equipos de trabajo enfrentan decisiones complejas donde:
- Las perspectivas individuales dominan la discusión
- Las emociones se mezclan con los hechos
- No hay estructura para explorar alternativas
- Las conclusiones son sesgadas o incompletas

El Método de los Seis Sombreros de Edward de Bono ofrece una solución,
pero su aplicación manual es:
- Difícil de facilitar correctamente
- Limitada por la disponibilidad de participantes
- Inconsistente en la calidad de las aportaciones

## Usuarios Objetivo

**Primario**: Profesionales que toman decisiones estratégicas
- Product Managers definiendo roadmaps
- Emprendedores evaluando oportunidades
- Consultores analizando problemas de clientes
- Equipos de innovación explorando ideas

**Características**:
- Familiaridad básica con metodologías de pensamiento
- Disposición a pagar por herramientas de productividad
- Trabajan individualmente o en equipos pequeños

## Objetivos de Negocio

1. Validar que el concepto de "Personas Sintéticas" aporta valor
2. Conseguir 1,000 usuarios activos en 6 meses
3. Alcanzar MRR de $5,000 en 12 meses
4. Tasa de retención > 40% al mes 3

## Métricas de Éxito

| Métrica | Objetivo | Frecuencia |
|---------|----------|------------|
| Sesiones completadas | 5,000/mes | Mensual |
| NPS | > 40 | Trimestral |
| Tiempo promedio en análisis | < 15 min | Semanal |
| % usuarios que repiten | > 60% | Mensual |

## Lo Que NO Es Este Producto

- No es un reemplazo de reuniones presenciales
- No es una herramienta de colaboración en tiempo real (v1)
- No es un sistema de gestión de proyectos
- No almacena documentos o archivos externos

## Restricciones

- **Técnicas**: Stack moderno (Bun, Elysia, Next.js)
- **Legales**: GDPR compliance desde día 1
- **Tiempo**: MVP en 3 meses
- **Presupuesto**: Bootstrapped, costos mínimos de infra
```

---

### 2. Contexto de Negocio

Documento que captura el entorno en el que opera el sistema.

#### Qué Incluye

```markdown
# Contexto de Negocio

## Situación Actual
¿Cómo se resuelve el problema hoy? ¿Qué herramientas existen?

## Competencia
¿Quién más está en este espacio? ¿Cómo nos diferenciamos?

## Tendencias del Mercado
¿Qué cambios están ocurriendo que hacen esto relevante?

## Sistemas Relacionados
¿Con qué otros sistemas debe integrarse o coexistir?

## Modelo de Negocio
¿Cómo generamos ingresos? ¿Cuál es la estructura de costos?
```

---

### 3. ADRs (Architecture Decision Records)

Los ADRs documentan decisiones importantes y su justificación.

#### Estructura de un ADR

```markdown
---
id: ADR-001
title: Elección de Backend Framework
status: accepted  # proposed | accepted | deprecated | superseded
date: 2024-01-15
deciders: ["@juan", "@maria"]
---

# ADR-001: Elección de Backend Framework

## Contexto
Necesitamos elegir un framework para el backend de la aplicación.
Requisitos: TypeScript, alta performance, developer experience.

## Opciones Consideradas

### Opción A: Express.js
- **Pros**: Maduro, gran ecosistema, mucha documentación
- **Cons**: Verbose, sin tipos nativos, middleware hell

### Opción B: Fastify
- **Pros**: Rápido, buen soporte TypeScript, schema validation
- **Cons**: Menos maduro, ecosistema más pequeño

### Opción C: Elysia
- **Pros**: Diseñado para Bun, end-to-end type safety, muy rápido
- **Cons**: Nuevo, ecosistema pequeño, menos documentación

## Decisión
Elegimos **Elysia** porque:
1. Estamos usando Bun como runtime, Elysia está optimizado para él
2. Type safety end-to-end con Eden elimina errores de tipos
3. Performance excepcional para el modelo de Personas Sintéticas

## Consecuencias

### Positivas
- DX superior con tipos compartidos frontend/backend
- Mejor performance para llamadas a LLMs concurrentes

### Negativas
- Curva de aprendizaje para nuevos desarrolladores
- Menos recursos de aprendizaje disponibles

### Riesgos
- Framework joven, posibles breaking changes
- Mitigación: fijar versiones, seguir changelog

## Referencias
- [Elysia Documentation](https://elysiajs.com)
- [Benchmark comparisons](https://...)
```

#### Cuándo Crear un ADR

- Elección de tecnología o framework
- Decisión de arquitectura (monolito vs microservicios)
- Cambio de proveedor o servicio externo
- Adopción de un patrón o práctica
- Cualquier decisión que alguien preguntará "¿por qué hicimos esto?"

---

## El Lenguaje Ubicuo Nace Aquí

Aunque los términos del dominio se definen formalmente en la capa 02-Domain, **comienzan a emerger en el PRD**.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   En el PRD mencionamos:                                                     │
│   - "Sesiones de análisis"                                                   │
│   - "Personas Sintéticas"                                                    │
│   - "Sombreros" (perspectivas)                                               │
│   - "Retos" (problemas a analizar)                                           │
│                                                                              │
│   Estos términos se formalizan en 02-Domain como Entidades:                 │
│   - [[Sesión]]                                                               │
│   - [[Persona Sintética]]                                                    │
│   - [[Sombrero]]                                                             │
│   - [[Reto]]                                                                 │
│                                                                              │
│   El PRD es donde el vocabulario compartido NACE.                           │
│   El Domain es donde se DEFINE formalmente.                                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Relación con Otras Capas

La capa de Problema es **referenciada** por todas las demás, pero **no referencia** a ninguna:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│   01-Problem                                                                 │
│       │                                                                      │
│       │  define contexto para                                                │
│       ▼                                                                      │
│   02-Domain ──────► "¿Por qué existe [[Reto]]?"                             │
│       │              → Ver PRD: "Los equipos enfrentan decisiones..."       │
│       │                                                                      │
│       │  justifica                                                           │
│       ▼                                                                      │
│   03-Capabilities ► "¿Por qué CMD-GenerateAnalysis?"                        │
│       │              → Ver PRD: "Métricas: tiempo promedio < 15 min"        │
│       │                                                                      │
│       │  guía                                                                │
│       ▼                                                                      │
│   04-Interaction ─► "¿Por qué este flujo de UX?"                            │
│       │              → Ver PRD: "Usuarios: profesionales ocupados"          │
│       │                                                                      │
│       │  valida                                                              │
│       ▼                                                                      │
│   05-Verification ► "¿Qué criterios de aceptación?"                         │
│                      → Ver PRD: "Métricas de éxito"                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Estructura de Carpetas

```
/specs/01-problem/
├── PRD.md                    # Documento principal del producto
├── context.md                # Contexto de negocio (opcional)
└── /decisions/
    ├── ADR-001-Backend.md
    ├── ADR-002-Database.md
    └── ADR-003-Auth.md
```

---

## Checklist: Definiendo el Problema

Antes de avanzar a otras capas, asegúrate de tener:

- [ ] **Problema claro**: ¿Qué dolor resolvemos? ¿Por qué ahora?
- [ ] **Usuarios definidos**: ¿Quiénes son? ¿Qué características tienen?
- [ ] **Objetivos de negocio**: ¿Qué queremos lograr como organización?
- [ ] **Métricas de éxito**: ¿Cómo medimos si funciona?
- [ ] **Límites claros**: ¿Qué NO es este producto?
- [ ] **Restricciones**: ¿Qué limitaciones tenemos?
- [ ] **ADRs iniciales**: ¿Qué decisiones tecnológicas tomamos?

---

## Anti-patrones a Evitar

### 1. PRD como Lista de Features

```markdown
# ❌ INCORRECTO
## Features
- Login con Google
- Dashboard con gráficas
- Exportar a PDF
- Modo oscuro

# ✅ CORRECTO
## El Problema
Los equipos toman decisiones sesgadas porque...

## Métricas de Éxito
- NPS > 40
- Tiempo de análisis < 15 min
```

### 2. Objetivos Vagos

```markdown
# ❌ INCORRECTO
## Objetivos
- Hacer una app útil
- Que la gente la use
- Ganar dinero

# ✅ CORRECTO
## Objetivos
- 1,000 usuarios activos en 6 meses
- MRR de $5,000 en 12 meses
- Retención > 40% al mes 3
```

### 3. Ausencia de "Lo Que NO Es"

```markdown
# ❌ INCORRECTO
(Sin sección de límites)

# ✅ CORRECTO
## Lo Que NO Es Este Producto
- No es un reemplazo de reuniones presenciales
- No es colaboración en tiempo real (v1)
- No gestiona proyectos
```

### 4. Decisiones Sin Documentar

```markdown
# ❌ INCORRECTO
"Usamos Elysia porque sí"

# ✅ CORRECTO
ADR-001 con:
- Contexto
- Opciones consideradas
- Pros/cons de cada una
- Decisión y justificación
- Consecuencias
```

---

## Cuándo Actualizar Esta Capa

La capa de Problema **cambia poco** una vez establecida. Actualízala cuando:

1. **Pivot de producto**: El problema o los usuarios cambian fundamentalmente
2. **Nuevos objetivos**: Los OKRs de la organización evolucionan
3. **Decisiones arquitectónicas**: Nuevos ADRs por cambios significativos
4. **Post-mortem**: Aprendizajes que cambian la visión

> **Regla de oro**: Si cambias el PRD frecuentemente, probablemente no tenías claro el problema desde el inicio.

---

## Resumen

La capa de Problema en KDD:

1. **Es el fundamento**: Todo lo demás deriva de aquí
2. **Es estable**: Cambia poco una vez definida
3. **Es el "por qué"**: Justifica cada decisión posterior
4. **Es medible**: Define métricas de éxito concretas
5. **Es limitante**: Establece lo que NO es el producto
6. **Documenta decisiones**: ADRs capturan el razonamiento

> **"Un sistema sin un problema claro es una solución buscando un problema. KDD empieza por el problema."**

---

## Artefactos Relacionados

- [[prd.template]] - Template para PRD
- [[adr.template]] - Template para ADRs
- [[02-domain]] - La siguiente capa: Dominio
- [[Introducción a KDD]] - Visión general de KDD

---

*Última actualización: 2024-12-14*
