# Explicativo Power BI Resumen y Liderazgo :rotating_light:

El siguiente explicativo contiene algunas consideraciones a tener para interpretar de manera óptima el dashboard desarrollado.

El Power BI presenta las siguiqntes páginas:
* 1 a modo de resumen. Esta página muestra directamente cómo estuvo el cumplimiento final (Proporción de respuestas Si respecto del total de preguntas respondidas). También, el número de verificaciones se presenta como el número de respuestas realizadas de forms.
* 1 página por compañía, con resumen más detallado de las estadísticas presentadas en las distintas evaluaciones.
* 1 página por compañía, con las Prácticas de Liderazgo respecto a la cantidad de preguntas respondidas por evaluador, tanto en RECSS, EDC Y Buenas Prácticas.

## Consideraciones

Se asignó un target a cada tipo de evaluador: Operadores, Supervisores y Ejecutivos. En el caso de RECSS, todos son asignados como Evaluadores de contratos.
Notar que actualmente este target solo depende del cargo. Por ejemplo, para las buenas prácticas se usó esta formula:

```
Target_12PP = IF(Operador_Mantenedor[Tipo]="Operador/a",10,IF(Operador_Mantenedor[Tipo]="Supervisor/a",5,3))
```
Si se quisiera otorgar un target que también dependa de la compañía (u otro parámetro), basta con agregar flujos de IF - ELSE.

Para el parámetro Logro (cumple o no cumple) debe cumplir todos los target asociados a cada forms.

Ojo: En caso de que el evaluador haya ingresado evaluaciones con dos cargos distintos, sus evaluaciones aparecerán separadas para las respuestas que realizó con cada cargo. Esto podría deberse tanto a un error de input del verificador, como también poseer distintos cargos en el período.

Notar que existen formularios que se pueden responder "por partes". Esto es un punto a consideración, pues podría darse el caso que un evaluador responda cada pregunta de un formulario en un ingreso distinto. Esto es, si el forms tiene 10 preguntas distintas, y el evaluador decide responder por separado las 10 veces, tendrá asociadas 10 respuestas en vez de una. Esto debe ser considerado, pues podría deberse a una falta de ética para contabilizar más respuestas, pues el fin principal de permitir responder los formularios en partes, es que aquellas preguntas que no aplican para determinado verificador, no sean respondidas.

Las superintendencias y gerencias se encuentran homologadas para cada compañía.
Para el caso de Antucoya, de las EDC se encuentra la superintendencia, de la cual se obtiene también la gerencia con la homologación. 
Para el caso de Centinela, de las EDC se obtiene solo la gerencia. Esta se obtiene a partir de la tabla de homologación de los nombres de las EDC con los nombres reales.
En las buenas prácticas se encuentra el parámetro Gerencia/SI, el cual tiene los valores de SAP. Con la misma tabla de homologación, obtenemos la gerencia y SI a la que corresponde este.

## Pendientes o a considerar
Actualmente, los formularios de buenas prácticas plantas y de las estrategias de control, presentan el campo RUT. Para el caso de RECSS, el campo RUT está pendiente.
Una vez ingresado el campo RUT, se debe enlazar en el modelo relacional con el campo RUT de la tabla Nombre. Dado que la tabla Nombre se forma a partir de los nombres y RUT de todos los empleados existentes en las bases de datos usadas, se debe actualizar su cálculo con los valores de las tablas de RECSS. La fórmula para esta tabla quedaría de la siguiente manera:

```
Nombre = DISTINCT(UNION(SELECTCOLUMNS(Operador_Mantenedor,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía], "RUT",[RUT]),CALCULATETABLE(SELECTCOLUMNS(Corporativo,"Nombre",

[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),Corporativo[Nombre]<>""),SELECTCOLUMNS(Fase1,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),SELECTCOLUMNS(Fase2,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),SELECTCOLUMNS(Fase3,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT])))
```

Mientras que actualmente se encuentra así:

```
Nombre = DISTINCT(UNION(SELECTCOLUMNS(Operador_Mantenedor,"Nombre",[Nombre_Ingresado],"Tipo",[Tipo],"Compañía",[Compañia],"RUT",[RUT]),CALCULATETABLE(SELECTCOLUMNS(Corporativo,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),Corporativo[Nombre]<>"")))
```

Para presentar los nombres de los verificadores, existe más de una alternativa. Por un lado, Antucoya presentó sus tablas de pares Nombre - RUT de los empleados internos y externos. En el caso de Centinela, solo de los internos. De esta manera (y como se puede ver en la pestaña Homologación del modelo relacional), se pueden conectar estas tablas intermedias con la tabla Nombre (mediante el RUT) para tener los nombres "limpios" de cada empleado. Notar que estas tablas tendrían que actualizarse de manera manual (por ejeplo, mensualmente), para nuevos empleados. Para el caso de la tabla Nombre, esta se actualiza automáticamente con los datos ingresados en los forms. Por ende, la opción alternativa es no usar estos pares RUT-Nombre, a cambio de perder la posibilidad de mostrar en un único nombre los múltiples ingesos asociados al mismo RUT. (Aunque, de todos modos, el logro viene asociado al RUT, por lo que se tendrían los mismos resultados para los distintos nombres asociados al mismo RUT; solo que para visualizar es más engorroso)


