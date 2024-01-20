# Dominio
### 1- Diagrama de clases
![Diagrama de clases](img.png)

### 2- Justificaciones

- El Ponderador se encarga de aplicar el ordenamiento en función a las postulaciones de los candidatos
respecto de la evaluación de referencia de la CampaniaReclutamiento.
- El puntaje de RespuestaEvaluacion es un Integer pero al crearlo se debería validar que el mismo
sólo pueda ser 0 o 1, para no tener que hacer conversiones en el Ponderador.
Inicialmente había pensado en que sea un booleano esCorrecta pero lo cambié por el motivo mencionado
- Asumo que cuando se guarde o se cree efectivamente la Postulacion, ya se habrá realizado (en la capa de controllers)
las llamadas correspondientes a los servicios que me dan los resultados para cada índice.
- Por el motivo anterior, no grafiqué el Adapter que necesitaría para obtener los resultados de los índices

### 3- Creación de nuevo Índice
Para crear un nuevo Indice en principio debería validar que el mismo esté contemplado en el sistema externo
que me brinda los resultados, y lo equivalente con el componente "Cuestionarios Frontend" respecto a la posibilidad
de obtener las métricas necesarias para ese Indice.
Luego de las validaciones, en caso de necesitar nuevas métricas se crearían y persistirían, y por último ya podría
crear el Indice correspondiente vinculándolo a las métricas necesarias

### 4- Modelado de una CampaniaReclutamiento
![Diagrama de objetos](img_1.png)

### 5- Generación de campaña
```java
// Desde el controller
CampaniaReclutamiento campania = repositorioCampania.buscarPorId(campaniaId)
Ponderador ponderador = new Ponderador();

List<IndiceParametro> indicesParametros = new ArrayList();
Indice indiceCopia = repositorioIndice.buscarPorNombre("Copia");
IndiceParametro indiceParametro = new IndiceParametro(indiceCopia, -1);
indicesParametros.add(indiceParametro);
ParametrosPonderador parametros = new ParametrosPonderador(
    importanciaPuntaje = 1,
    importanciaIndices = indicesParametros
);

List<Postulacion> postulacionesRecomendadas = ponderador.ponderar(postulaciones, parametros);
campania.setPostulacionesDePosiblesCandidatos(postulacionesRecomendadas);
repositorioCampania.modificar(campania)
```

