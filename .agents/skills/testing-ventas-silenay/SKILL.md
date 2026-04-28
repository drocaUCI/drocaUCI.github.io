# Testing Ventas Silenay App

## Overview
Ventas Silenay is a static HTML/CSS/JS sales tracking app hosted on GitHub Pages. No build tools, no server — just open `index.html` in a browser.

## Local Testing Setup
1. Open `file:///path/to/index.html` in Chrome
2. No server needed — all data persists in `localStorage` with prefix `ventas_silenay_diario_`
3. Chrome may need to be started manually with `--remote-debugging-port=29229` for CDP access

## Entering Test Data
Use Playwright via CDP for reliable data entry:
```python
from playwright.async_api import async_playwright
browser = await p.chromium.connect_over_cdp("http://localhost:29229")
```
Key selectors:
- Product inputs: `#cuerpoTablaDiario input[placeholder="Nombre del producto"]`
- Total inputs: `#cuerpoTablaDiario .total-input`
- Investment inputs: `#cuerpoTablaDiario .inversion-input`

## PDF Export Testing
- The app uses `html2pdf.js` with jsPDF configuration
- Two export functions: daily ("Trabajo Diario" tab) and monthly ("Resumen Mensual" tab)
- To verify PDF orientation programmatically, parse the downloaded PDF's `/MediaBox` entry:
  - Portrait A4: `[0 0 595.28 841.89]` (height > width)
  - Landscape A4: `[0 0 841.89 595.28]` (width > height)
- Set Chrome download directory via CDP `Page.setDownloadBehavior` before clicking export
- Daily PDFs download as `ventas_roca_YYYY-MM-DD.pdf`
- Monthly PDFs download as `ventas_roca_resumen_YYYY-MM.pdf`

## Export/Import JSON Testing
- "Exportar Datos" button downloads a JSON backup of all localStorage data
- "Importar Datos" button restores from a JSON file
- Test cycle: export → delete all → import → verify data matches

## Key UI Elements
- Tabs: "Trabajo Diario" (daily) and "Resumen Mensual" (monthly)
- Bottom buttons: Agregar Fila, Eliminar Todo, Exportar PDF, Enviar WhatsApp, Exportar Datos, Importar Datos
- Toast notifications appear for all actions (success/error/info/warning)
- Search bar filters products in real time
- Delete row buttons (✕) on each row

## Common Issues
- The `google-chrome` wrapper script on Devin's VM connects via CDP — if Chrome isn't running, start the actual binary from `/opt/.devin/chrome/`
- Chrome "Restore pages" dialog may appear — dismiss it before testing
- PDF content may render very small visually, but orientation can be verified programmatically via MediaBox

## Devin Secrets Needed
None — this is a fully static app with no authentication or API keys required.
