# ADR-001: Selección de Infraestructura de Almacenamiento para la Plataforma EducaMás

**Fecha:** 20 de Marzo de 2026  
**Estado:** Aceptado  

---

## Contexto

La empresa **EducaMás** está desarrollando una plataforma de cursos online. Han definido los siguientes requisitos iniciales:
- **Usuarios:** Estudiantes, instructores y administradores.  
- **Cursos:** Video, texto, ejercicios prácticos y foros de discusión.  
- **Pagos:** Integración con pasarelas de pago.  
- **Seguimiento del progreso:** Visualización del avance por curso.  

El contenido contempla:
- Más de **500 horas de video** durante el primer año.  
- Aproximadamente **10.000 usuarios concurrentes** en horas de mayor tráfico.  

### Requisitos clave

- **Rendimiento:** Baja latencia en reproducción de video.  
- **Seguridad:** Control o prohibición de descargas no autorizadas.  
- **Presupuesto:** Limitado para infraestructura.  

---

## Alternativas consideradas

### A. Almacenamiento en la nube con CDN

Almacenamiento en la nube con CDN (Red de Entrega de Contenido):Utilizar un servicio de almacenamiento en la nube como AWS S3 o Google Cloud Storage, combinado con una CDN para la entrega eficiente de los videos a los usuarios.

#### Ventajas
- Distribución de datos en múltiples servidores  
- Alta disponibilidad  
- Ahorro en infraestructura  
- Escalabilidad  

#### Desventajas
- Limitaciones con contenido dinámico  
- Dependencia del servidor de origen  
- Posibles fallos de caché  

---

### B. Almacenamiento en servidores propios

Almacenamiento en servidores propios: Mantener los videos y el contenido disponible en el servidor propio de la empresa.

#### Ventajas
- Respaldo continuo  
- Mayor control y privacidad  
- Acceso remoto seguro  

#### Desventajas
- Mantenimiento complejo  
- Alto costo  
- Requiere espacio físico  
- Necesidad de control de temperatura  
- Riesgos físicos (ej. terremotos)  

---

## Decisión

Se opta por:

- Almacenamiento en **Amazon S3**
- Distribución mediante **Amazon CloudFront (CDN)**

### Justificación

1. **Costo:**  
   El presupuesto limitado descarta infraestructura propia debido a altos costos fijos.

2. **Rendimiento:**  
   La CDN permite reducir latencia al distribuir contenido desde ubicaciones cercanas.

3. **Seguridad:**  
   Uso de:
   - Signed URLs  
   - Origin Access Control  

Esto evita desarrollos adicionales para control de acceso.

### Conclusión

Aunque los servidores propios ofrecen mayor control, sus costos y riesgos superan sus beneficios. AWS permite:

- Escalabilidad automática  
- Costos variables  
- Menor carga operativa  

---

## Servicios utilizados

### Amazon S3
Servicio de almacenamiento de objetos con:

- Durabilidad: **99.999999999%**
- Escalabilidad ilimitada  
- Gestión mediante buckets  

### Amazon CloudFront
CDN que:

- Distribuye contenido globalmente  
- Usa servidores perimetrales  
- Mejora rendimiento y resiliencia  

---

## Arquitectura (Descripción)

1. La aplicación valida usuarios  
2. Se verifica acceso al contenido  
3. CloudFront gestiona la solicitud  
4. Si el contenido está en caché → se entrega  
5. Si no → se obtiene desde S3, se cachea y se entrega  

---

## Consecuencias

### a. Impacto en el desarrollo

#### Positivo
- Arquitectura desacoplada  
- Uso de APIs/SDKs de AWS  
- Control de acceso con Signed URLs  
- Posibilidad de lógica en el edge  
- Escalabilidad automática  

#### Negativo
- Complejidad inicial (CORS, caché, políticas)  
- Estrategias de cacheo necesarias  
- Curva de aprendizaje en AWS  

---

### b. Impacto en el mantenimiento

#### Positivo
- Menor operación manual  
- Alta durabilidad y disponibilidad  
- Mejor resiliencia global  
- Monitoreo y logging integrados  
- Reducción de carga backend  

#### Negativo
- Menor control de infraestructura  
- Dependencia de herramientas AWS  
- Mantenimiento de configuraciones  

---

### c. Impacto en los costos

#### Positivo
- Pago por uso  
- Sin inversión en hardware  
- Optimización con CDN  
- Lifecycle policies disponibles  
- Transferencias internas eficientes  

#### Negativo
- Costos variables  
- Alto costo en streaming intensivo  
- Costos por requests e invalidaciones  
- Necesidad de optimización constante  

---

### d. Riesgos

- **Vendor lock-in:** Dependencia de AWS  
- **Errores de configuración:** Exposición de datos  
- **Caché inconsistente:** Contenido desactualizado  
- **Hotlinking:** Uso no autorizado  
- **Tráfico anómalo:** Bots o scraping  
- **Dependencia de red global:** Factores externos  

---
