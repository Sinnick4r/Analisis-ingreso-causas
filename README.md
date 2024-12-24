# Proyecto de Limpieza de Datos - Estadísticas de Juzgado

## Introducción
Este repositorio contiene el proyecto de limpieza de datos que hice usando un Excel del lugar en el cual trabajo actualmente. Incluye los archivos procesados, un archivo de Power BI para visualización, y documentación detallada del proceso realizado. Este Excel fue usado por, como mínimo, 5 personas, por lo cual contaba con inconsistencias, errores, abreviaciones mal hechas, cosas mal ingresadas, etc.

## Estructura del Proyecto

El proyecto está organizado de la siguiente manera:

```
limpieza-datos-proyecto/
├── README.md              # Documentación principal del proyecto
├── data/
│   ├── original/          # Datos originales (sin información sensible)
│   │   ├── Dataset Limpio.xlsx
│   │   └── Esquema de registro original.xlsx
│   ├── procesada/         # Datos procesados y limpios
│       └── Dataset juzgado.csv
├── analisis/
│   └── Estadistica ingresos 2020-2024.pbix # Archivo de Power BI con visualizaciones
└── docs/                  # Documentación adicional (opcional)
```

### Descripción de Carpetas
- **data/original/**: Contiene los datos originales o semi procesados antes de la limpieza final.
- **data/procesada/**: Contiene los datasets procesados y listos para análisis.
- **analisis/**: Contiene el archivo Power BI y otros análisis derivados.

## Proceso de Limpieza de Datos

### 1. Revisión Inicial
- Identificación de duplicados, datos faltantes, errores de ingreso y formatos inconsistentes. En este caso no se encontraron duplicados.

### 2. Eliminación de Nombres Propios y Extracción de Delito en Crudo
En el Excel original, se encontraban cargados nombre y apellidos de personas imputadas, por lo que se procedió a eliminar gradualmente esos datos. No se podía eliminar la columna directamente porque la misma tenía el formato "nombre s/ delito", siendo este uno de los datos a analizar.

- **Fórmulas utilizadas**:
  - Para convertir el nombre en iniciales, teniendo en cuenta que el separador es "s/":
    ```excel
    =IF( G4="", "Sin nombre s/ Sin delito", IFERROR( CONCATENATE( IF( OR(ISERROR(FIND(", ",G4)), ISERROR(FIND(" ",G4))), "", LEFT(G4,1)&". " ), IF( AND(NOT(ISERROR(FIND(", ",G4))), NOT(ISERROR(FIND(" s/ ",G4)))), LEFT(MID(G4,FIND(", ",G4)+2,FIND(" s/ ",G4)-FIND(", ",G4)-2),1)&". ", IF( AND(NOT(ISERROR(FIND(" ",G4))), NOT(ISERROR(FIND(" s/ ",G4)))), LEFT(MID(G4,FIND(" ",G4)+1,FIND(" s/ ",G4)-FIND(" ",G4)-1),1)&". ", "" ) ), IFERROR( MID(G4,FIND(" s/ ",G4)+1,LEN(G4)-FIND(" s/ ",G4)), "Sin delito" ) ), "Sin nombre s/ Sin delito" ) )
    ```
  - Para separar el delito de la columna:
    ```excel
    =LOWER(TRIM(LEFT(RIGHT(G3, LEN(G3) - FIND("/", G3)), IFERROR(FIND(" por ", RIGHT(G3, LEN(G3) - FIND("/", G3))) - 1, LEN(RIGHT(G3, LEN(G3) - FIND("/", G3)))))))
    ```

### 3. Estandarización de Tipo de Trámite
Esto lo realicé utilizando la función de buscar y reemplazar tiempo antes de empezar este proyecto. De haberlo hecho ahora, hubiera utilizado un diccionario como hice con los delitos.

### 4. Estandarización de Delitos
- Eliminación de la tentativa (en el análisis no lo iba a utilizar) con la fórmula:
  ```excel
  =LOWER(TRIM(SUBSTITUTE(H3, "en tentativa", "")))
  ```
- Creación de una columna que indica si el delito es tentado o no:
  ```excel
  =IF(OR(ISNUMBER(SEARCH("tva", G3)), ISNUMBER(SEARCH("tentativa", G3))), "si", "no")
  ```
- Normalización de texto a minúsculas y eliminación de espacios adicionales utilizando funciones generales de Excel.
- Utilización de listado de delitos estandarizados: Se copió la columna de delitos y se eliminaron duplicados. Luego se creó otra columna con los valores que debían utilizarse y se aplicó:
  ```excel
  =IFERROR(VLOOKUP(A25, $B$2:$C$147, 2, FALSE), "NOT FOUND")
  ```

### 5. Exportación Final
Los datos procesados se guardaron en `Dataset juzgado.csv` para su uso posterior.

## Resultados Finales
- **Principales correcciones**:
  - Eliminación de cualquier vestigio de nombre propio.
  - Eliminación de columnas intrascendentes.
  - Estandarización de formatos en 2 columnas clave.

## Licencia
Este proyecto está licenciado bajo la licencia CC BY 4.0. Consulta el archivo `LICENSE` para más información.

## Contacto
Para más información o dudas, puedes contactar al propietario del proyecto a través de emilioge@protonmail.com.


