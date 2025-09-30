
Este laboratorio tiene una función de verificación de existencias que obtiene datos de un sistema interno.

Para resolver el laboratorio, cambie la URL de verificación de existencias para acceder a la interfaz de administración en http://localhost/admin y elimine el usuario carlos.

El desarrollador ha desplegado dos defensas anti-SSRF débiles que deberás evitar.

----

Para este método primero interceptamos el botón de **Check stock**

![[Pasted image 20241219111045.png]]



Interceptamos la petición y la mandamos al Intruder, URLcodeamos la información en el **referer** y ahora podemos hacer varios intentos para enviar la petición por el **local host** tipo; 217.0.0.1, 217.1 etc

Ahora encontrado el primer filtro vemos que al admin tambien lo tiene bloqueado así que lo podemos urlcodear para bypasearlo 

recuerden urlcodear al porcentaje ya que les saldrá error tambien `%`, así que toca hacer dos urlcodeadas para lograr el bypass 

![[Pasted image 20241219112119.png]]


Lo enviamos y vemos que nos da un 200 ok y si revisamos el render vemos que nos muestra los dos usuarios 

![[Pasted image 20241219112221.png]]

Ahora filtramos en el **raw** por : **delete** y encontraremos la función con permisos administrativos para eliminar el usuario Carlos, lo copiamos despues de nuestro **admin** URLcodeado para que siga funcionando y lo enviamos 

![[Pasted image 20241219112845.png]]
El cual nos dará un error 302 que sería buena señal, así que le damos al **follow redirection**

Nos saldrá el panel de congratulations

![[Pasted image 20241219112740.png]]

![[Pasted image 20241219112917.png]]
