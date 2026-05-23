# Herramientas del Servidor MCP BOPBA (Tools)

Este servidor expone **15 herramientas (tools)** diseñadas para interactuar con el Boletín Oficial de la Provincia de Buenos Aires (BOPBA).

---

## Búsqueda

### 1. `buscar_boletin`

Busca boletines oficiales usando la página de búsqueda del BOPBA con filtros avanzados.

**Parámetros:**
- `words` (string, opcional): Palabras clave para la búsqueda.
- `date_gteq` (string, opcional): Fecha de inicio en formato YYYY-MM-DD.
- `date_lteq` (string, opcional): Fecha de fin en formato YYYY-MM-DD.
- `section` (enum, opcional): Sección del boletín (OFICIAL, JUDICIAL, JURISPRUDENCIA, SUPLEMENTO).
- `sort` (enum, opcional): Ordenamiento (by_match_desc, by_date_desc, by_date_asc).
- `page` (number, opcional): Número de página para paginación.

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

**Respuesta:**
- `results`: Array de resultados con título, fecha, ID, links de descarga/vista, y extractos del contenido.
- `pagination`: Información de paginación (currentPage, totalPages, hasNext, hasPrev).

---

### 2. `buscar_por_semantica`

Busca publicaciones en el BOPBA utilizando expansión semántica de términos. El LLM debe generar sinónimos y términos equivalentes antes de llamar esta herramienta.

**Parámetros:**
- `concepto` (string, **requerido**): Concepto central a buscar (ej. 'teletrabajo', 'licencia parental').
- `terminos_equivalentes` (array of strings, **requerido**): Lista de sinónimos o términos relacionados generados por el LLM.
- `fecha_desde` (string, opcional): Fecha desde YYYY-MM-DD.
- `fecha_hasta` (string, opcional): Fecha hasta YYYY-MM-DD.
- `seccion` (enum, opcional): Sección del boletín (OFICIAL, JUDICIAL, JURISPRUDENCIA, SUPLEMENTO).

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

**Respuesta:**
- Términos de búsqueda utilizados
- Lista de publicaciones encontradas con extractos

---

### 3. `relacionar_publicaciones`

Busca publicaciones relacionadas con una sección específica del boletín (mismas fechas, mismos organismos, temas similares).

**Parámetros:**
- `id` (string, **requerido**): ID de la sección de referencia.
- `palabras_clave` (string, opcional): Palabras clave adicionales para buscar publicaciones relacionadas.
- `fecha_desde` (string, opcional): Fecha desde YYYY-MM-DD para ampliar búsqueda.
- `fecha_hasta` (string, opcional): Fecha hasta YYYY-MM-DD para ampliar búsqueda.

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

**Respuesta:**
- Publicación de referencia (título, fecha, ID)
- Lista de publicaciones relacionadas encontradas
- Criterio de búsqueda utilizado

---

## Texto y Metadatos

### 4. `descargar_seccion`

Descarga y extrae el texto del PDF de una sección específica del BOPBA.

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a descargar.

**Ejemplo de uso (JSON):**
```json
{
  "name": "descargar_seccion",
  "arguments": {
    "id": "123456"
  }
}
```

**Respuesta:**
- Texto completo del PDF extraído (limitado a 50,000 caracteres).

---

### 5. `ver_seccion`

Obtiene metadatos y vista previa de una sección específica del boletín.

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a ver.

**Ejemplo de uso (JSON):**
```json
{
  "name": "ver_seccion",
  "arguments": {
    "id": "14177"
  }
}
```

**Respuesta:**
- `id`: ID de la sección.
- `titulo`: Título del documento.
- `link_ver`: URL de vista.
- `link_descargar`: URL de descarga del PDF.
- `contenido_previo`: Contenido previo del documento.

---

### 6. `verificar_vigencia`

Verifica si una sección del boletín está disponible, su fecha de publicación y si hay versiones modificadas o correcciones posteriores.

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a verificar.

**Ejemplo de uso (JSON):**
```json
{
  "name": "verificar_vigencia",
  "arguments": {
    "id": "123456"
  }
}
```

**Respuesta:**
- Estado de disponibilidad (DISPONIBLE/NO DISPONIBLE)
- Fecha de publicación
- Alertas de modificación o corrección
- Enlaces de vista y descarga

---

### 7. `exportar_seccion`

Exporta una sección del BOPBA a formato Markdown estructurado con frontmatter YAML para sistemas de gestión del conocimiento (Notion, Obsidian, etc.).

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a exportar.
- `incluir_texto` (boolean, opcional): Incluir el texto completo del PDF (por defecto: true).

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

**Respuesta:**
- Documento Markdown con frontmatter YAML
- Metadatos estructurados (título, fecha, URLs, tags)
- Contenido exportado listo para sistemas de gestión del conocimiento

---

## Boletín

### 8. `obtener_ultimo_boletin`

Obtiene información del último boletín publicado con sus secciones disponibles.

**Parámetros:**
- *Ninguno*

**Ejemplo de uso (JSON):**
```json
{
  "name": "obtener_ultimo_boletin",
  "arguments": {}
}
```

**Respuesta:**
- `ultimo_boletin`: Texto con número y fecha del último boletín.
- `secciones`: Array de secciones con IDs y links.

---

### 9. `listar_ediciones_anteriores`

Lista ediciones anteriores del boletín con filtros de fecha y paginación.

**Parámetros:**
- `date_gteq` (string, opcional): Fecha desde en formato YYYY-MM-DD.
- `date_lteq` (string, opcional): Fecha hasta en formato YYYY-MM-DD.
- `page` (number, opcional): Número de página (por defecto 1).

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

**Respuesta:**
- `ediciones`: Array de ediciones con título e información de expansión.
- `pagination`: Información de paginación.

---

## Información

### 10. `alcance_fuente`

Obtiene información sobre el alcance, limitaciones y disclaimer del BOPBA.

**Parámetros:**
- *Ninguno*

**Ejemplo de uso (JSON):**
```json
{
  "name": "alcance_fuente",
  "arguments": {}
}
```

**Respuesta:**
- Información sobre el alcance, limitaciones y disclaimer del BOPBA.

---

### 11. `listar_agencias`

Obtiene el listado completo de agencias del BOPBA con información de contacto.

**Parámetros:**
- *Ninguno*

**Ejemplo de uso (JSON):**
```json
{
  "name": "listar_agencias",
  "arguments": {}
}
```

**Respuesta:**
- Array de agencias con número, nombre, dirección, teléfono, horario y email.

---

## Tasas

### 12. `calcular_tarifa`

Calcula una aproximación de la tarifa de publicación en el BOPBA basándose en las tasas oficiales del flyer. **NOTA:** Este cálculo es una aproximación. Para obtener el precio exacto, códigos QR de pago y enlaces de pago oficiales, debe utilizar el simulador web: https://tasador.boletinoficial.gba.gob.ar/

**Parámetros:**
- `categoria` (enum, **requerido**): Categoría de publicación (Edictos sucesorios, Avisos por palabras, Balances, Entidades Financieras, Otras Sociedades, Constitución de SAS).
- `texto` (string, opcional): Texto a publicar (requerido para 'Avisos por palabras').
- `dias` (enum, opcional): Cantidad de días (1 o 3, requerido para 'Edictos sucesorios').
- `urgencia` (enum, opcional): Tipo de trámite (Normal (72 hs.), Urgente (24 hs.)).
- `actualizar` (boolean, opcional): Forzar actualización desde PDF oficial.

**Ejemplo de uso (JSON):**
```json
{
  "name": "calcular_tarifa",
  "arguments": {
    "categoria": "Avisos por palabras",
    "texto": "Convocatoria a asamblea ordinaria...",
    "urgencia": "Normal (72 hs.)"
  }
}
```

**Respuesta:**
- `categoria`: Categoría de publicación.
- `urgencia`: Tipo de trámite.
- `calculo`: Detalles del cálculo (valor en UT y AR$).
- `advertencia`: Advertencia sobre que es una aproximación.
- `fuente`: Fuente de las tasas.
- `url_verificacion`: URL del simulador web oficial.
- `url_pago_oficial`: URL para obtener precios exactos y métodos de pago.
- `version_tasas`: Versión de las tasas utilizadas.
- `ultima_actualizacion`: Fecha de última actualización.

---

### 13. `actualizar_tasas`

Verifica y actualiza las tasas desde el PDF oficial del BOPBA si hay cambios.

**Parámetros:**
- `forzar` (boolean, opcional): Forzar actualización incluso si el PDF no cambió.

**Ejemplo de uso (JSON):**
```json
{
  "name": "actualizar_tasas",
  "arguments": {
    "forzar": true
  }
}
```

**Respuesta:**
- `mensaje`: Mensaje de estado de la actualización.
- `version`: Versión de las tasas actualizadas.
- `ultima_actualizacion`: Fecha de última actualización.
- `pdf_hash`: Hash del PDF para verificación de cambios.
- `url_origen`: URL del PDF oficial.

---

## Análisis Forense

### 14. `generar_certificacion_forense`

Genera una certificación forense de autenticidad para una sección del BOPBA con hash SHA-256, timestamp y metadatos de integridad.

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a certificar.

**Ejemplo de uso (JSON):**
```json
{
  "name": "generar_certificacion_forense",
  "arguments": {
    "id": "123456"
  }
}
```

**Respuesta:**
- Acta de certificación forense
- Hash SHA-256 del documento
- Timestamp UTC
- Metadatos de integridad (tamaño, URLs)
- Método de verificación

---

### 15. `detector_plazos_edictos`

Audita el texto de una sección del BOPBA para detectar e indexar plazos, fechas límite y hitos temporales relevantes (especialmente útil para edictos sucesorios).

**Parámetros:**
- `id` (string, **requerido**): ID de la sección a auditar.
- `texto_manual` (string, opcional): Texto manual para analizar (si no se proporciona descarga el PDF).

**Ejemplo de uso (JSON):**
```json
{
  "name": "detector_plazos_edictos",
  "arguments": {
    "id": "123456"
  }
}
```

**Respuesta:**
- Auditoría de plazos y hitos temporales
- Cláusulas temporales detectadas con indicadores
- Patrones de búsqueda utilizados
- Resumen de hallazgos

