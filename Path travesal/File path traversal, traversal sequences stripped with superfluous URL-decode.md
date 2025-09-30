

En este caso un poco más hard vemos que ninguna de las anteriores tecnicas funcionaron asi que probaremos URLcodearlas para poder ver la data de /etc/passwd

1. La primera barra de `..//` la URLcodeamos y deberia quedar así 
![[Pasted image 20241212115237.png]]
![[Pasted image 20241212115433.png]]

Al momento de URLcodearlo nos cambiara a `%2f` en este caso nos toca ufuscar nuestro payload para poder ir para atras ya que la web el ../ nos lo quita y es el porcentaje `%` y quedaría finalmente en `..%252fetc/passwd` para que el ../lo represente de otra manera

Ahora nos tocaría repetir para probar el path traversal que queríamos realizar

```
..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252f/etc/passwd
```


![[Pasted image 20241212120140.png]]

También lo podríamos forzar directamente desde la web con `view-source:http://`

