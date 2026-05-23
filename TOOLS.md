# Herramientas y Prompts (TOOLS.md)

Este documento detalla las herramientas (Tools) implementadas por el servidor para el MCP del Boletín Oficial de la Provincia de Buenos Aires (BOPBA), así como los Prompts programáticos disponibles según la especificación V2.

## Herramientas (Tools)

### 1. `buscar_boletin`
Busca boletines oficiales usando la página de búsqueda del BOPBA con filtros avanzados.

**Parámetros:**
- `words` (string, opcional): Palabras clave para la búsqueda.
- `date_gteq` (string, opcional): Fecha de inicio de la búsqueda en formato YYYY-MM-DD.
- `date_lteq` (string, opcional): Fecha de fin de la búsqueda en formato YYYY-MM-DD.
- `section` (enum, opcional): Sección del boletín para filtrar (OFICIAL, JUDICIAL, JURISPRUDENCIA, SUPLEMENTO).
- `sort` (enum, opcional): Ordenamiento (by_match_desc, by_date_desc, by_date_asc).
- `page` (number, opcional): Número de página para paginación de resultados.

**Respuesta:**
- `results`: Array de resultados con título, fecha, ID, links de descarga/vista, y extractos del contenido.
- `pagination`: Información de paginación (currentPage, totalPages, hasNext, hasPrev).

**Ejemplo de uso (JSON):**
```json
{
  "name": "buscar_boletin",
  "arguments": {
    "words": "licitación pública",
    "date_gteq": "2023-01-01",
    "date_lteq": "2023-12-31",
    "section": "OFICIAL",
    "sort": "by_date_desc",
    "page": 1
  }
}
```

### 2. `descargar_seccion`
Descarga y extrae el texto del PDF de una sección específica del BOPBA.

**Parámetros:**
- `id` (string, requerido): ID de la sección a descargar (obtenido previamente mediante `buscar_boletin`).

**Ejemplo de uso (JSON):**
```json
{
  "name": "descargar_seccion",
  "arguments": {
    "id": "123456"
  }
}
```

### 3. `listar_agencias`
Obtiene el listado completo de agencias del BOPBA con información de contacto.

**Parámetros:**
- *Sin parámetros.*

**Respuesta:**
- Array de agencias con número, nombre, dirección, teléfono, horario y email.

**Ejemplo de uso (JSON):**
```json
{
  "name": "listar_agencias",
  "arguments": {}
}
```

### 4. `listar_ediciones_anteriores`
Lista ediciones anteriores del boletín con filtros de fecha y paginación.

**Parámetros:**
- `date_gteq` (string, opcional): Fecha desde en formato YYYY-MM-DD.
- `date_lteq` (string, opcional): Fecha hasta en formato YYYY-MM-DD.
- `page` (number, opcional): Número de página (por defecto 1).

**Respuesta:**
- `ediciones`: Array de ediciones con título e información de expansión.
- `pagination`: Información de paginación.

**Ejemplo de uso (JSON):**
```json
{
  "name": "listar_ediciones_anteriores",
  "arguments": {
    "date_gteq": "2023-01-01",
    "date_lteq": "2023-12-31",
    "page": 1
  }
}
```

### 5. `calcular_tarifa`
Calcula la tarifa aproximada de publicación en el BOPBA usando el simulador de tasas.

**Parámetros:**
- `tipo_publicacion` (enum, requerido): Tipo de publicación (Avisos particulares, Sociedades comerciales, Convocatorias, Transferencias, Edictos judiciales (no sucesorios), Subastas).
- `texto` (string, requerido): Texto a publicar para contar palabras/caracteres.
- `dias` (number, opcional): Cantidad de días de publicación (por defecto 1).
- `urgencia` (enum, opcional): Tipo de trámite (Normal (72 hs.), Urgente (24 hs.)).

**Respuesta:**
- Estadísticas del texto (palabras, caracteres).
- Parámetros usados (días, urgencia).
- Estimación de costo en UT y AR$.

**Ejemplo de uso (JSON):**
```json
{
  "name": "calcular_tarifa",
  "arguments": {
    "tipo_publicacion": "Sociedades comerciales",
    "texto": "Convocatoria a asamblea ordinaria...",
    "dias": 3,
    "urgencia": "Normal (72 hs.)"
  }
}
```

### 6. `obtener_ultimo_boletin`
Obtiene información del último boletín publicado con sus secciones disponibles.

**Parámetros:**
- *Sin parámetros.*

**Respuesta:**
- `ultimo_boletin`: Texto con número y fecha del último boletín.
- `secciones`: Array de secciones (OFICIAL, JUDICIAL, SUPLEMENTO) con IDs y links.

**Ejemplo de uso (JSON):**
```json
{
  "name": "obtener_ultimo_boletin",
  "arguments": {}
}
```

### 7. `ver_seccion`
Obtiene metadatos y vista previa de una sección específica del boletín.

**Parámetros:**
- `id` (string, requerido): ID de la sección a ver.

**Respuesta:**
- Título, links de vista/descarga, y contenido previo del documento.

**Ejemplo de uso (JSON):**
```json
{
  "name": "ver_seccion",
  "arguments": {
    "id": "14177"
  }
}
```

### 8. `alcance_fuente`
Obtiene información sobre el alcance, limitaciones y disclaimer del BOPBA.

**Parámetros:**
- *Sin parámetros.*

**Ejemplo de uso (JSON):**
```json
{
  "name": "alcance_fuente",
  "arguments": {}
}
```

### 9. `verificar_vigencia`
Verifica si una sección del boletín está disponible, su fecha de publicación y si hay versiones modificadas o correcciones posteriores.

**Parámetros:**
- `id` (string, requerido): ID de la sección a verificar.

**Respuesta:**
- Estado de disponibilidad (DISPONIBLE/NO DISPONIBLE)
- Fecha de publicación
- Alertas de modificación o corrección
- Enlaces de vista y descarga

**Ejemplo de uso (JSON):**
```json
{
  "name": "verificar_vigencia",
  "arguments": {
    "id": "123456"
  }
}
```

### 10. `relacionar_publicaciones`
Busca publicaciones relacionadas con una sección específica del boletín (mismas fechas, mismos organismos, temas similares).

**Parámetros:**
- `id` (string, requerido): ID de la sección de referencia.
- `palabras_clave` (string, opcional): Palabras clave adicionales para buscar publicaciones relacionadas.
- `fecha_desde` (string, opcional): Fecha desde YYYY-MM-DD para ampliar búsqueda.
- `fecha_hasta` (string, opcional): Fecha hasta YYYY-MM-DD para ampliar búsqueda.

**Respuesta:**
- Publicación de referencia (título, fecha, ID)
- Lista de publicaciones relacionadas encontradas
- Criterio de búsqueda utilizado

**Ejemplo de uso (JSON):**
```json
{
  "name": "relacionar_publicaciones",
  "arguments": {
    "id": "123456",
    "palabras_clave": "licitación"
  }
}
```

### 11. `buscar_por_semantica`
Busca publicaciones en el BOPBA utilizando expansión semántica de términos. El LLM debe generar sinónimos y términos equivalentes antes de llamar esta herramienta.

**Parámetros:**
- `concepto` (string, requerido): Concepto central a buscar (ej. 'teletrabajo', 'licencia parental').
- `terminos_equivalentes` (array of strings, requerido): Lista de sinónimos o términos relacionados generados por el LLM.
- `fecha_desde` (string, opcional): Fecha desde YYYY-MM-DD.
- `fecha_hasta` (string, opcional): Fecha hasta YYYY-MM-DD.
- `seccion` (enum, opcional): Sección del boletín (OFICIAL, JUDICIAL, JURISPRUDENCIA, SUPLEMENTO).

**Respuesta:**
- Términos de búsqueda utilizados
- Lista de publicaciones encontradas con extractos

**Ejemplo de uso (JSON):**
```json
{
  "name": "buscar_por_semantica",
  "arguments": {
    "concepto": "teletrabajo",
    "terminos_equivalentes": ["trabajo remoto", "home office", "trabajo a distancia"]
  }
}
```

### 12. `generar_certificacion_forense`
Genera una certificación forense de autenticidad para una sección del BOPBA con hash SHA-256, timestamp y metadatos de integridad.

**Parámetros:**
- `id` (string, requerido): ID de la sección a certificar.

**Respuesta:**
- Acta de certificación forense
- Hash SHA-256 del documento
- Timestamp UTC
- Metadatos de integridad (tamaño, URLs)
- Método de verificación

**Ejemplo de uso (JSON):**
```json
{
  "name": "generar_certificacion_forense",
  "arguments": {
    "id": "123456"
  }
}
```

### 13. `exportar_seccion`
Exporta una sección del BOPBA a formato Markdown estructurado con frontmatter YAML para sistemas de gestión del conocimiento (Notion, Obsidian, etc.).

**Parámetros:**
- `id` (string, requerido): ID de la sección a exportar.
- `incluir_texto` (boolean, opcional): Incluir el texto completo del PDF (por defecto: true).

**Respuesta:**
- Documento Markdown con frontmatter YAML
- Metadatos estructurados (título, fecha, URLs, tags)
- Contenido exportado listo para sistemas de gestión del conocimiento

**Ejemplo de uso (JSON):**
```json
{
  "name": "exportar_seccion",
  "arguments": {
    "id": "123456",
    "incluir_texto": true
  }
}
```

### 14. `detector_plazos_edictos`
Audita el texto de una sección del BOPBA para detectar e indexar plazos, fechas límite y hitos temporales relevantes (especialmente útil para edictos sucesorios).

**Parámetros:**
- `id` (string, requerido): ID de la sección a auditar.
- `texto_manual` (string, opcional): Texto manual para analizar (si no se proporciona descarga el PDF).

**Respuesta:**
- Auditoría de plazos y hitos temporales
- Cláusulas temporales detectadas con indicadores
- Patrones de búsqueda utilizados
- Resumen de hallazgos

**Ejemplo de uso (JSON):**
```json
{
  "name": "detector_plazos_edictos",
  "arguments": {
    "id": "123456"
  }
}
```

---

## Prompts Programáticos (V2)

El servidor expone prompts preconfigurados para automatizar flujos de trabajo comunes.

### 1. `buscar_edicto`
Plantilla para buscar un edicto específico y procesar sus resultados.

**Argumentos:**
- `query` (string, requerido): Términos de búsqueda del edicto.

**Ejemplo de uso (JSON):**
```json
{
  "name": "buscar_edicto",
  "arguments": {
    "query": "sucesorio Perez"
  }
}
```

### 2. `auditar_seccion_bopba`
Audita y analiza una sección descargada del BOPBA en busca de normativas clave, licitaciones o nombramientos.

**Argumentos:**
- `id` (string, requerido): ID de la sección del boletín a auditar.

**Ejemplo de uso (JSON):**
```json
{
  "name": "auditar_seccion_bopba",
  "arguments": {
    "id": "123456"
  }
}
```

### 3. `investigar_sociedad`
Investiga publicaciones de una sociedad comercial específica en el BOPBA.

**Argumentos:**
- `nombre_sociedad` (string, requerido): Nombre de la sociedad a investigar.
- `fecha_desde` (string, opcional): Fecha desde YYYY-MM-DD.
- `fecha_hasta` (string, opcional): Fecha hasta YYYY-MM-DD.

**Ejemplo de uso (JSON):**
```json
{
  "name": "investigar_sociedad",
  "arguments": {
    "nombre_sociedad": "Tech Solutions S.A.",
    "fecha_desde": "2023-01-01",
    "fecha_hasta": "2023-12-31"
  }
}
```

### 4. `consultar_agencia_cercana`
Consulta información de agencias del BOPBA para publicación presencial.

**Argumentos:**
- `zona` (string, opcional): Zona o ciudad de interés.

**Ejemplo de uso (JSON):**
```json
{
  "name": "consultar_agencia_cercana",
  "arguments": {
    "zona": "La Plata"
  }
}
```

### 5. `calcular_costo_publicacion`
Calcula el costo estimado de una publicación en el BOPBA.

**Argumentos:**
- `tipo` (string, requerido): Tipo de publicación.
- `texto` (string, requerido): Texto completo a publicar.
- `dias` (string, opcional): Cantidad de días.
- `urgencia` (string, opcional): Normal o Urgente.

**Ejemplo de uso (JSON):**
```json
{
  "name": "calcular_costo_publicacion",
  "arguments": {
    "tipo": "Sociedades comerciales",
    "texto": "Texto completo...",
    "dias": "3",
    "urgencia": "Normal"
  }
}
```

### 6. `monitorear_ultimas_publicaciones`
Monitorea las últimas publicaciones del BOPBA en secciones específicas.

**Argumentos:**
- `seccion` (enum, opcional): Sección de interés (OFICIAL, JUDICIAL, JURISPRUDENCIA, SUPLEMENTO).

**Ejemplo de uso (JSON):**
```json
{
  "name": "monitorear_ultimas_publicaciones",
  "arguments": {
    "seccion": "OFICIAL"
  }
}
```

### 7. `buscar_normativa_periodo`
Busca normativas publicadas en un período específico.

**Argumentos:**
- `fecha_desde` (string, requerido): Fecha desde YYYY-MM-DD.
- `fecha_hasta` (string, requerido): Fecha hasta YYYY-MM-DD.
- `palabras_clave` (string, opcional): Palabras clave para filtrar.

**Ejemplo de uso (JSON):**
```json
{
  "name": "buscar_normativa_periodo",
  "arguments": {
    "fecha_desde": "2023-01-01",
    "fecha_hasta": "2023-12-31",
    "palabras_clave": "educación"
  }
}
```
