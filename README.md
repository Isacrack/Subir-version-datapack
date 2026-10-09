# Actualizador de Datapacks, Plugins y Mods → 26.3

Página web de un solo archivo (`index.html`) que convierte datapacks, plugins y mods de Minecraft a una versión más reciente (por defecto **26.3**). Funciona en el navegador: los archivos no se suben a ningún servidor.

## Contenido

- [Cómo se usa](#cómo-se-usa)
- [Pestañas](#pestañas)
- [Formatos de entrada](#formatos-de-entrada)
- [Salida](#salida)
- [Datapacks](#datapacks)
- [Plugins](#plugins)
- [Mods](#mods)
- [Soporte de RAR](#soporte-de-rar)
- [Limitaciones](#limitaciones)
- [Requisitos técnicos](#requisitos-técnicos)
- [Pruebas realizadas](#pruebas-realizadas)

## Cómo se usa

1. Abre `index.html` en un navegador moderno (Chrome, Edge, Firefox o Safari).
2. Elige la pestaña: **Datapacks**, **Plugins** o **Mods**.
3. Selecciona la versión destino en el desplegable.
4. Arrastra tus archivos a la zona de carga, o haz clic en ella para elegirlos. Puedes subir varios a la vez.
5. Revisa el resumen de cada archivo y descarga el resultado con el botón **Descargar**.

> La página carga JSZip desde un CDN (cdnjs.cloudflare.com), así que necesita conexión a internet la primera vez que se abre.

## Pestañas

| Pestaña | Qué actualiza | Versiones destino |
|---|---|---|
| **Datapacks** | `pack.mcmeta` (formato del pack) y, opcionalmente, nombres de carpetas | 1.21 hasta 26.3 |
| **Plugins** | `api-version` en `plugin.yml` o `paper-plugin.yml` | 1.21, 1.21.4, 1.21.11, 26.1, 26.2, 26.3 |
| **Mods** | La versión de Minecraft que declara cada mod | 1.21, 1.21.1, 1.21.4, 1.21.8, 1.21.11, 26.1, 26.2, 26.3 |

## Formatos de entrada

| Pestaña | Entrada aceptada |
|---|---|
| Datapacks | `.zip`, `.rar`, o una carpeta de datapack dentro de un contenedor |
| Plugins | `.jar`, `.zip`, `.rar` (con uno o varios `.jar` dentro) |
| Mods | `.jar`, `.zip`, `.rar` (con uno o varios `.jar` dentro) |

Puedes subir **un archivo suelto** o un **contenedor** con varios elementos dentro:

- **Datapacks:** un contenedor puede tener varios datapacks como `.zip`, como `.rar` o como carpetas, cada una con su propio `pack.mcmeta`.
- **Plugins y mods:** un contenedor puede tener varios `.jar` dentro, también anidados en subcarpetas.

Cada elemento se convierte por separado. Si uno falla, los demás se siguen procesando.

## Salida

- Un archivo suelto genera un archivo con el sufijo de la versión. Ejemplo: `mi-pack.zip` → `mi-pack-26.3.zip`.
- Un contenedor genera **un solo `.zip`** llamado `<nombre>-<versión>.zip`. Ejemplo: `bundle.zip` → `bundle-26.3.zip`.
  - Dentro del `.zip`, cada elemento convertido conserva su ruta original.
  - Los elementos que fallan **se mantienen sin cambios** y aparecen en el resumen con su error.
- La salida es **siempre `.zip`**, incluso si la entrada era `.rar`. Para plugins y mods, cada `.jar` convertido se entrega como `.jar`.

El resumen de cada archivo indica qué cambió y qué avisos hay que revisar.

## Datapacks

El conversor:

- Escribe el formato del pack en `pack.mcmeta` según la versión destino:
  - Para **1.21 a 1.21.8** usa `pack_format`.
  - Para **1.21.9 y posteriores** usa `min_format` y `max_format`.
- Opcionalmente (activado por defecto) renombra las carpetas en plural a singular, como `functions` → `function` o `loot_tables` → `loot_table`.

### Tabla de formatos

| Versión | Data pack format |
|---|---|
| 1.21 – 1.21.1 | 48 |
| 1.21.2 – 1.21.3 | 57 |
| 1.21.4 | 61 |
| 1.21.5 | 71 |
| 1.21.6 | 80 |
| 1.21.7 – 1.21.8 | 81 |
| 1.21.9 – 1.21.10 | 88.0 |
| 1.21.11 | 94.1 |
| 26.1 – 26.1.2 | 101.1 |
| 26.2 | 107.1 |
| 26.3 | 121.0 |

Los formatos se toman de la tabla de la página. Los avisos son heurísticos: contrasta los datos con la wiki de Minecraft antes de usar el resultado en un servidor real.

## Plugins

El conversor:

- Actualiza `api-version` en `plugin.yml` o `paper-plugin.yml`.
- Revisa las clases del plugin y avisa si encuentra:
  - **NMS versionado** (`net.minecraft.server.v1_X_RY`).
  - **CraftBukkit versionado** (`org.bukkit.craftbukkit.v1_X_RY`).

> Cambiar `api-version` **no recompila el código**. Un servidor más antiguo que el `api-version` rechaza cargar el plugin, así que elige la versión de tu servidor. Si el plugin usa NMS o APIs cambiadas, hay que migrarlo y recompilarlo.

## Mods

El conversor actualiza la versión de Minecraft que declara cada mod en:

- `fabric.mod.json` (Fabric)
- `quilt.mod.json` (Quilt)
- `mods.toml` (Forge)
- `neoforge.mods.toml` (NeoForge)

Además, avisa si encuentra:

- **Nombres intermediarios de Fabric** (`net/minecraft/class_####`). Minecraft 26.x no está ofuscado, así que hay que recompilar con los nombres oficiales (Mojang).
- **Mixins con refmap** (`*.refmap.json`). Revisa que sus objetivos sigan existiendo en la versión destino.
- **`loaderVersion` de Forge/NeoForge**. Este valor no se cambia automáticamente.

> Estos cambios son solo de metadata. Un mod compilado contra nombres intermediarios o contra APIs que ya no existen necesita recompilarse desde su código fuente para funcionar en 26.x.

## Soporte de RAR

Los `.rar` se **leen** en el navegador, pero la salida siempre es `.zip`. Escribir RAR desde el navegador no es posible.

El lector es `node-unrar-js` 0.8.1 (MIT), en JavaScript puro. Va incluido en la propia página, así que no necesita WebAssembly ni `eval`. Por eso funciona con una Content Security Policy que bloquea `unsafe-eval`.

## Limitaciones

- **Solo lectura de RAR**, con la versión 0.8.1 de la librería. Puede fallar con RAR5 o con archivos protegidos con contraseña. En esos casos el error se muestra en el resumen y el archivo se mantiene sin cambios.
- **Sin recompilación.** Los cambios en plugins y mods son solo de metadata.
- **Heurísticas.** Los avisos de código (NMS, nombres intermediarios, refmaps) se basan en patrones de texto. No sustituyen una revisión del código.
- **Conexión inicial.** JSZip se carga desde un CDN.

## Requisitos técnicos

- Un navegador moderno con JavaScript habilitado.
- Conexión a internet para cargar JSZip desde cdnjs.
- No requiere servidor ni instalación. Puedes abrir `index.html` directamente.

Si se publica la página con una Content Security Policy, debe permitir scripts de `'self'`, `'unsafe-inline'` y `https:` (o al menos el origen de JSZip).

## Pruebas realizadas

Se probó en Chromium con la misma CSP que el visor, subiendo archivos por la interfaz:

- `dp.zip` (datapack suelto, formato 48) → `dp-26.3.zip`. Convertido correctamente.
- `rar_only.rar` suelto → el RAR se lee, y se informa el error esperado porque no contiene un datapack.
- `bundle.zip` con un datapack `.zip` y un `.rar` dentro → `bundle-26.3.zip`. El datapack se convierte y el `.rar` se mantiene con su error.
- `plugin.jar` → `plugin-26.3.jar`, con `api-version` 1.21 → 26.3.

Sin violaciones de CSP ni errores de página. La sintaxis del script principal se comprobó con `node --check`.

No se probó un `.rar` real que contenga un datapack completo.
