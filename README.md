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
Nombre = DISTINCT(UNION(SELECTCOLUMNS(Operador_Mantenedor,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía], "RUT",[RUT]),CALCULATETABLE(SELECTCOLUMNS(Corporativo,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),Corporativo[Nombre]<>""),SELECTCOLUMNS(Fase1,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),SELECTCOLUMNS(Fase2,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),SELECTCOLUMNS(Fase3,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT])))
```

Mientras que actualmente se encuentra así:

```
Nombre = DISTINCT(UNION(SELECTCOLUMNS(Operador_Mantenedor,"Nombre",[Nombre_Ingresado],"Tipo",[Tipo],"Compañía",[Compañia],"RUT",[RUT]),CALCULATETABLE(SELECTCOLUMNS(Corporativo,"Nombre",[Nombre],"Tipo",[Tipo],"Compañía",[Compañía],"RUT",[RUT]),Corporativo[Nombre]<>"")))
```



Actualmente, Antucoya presentó una lista de asociaciones RUT - Nombre Empleado. Por ende, se está usando para esta compañia esta lista para presentar las tablas. Notar que en caso de agregar nuevos verificadores, se puede ir actualizando de manera mensual o periódica manualmente la tabla. Por lo mismo, se debe considerar los posibles nombres que no puedan estar en las tablas presentadas, los cuales saldrían sin nombre asociado (solo RUT).

En el caso de Centinela, se está usando la tabla general de nombres, por lo que se debe considerar los nombres escritos de distintas maneras.

