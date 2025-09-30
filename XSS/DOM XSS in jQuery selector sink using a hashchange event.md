### Contexto del Laboratorio

El laboratorio de PortSwigger presenta una vulnerabilidad de Cross-Site Scripting basado en DOM (DOM XSS) en la página de inicio. Esta vulnerabilidad está relacionada con el uso de la función `$()` de jQuery para seleccionar elementos del DOM utilizando el valor de `location.hash`.

**Descripción Oficial:**

> Este laboratorio contiene una vulnerabilidad DOM-based cross-site scripting en la página de inicio. Utiliza la función $() de jQuery como selector para hacer scroll automático hacia una publicación, cuyo título se pasa mediante la propiedad `location.hash`.

El objetivo del laboratorio es explotar esta vulnerabilidad para que, al visitar nuestro exploit, el navegador de la víctima ejecute la función `print()`.

### Análisis del Código Malicioso

Este es el código de explotación utilizado:

```html
<iframe src="https://0a1f009304e97509803b44300043007c.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```

#### Desglosando el Código

##### 1. Creación del Iframe

```html
<iframe src="https://0a1f009304e97509803b44300043007c.web-security-academy.net/#" ...>
```

Esto genera un `iframe` que carga la página vulnerable. La URL contiene `#` que representa el fragmento de la URL, es decir, la parte que sigue después del signo `#`.

##### 2. Atributo `onload`

```html
onload="this.src+='<img src=x onerror=print()>'"
```

Cuando el `iframe` termina de cargar, se ejecuta el evento `onload`. La instrucción que se ejecuta concatena el siguiente fragmento a la URL cargada:

```html
<img src=x onerror=print()>
```

Este código malicioso se introduce en el fragmento `location.hash`. Como la página vulnerable utiliza jQuery de forma insegura para insertar el valor del `hash` en el DOM, el navegador interpretará y ejecutará el código HTML y JavaScript que se encuentra en el `hash`.

##### 3. Efecto del Payload

Cuando el navegador interpreta:

```html
<img src=x onerror=print()>
```

Se intenta cargar una imagen con un valor inválido `x`. Como el recurso no existe, se activa el evento `onerror`, que ejecuta la función `print()`. Esta función abre el cuadro de diálogo de impresión del navegador de la víctima.

### Entendiendo la Vulnerabilidad

La vulnerabilidad DOM XSS ocurre porque el valor de `location.hash` es manipulado por el atacante y luego es interpretado como código HTML/JavaScript por jQuery. Cuando se usa la función `$()` con entradas controladas por el usuario, se puede generar inyección de código si no se escapa adecuadamente el contenido.

### Resumen del Flujo de Explotación

1. La víctima visita el exploit que contiene el `iframe` malicioso.
2. El `iframe` carga la página vulnerable con el fragmento modificado.
3. jQuery interpreta el `hash` como código HTML.
4. El navegador ejecuta el código `<img src=x onerror=print()>`.
5. Aparece el cuadro de diálogo de impresión.

### Solución Segura

- Evitar usar `$()` con valores no controlados por el usuario.
- Usar funciones como `text()` o `attr()` para insertar contenido de forma segura.
- Escapar adecuadamente los valores que provienen de `location.hash`.

### Referencias

- [DOM-based XSS | PortSwigger](https://portswigger.net/web-security/dom-based)
- [jQuery Security Best Practices](https://learn.jquery.com/security/)

Este writeup está diseñado para facilitar la comprensión del funcionamiento del script y la vulnerabilidad subyacente. Es ideal para quienes están aprendiendo sobre XSS basado en DOM y la manipulación de fragmentos de URL en jQuery.