# Nekonata Network

## ¿Por qué?

Nekonata surge como la unión entre la red TOR y las criptodivisas anónimas. Basándose en el principio de que cuantos más usuarios tiene un sistema, más fácil es pasar desapercibido y por lo tanto más anónimo se es. 

## Nekonata

Pretende crear una red red distribuida e interconectada que garantice el anonimato y recompense a los usuarios que pongan sistemas a disposición de la red por medio de micropagos, por lo que los usuarios deberán hacer pequeños pagos para poder utilizar la red. Dicho dinero será repartido entre los nodos de la red. 

Para llevar a cabo dicho propósito se deberá además crear una criptomoneda con su correspondiente sistema de pagos y una cartera, dicha moneda será de dominio propio, por lo que controlando la velocidad de emisión de bloque y la dificultad, se podrá controlar la velocidad de las transacciones.

## Descripción del sistema

Para llevar a cabo dicho propósito, se deben crear las siguientes partes:

### Criptomoneda 

La criptomoneda empleada es una criptomoneda que habilita el pago anónimo por medio de firmas en anillo. Dicha criptomoneda es un fork de Forknote, que a su vez deriva de Bytecoin. Dicha criptomoneda ha sido configurada para que realice una emisión de bloque cada 10 segundos, por lo que la velocidad de las transacciones será elevada.

### Servidor central

El servidor central ha sido desarrollado en el micro web-framework Flask. Y su principal función es recibir un pago del cliente y validarlo, comprobando que la transacción se ha realizado correctamente. Una vez recibido el pago, generará una fecha futura dependiendo de la cantidad del pago y la encriptará haciendo uso de un par de claves RSA (pública en este caso), devolviendo lo que conocemos como token. Además dará de alta a nuevos nodos en la red de nodos y servirá la clave privada de RSA a los nodos para que validen el token.

### Red de nodos

La red de nodos consta de un conjunto de al menos 3 nodos desplegados, con idéntico contenido. Dichos nodos proporcionan al sistema la ofuscación de la información y el anonimato. Están desarrollados en Flask y utilizan las librerias de websockets SocketIO-client y Flask-SocketIO, para establecer las comunicaciones entre los nodos (actúan de maestro-esclavo simultáneamente para comunicarse entre los nodos del camino elegido).Al mandar una petición el cliente, cada nodo deberá validar si el token es válido utilizando la clave privada del servidor central, de no serlo terminará la conexión. 

Una vez que el cliente se conecta establece una llave con cada nodo usando Diffie-Hellman. Y la utiliza para desencriptar el mensaje con AES-CBF cada vez que pasa el mensaje por un nodo. Siendo conscientes de que si es el último nodo deberá realizar la petición a la url del mensaje e iniciar el camino de vuelta del mensaje.


### Cliente

El cliente será el encargado de iniciar toda comunicación con el usuario del sistema. Está basado en Electron, React, Redux y Ant Design. Permite realizar el pago y recibir el token del servidor central, así como administrarlos. Una vez ha recibido el token podrá realizar una petición a la red de nodos. 
Para ello realizará una petición al nodo central recibiendo los nodos y eligiendo 3 al azar, pudiendo comenzar la comunicación.
Primero generará una clave compartida con el nodo 1 usando diffie hellman y a través de este generará otra clave con el nodo 2, y a través del nodo 2 generará la clave con el nodo 3(nodos maestro-esclavo), haciendo que los nodos solo conozcan quien es su salto anterior y quien es el siguiente, pero no tenga otra información de la conexión. Una vez tiene las claves, generará 3 cifrados simétricos AES-CBF, encriptando el mensaje 3 veces consecutivas, por capas, empezando por el nodo de entrada, intermedio y de salida. Una vez ha encriptado el mensaje podrá hacer la petición y si el token es válido recibirá la respuesta correcta.


## Resultados y trabajo futuro

La implementación final del proyecto ha sido un prototipo funcional del sistema descrito. Hemos comprobado la viabilidad de mezclar ambas tecnologías y la posibilidad real de un ecosistema de servicios que garanticen el anonimato y a su vez permita la transferencia de bienes y valor entre usuarios.

Hemos conseguido implementar de principio a fin una interacción de usuario y uso del servicio, incluyendo el pago al servidor empleando la criptodivisa creada y el enrutamiento de tráfico entre nodos ofuscando origen y destino. 

Algunos de los aspectos a destacar del prototipo frente a lo que se considera un producto final son: 

- Cambio del algoritmo Diffie-Hellman convencional por ECDH (Diffie-Hellman de curva elíptica)

- Implementación del sistema de pagos a los nodos como remuneración por el trabajo, ya que esta funcionalidad no se ha podido implementar en un primer momento por limitaciones de las librerías y proyectos en los que se ha basado el trabajo.

- Mecanismos de mitigación de ataques de denegación de servicio


- Mecanismos para garantizar que un único usuario hace uso de un token particular, y que este es el mismo que ha realizado el pago (en el prototipo solamente se garantiza que el usuario que recibe el token está en posesión del ID de la transacción de pago).


- Mejora en el manejo de las conexiones por websocket entre usuarios y nodos: Hemos encontrado diversos problemas y limitaciones en el uso de la librería Flask-SocketIO. Para un entorno en producción, es recomendable migrar a una tecnología que facilite ese paradigma de funcionamiento (NodeJS, Golang, etc).

Finalmente, cabe comentar que la unión de ambas redes, la criptodivisa y los nodos, es un concepto interesante a estudiar en el futuro.
Consideramos que es posible encontrar un balance entre mantener la criptodivisa y la infraestructura de la red de anonimato usando los mismos nodos y minimizando así los problemas de acoplamiento entre ambas tecnologías. 

Esta idea queda como el mayor milestone al que se puede aspirar como trabajo futuro y estudio, junto con la creación de una interfaz amigable para que otros productos y servicios puedan ser alojados en la red.
