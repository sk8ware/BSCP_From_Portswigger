
## Descripción del laboratorio

Este laboratorio de PortSwigger contiene una vulnerabilidad de Cross-Site Scripting (XSS) basado en DOM en la funcionalidad de búsqueda. La vulnerabilidad se encuentra en el uso de `document.write`, que inserta directamente el contenido de `location.search` en la página sin ninguna sanitización.

Para explotar esta vulnerabilidad, podemos inyectar código malicioso dentro de la URL del sitio web y lograr la ejecución de JavaScript en el navegador de la víctima.

## Análisis de la vulnerabilidad

La función `document.write` es peligrosa cuando se usa con datos controlables por el usuario, ya que permite la ejecución directa de código HTML y JavaScript. En este caso, `document.write` está utilizando `location.search`, lo que significa que cualquier valor pasado en la URL después del signo de interrogación (`?`) se insertará sin validación en la página.

### Ejemplo de código vulnerable

Supongamos que el sitio web tiene un código similar a este:

```javascript
<script>
    document.write("Resultados de búsqueda: " + location.search);
</script>
```

Si el usuario accede a la página con una URL como:

```
https://victima.com/?search=test
```

El navegador interpretará la línea como:

```html
Resultados de búsqueda: ?search=test
```

Pero si el atacante modifica la URL con un payload malicioso, puede lograr la ejecución de código JavaScript en el navegador de la víctima.

## Explotación

Para explotar esta vulnerabilidad, podemos inyectar un payload que cierre la cadena abierta de `document.write` y agregue una etiqueta `<svg>` con un evento `onload` para ejecutar JavaScript.

### Payload utilizado:

```
hunter"><svg onload=alert(1)>
```

### URL maliciosa generada:

```
https://victima.com/?search=hunter%22%3E%3Csvg%20onload%3Dalert(1)%3E
```

### Explicación del payload:

1. **`hunter"`**: Cierra cualquier atributo abierto dentro de `document.write`.
2. **`><svg onload=alert(1)>`**: Inyecta una etiqueta SVG que ejecuta `alert(1)` cuando se carga en la página.

Al visitar esta URL, se ejecuta el `alert(1)`, confirmando la explotación exitosa de la vulnerabilidad.

## Solución y Mitigación

Para evitar este tipo de vulnerabilidades, se recomienda:

- No usar `document.write` para insertar datos dinámicos.
- Usar `textContent` en lugar de `innerHTML` o `document.write`.
- Validar y sanitizar los valores de entrada del usuario.
- Implementar una política de seguridad de contenido (CSP) estricta para restringir la ejecución de scripts no autorizados.

## Conclusión

Este laboratorio demuestra cómo una implementación insegura de `document.write` puede exponer una aplicación web a ataques XSS basados en DOM. Es fundamental evitar el uso de `document.write` con datos no confiables y adoptar prácticas seguras de desarrollo web para proteger las aplicaciones contra este tipo de vulnerabilidades.