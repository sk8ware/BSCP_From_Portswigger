
---

**Explotación de vulnerabilidad XSS almacenada en el campo "Website" (PortSwigger Lab)**

En este laboratorio de PortSwigger, se encuentra una vulnerabilidad de Cross-Site Scripting (XSS) almacenada en la funcionalidad de comentarios. La solución consiste en inyectar un payload JavaScript en el campo "Website" que se ejecutará cuando el usuario haga clic en el nombre del autor del comentario.

### Pasos para resolver el laboratorio:

1. **Intercepción de la solicitud con Burp Suite:**
    
    - Primero, en el campo "Website", se insertó un string alfanumérico aleatorio.
    - Luego, se utilizó Burp Suite para interceptar la solicitud y enviarla a Burp Repeater.
2. **Visualización del comentario:**
    
    - Hice una segunda solicitud en el navegador para ver el comentario y nuevamente utilicé Burp Suite para interceptar la solicitud y enviarla a Burp Repeater.
    - En este punto, se observó que el string aleatorio introducido se reflejaba dentro del atributo `href` del enlace del comentario.
3. **Inyección del payload XSS:**
    
    - Repetí el proceso, pero esta vez reemplacé el contenido del campo "Website" con el siguiente payload JavaScript:
        
        ```
        javascript:alert('hacked')
        ```
        
    - Al hacer esto, el enlace generado contenía una URL maliciosa que ejecutaría un `alert` cuando se clickeaba.
4. **Verificación del ataque:**
    
    - Para verificar que el ataque funcionó, hice clic derecho sobre el enlace y seleccioné "Copiar URL", luego pegué la URL en el navegador.
    - Al hacer clic en el nombre del autor del comentario, se mostró el mensaje de alerta con el texto "hacked", confirmando que la vulnerabilidad XSS había sido explotada correctamente.

### Conclusión:

La vulnerabilidad de XSS almacenada en el campo "Website" fue explotada al inyectar un payload JavaScript, lo que permitió ejecutar código malicioso cuando el usuario hizo clic en el nombre del autor del comentario. Este tipo de vulnerabilidad es crítica ya que puede ser utilizada para robar información de usuarios o ejecutar acciones no autorizadas en el navegador del mismo.

---


