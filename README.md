# leandro-card

Tarjeta de contacto digital para **Leandro Montedonico**, Director de Torque and Taste.

**URL pública:** https://404mqs.github.io/leandro-card/  
**Repo:** https://github.com/404mqs/leandro-card

---

## Qué es

Página estática de un archivo — `index.html` — deployada en GitHub Pages. No tiene backend, dependencias npm ni build step. Para actualizar: editar el archivo y hacer push.

---

## Diseño

La UI está inspirada en [torqueandtaste.com](https://torqueandtaste.com/): estética editorial de lujo, minimalista y oscura.

- **Tipografía:** Cormorant Garamond (serif, Google Fonts) para el nombre. Sistema sans-serif para el resto.
- **Paleta modo oscuro:** fondo `#0d0c0b`, acentos dorados `#c9a96e`, card `#151210`.
- **Paleta modo claro:** fondo `#f4efe6`, acentos `#8a6020`, card `#ffffff`.
- **Border-radius:** 2px — deliberadamente arquitectónico, sin esquinas redondeadas.
- **Monograma** `L·M` en círculo con borde dorado en lugar de ícono genérico.
- **Company tag** con líneas decorativas a los costados (guiño al motivo tipográfico del sitio).

### Modo claro / oscuro

- Botón sol/luna en la esquina superior derecha de la card.
- Al cargar: detecta `prefers-color-scheme` del sistema operativo.
- La preferencia del usuario se persiste en `localStorage` (key: `tat-theme`).
- Script inline en `<head>` aplica el tema antes del primer paint → sin flash de pantalla.

---

## Agregar a Contactos

El botón genera un vCard v3.0 en memoria y lo entrega de forma nativa según la plataforma:

| Plataforma | Mecanismo |
|---|---|
| **iOS Safari** | Navega a `data:text/vcard;charset=utf-8,...` → la app Contactos lo intercepta automáticamente |
| **Android Chrome** | Navega a un Blob URL (`window.location.href`) sin atributo `download` → el SO lo abre con Contactos directamente (sin dialog de descarga) |
| **Desktop** | Descarga el archivo `leandro-montedonico.vcf` via `<a download>` |

> **Por qué no `a.download` en Android:** el atributo `download` le indica a Chrome que fuerce la descarga del archivo en lugar de delegarle el MIME type al sistema operativo. Sin él, Android reconoce `text/vcard` y abre la app de Contactos sin pasos intermedios.

El archivo `leandro.vcf` en el repo es una copia estática del mismo contacto (no se usa en producción, queda como respaldo legible).

---

## Datos del contacto

```
Nombre:   Leandro Montedonico
Cargo:    Director
Empresa:  Torque and Taste
Tel:      +54 11 3008-4910
Email:    lean@torqueandtaste.com
```

---

## Archivos

```
leandro-card/
├── index.html      # toda la app (HTML + CSS + JS inline)
├── leandro.vcf     # vCard estática de respaldo
└── qr.png          # QR dorado sobre negro apuntando a la URL de Pages
```

---

## QR

Generado con Python (`qrcode` + `Pillow`). Módulos redondeados, dorado `(201,169,110)` sobre negro `(13,12,11)`, label `torqueandtaste · leandro` en Georgia. Para regenerarlo:

```python
import qrcode
from qrcode.image.styledpil import StyledPilImage
from qrcode.image.styles.moduledrawers import RoundedModuleDrawer
from qrcode.image.styles.colormasks import SolidFillColorMask
from PIL import Image, ImageDraw, ImageFont

url = "https://404mqs.github.io/leandro-card/"

qr = qrcode.QRCode(version=2, error_correction=qrcode.constants.ERROR_CORRECT_H, box_size=14, border=3)
qr.add_data(url)
qr.make(fit=True)

img = qr.make_image(
    image_factory=StyledPilImage,
    module_drawer=RoundedModuleDrawer(),
    color_mask=SolidFillColorMask(front_color=(201,169,110), back_color=(13,12,11))
).convert("RGB")

w, h = img.size
pad, label_h = 40, 52
canvas = Image.new("RGB", (w + pad*2, h + pad*2 + label_h), (13,12,11))
canvas.paste(img, (pad, pad, pad+w, pad+h))

draw = ImageDraw.Draw(canvas)
font = ImageFont.truetype("C:/Windows/Fonts/Georgia.ttf", 18)
text = "torqueandtaste · leandro"
bbox = draw.textbbox((0,0), text, font=font)
draw.text(((canvas.width - (bbox[2]-bbox[0])) // 2, h+pad+16), text, fill=(201,169,110), font=font)

canvas.save("qr.png")
```

---

## Origen

Duplicado y rediseñado a partir de `monica-card` (mismo repo owner: `404mqs`).
