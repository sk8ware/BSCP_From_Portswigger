
Es casi igual el mismo procedimiento anterior, pero ahora más que el local host nos da una dirección ip imcompleta, asi que enviaremos al Intruder para poder encontrar 

Seleccionamos el último octeto de la ip, es decir el número 1 le ponemos en add para seleccionarlo para el payload

![[Pasted image 20241217124330.png]]

Luego en el apartado de payloads le indicamos un tipo de ataque numérico del 1 al 254 y luego a start atack 

![[Pasted image 20241217125006.png]]

Ahora sería cuestión de esperar y ver cuál da un resultado 200 ok para poder revisar 

Encontramos el que da un 200 ok con el numero 60 así que lo mandamos al repeater y le damos al send para filtrar el **delete** para el usuario Carlos

![[Pasted image 20241217130229.png]]

Pegamos sola la ruta indicada `/admin/delete?username=carlos` y veremos que nos da un error **302** y le damos al **Follow redirection**

![[Pasted image 20241217130720.png]]

Y habremos terminado el siguiente nivel 
