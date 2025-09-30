
Cuando buscamos por la página nos encontramos con un botón el cual lo podemos modificiar en burp 

![[Pasted image 20241212195841.png]]

Seleccionamos el stockApi= para URLcodesrles seleccionando todo el texto y aplastar 

```
CTRL + SHIFT + U
```

![[Pasted image 20241212200100.png]]

Adicional tenemos que URLcodear al `&` en el repetear seleccionandolo y aplastando `Ctrl + U` ya que si no lo hacemos nos saldrá un error 

Debería quedar tipo así 

![[Pasted image 20241212200518.png]]

ahora usamos la ruta que nos indicaron al principio que 

Luego nos dirigimos al **Raw** y filtramos por **Delete** y copiaríamos la ruta para ponerlo en el Repeater `/admin`

![[Pasted image 20241217120952.png]]

Le damos al **Send** y luego nos dará un 302 not found, así que ponemos en Follow redirection 

![[Pasted image 20241217121730.png]]

