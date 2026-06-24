# Formato de documentos corporativos OCASA — Especificación para generación de .docx

> Este archivo le indica al agente **exactamente** cómo debe verse el `.docx` que genera, para que
> respete al 100 % el template oficial de OCASA (`Template Políticas`). RR.HH. es estricto con el
> formato: cualquier desvío respecto de lo descripto acá se considera un error.
>
> **Stack objetivo:** librería `docx` (Node/JS). Las unidades y los ejemplos de código usan su API.
> Todos los valores están dados en su unidad nativa de OOXML **y** en su equivalente humano.

---

## 0. Reglas de oro (no negociables)

Estas son las tres cosas que hoy se generan mal. Tienen prioridad sobre cualquier otra consideración:

1. **El ÍNDICE es un campo TOC automático de Word**, NO una lista escrita a mano.
   Se construye con `TableOfContents` y toma las entradas de los estilos `Heading 1..6`.
   → Ver §6.
2. **La línea celeste del pie de página** (`#009AA8`) va en **todas** las hojas, ocupa el **ancho
   completo** de la página (sangra hasta los bordes) y mide ~0,37 cm de alto. → Ver §8.
3. **El logo OCASA** va en el **encabezado, arriba a la izquierda** (no en el cuerpo, no en el pie).
   Usar el archivo `assets/ocasa-logo.png` que se entrega junto a este documento. → Ver §7.

Además, **siempre**:
- **El teal de los documentos Word es `#009AA8`**, NO `#1DB4C2` (ese es el teal de las
  *slides*; en el `.docx` no se usa). El texto principal es `#262626` / `#000000`, no `#1C1915`.
- Fuente **Montserrat** en todo el documento (títulos, cuerpo, header, footer, tablas).
- Tamaño A4, márgenes y colores exactos de las tablas siguientes.
- Portada distinta del resto (primera página con header/footer propios).

---

## 0 bis. Diferencias contra la salida actual del agente (qué corregir, valor por valor)

Comparación entre lo que el agente genera hoy (`PROCESO-Alta de proveedores`) y lo que exige el
template. Corregir **cada** fila:

| Aspecto | Hoy genera ❌ | Template exige ✅ |
|---|---|---|
| **Teal** | `#1DB4C2` (teal de slides) | `#009AA8` |
| **Color de texto** | `#1C1915` | `#262626` (cuerpo `#000000`) |
| **Márgenes** | 2,54 / 2,54 / 2,0 / 2,0 cm (1440/1440/1134/1134) | 1,5 / 1,5 / 1,9 / 1,9 cm (851/851/1077/1077) |
| **1ª página distinta** | no (`titlePg=0`) | sí (`titlePage: true`) |
| **Portada** | título 22 pt **bold** `#1C1915`, arriba de la pág. 1, con el TOC pegado debajo | título **29 pt sin negrita** `#262626`, **centrado en página propia**, luego salto de página |
| **Índice** | TOC `\o "1-2"` pegado al título, **sin rótulo**, misma página | **página propia** + rótulo **"ÍNDICE"** (10 pt bold) + TOC `\t "Heading 1..6"` |
| **Heading 1** | 12 pt bold `#1DB4C2` | **10 pt** bold `#009AA8` |
| **Heading 2** | 11 pt bold `#1C1915` (negro) | **10 pt** bold **`#009AA8`** (teal, igual que H1) |
| **Cuerpo** | 10 pt `#1C1915`, **sin justificar**, interlineado simple | 10 pt `#000000`, **justificado**, **interlineado 1,5**, 12 pt antes/después |
| **Header — título** | **bold** `#666666` | **regular** `#767171` |
| **Header — reglas** | líneas grises arriba y abajo (`#CCCCCC`) | **sin líneas** (solo logo + título) |
| **Header — logo** | 2,78 × 0,58 cm | 2,73 × 0,79 cm |
| **Footer — texto** | 7 pt `#666666` | 8 pt `#262626` (N° de pág. 9 pt) |
| **Footer — línea** | **línea gris** arriba (`#CCCCCC`) | **barra celeste `#009AA8`** a ancho completo, **abajo** |
| **Footer — N° de página** | falta (solo `/`) | campo `PAGE` + ` /  N°`, a la derecha |
| **Footer — código** | `PRO - MC - 0001 - V00` todo en una línea | **3 líneas**: código / `Versión XX` / `Vencimiento (…)` |
| **Tabla de aprobación** | filas `Revisión` / `Aprobación`; cabecera en Title Case; teal `#1DB4C2` | **BORRADOR / REVISIÓN / VALIDACIÓN**; cabecera en MAYÚSCULAS; teal `#009AA8` |

**Notas sobre contenido (no romper lo que está bien):**
- El esquema de secciones del PRO (Objetivo, Alcance, Responsables, Glosario, Actividades, etc.)
  está OK; acá solo se corrige el **formato visual**.
- El **código** que arma hoy (`PRO-MC-0001`, tipo-área-número) es válido y más rico que el
  `XXX-0000` del template. Conservalo, pero mostralo con el **layout de 3 líneas** del footer (§8.1)
  y separá la versión y el vencimiento en sus propias líneas.
- La tabla **"Historial de revisiones"** no existe en el template oficial. Si la organización la pide,
  está bien mantenerla, pero con Montserrat y teal `#009AA8` (no `#1DB4C2`). El versionado formal
  vive en el **footer** + la **tabla de aprobación**.

---

## 1. Configuración de página (A4)

| Propiedad | Valor OOXML (twips) | Equivalente | docx (Node) |
|---|---|---|---|
| Tamaño | 11906 × 16838 | A4 (210 × 297 mm) | `size: { width: 11906, height: 16838 }` |
| Margen superior | 851 | 1,5 cm | `margin: { top: 851 }` |
| Margen inferior | 851 | 1,5 cm | `margin: { bottom: 851 }` |
| Margen izquierdo | 1077 | 1,9 cm | `margin: { left: 1077 }` |
| Margen derecho | 1077 | 1,9 cm | `margin: { right: 1077 }` |
| Distancia al header | 709 | 1,25 cm | `margin: { header: 709 }` |
| Distancia al footer | 709 | 1,25 cm | `margin: { footer: 709 }` |
| Primera página distinta | `titlePg = 1` | sí | `titlePage: true` |

```js
const section = {
  properties: {
    page: {
      size: { width: 11906, height: 16838 },
      margin: { top: 851, bottom: 851, left: 1077, right: 1077, header: 709, footer: 709 },
    },
    titlePage: true,
  },
  headers: { default: headerDefault, first: headerFirst },
  footers: { default: footerDefault, first: footerFirst },
  children: [ /* portada, índice, cuerpo, tabla de firmas */ ],
};
```

---

## 2. Paleta de colores (exacta)

| Uso | HEX | Dónde |
|---|---|---|
| Celeste/teal OCASA | `#009AA8` | títulos de sección, bordes y fondos de tablas, **línea del footer**, fondo de celdas de etiqueta en la tabla de firmas |
| Texto principal | `#262626` | cuerpo, código del footer, número de página |
| Gris secundario | `#767171` | título del documento en el **encabezado** |
| Negro | `#000000` | "ÍNDICE", subtítulos, texto de cuerpo alternativo |
| Blanco | `#FFFFFF` | texto sobre celdas teal |

> No introducir otros colores. No usar degradados.

---

## 3. Tipografía

- **Familia única: Montserrat** (`ascii`, `hAnsi`, `eastAsia`, `cs` = `Montserrat`).
- En `docx`, definir la fuente por defecto del documento para no repetirla en cada run:

```js
const doc = new Document({
  styles: { default: { document: { run: { font: "Montserrat", color: "262626" } } } },
  features: { updateFields: true }, // hace que Word actualice el TOC al abrir
  sections: [ section ],
});
```

Tamaños (recordar: en `docx` `size` está en **medios puntos** → 10 pt = `size: 20`):

| Elemento | Tamaño | `size` | Peso | Color |
|---|---|---|---|---|
| Título de portada | 29 pt | 58 | normal | `262626` |
| "ÍNDICE" (encabezado del índice) | 10 pt | 20 | **bold** | `000000` |
| Título de sección (cuerpo) | 10 pt | 20 | **bold** | `009AA8` |
| Cuerpo / párrafos | 10 pt | 20 | normal | `000000` |
| Subtítulo inline ("A. Subtítulo:") | 10 pt | 20 | **bold** | `000000` |
| Título del doc en el header | 9 pt | 18 | normal | `767171` |
| Código / versión / vencimiento (footer) | 8 pt | 16 | mixto (ver §8) | `262626` |
| Número de página (footer) | 9 pt | 18 | normal | `262626` |

---

## 4. Estructura del documento (orden)

1. **Portada** (página 1, con header/footer de primera página).
2. *Salto de página.*
3. **Índice** — encabezado "ÍNDICE" + **campo TOC automático**.
4. *Salto de página.*
5. **Cuerpo** — secciones con títulos numerados (estilos Heading) + párrafos + listas.
6. **Tabla de firmas** (Borrador / Revisión / Validación) al final.

---

## 5. Portada

- Varios párrafos vacíos arriba para empujar el título hacia el centro vertical (el template usa ~7).
- **Título del documento**: Montserrat **29 pt** (`size: 58`), color `262626`, **sin negrita**, **centrado**.
  Reemplaza el placeholder `ESCRIBIR TÍTULO AQUÍ`.
- Después del título: salto de página hacia el índice.

```js
new Paragraph({
  alignment: AlignmentType.CENTER,
  spacing: { before: 0, after: 0, line: 240, lineRule: "auto" },
  children: [ new TextRun({ text: tituloDoc, font: "Montserrat", size: 58, color: "262626" }) ],
});
```

---

## 6. Índice — campo TOC automático (FIX #1)

**Mal:** escribir manualmente "1. Objetivo …… 3" como párrafos.
**Bien:** un encabezado "ÍNDICE" seguido de un **campo TOC** que Word arma solo a partir de los estilos Heading.

- Encabezado: párrafo "ÍNDICE", Montserrat 10 pt (`size: 20`), **bold**, color `000000`, alineado a la izquierda.
- Campo TOC con el instructivo exacto del template:
  `TOC \h \u \z \t "Heading 1,1,Heading 2,2,Heading 3,3,Heading 4,4,Heading 5,5,Heading 6,6,"`

```js
import { TableOfContents, Paragraph, TextRun } from "docx";

const indice = [
  new Paragraph({
    spacing: { before: 0, after: 0 },
    children: [ new TextRun({ text: "ÍNDICE", bold: true, size: 20, color: "000000", font: "Montserrat" }) ],
  }),
  new TableOfContents("Índice", {
    hyperlink: true,
    headingStyleRange: "1-6",
  }),
];
```

> Requisitos para que el índice salga bien:
> - Los títulos de sección del cuerpo **deben usar estilos `Heading 1/2/3`** (§7), no formato directo.
>   Si son texto suelto, el TOC sale vacío.
> - Setear `features: { updateFields: true }` en el `Document` para que Word actualice el índice
>   (números de página + entradas) al abrir el archivo.

---

## 7. Encabezado (header) con logo (FIX #3)

Tabla de 2 columnas, sin bordes:

- **Celda izquierda:** logo OCASA (`assets/ocasa-logo.png`), tamaño **2,73 cm × 0,79 cm**
  (= 982124 × 285750 EMU).
- **Celda derecha:** título del documento, Montserrat **9 pt** (`size: 18`), color `767171`,
  **alineado a la derecha**. Reemplaza el placeholder `TÍTULO AQUÍ`.

```js
import { Header, ImageRun, Table, TableRow, TableCell, BorderStyle, WidthType } from "docx";
import fs from "fs";

const noBorder = { style: BorderStyle.NONE, size: 0, color: "FFFFFF" };
const sinBordes = { top: noBorder, bottom: noBorder, left: noBorder, right: noBorder,
                    insideHorizontal: noBorder, insideVertical: noBorder };

const headerDefault = new Header({
  children: [
    new Table({
      width: { size: 100, type: WidthType.PERCENTAGE },
      borders: sinBordes,
      rows: [ new TableRow({ children: [
        new TableCell({ borders: sinBordes, children: [ new Paragraph({ children: [
          new ImageRun({
            data: fs.readFileSync("assets/ocasa-logo.png"),
            transformation: { width: 103, height: 30 }, // px ≈ 2,73 × 0,79 cm
          }),
        ]})]}),
        new TableCell({ borders: sinBordes, children: [ new Paragraph({
          alignment: AlignmentType.RIGHT,
          children: [ new TextRun({ text: tituloDoc, font: "Montserrat", size: 18, color: "767171" }) ],
        })]}),
      ]})],
    }),
  ],
});
```

> **No** agregar reglas/bordes grises (`#CCCCCC`) arriba ni abajo del header: el template no las tiene.
> El título va en peso **regular** (no bold) y color `#767171`.
>
> El header de **primera página** (`headerFirst`) lleva el mismo logo + título. (En el template ambos
> son idénticos; mantené uno solo y reutilizalo en `default` y `first`.)

---

## 8. Pie de página + línea celeste (FIX #2)

El footer tiene **dos partes**:

### 8.1 Bloque de datos (tabla de 3 columnas, sin bordes)

- **Columna izquierda** — tres líneas, Montserrat **8 pt** (`size: 16`), color `262626`:
  1. `XXX-0000` → **código del documento** (negrita). Ver §10.
  2. `Versión` (normal) + ` XX` (negrita) → `Versión XX`.
  3. `Vencimiento ` (normal) + `(DÍA/MES/AÑO)` → fecha de vencimiento.
- **Columna derecha** — número de página: campo `PAGE` + ` /  N°`, Montserrat **9 pt** (`size: 18`),
  color `262626`, alineado a la derecha.

### 8.2 La línea celeste (barra full-bleed)

En el template es un **rectángulo relleno** color `#009AA8`, de **21,21 cm × 0,37 cm**
(7634924 × 134297 EMU), anclado al **borde inferior** de la página y sangrando hasta los bordes
(ancho completo, más allá de los márgenes). Va en **todas** las hojas, incluida la portada.

Como la librería `docx` no maneja shapes ancladas con sangrado, usar **una de estas dos
aproximaciones** (ambas aceptadas por RR.HH. siempre que el color y el grosor coincidan):

**Opción A — tabla de 1 celda rellena (recomendada, controla alto y color):**

```js
import { Footer, Table, TableRow, TableCell, ShadingType, WidthType, VerticalAlign } from "docx";

const barraCeleste = new Table({
  width: { size: 100, type: WidthType.PERCENTAGE },
  borders: sinBordes,
  indent: { size: -260, type: WidthType.DXA }, // empuja la barra hacia los márgenes
  rows: [ new TableRow({
    height: { value: 210, rule: "exact" }, // ~0,37 cm
    children: [ new TableCell({
      borders: sinBordes,
      shading: { type: ShadingType.CLEAR, fill: "009AA8", color: "009AA8" },
      children: [ new Paragraph({ spacing: { before: 0, after: 0 }, children: [] }) ],
    })],
  })],
});
```

**Opción B — borde inferior teal grueso** en un párrafo a ancho completo del footer
(`border.bottom = { style: SINGLE, size: 84, color: "009AA8" }`, donde `size` está en octavos de
punto → 84 ≈ 10,5 pt ≈ 0,37 cm).

Footer completo (bloque de datos + barra):

```js
const footerDefault = new Footer({ children: [ tablaDatosFooter, barraCeleste ] });
// Reutilizar el mismo objeto para footers.first (la portada también lleva la barra).
```

---

## 9. Cuerpo

### 9.1 Títulos de sección (numerados) — deben ser estilos Heading

Para que el TOC (§6) funcione, los títulos van con **estilos `Heading 1/2/3`** configurados para verse
como el template: Montserrat **10 pt** (`size: 20`), **bold**, color `009AA8`, justificado,
interlineado **1,5** (`line: 360`), `keepNext`, con **numeración multinivel**:

| Nivel | Formato | Ejemplo |
|---|---|---|
| Heading 1 (ilvl 0) | decimal `%1.` | `1.` |
| Heading 2 (ilvl 1) | letra minúscula `%2.` | `a.` |
| Heading 3 (ilvl 2) | romano minúsculo `%3.` | `i.` |

```js
// En styles del Document:
paragraphStyles: [
  {
    id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
    run: { font: "Montserrat", size: 20, bold: true, color: "009AA8" },
    paragraph: { alignment: AlignmentType.JUSTIFIED, spacing: { line: 360, lineRule: "auto" }, keepNext: true },
  },
  // Heading2 / Heading3 idénticos en run; cambia sólo el nivel de numeración.
],
```

Numeración multinivel (en `numbering.config`): nivel 0 `"%1."` decimal, nivel 1 `"%2."` lowerLetter,
nivel 2 `"%3."` lowerRoman, todos alineados a la izquierda.

### 9.2 Párrafos de cuerpo

- Montserrat **10 pt** (`size: 20`), color `000000`, **justificado** (`AlignmentType.JUSTIFIED`).
- Interlineado **1,5** (`line: 360, lineRule: "auto"`).
- Espacio antes y después: **12 pt** (`before: 240, after: 240`).

```js
new Paragraph({
  alignment: AlignmentType.JUSTIFIED,
  spacing: { before: 240, after: 240, line: 360, lineRule: "auto" },
  children: [ new TextRun({ text: parrafo, font: "Montserrat", size: 20, color: "000000" }) ],
});
```

### 9.3 Subtítulos inline

Formato `A. Subtítulo:` en **negrita** (negro), seguido del texto en peso normal, dentro del mismo
párrafo justificado.

### 9.4 Listas

- Viñetas: jerarquía `●` → `o` → `▪`.
- Numeradas: usar la numeración multinivel de §9.1 cuando corresponda a títulos; para listas dentro
  del cuerpo, decimal `1.` / letra `a.`.

---

## 10. Tabla de firmas (Borrador / Revisión / Validación)

Va al final del documento. Tabla de **3 columnas**, **centrada**, ancho total ~8205 twips
(columnas: 2400 / 2880 / 2925 twips ≈ 4,2 / 5,1 / 5,2 cm).

- **Bordes:** teal `#009AA8`. Borde superior de la fila de cabecera ~1 pt (`size: 8`), bordes internos
  finos 0,25 pt (`size: 2`).
- **Celdas de etiqueta (columna 1) y fila de cabecera:** fondo teal `#009AA8`, texto **blanco**,
  centrado vertical (`VerticalAlign.CENTER`).
- **Celdas de datos:** fondo blanco `#FFFFFF`, contenido a completar (placeholder `xxx`).

Contenido:

| | NOMBRE Y POSICIÓN | *FIRMA Y FECHA |
|---|---|---|
| **BORRADOR** | _(a completar)_ | _(a completar)_ |
| **REVISIÓN** | _(a completar)_ | _(a completar)_ |
| **VALIDACIÓN** | _(a completar)_ | _(a completar)_ |

- Fila de cabecera (teal): celda 1 `BORRADOR`, celda 2 `NOMBRE Y POSICIÓN`, celda 3 `*FIRMA Y FECHA`.
- Filas siguientes: etiqueta teal en col 1 (`REVISIÓN`, `VALIDACIÓN`), celdas de datos en blanco.
- Texto en MAYÚSCULAS para las etiquetas; Montserrat.

```js
const tealShade = { type: ShadingType.CLEAR, fill: "009AA8", color: "009AA8" };
const bordeTeal = { style: BorderStyle.SINGLE, size: 2, color: "009AA8" };
// celda etiqueta:
new TableCell({
  shading: tealShade, verticalAlign: VerticalAlign.CENTER,
  children: [ new Paragraph({ alignment: AlignmentType.CENTER,
    children: [ new TextRun({ text: "REVISIÓN", bold: true, color: "FFFFFF", font: "Montserrat", size: 20 }) ] }) ],
});
```

---

## 11. Metadatos (reglas de código, versión, vencimiento)

Estos tres datos se muestran en el **pie de página** (§8.1) y deben pedirse/validarse antes de generar:

- **Código** (`XXX-0000`): prefijo + guion + número de 4 dígitos.
  - Prefijo según el **tipo de documento**: `PRO` (Proceso), `SOP` (Procedimiento), `INS` (Instructivo).
    Si la organización usa código de área en su lugar, respetar ese criterio, pero mantener el patrón
    `LLL-NNNN`.
  - Número secuencial de 4 dígitos con ceros a la izquierda (ej. `PRO-0007`).
- **Versión** (`Versión XX`): entero de 2 dígitos con cero a la izquierda (ej. `Versión 01`).
  Subir el número en cada revisión.
- **Vencimiento** (`(DÍA/MES/AÑO)`): fecha en formato `DD/MM/AAAA`.

> El **título** del documento aparece en la portada (§5) y en el encabezado (§7).
> El **autor/área** no va impreso en el cuerpo del template: usalo para `docProps` (metadata del
> archivo) y, si corresponde, en la tabla de firmas.

---

## 12. Checklist de QA antes de entregar el .docx

- [ ] Fuente Montserrat en TODO (incluidos header, footer y tablas).
- [ ] A4, márgenes 1,5 / 1,5 / 1,9 / 1,9 cm; primera página distinta.
- [ ] Portada: título 29 pt centrado, color `262626`.
- [ ] Índice = **campo TOC automático** (no lista manual); `updateFields: true`.
- [ ] Títulos de sección con estilos Heading (para que el TOC los tome), 10 pt bold teal `009AA8`,
      numerados `1. / a. / i.`.
- [ ] Cuerpo justificado, 10 pt, interlineado 1,5, espacio 12 pt antes/después.
- [ ] Header: logo OCASA arriba-izquierda (2,73 × 0,79 cm) + título a la derecha 9 pt `767171`.
- [ ] Footer: código/versión/vencimiento (8 pt) + N° de página (9 pt) + **línea celeste `009AA8`**
      a ancho completo, en todas las hojas.
- [ ] Tabla de firmas al final: Borrador/Revisión/Validación, etiquetas teal con texto blanco,
      bordes teal.
- [ ] Código con patrón `LLL-NNNN`, versión `NN`, vencimiento `DD/MM/AAAA`.

---

## 13. Activos entregados

- `assets/ocasa-logo.png` — logo OCASA oficial (PNG transparente, 2048×430). Usar en el encabezado.
