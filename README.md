# Laboratorio Práctico - Sistema de Evaluación Técnica para Desarrolladores
## Integración de POO con C# y Web API con .NET Core

### Descripción del Proyecto
Los practicantes desarrollarán un **Sistema de Evaluación Técnica** que permita crear, administrar y ejecutar evaluaciones de conocimientos para candidatos a desarrolladores en diferentes lenguajes de programación y roles técnicos.

### Objetivos de Aprendizaje
- Aplicar principios de POO (Herencia, Polimorfismo, Encapsulación)
- Crear una Web API RESTful con .NET Core
- Implementar autenticación JWT con roles diferenciados
- Diseñar y gestionar base de datos con SQL Server
- Utilizar Dapper para acceso a datos
- Documentar la API con Swagger
- Crear procedimientos almacenados para reportes y estadísticas

### Arquitectura del Sistema

#### Entidades Principales (Aplicando POO)
```
Usuario (Clase Base)
├── Reclutador (Herencia) - Crea y gestiona evaluaciones
├── Candidato (Herencia) - Presenta evaluaciones
└── Administrador (Herencia) - Gestiona el sistema

Pregunta (Clase Base)
├── PreguntaOpcionMultiple (Herencia)
├── PreguntaCodigo (Herencia)
├── PreguntaVerdaderoFalso (Herencia)
└── PreguntaAbierta (Herencia)

Evaluacion
Respuesta
TecnologiaLenguaje
Rol
```

### Diseño de Base de Datos

#### Tablas Requeridas:
1. **Usuarios**
   - Id, Nombre, Apellido, Email, Password, TipoUsuario, Empresa, FechaRegistro, Activo

2. **TecnologiasLenguajes**
   - Id, Nombre, Descripcion, Categoria (Frontend, Backend, Database, DevOps, etc.)

3. **Roles**
   - Id, Nombre, Descripcion, Nivel (Junior, Semi-Senior, Senior)

4. **Evaluaciones**
   - Id, Titulo, Descripcion, ReclutadorId, TecnologiaId, RolId, DuracionMinutos, FechaCreacion, Activa

5. **Preguntas**
   - Id, EvaluacionId, TipoPercenajePonderacion, Titulo, Descripcion, TipoPregunta, PuntajeMaximo

6. **OpcionesPregunta**
   - Id, PreguntaId, TextoOpcion, EsCorrecta

7. **SesionesEvaluacion**
   - Id, EvaluacionId, CandidatoId, FechaInicio, FechaFin, Estado, PuntajeTotal, PorcentajeAprobacion

8. **Respuestas**
   - Id, SesionId, PreguntaId, RespuestaTexto, OpcionSeleccionada, Puntaje, TiempoRespuesta

### Estructura del Proyecto

```
EvaluacionTecnicaAPI/
├── Models/
│   ├── Entities/
│   │   ├── Usuario.cs (Clase base)
│   │   ├── Reclutador.cs
│   │   ├── Candidato.cs
│   │   ├── Administrador.cs
│   │   ├── Pregunta.cs (Clase base)
│   │   ├── PreguntaOpcionMultiple.cs
│   │   ├── PreguntaCodigo.cs
│   │   ├── Evaluacion.cs
│   │   ├── SesionEvaluacion.cs
│   │   └── Respuesta.cs
│   ├── DTOs/
│   │   ├── LoginDto.cs
│   │   ├── EvaluacionDto.cs
│   │   ├── PreguntaDto.cs
│   │   └── ResultadoEvaluacionDto.cs
│   └── Enums/
│       ├── TipoUsuario.cs
│       ├── TipoPregunta.cs
│       └── EstadoSesion.cs
├── Services/
│   ├── IAuthService.cs
│   ├── AuthService.cs
│   ├── IEvaluacionService.cs
│   ├── EvaluacionService.cs
│   ├── IReporteService.cs
│   └── ReporteService.cs
├── Repositories/
│   ├── IRepository.cs (Genérico)
│   ├── Repository.cs
│   ├── IEvaluacionRepository.cs
│   ├── EvaluacionRepository.cs
│   ├── IPreguntaRepository.cs
│   └── PreguntaRepository.cs
├── Controllers/
│   ├── AuthController.cs
│   ├── EvaluacionesController.cs
│   ├── PreguntasController.cs
│   ├── CandidatosController.cs
│   └── ReportesController.cs
└── Data/
    └── DatabaseContext.cs
```

### Requerimientos Funcionales

#### Módulo de Autenticación
- **POST** `/api/auth/login` - Iniciar sesión (Reclutador/Candidato/Admin)
- **POST** `/api/auth/register/reclutador` - Registrar reclutador
- **POST** `/api/auth/register/candidato` - Registrar candidato
- **POST** `/api/auth/refresh` - Renovar token

#### Módulo de Evaluaciones (Reclutadores)
- **GET** `/api/evaluaciones` - Listar evaluaciones del reclutador
- **POST** `/api/evaluaciones` - Crear nueva evaluación
- **PUT** `/api/evaluaciones/{id}` - Actualizar evaluación
- **DELETE** `/api/evaluaciones/{id}` - Eliminar evaluación
- **GET** `/api/evaluaciones/{id}/resultados` - Ver resultados de candidatos
- **POST** `/api/evaluaciones/{id}/invitar` - Invitar candidatos por email

#### Módulo de Preguntas
- **GET** `/api/preguntas/evaluacion/{evaluacionId}` - Preguntas de una evaluación
- **POST** `/api/preguntas` - Crear pregunta (diferentes tipos)
- **PUT** `/api/preguntas/{id}` - Actualizar pregunta
- **DELETE** `/api/preguntas/{id}` - Eliminar pregunta
- **GET** `/api/preguntas/banco` - Banco de preguntas por tecnología

#### Módulo de Candidatos
- **GET** `/api/candidatos/evaluaciones-disponibles` - Evaluaciones asignadas
- **POST** `/api/candidatos/iniciar-evaluacion/{evaluacionId}` - Iniciar evaluación
- **POST** `/api/candidatos/responder` - Enviar respuesta
- **POST** `/api/candidatos/finalizar-evaluacion/{sesionId}` - Finalizar evaluación
- **GET** `/api/candidatos/mis-resultados` - Ver resultados históricos

#### Módulo de Reportes y Estadísticas
- **GET** `/api/reportes/evaluacion/{id}/estadisticas` - Estadísticas de evaluación
- **GET** `/api/reportes/candidato/{id}/detalle` - Reporte detallado del candidato
- **GET** `/api/reportes/tecnologia/{id}/ranking` - Ranking por tecnología
- **GET** `/api/reportes/dashboard` - Dashboard general

### Implementación por Fases

#### Fase 1: Fundamentos POO y Estructura
**Tareas:**
1. Crear la jerarquía de usuarios (Usuario → Reclutador/Candidato/Admin)
2. Implementar la jerarquía de preguntas (Pregunta → tipos específicos)
3. Aplicar polimorfismo en el cálculo de puntajes por tipo de pregunta
4. Crear factory pattern para generar diferentes tipos de preguntas
5. Implementar interfaces para servicios

**Conceptos POO a evaluar:**
- Herencia múltiple nivel
- Factory Pattern
- Polimorfismo en métodos de cálculo
- Interfaces y abstracción
- Enums y constantes

**Ejemplo de Polimorfismo:**
```csharp
public abstract class Pregunta
{
    public virtual double CalcularPuntaje(Respuesta respuesta)
    {
        // Implementación base
    }
}

public class PreguntaOpcionMultiple : Pregunta
{
    public override double CalcularPuntaje(Respuesta respuesta)
    {
        // Lógica específica para opción múltiple
    }
}

public class PreguntaCodigo : Pregunta
{
    public override double CalcularPuntaje(Respuesta respuesta)
    {
        // Lógica específica para preguntas de código
        // Podría incluir análisis de sintaxis, casos de prueba, etc.
    }
}
```

#### Fase 2: Base de Datos y Repositorios
**Tareas:**
1. Crear la base de datos `EvaluacionTecnicaDB`
2. Implementar procedimientos almacenados:
   - `sp_GetEvaluacionesPorTecnologia`
   - `sp_CalcularEstadisticasEvaluacion`
   - `sp_GetRankingCandidatos`
   - `sp_GetReporteCandidato`
3. Crear repositorios específicos usando Dapper
4. Implementar patrón Repository genérico

#### Fase 3: Web API Core y Autenticación 
**Tareas:**
1. Configurar JWT con roles (Reclutador, Candidato, Admin)
2. Implementar middleware de autorización por roles
3. Crear controladores con endpoints específicos por rol
4. Configurar Swagger con autenticación JWT
5. Implementar validaciones de negocio

#### Fase 4: Lógica de Evaluación y Tiempo Real
**Tareas:**
1. Implementar timer de evaluación
2. Crear sistema de auto-guardado de respuestas
3. Implementar cálculo automático de puntajes
4. Crear sistema de notificaciones

### Criterios de Evaluación

#### POO Avanzada 
- **Herencia Múltiple Nivel**: Implementación correcta de jerarquías complejas
- **Polimorfismo Avanzado**: Métodos virtuales con lógica específica por tipo
- **Patrones de Diseño**: Factory, Repository, Strategy patterns
- **Abstracción**: Interfaces y clases abstractas

#### Web API 
- **Autenticación/Autorización**: JWT con roles, middleware de seguridad
- **Endpoints RESTful**: Implementación completa y consistente
- **Validaciones**: Validaciones de negocio y entrada
- **Documentación**: Swagger completo y funcional

#### Base de Datos
- **Diseño Normalizado**: Estructura óptima y relaciones correctas
- **Procedimientos Complejos**: SP's con lógica de negocio y reportes
- **Optimización**: Índices, queries eficientes

#### Funcionalidad de Negocio
- **Flujo Completo**: Desde creación hasta resultados
- **Lógica de Negocio**: Cálculos, validaciones, reglas
- **Experiencia de Usuario**: Flujo intuitivo y manejo de errores

### Casos de Uso

#### Escenario 1: Reclutador Técnico
1. Registrarse como reclutador de empresa
2. Crear evaluación para "Desarrollador .NET"
3. Agregar preguntas de diferentes tipos (código, teóricas, casos prácticos)
4. Invitar candidatos por email
5. **Revisar resultados y generar reportes

#### Escenario 2: Candidato
1. Recibir invitación por email
2. Registrarse en la plataforma
3. Ver evaluación disponible con información (duración, tecnologías)
4. **Iniciar evaluación con timer
5. Responder preguntas de diferentes tipos
6. Ver resultado y feedback inmediato

#### Escenario 3: Análisis de Datos
1. **Dashboard con estadísticas generales
2. **Ranking de candidatos por tecnología
3. **Análisis de preguntas más difíciles
4. **Reportes de tendencias en el mercado laboral

### Funcionalidades Avanzadas

#### Tipos de Preguntas Especializadas:

1. **Preguntas de Código**: Con editor de código y casos de prueba
2. **Preguntas de Arquitectura**: Con diagramas y patrones
3. **Preguntas de Debugging**: Encontrar errores en código
4. **Preguntas de Performance**: Optimización y análisis de complejidad

### Entregables 

1. **MVP Funcional** con todas las características core
2. **Documentación Técnica** completa (arquitectura, APIs, base de datos)
3. **Demo Presentation** para potenciales clientes
4. **Código Fuente** con buenas prácticas y comentarios

---
