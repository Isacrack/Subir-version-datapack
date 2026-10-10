# Actualizador de Datapacks, Texture packs, Plugins y Mods → 26.3

Página web de un solo archivo (`index.html`) que convierte datapacks, texture packs, plugins y mods de Minecraft a una versión más reciente (por defecto **26.3**). Funciona en el navegador: los archivos no se suben a ningún servidor.

## Contenido

- [Cómo se usa](#cómo-se-usa)
- [Pestañas](#pestañas)
- [Formatos de entrada](#formatos-de-entrada)
- [Salida](#salida)
- [Detección de versiones](#detección-de-versiones)
- [Datapacks](#datapacks)
- [Texture packs](#texture-packs)
- [Plugins](#plugins)
- [Mods](#mods)
- [Soporte de RAR](#soporte-de-rar)
- [Limitaciones](#limitaciones)n
- [Requisitos técnicos](#requisitos-técnicos)
- [Pruebas realizadas](#pruebas-realizadas)

## Cómo se usa

1. Abre `index.html` en un navegador moderno (Chrome, Edge, Firefox o Safari).
2. Elige la pestaña: **Datapacks**, **Plugins**, **Mods** o **Texture pack** .
3. Selecciona la versión destino en el desplegable.
4. Arrastra tus archivos a la zona de carga, o haz clic en ella para elegirlos. Puedes subir varios a la vez.
5. Revisa el resumen de cada archivo y descarga el resultado con el botón **Descargar**.

> La página carga JSZip desde un CDN (cdnjs.cloudflare.com), así que necesita conexión a internet la primera vez que se abre.

## Pestañas

| Pestaña | Qué actualiza | Versiones destino |
|---|---|---|
| **Datapacks** | `pack.mcmeta` (formato del pack) y, opcionalmente, nombres de carpetas | 1.12 hasta 26.3 (en 1.12 solo como referencia: los datapacks empiezan en 1.13) |
| **Texture packs** | `pack.mcmeta` (formato de resource pack) | 1.12 hasta 26.3 |
| **Plugins** | `api-version` en `plugin.yml` o `paper-plugin.yml` | Todas las versiones de 1.12 a 26.3 |
| **Mods** | La versión de Minecraft que declara cada mod | Todas las versiones de 1.12 a 26.3 |

## Formatos de entrada

| Pestaña | Entrada aceptada |
|---|---|
| Datapacks | `.zip`, `.rar`, o una carpeta de datapack dentro de un contenedor |
| Texture packs | `.zip`, `.rar`, o una carpeta de texture pack dentro de un contenedor |
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

## Detección de versiones

Cada elemento muestra en su resumen lo que ha detectado antes de convertirlo:

| Tipo | Qué detecta | Ejemplo de resumen |
|---|---|---|
| Datapacks | Versión de formato actual de `pack.mcmeta` | `pack_format 48 (1.21 – 1.21.1) → 26.3` |
| Texture packs | Descripción y versión de formato actual de `pack.mcmeta` | `pack_format 34 (1.21 – 1.21.1) → 26.3` |
| Plugins | Nombre, versión propia del plugin y tipo de descriptor (`plugin.yml` o `paper-plugin.yml`), más el `api-version` actual | `MiPlugin v2.3.1 · plugin.yml` |
| Mods | Cargador (Fabric, Quilt, Forge o NeoForge), id y versión propia del mod, más la versión de Minecraft que declaraba | `Fabric · cool v0.4.0` |

En datapacks, texture packs y plugins, si el elemento ya declaraba la versión destino, se muestra un aviso.

## Datapacks

El conversor:

- Escribe el formato del pack en `pack.mcmeta` según la versión destino:
  - Hasta 1.21.8 usa `pack_format`.
  - Desde 1.21.9 usa `min_format` y `max_format`.
- Opcionalmente (activado por defecto) ajusta los nombres de carpetas a la versión destino: en 1.21 y posteriores usa singular (`functions` → `function`, `loot_tables` → `loot_table`); en versiones anteriores usa plural.
- Al bajar de versión avisa de que los cambios de contenido (atributos, predicados, comandos…) no se revierten automáticamente.

### Tabla de formatos

| Versión | Data pack format |
|---|---|
| 1.11 – 1.12.2 (solo referencia; los datapacks empiezan en 1.13) | 3 |
| 1.13 – 1.14.4 | 4 |
| 1.15 – 1.16.1 | 5 |
| 1.16.2 – 1.16.5 | 6 |
| 1.17 – 1.17.1 | 7 |
| 1.18 – 1.18.1 | 8 |
| 1.18.2 | 9 |
| 1.19 – 1.19.3 | 10 |
| 1.19.4 | 12 |
| 1.20 – 1.20.1 | 15 |
| 1.20.2 | 18 |
| 1.20.3 – 1.20.4 | 26 |
| 1.20.5 – 1.20.6 | 41 |
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

## Texture packs

El conversor:

- Actualiza `pack.mcmeta` según la versión destino:
  - Hasta 1.21.8 usa `pack_format`.
  - Desde 1.21.9 usa `min_format` y `max_format`.
- Avisa si las carpetas de texturas no coinciden con la versión: hasta 1.12 son `blocks/` e `items/`; desde 1.13, `block/` e `item/`. Las carpetas **no se renombran automáticamente**.
- Conserva el resto del `pack.mcmeta`, incluidos los `overlays`, que no se modifican.
- Avisa si el pack tiene modelos de ítems en `models/item/` sin definiciones en `items/` (desde 1.21.4 hacen falta).

Un contenedor puede tener varios texture packs como carpetas, `.zip` o `.rar`. Un pack con una carpeta `data/` y sin `assets/` se rechaza y debe subirse a la pestaña **Datapacks**.

### Tabla de formatos

| Versión | Resource pack format |
|---|---|
| 1.11 – 1.12.2 | 3 |
| 1.13 – 1.14.4 | 4 |
| 1.15 – 1.16.1 | 5 |
| 1.16.2 – 1.16.5 | 6 |
| 1.17 – 1.17.1 | 7 |
| 1.18 – 1.18.2 | 8 |
| 1.19 – 1.19.2 | 9 |
| 1.19.3 | 12 |
| 1.19.4 | 13 |
| 1.20 – 1.20.1 | 15 |
| 1.20.2 | 18 |
| 1.20.3 – 1.20.4 | 22 |
| 1.20.5 – 1.20.6 | 32 |
| 1.21 – 1.21.1 | 34 |
| 1.21.2 – 1.21.3 | 42 |
| 1.21.4 | 46 |
| 1.21.5 | 55 |
| 1.21.6 | 63 |
| 1.21.7 – 1.21.8 | 64 |
| 1.21.9 – 1.21.10 | 69 |
| 1.21.11 | 75 |
| 26.1 – 26.1.2 | 84 |
| 26.2 | 88 |
| 26.3 | 97.1 |

Los formatos se tomaron de la wiki de Minecraft (Pack format) y de la tabla de packsmc. El formato 3 es el de los packs de recursos 1.11–1.12.2: Minecraft no lo carga como datapack. Los de 26.x se contrastaron con los changelogs de 26.2 y 26.3.

## Plugins

El conversor:

- Actualiza `api-version` en `plugin.yml` o `paper-plugin.yml`. Como `api-version` solo existe desde 1.13, al elegir 1.12.x se **elimina** esa línea y se avisa en el resumen.
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
- **Cargador no disponible en la versión destino**: Fabric y Quilt empiezan en 1.14, y `mods.toml` en 1.13. Si eliges una versión anterior, se avisa.

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

Además, con Node.js y una simulación del DOM se probaron la lógica de conversión y la detección de versiones, incluidas las bajadas de versión (carpetas en plural, eliminación de `api-version` antes de 1.13 y avisos de texturas): texture packs sueltos, con rango `min_format`/`max_format`, en contenedores (carpetas y .zip anidados) y con datapacks mal subidos; plugins `plugin.yml` y `paper-plugin.yml`; mods Fabric y NeoForge; y contenedores de plugins y mods con elementos válidos e inválidos. También se comprobó que los datapacks no cambian con esta versión.

Sin violaciones de CSP ni errores de página. La sintaxis del script principal se comprobó con `node --check`.

No se probó un `.rar` real que contenga un datapack completo.
