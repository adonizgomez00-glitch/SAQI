---
name: rtl-aware-development
description: "RTL/LTR behavior para SAQI. CARGA BAJO DEMANDA: solo se activa cuando el agente detecta necesidad de RTL (archivos CSS con logical properties, dir='rtl', locales RTL en config, componentes portaled). Evita consumo de tokens en proyectos sin RTL."
trigger: detect-rtl
---

# RTL-Aware Development (Conditional Load)

**Solo se carga cuando se detecta necesidad de RTL.** El agente detecta:
- Archivos CSS con `padding-inline-start`, `inset-inline-end`, `border-inline-*`, `text-align: start`
- `dir="rtl"` o `dir="auto"` en HTML/JSX
- Locales RTL en config (ar, he, fa, ur, etc.)
- Componentes portaled (menus, popovers, drawers)
- Electron titlebar customization

---

## Guidelines (Cuando Activo)

### CSS Logical Properties (MUST)
```css
/* Avoid - Physical */
padding-left: 12px;
right: 0;
border-right: 1px solid;
text-align: left;
margin-left: 8px;

/* Prefer - Logical */
padding-inline-start: 12px;
inset-inline-end: 0;
border-inline-end: 1px solid;
text-align: start;
margin-inline-start: 8px;
```

### Mixed-Direction Text
```html
<!-- Unknown text: auto direction -->
<span class="file-row"><bdi dir="auto">README.md</bdi></span>

<!-- Code/URLs/paths: force LTR -->
<bdi dir="ltr"><code>C:\src\app.ts</code></bdi>
<bdi dir="ltr"><a href="https://example.com/path">link</a></bdi>
```

### Mirroring - Solo Significado Direccional
| Mirror | Don't Mirror |
|--------|--------------|
| Back/forward, prev/next | Brands, logos |
| Disclosure (chevron) | Clocks, media controls |
| Indentation, progress | Charts, data visualizations |
| Scroll direction | Text content |

### Interactions
- `clientX` = physical (no cambiar)
- Resize logical edge = RTL-aware delta
- Keyboard: ArrowLeft/ArrowRight swap en previous/next lógico
- Follow WAI-ARIA widget patterns

### Scrolling
```javascript
// Avoid: assumes LTR
element.scrollLeft = 100;

// Prefer: direction-aware
element.scrollIntoView({ inline: "nearest" });
// Or helper:
const scrollStart = getComputedStyle(element).direction === 'rtl' 
  ? element.scrollWidth - element.clientWidth 
  : 0;
```

### Electron Title Bars
- Native caption controls + `titleBarOverlay`
- `env(titlebar-area-*)` para safe content rectangle
- Windows/macOS native-control avoidance = physical
- App navigation inside rectangle = logical
- Interactive children: `app-region: no-drag`

---

## Test Matrix (Cuando Activo)

- [ ] English + LTR
- [ ] English + forced RTL (`dir="rtl"` en html)
- [ ] Real RTL locale + RTL (ar, he, fa)
- [ ] Mixed RTL/LTR content, long labels, numbers, code, paths
- [ ] Keyboard, pointer resize, scrolling, menus/submenus, Electron titlebar

---

## Detección Automática (Agent)

```typescript
function shouldLoadRTLSkill(project: Project): boolean {
  // 1. Check CSS for logical properties
  const cssFiles = glob("**/*.{css,scss,tsx,jsx}");
  for (const file of cssFiles) {
    const content = read(file);
    if (/\b(padding|margin|border|inset)-inline-(start|end)\b/.test(content)) return true;
    if (/\btext-align:\s*start\b/.test(content)) return true;
  }
  
  // 2. Check for dir="rtl" or dir="auto"
  const htmlFiles = glob("**/*.{html,tsx,jsx}");
  for (const file of htmlFiles) {
    const content = read(file);
    if (/dir\s*=\s*["']rtl["']/.test(content)) return true;
    if (/dir\s*=\s*["']auto["']/.test(content)) return true;
  }
  
  // 3. Check config for RTL locales
  const configFiles = glob("**/config*.{json,js,ts,yaml}");
  for (const file of configFiles) {
    const content = read(file);
    if (/(ar|he|fa|ur|ps|sd|ug|yi|dv)\b/.test(content)) return true;
  }
  
  // 4. Check for portaled components
  const componentFiles = glob("**/*.{tsx,jsx}");
  for (const file of componentFiles) {
    const content = read(file);
    if (/Portal|Popover|Menu|Drawer|Tooltip/.test(content)) return true;
  }
  
  return false;
}
```

---

## Referencias
- [RTL Styling 101, Ahmad Shadeed](https://rtlstyling.com/posts/rtl-styling/)
- [CSS-Tricks: RTL Styling 101](https://css-tricks.com/rtl-styling-101/)
- [CSS-Tricks: CSS Logical Properties](https://css-tricks.com/css-logical-properties-and-values/)
- [W3C: Structural markup RTL](https://www.w3.org/International/questions/qa-html-dir)
- [W3C: Inline bidirectional markup](https://www.w3.org/International/articles/inline-bidi-markup/)
- [MDN: CSS logical properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Logical_properties_and_values)
- [MDN: `dir` attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/dir)
- [MDN: `scrollLeft`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollLeft)
- [web.dev: Logical properties](https://web.dev/learn/css/logical-properties/)
- [Electron: Custom title bar](https://www.electronjs.org/docs/latest/tutorial/custom-title-bar)
- [WAI-ARIA: Window splitter](https://www.w3.org/WAI/ARIA/apg/patterns/windowsplitter/)
- [Kobalte: I18n Provider](https://kobalte.dev/docs/core/components/i18n-provider/)