# 1. Diagnostico de la arquitectura actual 

bueno teneindo en cuneta que el sistema es una aplicacion de control escolar, por lo que nos podemos hacer a la idea de que actores/componente debe tener el sistema a alto nivel, pero el problema que tiene este sistema es que solo tiene un componenete (aunque est o solo un pprblema de mcuhos), bueno teniendo en cuenta esto podemos desglozar los problemas para tener un entendimiento mayor y saber que es lo que esta mal.

## Responsabilidades mezcladas, 
aqui podemos ver que rompe con el principio de SOLId que es single responsabilty, ya que como vemos el componenete unico que tiene el sistema, por lo que se vuelve un monolito sin capas, por lo que por obvis razones estos componetnees deberian de ser separados/modularizados para mayor mantenibilidad, y tambien para respetar el singlerspobalitites, lo que aqui tiene baja cohesion y esto afecta el codigo a la larga por la deuda tecnica

## Cohesion baja
si lo que vemos es baja chosion ya que el componente hace demasiadas cosas que no tienen sentido en tre si por lo que no tiene un porpropsito bien defindio y esto afecta al sistrem aya que para hacer cambios afecta a la reutilziacion y reducir errores.

## Acoplamiento alto
con esta en el mismo componente cada funcion esta en el mismo entrorn por lo que estan muy connetado po lo que si cambias algo afectaria a la funcionalidad de las de mas funciones.

## Dependencia directa de tecnologias. 
pues no tenemos mucha informacion acerca de l  as tecnologias qu son usadas, pero podemos ver que habla de "Accede directamente a la base de datos. " pero tambien en el mismo componente se comunica con otros sistemas, esto rompe el principioop de inversion de dependencia de SOLID, ya que la logica de negocio (alto nievel) depende de detalles concretos (BD, APIs externas, UI) en lugar de depender de abstracciones (interfaces).

## Duplicacion de logica. 
al igual que en el anterio no tenemos el codigo explicito, pero podemos deducirlo porque hay una parte que dice que cuando se hace conexion con otro sistema, varias partes de la aplicacion dejan de funcionar, por lo que esto nos puede decir que hay logica que se repite, indica que el codigo de conexion (URL, formato, autenticacion) esta copiado en cada funcion que lo usa.

## Ausencia de interfaces
no tenemos clases o componenetes que funcionannecn ocmo interfaz y esto puede causar que el codigo que se escribe de cada fcun que esta dentro del unico componente no tenga un contrato de como se ejcutan las acciones por lo que puede haber inconcistencias en el codigo y esto puede ser contraproducente, para eso sirven las interfaz

## Falta de manejo de errores
aunque no tengamos el codigo podemos ver algunos indicios de si la arquitectura tiene manejos de errores o si los tienes que sean buenos manejos de errores, el primer indicio es que dice "Cuando cambia la conexion con otro sistema, varias partes de la aplicacion dejan de funcionar", por lo que esto nos puede decir que hay un fallo que no esta siendo encapsulado, ya que lo que deberia pasar es que si hay un error en una cierta parte por x o y falla lo que deberia de pasar es que solo esa falla por lo que no deberia afectar a la sotras partes del sistema ese es el mas evidente de todos, pero tambein podemos ver que como solo hay un solo componente  No hay separacion entre capas, asi que no existe un punto central que capture y trate los errores. Un error en el acceso a la base de datos puede llegar hasta el formulario sin ningun control intermedio.


##  Dificultad para realizar pruebas
al dificultad d ehace rpruebas se ve, ya que como sabemos es un componente con todas las funcionalidades, por lo que si quieres hacer pruebas no puedes separar los componentes para probarlos por separado, por lo que dificulta por ejemplo las pruebas unitarias y esto es un gran problema.

## Dependencia directa entre cliente y servicio remoto. 
el mismo componente que muestra formularios y calcula promedios es el que "se comunica con otros sistemas", sin ningun intermediario (adaptador, gateway o API propia) de por medio. Esto se confirma con "cuando cambia la conexion con otro sistema, varias partes de la aplicacion dejan de funcionar", lo que indica que la aplicacion conoce directamente la ubicacion, el protocolo y el formato de datos del servicio remoto, y depende de que este disponible. Por eso cualquier cambio o caida del otro sistema afecta directamente a la aplicacion, cuando con un intermediario solo habria que ajustar ese punto.

# 2_Identificacion de responsabilidades 

| Responsabilidad    | componente actual   | componente propuesto   | 
|---------|-----|-----|
| Mostrar informacion   | componente unico, muestra la inforamcion, pero si una interfaz definida  | tener una capa de presentacion (UI)  |
| Recibir acciones | componente unico, se hace con la funcionalida de " Se comunica con otros sistemas. "  | teniendo en cunta el modelo mvc, deberia ser un controlado que recibe la accion y decide que hacer con ella: llama al modelo y luego elige que vista mostrar| 
| Aplicar reglas  | ahora esta en componente, que es el unico que tenemos   | en este caso el compoente encarjado de eso, seria el de service que se encarga de ver todao la parte de validacion y reglas de negiocio  | 
| Acceder a datos  | ahora estan en el unicos componente | pero lo ideal seria usar un repositorio, (mvc)  | 
|Comunicar servicios  | ahora esya en el unico cmponente  | broker, porque se comunicar son sitemas externos | 
| Gestionar errores  | unco componente | deberiamos enviar todos los errores a un mismo lugar y ahi procesarcsalo (manejador cnetralizado de errores) | 


# 3. Seleccion de patrones 

como sabemos podemos coombinar dos modelo scomo vendria siendo mvc y broker ya que no chocan ya que estan en niveles diferentes.

mvc: organizacion interior de una pagina, se separa en Model, view y controlador 
broker: organiza la comunicacion con tecnologias diferentes y con sistemas por lo que comple optra funcion

estas dos se puden combinar y la verdad veo en este sistema que probablemente se tenga que usar ya que vemos indicios de que este sistema es porpenso al uso de ambos,. porque tenemos el uso interno en el recibimeinto de la accion, para recibir la accion hacer las reglas y aplicaralas  en est caso en nuestro sistema podemos decir que usaremos mvc, ya que esta hace la organizacion interna del sistema.
pero hay otras caracteristicas que tiene el sistema que no sueden hacer pesar que deveriamos usar de igual manera el patron broker, ya que vemos que tenemos comunicoa con sistemas externos y el uso de tecnologias dioeferentes por lo que para organizacion de la comunicacion usaremos el broker que nos ayuda a tene runc mejor comunicacion con sistemas externos con deventajas que heos explicado en la tarea anterior.

pero hay un cosa importante que hay que tener en cuenta en la parte de la organziacion total del sistema y es que el sistemas inclina a usar una aruitectua en capaz definida o oprganizada de la siguiente manera: 

## Organizacion de la arquitectura en capas

La arquitectura en capas es la que define la **estructura general** del sistema, y sobre ella se acomodan los otros dos patrones: MVC organiza la capa de presentacion (y su "Modelo" se reparte entre negocio y datos) y Broker se convierte en la capa de integracion con los sistemas externos. Por eso no chocan entre si: cada uno resuelve un problema a un nivel diferente.

```
┌─────────────────────────────────────────────────────────────┐
│ CAPA DE PRESENTACION  (MVC: Vista + Controlador)            │
│  Vistas: formularios de alumnos, calificaciones, reportes   │
│  Controladores: AlumnoController, CalificacionController    │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ CAPA DE NEGOCIO  (MVC: Modelo, parte logica)                │
│  Services: AlumnoService, CalificacionService               │
│  Reglas: validaciones, calculo de promedios, inscripcion    │
│  Depende de interfaces: IAlumnoRepo, ISistemaExterno        │
└──────────────┬───────────────────────────────┬──────────────┘
               ▼                               ▼
┌─────────────────────────────┐ ┌─────────────────────────────┐
│ CAPA DE DATOS               │ │ CAPA DE INTEGRACION (BROKER)│
│ (MVC: Modelo, parte datos)  │ │  Broker: localiza y enruta  │
│  Repositories → BD          │ │  Proxies hacia cada         │
│  Entidades: Alumno, Materia,│ │  sistema externo (traducen  │
│  Calificacion               │ │  formato y protocolo)       │
└─────────────────────────────┘ └─────────────────────────────┘

 TRANSVERSAL: manejador centralizado de errores (todas las capas)
```


## Ejemplo de flujo: registrar una calificacion

1. El profesor llena el formulario de calificacion (**Vista**) y lo envia; el `CalificacionController` recibe la accion (**presentacion / MVC**).
2. El controlador llama a `CalificacionService`, que valida que la calificacion este en rango y recalcula el promedio del alumno (**negocio**).
3. El servicio guarda la calificacion por medio de `CalificacionRepository` (**datos**).
4. Si la calificacion se tiene que reportar a otro sistema, el servicio llama a `ISistemaExterno`; el **broker** localiza el sistema y su proxy traduce el formato y el protocolo (**integracion**).
5. Si el sistema externo no responde, el error llega al **manejador centralizado**: la calificacion queda guardada y solo se avisa que el envio esta pendiente, sin afectar las demas funciones.
6. El controlador elige la vista de confirmacion que se le muestra al profesor (**MVC**).

## Como resuelve los problemas del diagnostico

| Problema detectado | Como lo resuelve la arquitectura en capas |
|--------------------|-------------------------------------------|
| Responsabilidades mezcladas | Cada capa tiene una sola responsabilidad (Single Responsibility) |
| Cohesion baja | Cada componente agupa solo funciones relacionadas entre si |
| Acoplamiento alto | Las capas se comunican por interfaces; cambiar una no rompe a las demas |
| Dependencia directa de tecnologias | Negocio depende de abstracciones; la BD y las APIs externas quedan en datos e integracion |
| Duplicacion de logica | La conexion con cada sistema externo vive en un solo proxy del broker |
| Ausencia de interfaces | Las interfaces entre capas funcionan como contrato |
| Falta de manejo de errores | El manejador centralizado captura y trata los errores de todas las capas |
| Dificultad para realizar pruebas | Cada capa se prueba por separado usando implementaciones falsas de las interfaces |
| Dependencia directa entre cliente y servicio remoto | El broker funciona como intermediario: si cambia el sistema externo solo se ajusta su proxy |


4. Diseño de la nueva arquitectura 

bueno ya vimos que patrones se usaran pero ahora haremos el diseño de la arquitectura, poara tener mas claro cada cosa de la que estamos hablando y que la arquitectura este mejor definida para eso el diseño, ya que lo que hemos hecho hasta ahora es solo definidrlo a alto niuvel, por lo que ahora lo que toca es hacerlo pero bajo nivel siendo mas especifico.

## componentes y responsabilidad

para que se ejemplifique mejor haremos uan tabla para entender mejor que componentes tiene.

| Componente | Capa | Responsabilidad |
|------------|------|-----------------|
| Vistas (formularios, reportes) | Presentacion | Mostrar informacion al usuario |
| `AlumnoController`, `CalificacionController` | Presentacion | Recibir las acciones y decidir que servicio llamar y que vista mostrar |
| `AlumnoService`, `CalificacionService` | Negocio | Reglas de la escuela: validaciones, promedios, inscripciones |
| `AlumnoRepository`, `CalificacionRepository` | Datos | Guardar y consultar la base de datos |
| Broker | Integracion | Localizar y enrutar las peticiones a los sistemas externos |
| Proxies (uno por sistema externo) | Integracion | Traducir formato, protocolo y autenticacion de cada sistema |
| `ManejadorErrores` | Transversal | Recibir y procesar los errores de todas las capas |
| Base de datos / sistemas externos | Externos | No se construyen dentro del sistema, pero son componentes con los que se conecta |

## interfaces.

buneo lo que sabemos que hay dos tipos de interfaces, pero como la interfaz de usuario (UI) ya ha sido definida, lo que tenemos que definir es la interfaz en la arquitectura, que como sabemos haciendo una simil en java de las clases interfece sabemos que es un contrato para todos los componentes del sistema, ya que con ellas logramos que no haya desfaces y cada component del sistema haga a su manera su funcionalidad, si no que con las interfaces ponemos leyes que rigen a todos.

### tabla interfaces

haremos una tbal comparativa de interface, solo definiendo la interfaz y donde se implementa.

| interfaz   | la implementa | la usa          |
|------------|---------------|-----------------|
| ICalificacionService |  CalificacionService | CalificacionController |
| IAlumnoService |  AlumnoService | AlumnoController |
|  ICalificacionRepository | CalificacionRepository | CalificacionService |
| IAlumnoRepository |  AlumnoRepository |  AlumnoService |
| Broker | Integracion | Localizar y enrutar las peticiones a los sistemas externos |
| ISistemaExterno |  Broker + proxies | Los services |


## Relaciones permitidas. 
relaciones en la arquitectura, como vimos en el ADA1, vemoa que si los componentes son las cajas las relaciones son las lineas que lo unen.

### tabla de relaciones 

| origen    | destino        | interfaz        | tipo          | proposito       |
|------------|---------------|-----------------|---------------|-----------------|
| vista   | controller | peticciones HTTP          | sinrona | enviar las acciones del usuario       |
| controller   | service |      ICalificacionService, IAlumnoService     | │ Uso, sincrona | Pedir que se aplique la regla de negocio         |
|  Service   |  Repository |  ICalificacionRepository, IAlumnoRepository    |  Uso, sincrona | Guardar y consultar datos |
|  repository   |  base de datos |  ORM/SQL    |   sincrona | perisistir la informacion |
|  Service   |  broker |  ISistemaExterno    |  Uso | pedir comunicacion con otro sistema |
| broker   |  Proxy - sistema externo | protoco del sistema externo    |  remota (sincrona o asincrona) | enviar o recibir informacion externa |
| todas las capas   |  manejadorErrores | Imanejodor Erroes   |  uso |  rerportar |

## Direccion de las dependencias. 
bueno en este caso como nuestra arquitectura fue definida en capaz, por lo que va hacia abajo asi que hay un flujo defindo hacia un solo sentido poe lo que las direciones va haceia un mismo sentido sin posiblidad de regresar hasta que se termine el ciclo y empieze de nuevo.

La direccion de una dependencia indica quien necesita conocer a quien: la flecha sale del componente que usa y apunta al componente que es usado. Si el componente de destino cambia, el de origen puede verse afectado, pero no al reves.

Hay que diferenciarla del flujo de datos: la informacion baja hasta la base de datos y la respuesta sube de regreso hasta la vista, pero la dependencia solo va en un sentido. Por ejemplo, el service conoce al repository, pero el repository no sabe que existe el service.

### Regla 1: en capas, las dependencias van hacia abajo

```
Presentacion  ──►  Negocio  ──►  Datos / Integracion
```

Ninguna capa depende de una capa superior y ninguna se salta capas (por ejemplo, una vista nunca depende de un repository).

### Regla 2: inversion de dependencias (SOLID)

La capa de negocio no depende de detalles concretos como la base de datos o la API de un sistema externo, sino de interfaces. Esas interfaces pertenecen a la capa de negocio, y las capas de datos e integracion las implementan:

```
CalificacionService ──usa──► ICalificacionRepository ◄──implementa── CalificacionRepository
CalificacionService ──usa──► ISistemaExterno         ◄──implementa── Broker + proxies
```

Las flechas de "implementa" apuntan hacia la capa de negocio; por eso se llama inversion. Asi la logica de negocio queda en el centro y, si cambia la base de datos o el sistema externo, solo se modifica su implementacion. Esto corrige el problema del diagnostico de que la logica de negocio dependia directamente de la BD y de las APIs externas.

### Tabla de dependencias

| Componente | Depende de | Direccion |
|------------|------------|-----------|
| Vistas | Controllers | Presentacion → Presentacion |
| Controllers | `IAlumnoService`, `ICalificacionService` | Presentacion → Negocio |
| Services | `IAlumnoRepository`, `ICalificacionRepository`, `ISistemaExterno`, `IManejadorErrores` | Solo de interfaces de negocio |
| Repositories | Implementan las interfaces de repositorio | Datos → Negocio (invertida) |
| Broker y proxies | Implementan `ISistemaExterno` y dependen de los sistemas externos | Integracion → Negocio (invertida) e Integracion → Externos |
| Todas las capas | `IManejadorErrores` | → Transversal |

## Patron aplicado en cada parte

como ya vimos, la arquitectura no usa un solo patron sino una combinacion, donde cada patron se encarga de una parte diferente del sistema. en la siguiente tabla se ve que patron se aplica en cada parte y como se aplica.

| Parte del sistema | Patron aplicado | Como se aplica |
|-------------------|-----------------|----------------|
| Sistema completo | Capas | Divide el sistema en presentacion, negocio, datos e integracion; cada capa solo depende de la capa de abajo |
| Presentacion | MVC (Vista y Controlador) | Las vistas muestran la informacion y los controllers reciben las acciones del usuario, deciden que service llamar y que vista mostrar |
| Negocio | MVC (Modelo, parte logica) | Los services concentran las reglas de la escuela (validaciones, promedios, inscripciones) y no saben nada de la UI ni de la base de datos |
| Datos | MVC (Modelo, parte datos) con repositorios | Los repositories son el unico punto de acceso a la base de datos |
| Integracion | Broker (con proxies) | El broker localiza y enruta las peticiones; cada proxy traduce el formato, protocolo y autenticacion de un sistema externo |
| Entre capas | Inversion de dependencias (SOLID) | Las capas se comunican por interfaces que pertenecen a la capa de negocio |
| Transversal | Manejador centralizado de errores | Todas las capas reportan sus errores a un mismo componente |

de los patrones que hemos visto (capas, MVC, broker y reflection), reflection es el unico que no se aplica, porque el sistema no necesita cambiar su comportamiento en tiempo de ejecucion; los cambios que puede tener se resuelven cambiando la implementacion que esta detras de una interfaz.

## Contratos entre componentes

en la seccion de interfaces definimos que interfaces hay, quien las implementa y quien las usa, pero un contrato va un paso mas alla: dice exactamente que operaciones ofrece cada interfaz, que recibe, que regresa y que pasa si algo falla. asi cualquier componente que implemente la interfaz sabe que tiene que cumplir, y cualquier componente que la use sabe que puede esperar.

ademas de las interfaces que ya teniamos, se agrega `IAutenticacion`, que se encarga de validar quien entra al sistema y con que rol. la interfaz pertenece a la capa de negocio y la implementa un proxy del broker que se comunica con el proveedor de autenticacion (que es un sistema externo).

| Contrato | Operacion | Recibe | Regresa | Si falla |
|----------|-----------|--------|---------|----------|
| API HTTP (Vista → Controller) | `POST /calificaciones` | matricula, materia y calificacion en JSON | calificacion registrada y promedio actualizado en JSON | responde 400 si los datos son invalidos |
| API HTTP (Vista → Controller) | `GET /alumnos/{matricula}/promedio` | matricula | promedio del alumno en JSON | responde 404 si el alumno no existe |
| `ICalificacionService` | `registrarCalificacion()` | matricula, materia y calificacion | la calificacion guardada | error de validacion si la calificacion esta fuera de rango (0 a 100) |
| `ICalificacionService` | `obtenerPromedio()` | matricula | promedio del alumno | error si el alumno no existe |
| `IAlumnoService` | `registrarAlumno()` | datos del alumno | el alumno registrado | error si la matricula ya existe |
| `IAlumnoService` | `buscarAlumno()` | matricula | datos del alumno | error si el alumno no existe |
| `ICalificacionRepository` | `guardar()` | objeto Calificacion | la calificacion guardada | error de datos si falla la base de datos |
| `ICalificacionRepository` | `buscarPorAlumno()` | matricula | lista de calificaciones | lista vacia si no tiene calificaciones |
| `IAlumnoRepository` | `guardar()` | objeto Alumno | el alumno guardado | error de datos si falla la base de datos |
| `IAlumnoRepository` | `buscarPorMatricula()` | matricula | el alumno | vacio si no existe |
| `ISistemaExterno` | `enviar()` | nombre del sistema y datos | confirmacion de envio | error de comunicacion; el envio queda pendiente |
| `ISistemaExterno` | `consultar()` | nombre del sistema y consulta | datos ya traducidos al formato del sistema | error de comunicacion |
| `IAutenticacion` | `iniciarSesion()` | usuario y contraseña | sesion (token) con el rol del usuario | error si las credenciales son invalidas |
| `IAutenticacion` | `validarSesion()` | token | usuario y rol | error si la sesion expiro o no es valida |
| `IManejadorErrores` | `registrarError()` | error y capa donde ocurrio | nada (solo lo registra) | no aplica |
| `IManejadorErrores` | `notificar()` | error | mensaje entendible para el usuario | no aplica |

reglas generales que cumplen todos los contratos:

1. los datos viajan entre capas como objetos del sistema (`Alumno`, `Calificacion`), nunca en el formato de la base de datos ni en el formato de un sistema externo.
2. los errores se reportan como errores del sistema y llegan a `IManejadorErrores`; nunca se le muestra al usuario un error de SQL o de HTTP tal cual.
3. la API HTTP siempre usa JSON, asi que cualquier cliente (web o movil) la puede consumir.
4. si un contrato cambia, cambian todos los que lo usan, por eso los contratos deben mantenerse estables y solo se les agregan operaciones nuevas en lugar de modificar las existentes.


# 5. Analisis de cambio

para comprobar que la nueva arquitectura realmente es mejor que la anterior, vamos a analizar como responderia ante algunos cambios. en el sistema actual, como todo esta en un solo componente, cualquiera de estos cambios obligaria a modificar ese componente completo y arriesgar a que otras funciones dejen de funcionar; con la nueva arquitectura la idea es que cada cambio se quede en una sola capa.

##  Crear una aplicacion movil

la aplicacion movil es solo una nueva vista, es decir, otro cliente en la capa de presentacion. como los controllers ya ofrecen una API HTTP con JSON (contrato), la aplicacion movil consume los mismos endpoints que la version web. si la app necesita algo que no existe, se agrega un endpoint nuevo, pero este llama a los mismos services.

- **Que cambia:** solo la capa de presentacion (se crea la app y, si hace falta, algun endpoint nuevo).
- **Que no cambia:** negocio, datos e integracion. el calculo de promedios y las validaciones no se duplican en la app.

##  Cambiar la base de datos

como los services dependen de las interfaces `ICalificacionRepository` e `IAlumnoRepository` y no de la base de datos, solo hay que crear nuevas implementaciones de esos repositorios para la nueva base de datos (o, si se usa un ORM, puede bastar con cambiar la configuracion). la migracion de los datos existentes es una tarea aparte, pero no afecta el codigo de las demas capas.

- **Que cambia:** solo la capa de datos.
- **Que no cambia:** presentacion, negocio e integracion.

## Consumir un nuevo servicio remoto

se crea un nuevo proxy para ese servicio y se registra en el broker. el service que lo necesite lo usa por medio de `ISistemaExterno`, igual que con los demas sistemas. si el nuevo servicio falla, el error llega al manejador centralizado y no afecta a las demas partes del sistema, que era justo el problema que tenia el sistema actual ("cuando cambia la conexion con otro sistema, varias partes de la aplicacion dejan de funcionar").

- **Que cambia:** la capa de integracion (nuevo proxy) y el service que va a usar el nuevo servicio.
- **Que no cambia:** presentacion, datos y los proxies de los otros sistemas.

## Agregar una vista para personal directivo

se agregan nuevas vistas y un `DirectivoController` en la capa de presentacion. si los directivos necesitan informacion que todavia no existe (por ejemplo, promedios por grupo o estadisticas generales), se agrega una operacion a un service existente o un `ReporteService` que reutiliza los repositorios que ya hay. para que solo el personal directivo pueda entrar, el controller valida el rol con `IAutenticacion`.

- **Que cambia:** la capa de presentacion y, si hay reportes nuevos, la capa de negocio.
- **Que no cambia:** datos (salvo que se necesite una consulta nueva en un repositorio) e integracion.

##  Sustituir el proveedor de autenticacion

como la autenticacion esta detras de la interfaz `IAutenticacion` y la implementa un proxy del broker, cambiar de proveedor significa crear un nuevo proxy para el nuevo proveedor y reemplazar el anterior. los controllers y services no se enteran del cambio porque solo conocen la interfaz. lo unico a considerar es migrar los usuarios y roles al nuevo proveedor.

- **Que cambia:** solo la capa de integracion (el proxy de autenticacion).
- **Que no cambia:** presentacion, negocio y datos.

## Resumen del analisis de cambio

| Cambio | Capa que cambia | Que se modifica | Impacto |
|--------|-----------------|-----------------|---------|
| Crear una aplicacion movil | Presentacion | Nueva vista que consume la API HTTP existente | Bajo |
| Cambiar la base de datos | Datos | Nuevas implementaciones de los repositorios | Bajo |
| Consumir un nuevo servicio remoto | Integracion (y el service que lo usa) | Nuevo proxy registrado en el broker | Bajo |
| Agregar una vista para personal directivo | Presentacion (y negocio si hay reportes nuevos) | Nuevas vistas, `DirectivoController` y validacion de rol | Medio |
| Sustituir el proveedor de autenticacion | Integracion | Nuevo proxy que implementa `IAutenticacion` | Bajo |


# 6. Evaluacion de atributos de calidad

por ultimo, analizaremos como la propuesta afecta a los atributos de calidad del sistema. no todo es positivo, ya que separar el sistema en capas y agregar un broker tambien tiene un costo.

## Mantenibilidad

**Efecto: positivo.** cada componente tiene una sola responsabilidad, por lo que es mas facil encontrar donde esta un error y corregirlo sin afectar a los demas. la logica de conexion con sistemas externos ya no esta duplicada, sino que vive en un solo proxy, y los errores se procesan en un solo lugar. el costo es que el sistema tiene mas componentes y archivos, y el equipo tiene que respetar las reglas de las capas para que la arquitectura no se degrade.

## Modificabilidad

**Efecto: positivo.** como se vio en el analisis de cambio, casi todos los cambios se quedan en una sola capa, porque las capas se comunican por interfaces y se puede cambiar la implementacion sin tocar a quien la usa. el riesgo es que si cambia un contrato (una interfaz), cambian todos los componentes que lo usan, por eso los contratos deben ser estables.

## Testabilidad

**Efecto: positivo.** cada capa se puede probar por separado usando implementaciones falsas de las interfaces (mocks). por ejemplo, se puede probar `CalificacionService` con un repositorio falso sin necesidad de una base de datos, o probar el broker con un sistema externo simulado. en el sistema actual esto no era posible porque todo estaba en un solo componente.

## Interoperabilidad

**Efecto: positivo.** el broker se encarga de traducir formatos y protocolos, asi que el sistema se puede comunicar con sistemas hechos en otras tecnologias sin que el resto del codigo lo sepa. ademas, la API HTTP con JSON permite que otros clientes, como la aplicacion movil, se conecten facilmente.

## Rendimiento

**Efecto: ligeramente negativo.** cada peticion pasa por mas capas, y las llamadas a sistemas externos pasan por el broker y el proxy, lo que agrega un paso extra y tiempo de traduccion. en un sistema de control escolar este impacto es pequeño, pero el broker puede convertirse en un cuello de botella en momentos de mucha carga (por ejemplo, en inscripciones). para reducirlo se puede usar cache en las consultas frecuentes (como los promedios), hacer asincronas las llamadas a sistemas externos para que el usuario no tenga que esperar, y definir tiempos de espera (timeouts) para no quedarse bloqueado si un sistema no responde.

## Seguridad

**Efecto: positivo, con un punto a cuidar.** la base de datos solo se accede por medio de los repositorios, nunca desde la vista; la autenticacion esta centralizada en `IAutenticacion` y el rol se valida antes de atender una peticion; las credenciales de los sistemas externos estan en un solo lugar (los proxies); y el manejador de errores evita que se le muestren al usuario errores internos (como errores de SQL) que podrian dar informacion del sistema. el punto a cuidar es el broker, ya que todas las comunicaciones externas pasan por el y, si se cae o se compromete, afecta a todas; por eso hay que protegerlo y usar comunicaciones cifradas (HTTPS).

