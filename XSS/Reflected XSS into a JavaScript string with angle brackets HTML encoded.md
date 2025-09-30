
---

**Explotación de vulnerabilidad XSS reflejada con codificación HTML de corchetes angulares (PortSwigger Lab)**

En este laboratorio de PortSwigger, se presenta una vulnerabilidad de Cross-Site Scripting (XSS) reflejada en la funcionalidad de seguimiento de consultas de búsqueda, donde los corchetes angulares están codificados en HTML. El desafío consiste en realizar un ataque XSS que salga del string de JavaScript y ejecute la función `alert`.

### Pasos para resolver el laboratorio:

1. **Intercepción de la solicitud con Burp Suite:**
    
    - En el campo de búsqueda, se ingresó un string alfanumérico aleatorio.
    - Luego, se utilizó Burp Suite para interceptar la solicitud y enviarla a Burp Repeater.
2. **Observación del código JavaScript vulnerable:**
    
    - Al revisar la solicitud interceptada, se encontró un fragmento de código JavaScript en la respuesta que reflejaba la consulta de búsqueda dentro de una cadena JavaScript:
        
        ```javascript
        <script>
          var searchTerms = 'hunter';
          document.write('<img src="/resources/images/tracker.gif?searchTerms='+encodeURIComponent(searchTerms)
          +'">');
        </script>
        ```
        
    - Aquí, los corchetes angulares estaban codificados, pero la entrada del usuario aún se reflejaba en el código JavaScript.
3. **Inyección del payload XSS:**
    
    - Se reemplazó el contenido del campo de búsqueda con el siguiente payload para romper fuera del string JavaScript y ejecutar un `alert`:
        
        ```
        '-alert(1)-'
        ```
        
4. **Verificación del ataque:**
    
    - Para verificar que el ataque funcionó, se hizo clic derecho sobre el enlace, se seleccionó "Copiar URL", y se pegó la URL en el navegador.
    - Al cargar la página en el navegador, al hacer clic sobre el enlace, se activó el `alert(1)`, confirmando que el payload se ejecutó correctamente y la vulnerabilidad XSS fue explotada con éxito.

### Conclusión:

La vulnerabilidad de XSS reflejada en la funcionalidad de búsqueda fue explotada mediante la inyección de un payload JavaScript, que permitió romper fuera de la cadena JavaScript codificada y ejecutar un `alert`. Este tipo de vulnerabilidad puede ser utilizada para ejecutar código malicioso en el navegador de un usuario, lo que puede comprometer la seguridad de la aplicación.

---

