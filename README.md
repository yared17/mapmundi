# mapmundi

Visualiza el mundo y, al **seleccionar un país**, colorea **otros países** cuya **suma de población** sea lo más cercana posible a la del país elegido. Útil para dimensionar mercados, audiencias o simplemente para aprender geografía demográfica de forma visual.

> Proyecto 100% cliente (HTML/CSS/JS). Sin dependencias de build ni backend.

---

## ✨ Características

- 🌍 **Mapa interactivo** con Leaflet y tiles de CARTO/OSM.  
- 🧮 **Combinación equivalente** de poblaciones usando una heurística de _subset-sum_ (greedy + múltiples pasadas aleatorias con tolerancia).  
- ⚡ **Caché local** (LocalStorage) de poblaciones para cargas rápidas.  
- 📦 **Descarga** del dataset de población en JSON.  
- 📱 **Responsive**: en móvil, el panel lateral se prioriza y la lista de equivalentes gana espacio.  
- ♿ Detalles de **accesibilidad** (atributos ARIA en la lista).

---

## 🧠 ¿Cómo funciona?

- **Fronteras (GeoJSON):** se descargan con CORS abierto desde Natural Earth (con respaldos en Datasets/Johan).  
- **Población:** [REST Countries](https://restcountries.com) (name, altSpellings, cca3, population).  
- **Unificación de nombres:** se indexan `name/official/altSpellings` → ISO3 para mapear capas del GeoJSON con el dataset de población.  
- **Algoritmo (resumen):**  
  1. Greedy de mayor a menor población para un baseline.  
  2. Varias pasadas con _offset_ y probabilidad dependiente del “progreso” hacia el objetivo.  
  3. Acepta soluciones ≤ objetivo × (1 + tolerancia).  
  4. Devuelve la mejor diferencia encontrada.

---

## 🚀 Uso rápido

1. Clona o descarga este repo.  
2. Sirve la carpeta en **un servidor estático** (recomendado para `fetch()` y Leaflet):

   ```bash
   # cualquiera de estos
   npx serve .
   # o
   python -m http.server 5500
   # o con VS Code: extensión "Live Server"
   ```

3. Abre `http://localhost:5500` (o el puerto que muestre tu servidor).  
4. Haz clic sobre un país y mira la combinación equivalente.

> Puedes abrir `index.html` directamente, pero algunos navegadores bloquean `fetch()` de ficheros locales; usa un servidor.

---

## 🗂️ Estructura

```
/ (raíz)
├── index.html        # App completa (HTML + CSS + JS)
├── docs/
│   └── screenshot.png (opcional)
└── README.md
```

---

## 🔧 Personalización rápida

**Altura del mapa en móvil** (usa `svh` para un comportamiento fiable en móviles):

```css
@media (max-width: 600px){
  #app{
    /* header (auto) / MAPA (≈45% de pantalla) / ASIDE (resto) */
    grid-template-rows: auto clamp(220px, 45svh, 420px) minmax(0, 1fr);
  }
}
```

**Límites/recorte del mundo** (menos Pacífico a los lados):

```js
const WORLD_TIGHT_BOUNDS = L.latLngBounds(
  L.latLng(-58, -150),
  L.latLng( 82,  150)
);
// map.setMaxBounds(WORLD_TIGHT_BOUNDS);
```

**Proveedor de tiles** (CARTO por defecto):

```js
L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', { ... })
// alternativas:
// 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
// 'https://{s}.tile.openstreetmap.fr/hot/{z}/{x}/{y}.png'
```

**Tolerancia y número de intentos** del algoritmo:

```js
findApproxCombination(universe, target, { tries: 420, tolerance: 0.02 })
// ↑ sube 'tries' para buscar más; ajusta 'tolerance' (p.ej. 0.03)
```

---

## 📝 Descarga de datos

Botón **“Descargar JSON de población”** → exporta el dataset actual (limpio y ordenado por nombre) para su uso offline.

---

## 🔐 Privacidad

- No se envía información a ningún servidor propio.  
- Solo se consultan las fuentes públicas (CORS) indicadas.  
- La caché de población se guarda en **LocalStorage** del navegador.

---

## ⚠️ Limitaciones conocidas

- Algunas micro-naciones/territorios pueden no coincidir 1:1 entre las fuentes (nombres y límites).  
- El algoritmo es **heurístico**: no garantiza la combinación óptima, pero encuentra soluciones buenas rápidamente.  
- En móviles, la UI se adapta; si el mapa o la lista se sienten “estrechos”, ajusta el bloque `@media (max-width: 600px)`.

---

## 💡 Roadmap corto

- 🔍 Buscador de países.  
- ♻️ Botón “limpiar selección”.  
- 📤 Exportar la **combinación equivalente** en CSV/JSON.  
- 🎨 Temas (claro/oscuro) con CSS variables.

---

## 🤝 Contribuir

1. Haz un fork y crea una rama (`feat/mi-mejora`).  
2. Asegura que `index.html` sigue funcionando sin build.  
3. Abre un PR con una descripción clara (antes/después y capturas si aplica).

---

## 📚 Créditos

- [Leaflet](https://leafletjs.com) — motor de mapas.  
- Tiles: [CARTO](https://carto.com) / [OpenStreetMap](https://www.openstreetmap.org).  
- Fronteras: [Natural Earth](https://www.naturalearthdata.com/) (respaldo Datasets/Johan).  
- Población: [REST Countries](https://restcountries.com/).  

---

## 📄 Licencia

MIT. Si lo usas para algo chulo, ¡cuéntame! 🙌
