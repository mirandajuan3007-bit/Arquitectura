# 1. Diagnóstico de la arquitectura actual 

bueno teneindo en cuneta que el sistema es una aplicación de control escolar, por lo que nos podemos hacer a la idea de que actores/componente debe tener el sistema a alto nivel, pero el problema que tiene este sistema es que solo tiene un componenete (aunque est o solo un pprblema de mcuhos), bueno teniendo en cuenta esto podemos desglozar los problemas para tener un entendimiento mayor y saber que es lo que esta mal.

## Responsabilidades mezcladas, 
aqui podemos ver que rompe con el principio de SOLId que es single responsabilty, ya que como vemos el componenete unico que tiene el sistema, por lo que se vuelve un monolito sin capas, por lo que por obvis razones estos componetnees deberian de ser separados/modularizados para mayor mantenibilidad, y tambien para respetar el singlerspobalitites, lo que aqui tiene baja cohesion y esto afecta el codigo a la larga por la deuda tecnica

## Cohesión baja
si lo que vemos es baja chosion ya que el componente hace demasiadas cosas que no tienen sentido en tre si por lo que no tiene un porpropsito bien defindio y esto afecta al sistrem aya que para hacer cambios afecta a la reutilziacion y reducir errores.

## Acoplamiento alto
con esta en el mismo componente cada funcion esta en el mismo entrorn por lo que estan muy connetado po lo que si cambias algo afectaria a la funcionalidad de las de mas funciones.

## Dependencia directa de tecnologías. 
pues no tenemos mucha informacion acerca de l  as tecnologias qu son usadas, pero podemos ver que habla de "Accede directamente a la base de datos. " pero tambien en el mismo componente se comunica con otros sistemas, esto rompe el principioop de inversion de dependencia de SOLID, ya que la logica de negocio (alto nievel) depende de detalles concretos (BD, APIs externas, UI) en lugar de depender de abstracciones (interfaces).

## Duplicación de lógica. 
al igual que en el anterio no tenemos el codigo explicito, pero podemos deducirlo porque hay una parte que dice que cuando se hace conexion con otro sistema, varias partes de la aplicacion dejan de funcionar, por lo que esto nos puede decir que hay logica que se repite, indica que el código de conexión (URL, formato, autenticación) está copiado en cada función que lo usa.

## Ausencia de interfaces
no tenemos clases o componenetes que funcionannecn ocmo interfaz y esto puede causar que el codigo que se escribe de cada fcun que esta dentro del unico componente no tenga un contrato de como se ejcutan las acciones por lo que puede haber inconcistencias en el codigo y esto puede ser contraproducente, para eso sirven las interfaz

## Falta de manejo de errores
aunque no tengamos el codigo podemos ver algunos indicios de si la arquitectura tiene manejos de errores o si los tienes que sean buenos manejos de errores, el primer indicio es que dice "Cuando cambia la conexión con otro sistema, varias partes de la aplicación dejan de funcionar", por lo que esto nos puede decir que hay un fallo que no esta siendo encapsulado, ya que lo que deberia pasar es que si hay un error en una cierta parte por x o y falla lo que deberia de pasar es que solo esa falla por lo que no deberia afectar a la sotras partes del sistema ese es el mas evidente de todos, pero tambein podemos ver que como solo hay un solo componente  No hay separación entre capas, así que no existe un punto central que capture y trate los errores. Un error en el acceso a la base de datos puede llegar hasta el formulario sin ningún control intermedio.


##  Dificultad para realizar pruebas
al dificultad d ehace rpruebas se ve, ya que como sabemos es un componente con todas las funcionalidades, por lo que si quieres hacer pruebas no puedes separar los componentes para probarlos por separado, por lo que dificulta por ejemplo las pruebas unitarias y esto es un gran problema.

## Dependencia directa entre cliente y servicio remoto. 
el mismo componente que muestra formularios y calcula promedios es el que "se comunica con otros sistemas", sin ningún intermediario (adaptador, gateway o API propia) de por medio. Esto se confirma con "cuando cambia la conexión con otro sistema, varias partes de la aplicación dejan de funcionar", lo que indica que la aplicación conoce directamente la ubicación, el protocolo y el formato de datos del servicio remoto, y depende de que esté disponible. Por eso cualquier cambio o caída del otro sistema afecta directamente a la aplicación, cuando con un intermediario solo habría que ajustar ese punto.

# 2_Identificación de responsabilidades 

| Responsabilidad    | componente actual   | componente propuesto   | 
|---------|-----|-----|
| Mostrar información   | componente unico, muestra la inforamcion, pero si una interfaz definida  | tener una capa de presentacion (UI)  |
| Recibir acciones | componente unico, se hace con la funcionalida de " Se comunica con otros sistemas. "  | teniendo en cunta el modelo mvc, deberia ser un controlado que recibe la acción y decide qué hacer con ella: llama al modelo y luego elige qué vista mostrar| 
| Aplicar reglas  | ahora esta en componente, que es el unico que tenemos   | en este caso el compoente encarjado de eso, seria el de service que se encarga de ver todao la parte de validacion y reglas de negiocio  | 
| Acceder a datos  | ahora estan en el unicos componente | pero lo ideal seria usar un repositorio, (mvc)  | 
|Comunicar servicios  | ahora esya en el unico cmponente  | broker, porque se comunicar son sitemas externos | 
| Gestionar errores  | unco componente | deberiamos enviar todos los errores a un mismo lugar y ahi procesarcsalo (manejador cnetralizado de errores) | 


# 3. Selección de patrones 

como sabemos podemos coombinar dos modelo scomo vendria siendo mvc y broker ya que no chocan ya que estan en niveles diferentes.

mvc: organizacion interior de una pagina, se separa en Model, view y controlador 
broker: organiza la comunicacion con tecnologias diferentes y con sistemas por lo que comple optra funcion

estas dos se puden combinar y la verdad veo en este sistema que probablemente se tenga que usar ya que vemos indicios de que este sistema es porpenso al uso de ambos, 