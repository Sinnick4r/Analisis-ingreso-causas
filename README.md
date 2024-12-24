Proyecto de Limpieza de Datos - Estadìsticas de Juzgado

Introducción

Este repositorio contiene el proyecto de limpieza de datos que hice usando un excel del lugar en el cual trabajo actualmente. Incluye los archivos procesados, un archivo de Power BI para visualización, y documentación detallada del proceso realizado.
Este excel fue usado por , como minimo, 5 personas por lo cual contaba con inconsistencias, errroes, abreviaciones mal hechas, cosas mal ingresadas, etc.


Estructura del Proyecto

El proyecto está organizado de la siguiente manera:

limpieza-datos-proyecto/
├── README.md              # Documentación principal del proyecto
├── data/
│   ├── original/          # Datos originales (sin información sensible)
│   │   ├── Dataset Limpio.xlsx.xlsx
│   │   └── Esquema de registro original.xlsx
│   ├── procesada/         # Datos procesados y limpios
│       └── Dataset juzgado.csv
├── analisis/
│   └── Estadistica ingresos 2020-2024.pbix    # Archivo de Power BI con visualizaciones
└── docs/                  # Documentación adicional (opcional)

Descripción de Carpetas

data/original/: Contiene los datos originales o semi procesados antes de la limpieza final.

data/procesada/: Contien el datasets procesados y listos para análisis.

analisis/: Contiene el archivo Power BI y otros análisis derivados.

Proceso de Limpieza de Datos

1. Revisión Inicial

Identificación de duplicados, datos faltantes, errores de ingreso y formatos inconsistentes. En este caso no se encotraron duplicados

2. Eliminacion de nombres propios y extracción de delito en crudo

En el excel original, se encontraban caragados nombre y apellidos de personas imputadas, por lo que se procedio a eliminar gradualmente esos datos. No se podia eliminar la columna dirctamente porque la misma tenia el formado "nombre s/ delito", siendo el uno de los datos a analizar.

Se usaron dos formulas:

a) una para convertir el nombre en iniciales, teniendo en cuenta que el separador es "s/": 
=IF(
    G4="",
    "Sin nombre s/ Sin delito",
    IFERROR(
        CONCATENATE(
            IF(
                OR(ISERROR(FIND(", ",G4)), ISERROR(FIND(" ",G4))),
                "",
                LEFT(G4,1)&". "
            ),
            IF(
                AND(NOT(ISERROR(FIND(", ",G4))), NOT(ISERROR(FIND(" s/ ",G4)))),
                LEFT(MID(G4,FIND(", ",G4)+2,FIND(" s/ ",G4)-FIND(", ",G4)-2),1)&". ",
                IF(
                    AND(NOT(ISERROR(FIND(" ",G4))), NOT(ISERROR(FIND(" s/ ",G4)))),
                    LEFT(MID(G4,FIND(" ",G4)+1,FIND(" s/ ",G4)-FIND(" ",G4)-1),1)&". ",
                    ""
                )
            ),
            IFERROR(
                MID(G4,FIND(" s/ ",G4)+1,LEN(G4)-FIND(" s/ ",G4)),
                "Sin delito"
            )
        ),
        "Sin nombre s/ Sin delito"
    )
)

b) para separar el delito de la columna:

=LOWER(TRIM(LEFT(RIGHT(G3, LEN(G3) - FIND("/", G3)), IFERROR(FIND(" por ", RIGHT(G3, LEN(G3) - FIND("/", G3))) - 1, LEN(RIGHT(G3, LEN(G3) - FIND("/", G3)))))))

De nuevo, utilizando el separador "/", ya qeu el delito es todo lo que está a la derecha.

3. Estandarización de tipo de trámite:

Esto lo realicé utilizando la funciòn de buscar y reemplazar tiempo antes de empezar este proyecto. De haberlo hecho ahora, hubiera utilizado un diccionario como hice con los delitos.-


4. Estandarización de delitos:

Eliminación de la tentativa (en el analisis no lo iba utilizar) con la fórmula =LOWER(TRIM(SUBSTITUTE(H3, "en tentativa", ""))) teniendo como base el delito en crudo extraído 

Creación de una columna que indica si el delito es tentado o no usando =IF(OR(ISNUMBER(SEARCH("tva", G3)), ISNUMBER(SEARCH("tentativa", G3))), "si", "no")

Normalización de texto a minúsculas y eliminación de espacios adicionales utilizando funciones generales de excel

Utilización de listado de dedelitos estandarizados: se procedio a copiar la columna de delitos y a eliminar duplicados.

Una vez hecho esto, se creo otra columna con los valores que debian utilizarse y luego se utilizo la fórmula =IFERROR(VLOOKUP(A25, $B$2:$C$147, 2, FALSE), "NOT FOUND") para crear la columna de delitos definitiva.


5. Exportación Final

Los datos procesados se guardaron en Dataset juzgado.csv para su uso posterior.


Principales correcciones:

Eliminación de cualquier vestigio de nombre propio

Eliminación de columnas intrascendentes

Estandarización de formatos en 2 columnas clave

Licencia

Este proyecto está licenciado bajo la [nombre de la licencia, ej. MIT License]. Consulta el archivo LICENSE para más información.

Contacto

Para más información o dudas, puedes contactar al propietario del proyecto a través de emilioge@protonmail.com.

