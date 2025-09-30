## Descripción del Laboratorio

Este laboratorio de PortSwigger presenta una vulnerabilidad de **DOM-based Cross-Site Scripting (XSS)** en la funcionalidad de búsqueda del blog. El código JavaScript de la aplicación extrae datos de `location.search` y los asigna a un elemento `div` utilizando `innerHTML`, lo que permite la inyección de código malicioso.

### Objetivo del Laboratorio

Para resolver el laboratorio, debemos ejecutar un **XSS** que dispare un `alert()` en el navegador de la víctima.

---

## Análisis de la Vulnerabilidad

### ¿Qué es `location.search`?

`location.search` contiene la cadena de consulta (query string) de la URL después del `?`. Por ejemplo:

```
https://example.com/search?query=test
```

Aquí, `location.search` sería:

```
?query=test
```

Si la aplicación toma directamente este valor y lo inserta en el DOM sin sanitización, es posible inyectar código malicioso.

### Uso de `innerHTML`

`innerHTML` es una propiedad de los elementos HTML que permite modificar su contenido en formato HTML. Si el valor asignado proviene de una fuente no confiable (como `location.search`), un atacante puede ejecutar JavaScript arbitrario.

Ejemplo inseguro:

```javascript
var searchQuery = new URLSearchParams(window.location.search).get('query');
document.getElementById('search-results').innerHTML = "Resultados para: " + searchQuery;
```

Si no hay validación, un atacante podría inyectar código como:

```
?query=<imgj src=x onerror=alert('Hacked')>
```

El navegador interpretará `innerHTML` y ejecutará el `alert('Hacked')`.

---

## Explotación de la Vulnerabilidad

1. Accedemos al laboratorio y ubicamos el campo de búsqueda.
2. Interceptamos la petición o modificamos manualmente la URL para inyectar nuestro payload:
    
    ```
    https://example.com/search?query=<imgj src=x onerror=alert('Hacked')>
    ```
    
3. Al presionar Enter, el navegador ejecutará el código malicioso, activando el `alert('Hacked')`.

---

## Mitigación

Para prevenir esta vulnerabilidad, se deben aplicar las siguientes medidas:

4. **No usar `innerHTML`** para insertar contenido dinámico en el DOM. En su lugar, usar `textContent`:
    
    ```javascript
    document.getElementById('search-results').textContent = searchQuery;
    ```
    
5. **Escapar y sanitizar la entrada** antes de insertarla en el DOM.
6. **Utilizar bibliotecas de seguridad** como DOMPurify para limpiar el contenido antes de insertarlo:
    
    ```javascript
    document.getElementById('search-results').innerHTML = DOMPurify.sanitize(searchQuery);
    ```
    

---

## Conclusión

Este laboratorio demuestra cómo una mala práctica en la manipulación del DOM puede permitir ataques de **DOM XSS**. Exploitar la vulnerabilidad es tan sencillo como modificar la URL y agregar un payload malicioso. La mejor forma de prevenir este tipo de ataques es evitar `innerHTML` con datos no confiables y utilizar métodos seguros como `textContent` o bibliotecas especializadas para sanitización.