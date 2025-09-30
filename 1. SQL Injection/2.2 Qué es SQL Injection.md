## ¿Qué es la inyección SQL (SQLi)?

La inyección SQL (SQLi) es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación realiza a su base de datos. Esto puede permitirle acceder a datos que normalmente no podría recuperar. Esto puede incluir datos de otros usuarios o cualquier otro dato al que la aplicación tenga acceso. En muchos casos, un atacante puede modificar o eliminar estos datos, provocando cambios persistentes en el contenido o el comportamiento de la aplicación.

En algunas situaciones, un atacante puede escalar un ataque de inyección SQL para comprometer el servidor subyacente u otra infraestructura de back-end. Esto también puede permitirle realizar ataques de denegación de servicio.

## ¿Cuál es el impacto de un ataque de inyección SQL exitoso?

Un ataque de inyección SQL exitoso puede resultar en un acceso no autorizado a datos confidenciales, como:

- Contraseñas.
- Detalles de la tarjeta de crédito.
- Información personal del usuario.

A lo largo de los años, se han utilizado ataques de inyección SQL en numerosas filtraciones de datos de gran repercusión. Estos han causado daños a la reputación y multas regulatorias. En algunos casos, un atacante puede obtener una puerta trasera persistente en los sistemas de una organización, lo que provoca una vulnerabilidad a largo plazo que puede pasar desapercibida durante un período prolongado.

## Cómo detectar vulnerabilidades de inyección SQL

Puede detectar la inyección SQL manualmente mediante un conjunto sistemático de pruebas en cada punto de entrada de la aplicación. Para ello, normalmente enviaría:

- El carácter de comilla simple `'`y busque errores u otras anomalías.
- Alguna sintaxis específica de SQL que evalúa el valor base (original) del punto de entrada y un valor diferente, y busca diferencias sistemáticas en las respuestas de la aplicación.
- Condiciones booleanas como `OR 1=1`y `OR 1=2`, y buscan diferencias en las respuestas de la aplicación.
- Cargas útiles diseñadas para activar retrasos de tiempo cuando se ejecutan dentro de una consulta SQL y buscar diferencias en el tiempo que tarda en responder.
- Cargas útiles de OAST diseñadas para activar una interacción de red fuera de banda cuando se ejecutan dentro de una consulta SQL y monitorear cualquier interacción resultante.

Alternativamente, puede encontrar la mayoría de las vulnerabilidades de inyección SQL de forma rápida y confiable utilizando Burp Scanner.

## Inyección SQL en diferentes partes de la consulta

La mayoría de las vulnerabilidades de inyección SQL ocurren dentro de la `WHERE`cláusula de una `SELECT`consulta. La mayoría de los evaluadores experimentados están familiarizados con este tipo de inyección SQL.

Sin embargo, las vulnerabilidades de inyección SQL pueden ocurrir en cualquier punto de la consulta y en diferentes tipos de consulta. Otros puntos comunes donde se producen inyecciones SQL son:

- En `UPDATE`las declaraciones, dentro de los valores actualizados o de la `WHERE`cláusula.
- En `INSERT`las declaraciones, dentro de los valores insertados.
- En `SELECT`declaraciones, dentro del nombre de la tabla o columna.
- En `SELECT`los enunciados, dentro de la `ORDER BY`cláusula.

## Ejemplos de inyección SQL

Existen numerosas vulnerabilidades, ataques y técnicas de inyección SQL que se presentan en diferentes situaciones. Algunos ejemplos comunes de inyección SQL incluyen:

- [Recuperación de datos ocultos](https://portswigger.net/web-security/sql-injection#retrieving-hidden-data) , donde puede modificar una consulta SQL para devolver resultados adicionales.
- [Subvertir la lógica de la aplicación](https://portswigger.net/web-security/sql-injection#subverting-application-logic) , donde puedes cambiar una consulta para interferir con la lógica de la aplicación.
- [Ataques UNION](https://portswigger.net/web-security/sql-injection/union-attacks) , donde puedes recuperar datos de diferentes tablas de bases de datos.
- [Inyección SQL ciega](https://portswigger.net/web-security/sql-injection/blind) , donde los resultados de una consulta que usted controla no se devuelven en las respuestas de la aplicación.

## Recuperando datos ocultos

Imagine una aplicación de compras que muestra productos en diferentes categorías. Cuando el usuario hace clic en la categoría " **Regalos"** , su navegador solicita la URL:

`https://insecure-website.com/products?category=Gifts`

Esto hace que la aplicación realice una consulta SQL para recuperar detalles de los productos relevantes de la base de datos:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Esta consulta SQL solicita a la base de datos que devuelva:

- todos los detalles ( `*`)
- de la `products`mesa
- donde `category`esta`Gifts`
- y `released`es `1`.

La restricción `released = 1`se utiliza para ocultar productos no publicados. Podríamos asumir que, para productos no publicados, `released = 0`...

La aplicación no implementa ninguna defensa contra ataques de inyección SQL. Esto significa que un atacante podría construir el siguiente ataque, por ejemplo:

`https://insecure-website.com/products?category=Gifts'--`

Esto da como resultado la consulta SQL:

`SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1`

Es fundamental tener en cuenta que `--`es un indicador de comentario en SQL. Esto significa que el resto de la consulta se interpreta como un comentario, eliminándolo. En este ejemplo, la consulta ya no incluye `AND released = 1`. Como resultado, se muestran todos los productos, incluidos los que aún no se han lanzado.

Puedes usar un ataque similar para hacer que la aplicación muestre todos los productos de cualquier categoría, incluidas las categorías que no conoce:

`https://insecure-website.com/products?category=Gifts'+OR+1=1--`

Esto da como resultado la consulta SQL:

`SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1`

La consulta modificada devuelve todos los elementos donde el valor `category`es `Gifts`, o `1`es igual a `1`. Como `1=1`siempre ocurre, la consulta devuelve todos los elementos.
## Subvirtiendo la lógica de la aplicación

Imagine una aplicación que permite a los usuarios iniciar sesión con nombre de usuario y contraseña. Si un usuario envía el nombre de usuario `wiener`y la contraseña `bluecheese`, la aplicación comprueba las credenciales mediante la siguiente consulta SQL:

`SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'`

Si la consulta devuelve los datos de un usuario, el inicio de sesión se ha realizado correctamente. De lo contrario, se rechaza.

En este caso, un atacante puede iniciar sesión con cualquier usuario sin necesidad de contraseña. Para ello, puede usar la secuencia de comentarios SQL `--`para eliminar la comprobación de contraseña de la `WHERE`cláusula de la consulta. Por ejemplo, al enviar el nombre de usuario `administrator'--`y una contraseña en blanco, se obtiene la siguiente consulta:

`SELECT * FROM users WHERE username = 'administrator'--' AND password = ''`

Esta consulta devuelve el usuario cuyo `username`es `administrator`y registra exitosamente al atacante como ese usuario.

## Recuperar datos de otras tablas de bases de datos

Si la aplicación responde con los resultados de una consulta SQL, un atacante puede usar una vulnerabilidad de inyección SQL para recuperar datos de otras tablas de la base de datos. Puede usar la `UNION`palabra clave para ejecutar una consulta adicional `SELECT`y anexar los resultados a la consulta original.

Por ejemplo, si una aplicación ejecuta la siguiente consulta que contiene la entrada del usuario `Gifts`:

`SELECT name, description FROM products WHERE category = 'Gifts'`

Un atacante puede enviar la entrada:

`' UNION SELECT username, password FROM users--`

Esto hace que la aplicación devuelva todos los nombres de usuario y contraseñas junto con los nombres y descripciones de los productos.

## Examinando la base de datos

Algunas características principales del lenguaje SQL se implementan de la misma manera en todas las plataformas de bases de datos populares, y muchas formas de detectar y explotar vulnerabilidades de inyección SQL funcionan de manera idéntica en diferentes tipos de bases de datos.

Sin embargo, también existen muchas diferencias entre las bases de datos comunes. Esto significa que algunas técnicas para detectar y explotar la inyección SQL funcionan de forma distinta en distintas plataformas. Por ejemplo:

- Sintaxis para la concatenación de cadenas.
- Comentarios.
- Consultas por lotes (o apiladas).
- API específicas de la plataforma.
- Mensajes de error.

#### Leer más

[Hoja de trucos sobre inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet)

Tras identificar una vulnerabilidad de inyección SQL, suele ser útil obtener información sobre la base de datos. Esta información puede ayudarle a explotar la vulnerabilidad.

Puede consultar los detalles de la versión de la base de datos. Distintos métodos funcionan para distintos tipos de bases de datos. Esto significa que si encuentra un método específico que funcione, puede inferir el tipo de base de datos. Por ejemplo, en Oracle puede ejecutar:

`SELECT * FROM v$version`

También puede identificar las tablas de la base de datos y las columnas que contienen. Por ejemplo, en la mayoría de las bases de datos, puede ejecutar la siguiente consulta para listar las tablas:

`SELECT * FROM information_schema.tables`

#### Leer más

- [Examinando la base de datos en ataques de inyección SQL](https://portswigger.net/web-security/sql-injection/examining-the-database)
- [Hoja de trucos sobre inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Vulnerabilidades de inyección SQL ciega

Muchos casos de inyección SQL son vulnerabilidades ciegas. Esto significa que la aplicación no devuelve los resultados de la consulta SQL ni los detalles de los errores de la base de datos en sus respuestas. Las vulnerabilidades ciegas aún pueden explotarse para acceder a datos no autorizados, pero las técnicas implicadas suelen ser más complejas y difíciles de implementar.

Las siguientes técnicas se pueden utilizar para explotar vulnerabilidades de inyección SQL ciega, dependiendo de la naturaleza de la vulnerabilidad y la base de datos involucrada:

- Puede cambiar la lógica de la consulta para generar una diferencia detectable en la respuesta de la aplicación según el cumplimiento de una sola condición. Esto podría implicar inyectar una nueva condición en la lógica booleana o generar condicionalmente un error, como una división por cero.
- Puede activar condicionalmente un retraso en el procesamiento de la consulta. Esto le permite inferir la veracidad de la condición basándose en el tiempo que tarda la aplicación en responder.
- Puede activar una interacción de red fuera de banda mediante técnicas OAST. Esta técnica es extremadamente potente y funciona en situaciones donde otras técnicas no lo hacen. A menudo, puede exfiltrar datos directamente a través del canal fuera de banda. Por ejemplo, puede colocar los datos en una búsqueda DNS para un dominio que controle.

#### Leer más

- [Inyección SQL ciega](https://portswigger.net/web-security/sql-injection/blind)



## Inyección SQL en diferentes contextos

En los laboratorios anteriores, usó la cadena de consulta para inyectar su carga útil SQL maliciosa. Sin embargo, puede realizar ataques de inyección SQL utilizando cualquier entrada controlable que la aplicación procese como consulta SQL. Por ejemplo, algunos sitios web toman la entrada en formato JSON o XML y la utilizan para consultar la base de datos.

Estos diferentes formatos pueden ofrecer distintas maneras de [ofuscar ataques](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-xml-encoding) que, de otro modo, estarían bloqueados por los WAF y otros mecanismos de defensa. Las implementaciones débiles suelen buscar palabras clave comunes de inyección SQL dentro de la solicitud, por lo que es posible que pueda eludir estos filtros codificando o escapando caracteres en las palabras clave prohibidas. Por ejemplo, la siguiente inyección SQL basada en XML utiliza una secuencia de escape XML para codificar el `S`carácter en `SELECT`:

`<stockCheck> <productId>123</productId> <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId> </stockCheck>`

Esto se decodificará en el lado del servidor antes de pasarlo al intérprete SQL.
## Cómo prevenir la inyección de SQL

Puede evitar la mayoría de los casos de inyección SQL mediante consultas parametrizadas en lugar de la concatenación de cadenas dentro de la consulta. Estas consultas parametrizadas también se conocen como "sentencias preparadas".

El siguiente código es vulnerable a la inyección SQL porque la entrada del usuario se concatena directamente en la consulta:

`String query = "SELECT * FROM products WHERE category = '"+ input + "'"; Statement statement = connection.createStatement(); ResultSet resultSet = statement.executeQuery(query);`

Puede reescribir este código de manera que evite que la entrada del usuario interfiera con la estructura de la consulta:

`PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?"); statement.setString(1, input); ResultSet resultSet = statement.executeQuery();`

Puede usar consultas parametrizadas en cualquier situación en la que aparezcan entradas no confiables como datos dentro de la consulta, incluyendo la `WHERE`cláusula y los valores de una sentencia `INSERT``or` `UPDATE`. No se pueden usar para gestionar entradas no confiables en otras partes de la consulta, como nombres de tablas o columnas, o la `ORDER BY`cláusula. La funcionalidad de la aplicación que coloca datos no confiables en estas partes de la consulta debe adoptar un enfoque diferente, por ejemplo:

- Inclusión en la lista blanca de valores de entrada permitidos.
- Utilizando una lógica diferente para proporcionar el comportamiento requerido.

Para que una consulta parametrizada sea eficaz a la hora de prevenir la inyección de SQL, la cadena utilizada en la consulta siempre debe ser una constante codificada. Nunca debe contener datos variables de ningún origen. Evite decidir caso por caso si un dato es confiable y continúe utilizando la concatenación de cadenas dentro de la consulta para los casos que se consideren seguros. Es fácil cometer errores sobre el posible origen de los datos o que cambios en otro código dañen datos confiables.

#### Leer más

- [Encuentre vulnerabilidades de inyección SQL utilizando el escáner de vulnerabilidades web de Burp Suite](https://portswigger.net/burp/vulnerability-scanner)
## Inyección SQL de segundo orden

La inyección SQL de primer orden ocurre cuando la aplicación procesa la entrada del usuario desde una solicitud HTTP e incorpora la entrada en una consulta SQL de manera insegura.

La inyección SQL de segundo orden ocurre cuando la aplicación toma la información del usuario de una solicitud HTTP y la almacena para su uso posterior. Esto suele hacerse colocando la información en una base de datos, pero no se produce ninguna vulnerabilidad en el punto donde se almacenan los datos. Posteriormente, al procesar otra solicitud HTTP, la aplicación recupera los datos almacenados y los incorpora a una consulta SQL de forma insegura. Por esta razón, la inyección SQL de segundo orden también se conoce como inyección SQL almacenada.

![Inyección SQL de segundo orden](https://portswigger.net/web-security/images/second-order-sql-injection.svg)

La inyección SQL de segundo orden suele ocurrir cuando los desarrolladores conocen las vulnerabilidades de inyección SQL y, por lo tanto, gestionan de forma segura la entrada inicial de la base de datos. Al procesarse posteriormente, los datos se consideran seguros, ya que previamente se habían almacenado de forma segura en la base de datos. En este punto, los datos se gestionan de forma insegura porque el desarrollador los considera erróneamente confiables.

## Examinando la base de datos

Algunas características principales del lenguaje SQL se implementan de la misma manera en todas las plataformas de bases de datos populares, y muchas formas de detectar y explotar vulnerabilidades de inyección SQL funcionan de manera idéntica en diferentes tipos de bases de datos.

Sin embargo, también existen muchas diferencias entre las bases de datos comunes. Esto significa que algunas técnicas para detectar y explotar la inyección SQL funcionan de forma distinta en distintas plataformas. Por ejemplo:

- Sintaxis para la concatenación de cadenas.
- Comentarios.
- Consultas por lotes (o apiladas).
- API específicas de la plataforma.
- Mensajes de error.

#### Leer más

[Hoja de trucos sobre inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet)

Tras identificar una vulnerabilidad de inyección SQL, suele ser útil obtener información sobre la base de datos. Esta información puede ayudarle a explotar la vulnerabilidad.

Puede consultar los detalles de la versión de la base de datos. Distintos métodos funcionan para distintos tipos de bases de datos. Esto significa que si encuentra un método específico que funcione, puede inferir el tipo de base de datos. Por ejemplo, en Oracle puede ejecutar:

`SELECT * FROM v$version`

También puede identificar las tablas de la base de datos y las columnas que contienen. Por ejemplo, en la mayoría de las bases de datos, puede ejecutar la siguiente consulta para listar las tablas:

`SELECT * FROM information_schema.tables`

#### Leer más

- [Examinando la base de datos en ataques de inyección SQL](https://portswigger.net/web-security/sql-injection/examining-the-database)
- [Hoja de trucos sobre inyección SQL](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Inyección SQL en diferentes contextos

En los laboratorios anteriores, usó la cadena de consulta para inyectar su carga útil SQL maliciosa. Sin embargo, puede realizar ataques de inyección SQL utilizando cualquier entrada controlable que la aplicación procese como consulta SQL. Por ejemplo, algunos sitios web toman la entrada en formato JSON o XML y la utilizan para consultar la base de datos.

Estos diferentes formatos pueden ofrecer distintas maneras de [ofuscar ataques](https://portswigger.net/web-security/essential-skills/obfuscating-attacks-using-encodings#obfuscation-via-xml-encoding) que, de otro modo, estarían bloqueados por los WAF y otros mecanismos de defensa. Las implementaciones débiles suelen buscar palabras clave comunes de inyección SQL dentro de la solicitud, por lo que es posible que pueda eludir estos filtros codificando o escapando caracteres en las palabras clave prohibidas. Por ejemplo, la siguiente inyección SQL basada en XML utiliza una secuencia de escape XML para codificar el `S`carácter en `SELECT`:

`<stockCheck> <productId>123</productId> <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId> </stockCheck>`

Esto se decodificará en el lado del servidor antes de pasarlo al intérprete SQL.