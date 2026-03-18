# EtiScan — Lector de Etiquetas con Impresión Zebra

## ¿Qué hace esta app?
1. **Abre la cámara trasera** del celular
2. **Captura la etiqueta** de una caja (texto impreso o escrito con plumón)
3. **Analiza el texto** con Claude AI (extrae cliente, destino, peso, guía, etc.)
4. **Genera una etiqueta interna** con código de barras CODE128 propio
5. **Envía el ZPL** directamente a la impresora Zebra por red WiFi

---

## Cómo usar

### Opción 1 — Subir a un servidor web (recomendado para producción)
```bash
# Pon los 3 archivos en tu servidor web con HTTPS:
# - index.html
# - manifest.json
# - sw.js

# Ejemplo con Python (solo para prueba local):
python3 -m http.server 8080
# Luego abre: http://localhost:8080
```

**⚠️ La cámara REQUIERE HTTPS** en producción. Usa:
- GitHub Pages (gratis)
- Netlify (gratis)
- Vercel (gratis)
- Tu propio servidor con certificado SSL

### Opción 2 — GitHub Pages (más simple)
1. Crea un repositorio en GitHub
2. Sube los 3 archivos
3. Ve a Settings → Pages → Source: main
4. Accede desde tu celular a `https://tuusuario.github.io/tu-repo`

### Instalar como app nativa
En iOS: Safari → Compartir → "Añadir a pantalla de inicio"
En Android: Chrome → Menú → "Instalar aplicación"

---

## Configuración de IA (Claude AI)
1. Ve a **Ajustes** (⚙ arriba a la derecha)
2. Ingresa tu **API Key de Anthropic** (obtenla en console.anthropic.com)
3. Sin API Key funciona en **modo demo** con datos de ejemplo

---

## Configuración de Impresora Zebra

### Cómo encontrar la IP de tu Zebra
- En la impresora: mantén presionado el botón Feed hasta que imprima una página de configuración
- La IP aparece en esa hoja
- También puedes entrar a tu router y ver los dispositivos conectados

### Puertos estándar Zebra
- **9100**: Puerto raw TCP (más común)
- **80**: HTTP API (Link-OS)

### La app intenta dos métodos de conexión:
1. `POST http://[IP]/pstprnt` — API HTTP de Zebra Link-OS
2. `POST http://[IP]:9100` — Puerto raw

### ⚠️ Requisito importante
El celular y la impresora deben estar en la **misma red WiFi**.

### Si no logras imprimir directamente
Usa el botón **"Copiar ZPL al portapapeles"** y envía el contenido a:
- **ZebraDesigner** (Windows)
- **Zebra Setup Utilities**
- Cualquier software que acepte ZPL

---

## Tamaños de etiqueta soportados
| Tamaño | Uso típico |
|--------|-----------|
| 4×6 pulgadas | Etiqueta de despacho estándar |
| 4×4 pulgadas | Etiqueta cuadrada mediana |
| 2×1 pulgadas | Etiqueta pequeña para bultos |

---

## Campos de la etiqueta generada
- **Nombre de empresa** (configurable)
- **Nombre de cliente** (extraído por IA)
- **Descripción del producto**
- **Número de bulto / guía**
- **Peso**
- **Destino / dirección**
- **Referencia original**
- **Código de barras CODE128** (generado automáticamente: PREFIJO + FECHA + SECUENCIA)
- **Fecha y hora** del escaneo

---

## Estructura de archivos
```
etiqueta-scanner/
├── index.html      ← App completa (HTML + CSS + JS)
├── manifest.json   ← Configuración PWA
└── sw.js           ← Service Worker (offline)
```

---

## Tecnologías usadas
- **Vanilla JS** + CSS — sin frameworks, carga instantánea
- **MediaDevices API** — acceso a cámara
- **Claude API (claude-opus-4-5)** — análisis de imagen con vision
- **JsBarcode** — generación de código de barras CODE128
- **ZPL II** — lenguaje de programación de impresoras Zebra
- **PWA** — instalable como app nativa

---

## Personalización del ZPL
El archivo `index.html` contiene la función `generateZPL()` que puedes modificar
para adaptar el diseño exacto de tu etiqueta. Consulta la referencia ZPL en:
https://www.zebra.com/content/dam/zebra/manuals/printers/common/programming/zpl-zbi2-pm-en.pdf
