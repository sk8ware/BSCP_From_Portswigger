
---

### 🧭 Identificación de Puntos Finales de API – Resumen

Explorar la aplicación que usa la API te puede dar **mucha información útil**, incluso si ya tienes la documentación (que podría estar incompleta o desactualizada).

---

### 🛠️ Herramientas recomendadas

- 🔍 **Burp Scanner**:  
    Rastrear automáticamente la aplicación.
    
- 🌐 **Burp Browser**:  
    Navegación manual para analizar rutas interesantes.
    

---

### 🧠 Qué debes observar

- 📌 URLs con estructuras típicas de API, como `/api/`
    
- 📄 Archivos JavaScript que contengan rutas de API no visibles directamente.
    

---

### 🧪 Herramientas para detectar rutas en JS

- 🧷 **JS Link Finder (BApp)**:  
    Extrae endpoints desde archivos JavaScript automáticamente.
    
- 🖱️ También puedes revisar manualmente los JS desde Burp para descubrir rutas ocultas.
    

---

> **"Requires Burp Suite Professional"**

**JS Link Finder solo se puede usar si tienes la versión Pro de Burp Suite**. 

---

### 🔄 ¿Opciones alternativas?


#### ✅ **1. Usar JS Link Finder manualmente (fuera de Burp)**

El autor del plugin también tiene una versión **standalone en Python** que hace lo mismo desde consola:

- Repositorio: [https://github.com/InitRoot/JSFinder](https://github.com/InitRoot/JSFinder)
    
- Puedes clonar y usarlo así:
    

```bash
git clone https://github.com/InitRoot/JSFinder.git
cd JSFinder
python3 jsfinder.py -u https://target.com
```

#### ✅ **2. Revisar JS con otras herramientas**

Puedes usar:

- **LinkFinder**: también en Python → [https://github.com/GerbenJavado/LinkFinder](https://github.com/GerbenJavado/LinkFinder)
    
- **Waybackurls + gau + hakrawler** (si usas recon para Bug Bounty).
    
- O buscar enlaces en JS manualmente desde Burp → en la pestaña **Target > Sitemap**, inspeccionando archivos `.js`.
    

---

