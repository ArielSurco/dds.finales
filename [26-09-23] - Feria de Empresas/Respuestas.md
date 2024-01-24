# Modelo de dominio
### 1- Diagrama de clases
![Diagrama de clases](img.png)

### 2- Justificaciones

- Debido a la integración con un servicio externo para la obtención de datos de participante, se opta por usar un patrón Adapter, aportando extensibilidad y mantenibilidad
- Se utiliza el patrón Command donde Organización es el Invocador y AccionInteresado es la interface del comando, para ganar extensibilidad y alta cohesión en la configuración y ejecución de cada acción a realizar. Manteniendo la posibilidad de configurar cuáles y en qué orden se ejecutan
- Se emplea el patrón adapter tanto para el envío de mails y de whatsapp para evitar el acoplamiento con implementaciones concretas con determinadas librerías, que podrían cambiar en un futuro

# Persistencia
### 1- Modelo de datos
![Modelo de datos](img_1.png)

### 2- Justificaciones

- Se persiste Sector para que los Participante y Organizacion puedan tener intereses en común que será útil para las recomendaciones que se le podrían al Participante según sus sectores de interés
- Se persiste SectorDeInteres para romper la relación ManyToMany entre Participante y Sector
- Se persiste ParticipanteInteresado para romper la relación ManyToMany entre Participante y Organización
- Para resolver el Impedance Mismatch que surge por el enum MedioContacto, se persiste el valor del enum como un VARCHAR
- Para resolver el Impedance Mismatch que surge por quere almacenar clases Stateless, persistimos las acciones configuradas por la organización (para resolver la lista de acciones) y guardamos un VARCHAR que identifique a la clase de la acción correspondiente, para esto se utilizaría un @Converter

# Arquitectura
### 1- Gestión de cambio de pasarela de pago
...

### 2- Acciones asincrónicas con fecha/hora determinada
Podría persistir las acciones pendientes de realizar y utilizar un CronJob.
El CronJob se podría ejecutar cada 5 min y realizar las acciones pendientes cuya fecha/hora haya pasado

### 3- Reutilización de componente de entradas a eventos
Utilizaría una cola de mensajes para realizar esta integración asincrónica, porque si es un componente nuestro el que vamos a reutilizar,
podemos hacer que actúe como consumidor de la cola para que procese la generación de entradas a medida que su capacidad de procesamiento lo permita.
Además, si bien la otra alternativa es utilizar un WebHook, esto supondría tener que exponer 2 API REST, una para el componente de generación de entradas
y otro para nuestro componente de registro, lo que podría resultar incluso más complejo y menos eficiente que la cola de mensajes.
La integración se haría de forma que al registrarse correctamente a un Participante, se agregaría un mensaje a la cola, luego tendríamos nuestro componente
de generación de entradas actuando como consumidor, procesando los mensajes de la cola a medida que se agregan y que su capacidad lo permita.