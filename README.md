# Argentina BOPBA - MCP Server

[![TypeScript](https://img.shields.io/badge/TypeScript-5.4-blue.svg)](https://www.typescriptlang.org/)
[![MCP](https://img.shields.io/badge/Model_Context_Protocol-1.5.0-green.svg)](https://modelcontextprotocol.io/)
[![Vercel](https://img.shields.io/badge/Deployed_on-Vercel-black.svg?logo=vercel)](https://bopba-mcp.vercel.app)
[![NPM Version](https://img.shields.io/npm/v/bopba-mcp?color=cb3837&logo=npm)](https://www.npmjs.com/package/bopba-mcp)

MCP Server para buscar y extraer legislación provincial del **Boletín Oficial de la Provincia de Buenos Aires (BOPBA)**. Conecta cualquier LLM compatible (Claude, Cursor, Antigravity) con el portal oficial provincial para realizar búsquedas en tiempo real y extraer el articulado completo con **fidelidad jurídica sin alucinaciones**.

---

## 🚀 Características Principales

*   **Buscador Avanzado (`buscar_boletin`)**: Búsquedas avanzadas en tiempo real por texto libre, secciones, número de boletín y rango de fechas en toda la base histórica del BOPBA.
*   **Extracción de PDFs (`descargar_seccion`)**: Descarga y extrae el texto completo de secciones específicas desde los PDFs oficiales firmados digitalmente.
*   **Última Edición (`obtener_ultimo_boletin`)**: Recupera automáticamente el último boletín publicado con todos sus metadatos.
*   **Búsqueda Semántica (`buscar_por_semantica`)**: Búsqueda inteligente con expansión de términos y sinónimos para encontrar publicaciones relacionadas.
*   **Análisis de Vigencia (`verificar_vigencia`)**: Verifica la disponibilidad de publicaciones y detecta modificaciones o actualizaciones.
*   **Detección de Plazos (`detector_plazos_edictos`)**: Identifica automáticamente plazos y fechas límite en edictos judiciales y administrativos.
*   **Certificación Forense (`generar_certificacion_forense`)**: Genera certificados digitales con hash SHA-256 para garantizar la inalterabilidad del contenido extraído.
*   **Cálculo de Tasas (`calcular_tarifa`)**: Calcula automáticamente las tasas de publicación según el tipo de aviso y características.
*   **Directorio de Agencias (`listar_agencias`)**: Acceso al directorio oficial de agencias del BOPBA para contacto y consulta.
*   **Resiliencia Criptográfica**: Configurado con agentes HTTPS tolerantes a fallos criptográficos para saltear bloqueos por expiración de cadenas de confianza SSL en los portales gubernamentales.

---

## 🛠️ Instalación y Configuración Rápida

Para utilizar este servidor MCP de forma inmediata, necesitas tener instalado [Node.js](https://nodejs.org/) (versión 18 o superior).

### 1. Cursor IDE / Windsurf

Tanto Cursor como Windsurf admiten servidores MCP de forma nativa a través del protocolo standard I/O (`stdio`):
1.  Abre la configuración (Settings) de tu entorno de desarrollo y busca la sección **MCP**.
2.  Haz clic en **+ Add New MCP Server**.
3.  Completa los campos requeridos:
    *   **Name:** `bopba-mcp`
    *   **Type:** `command`
    *   **Command:** `node`
    *   **Args:** `D:/MCP/Legales/03-bopba-mcp/Argentina-Bopba-MCP/build/index.js`
    *   **Env:** `NODE_TLS_REJECT_UNAUTHORIZED=0`

### 2. Claude Desktop

1.  Abre tu archivo de configuración de Claude Desktop. En Windows, se localiza normalmente en:
    `C:\Users\<TuUsuario>\AppData\Roaming\Claude\claude_desktop_config.json`
2.  Agrega el servidor dentro de la clave `mcpServers`:
```json
{
  "mcpServers": {
    "bopba-mcp": {
      "command": "node",
      "args": ["D:/MCP/Legales/03-bopba-mcp/Argentina-Bopba-MCP/build/index.js"],
      "env": {
        "NODE_TLS_REJECT_UNAUTHORIZED": "0"
      }
    }
  }
}
```
> [!IMPORTANT]
> La variable de entorno `"NODE_TLS_REJECT_UNAUTHORIZED": "0"` es fundamental para saltear problemas periódicos con los certificados SSL vencidos o mal configurados de los sitios gubernamentales argentinos.

### 3. Antigravity / Codex

Configura el servidor dentro del archivo de configuración global `mcp_config.json`:
```json
{
  "mcpServers": {
    "bopba-mcp": {
      "command": "node",
      "args": ["D:/MCP/Legales/03-bopba-mcp/Argentina-Bopba-MCP/build/index.js"],
      "env": {
        "NODE_TLS_REJECT_UNAUTHORIZED": "0"
      }
    }
  }
}
```

---

## 💻 Instalación Manual (Para Desarrolladores)

Si deseas descargar el código fuente, auditar la lógica de scraping o contribuir al desarrollo local:

1.  Clona el repositorio oficial:
    ```bash
    git clone https://github.com/voftec/bopba-mcp.git
    cd bopba-mcp
    ```
2.  Instala las dependencias de desarrollo y producción:
    ```bash
    npm install
    ```
3.  Compila el código TypeScript a JavaScript de distribución ESM:
    ```bash
    npm run build
    ```
4.  Configura tu cliente MCP local apuntando directamente a la build construida:
    *   **Cursor Command:** `node D:/ruta-a-tu-carpeta/bopba-mcp/build/index.js`
    *   **Claude Desktop Config:**
        ```json
        "bopba-mcp": {
          "command": "node",
          "args": ["D:/ruta-a-tu-carpeta/bopba-mcp/build/index.js"],
          "env": {
            "NODE_TLS_REJECT_UNAUTHORIZED": "0"
          }
        }
        ```

---

## ⚖️ Catálogo de Herramientas Disponibles (Tools)

El servidor expone **15 herramientas especializadas** diseñadas para interactuar con el Boletín Oficial de la Provincia de Buenos Aires:

| Herramienta | Descripción Técnica | Parámetros Clave |
|---|---|---|
| `buscar_boletin` | Búsqueda avanzada de boletines con filtros múltiples | `words`, `date_gteq`, `date_lteq`, `section`, `sort`, `page` |
| `descargar_seccion` | Descarga y extrae texto de PDFs de secciones específicas | `id` |
| `obtener_ultimo_boletin` | Recupera el último boletín publicado con sus secciones | *(Ninguno)* |
| `ver_seccion` | Vista previa de secciones específicas con metadatos | `id` |
| `listar_ediciones_anteriores` | Lista ediciones históricas con paginación | `date_gteq`, `date_lteq`, `page` |
| `buscar_por_semantica` | Búsqueda semántica con expansión de términos equivalentes | `concepto`, `terminos_equivalentes`, `fecha_desde`, `fecha_hasta`, `seccion` |
| `verificar_vigencia` | Verifica disponibilidad y modificaciones de publicaciones | `id` |
| `relacionar_publicaciones` | Busca publicaciones relacionadas por tema o criterio | `id`, `palabras_clave`, `fecha_desde`, `fecha_hasta` |
| `detector_plazos_edictos` | Detecta plazos y fechas límite en edictos (especialmente sucesorios) | `id`, `texto_manual` |
| `exportar_seccion` | Exporta secciones a Markdown con frontmatter YAML | `id`, `incluir_texto` |
| `generar_certificacion_forense` | Genera certificación con hash SHA-256 para inalterabilidad | `id` |
| `calcular_tarifa` | Calcula aproximación de tasas de publicación (usa web para valores exactos) | `categoria`, `texto`, `dias`, `urgencia`, `actualizar` |
| `actualizar_tasas` | Actualiza tasas desde PDF oficial del BOPBA | `forzar` |
| `listar_agencias` | Directorio de agencias del BOPBA para contacto | *(Ninguno)* |
| `alcance_fuente` | Información sobre la fuente legal, limitaciones y disclaimer | *(Ninguno)* |

---

## 📝 Licencia

Este proyecto se distribuye de forma abierta y transparente bajo los términos de la **Licencia MIT**. Consulta el archivo `LICENSE` para más detalles.
