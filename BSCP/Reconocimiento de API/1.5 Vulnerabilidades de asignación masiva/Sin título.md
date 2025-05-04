
---

### 🧥 Laboratorio: Explotación de Mass Assignment para aplicar descuento

**Objetivo**: Comprar una chaqueta sin suficiente crédito aprovechando un parámetro oculto (`chosen_discount`).

---

### 🧠 Conceptos aplicados:

- Mass assignment permite que campos internos (no visibles en formularios) sean manipulados.
    
- Comparamos estructuras JSON de las solicitudes **GET** y **POST** a `/api/checkout`.
    
- Observamos un parámetro oculto: `chosen_discount`.
    

---

### 📌 Paso a paso para explotarlo:

1. 🔐 **Inicio de sesión** con:
    
    ```
    Usuario: wiener
    Contraseña: peter
    ```
    
2. 🛒 **Agregar producto** al carrito:
    
    - Producto: **Chaqueta l33t**
        
    - Intentar hacer checkout → Error por falta de fondos.
        
3. 🔍 En **HTTP history**:
    
    - Comparar la respuesta de `GET /api/checkout` con la solicitud `POST /api/checkout`.
        
    - Se detecta que la respuesta contiene el campo:
        
        ```json
        "chosen_discount": {
            "percentage": 0
        }
        ```
        
4. 📤 Enviar la solicitud `POST /api/checkout` al **Repeater**.
    
5. 🛠️ Modificar el cuerpo JSON, añadiendo manualmente:
    
    ```json
    "chosen_discount": {
        "percentage": 100
    }
    ```
    
6. ✅ Enviar la solicitud.  
    Si devuelve `HTTP/2 201 Created` y redirige a `/cart/order-confirmation?order-confirmed=true`, ¡éxito!
    
7. ❌ Si pones un valor inválido como `"percentage": "x"` o `400`, la app responde con error `400`, confirmando que el campo es procesado internamente.
    ![[Pasted image 20250503203206.png]]
    ![[Pasted image 20250503203325.png]]

---

### 🧠 Lección:

Siempre revisa las respuestas **GET** que revelan estructuras JSON internas. Pueden ayudarte a identificar parámetros ocultos válidos para explotar vulnerabilidades de asignación masiva.

---

