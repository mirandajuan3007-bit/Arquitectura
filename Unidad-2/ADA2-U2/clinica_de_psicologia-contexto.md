# sitema de agenda de clinica de psicologia 

## contexto

un sitemsa de agenda para la uady, es uso excliusivo par las secretarias ya que son los usuarios finales del sistema las que registran las citas, y a los pacientes que iran a las citas, al igual que registran a los cototers que atendaeran y los consultorios en la que estaran.

# requisitos funcionales

- debes poder resgistrar a un paciente, dicho paciente se guardar aen la base de dato Flujo de ejemplo: el usuario presiona "Comprar", luego el frontend hace POST /pedidos, para uqe la proxima vez este en el listado de paciente.
- debes poder registrar a un doctor, ya que los pacientes seran asiganado a un doctor, el cual sera el cual los atienda 
- debes poder resgistrar a un paciente, dicho paciente se guardar aen la base de dato Flujo de ejemplo: el usuario presiona "Comprar", luego el frontend hace POST /pedidos, para uqe la proxima vez este en el listado de paciente.
- debes poder resstrar la cita, que tiene la informacion del paciente la informacion de la cita que ya puede ser el dia, la hora, en que sala y con que doctor.
- debes poder tener una vista de un calendar, para mostrar las citas y cuales estan pendientes, cuales han sido canceladas, cuales son las que estan en estado de atrasadas.
- El sistema debe mostrar al usuario un resumen final de la cita una vez que se hayan capturado todos los datos necesarios y antes de confirmar o guardar definitivamente la cita.
- El sistema debe validar automaticamente la disponibilidad de los recursos necesarios antes de registrar una nueva cita o reprogramar una cita existente dentro del modulo de agenda.
- aunque no tiene que ver en nuestra parte que se implemento con mi equipo, se hace un envio del historial del paciente para que sea guardada y consumida por otro modulo del sitema 
- se puede reprogramar una cita, por si por x o y el cliente no puedo asistir a su cita o no padra


## usuario principal 

- secretaria: secretaria es la que interactua con el sistema, la que agenda las citas, registra a los pacientes, reguitra a lossoctores y a las consultas. 
- paciente: es el que atravez de la secretaria agrenada la cityya, pero no tiene acceso al sistema, pero podriamos decir que alrededor de su adstraccion en el sitema da vuelta todo 


## tecnologias usadas

- Spring boot 
- java
- mysql
- React 18 + TypeScript
- ORM: Hibernate / JPA
- usamos un contenedor/docker para un despliegue mas sencillo

# arquitectura

tenemos una tipica arquitectua de cliente servidor con las siguientes caracteritica
- se usa el modelo mvc 
    - modelo: en el modelo tenemos principalmente la abtraccion de los usuarioes principales, pero igual tenemos los consultorios, citas
    - vista: tenemos la logica de negocio de la clinica, como los claculos del horario, etc.
    - control: tenemos el control que es el uso y el llamaod para obtenerla infomracin de la ui

 Comunicación: el frontend envía peticiones HTTP en formato JSON a la API. La API consulta la base de datos y devuelve la respuesta.
 es unm molito en capas
