# Job-Sample-Data-Intern-Alejandro-Bravo-Angulo-
Explicación del código:

# Part 1 - Data Cleaning & Organization in Salesforce
El código inicio descargando las librerías necesarias: gspread, pandas y gspread_dataframe.
La librería gspread sirve para acceder, leer y escribir hojas de cálculo de Google Sheets usando Python.
La librería pandas sirve para analizar y manipular datos tabulares en python
La librería gspread_dataframe sirve para interactucar Hoja de google sheet y dataframes de pandas, pudiendo pasar el contenido de hoja en google sheet a dataframe y viceversa. 
 
Luego se debe autorizar el acceso a google drive y se accede a travez del ID del link del google sheet con "gc.open_by_key"
El contenido de la hoja del google sheet se guarda en "worksheet"
Ese contenido, usando "get_as_dataframe", se convierte en el dataframe "df_original" para que colab lo pueda trabajar

Luego se limpian los datos, primero los copiamos a un nuevo dataframe "df_1" para mantener la versión anterior en caso de error usando. Se eliminan las filas y columnas completamente vacías con "df_1.dropna(how='all')" y "df_1.dropna(axis=1, how='all')". Con "df_1.drop_duplicates()" se eliminan las filas duplicadas.
Revisando el google sheet se ve que hay dos columnas con el mismo nombre "Country". Cada una tiene los datos que le faltan a la otra en sus filas, pero cuando ambos tienen datos en la misma fila son iguales, por lo tanto se trata de la misma columna pero por error se creó una segunda columna con el mismo nombre. Así que se toma una de las columnas "Country" en sus filas vacías se ponen los datos de la otra columna, y cuando ambos tienen datos en la misma columna no se hace nada porque son duplicados.
Se quitan espacios en blanco al inicio y final de los strings en las celdas de las columnas.

Luego de cada uno de estos procesos de limpieza de datos se aplica el código "df_1.reset_index(drop=True, inplace=True)" para reiniciar indice cuando limpio datos con pandas, cada fila de un dataframe tiene un índice de 0 a n-1  pero cuando elimino una fila los indices no cambian: si borro 1, ahora es 0,2,3  df.reset_index() crea un nuevo indice ordenado como antes: 0,1,2,3...  drop=True indica que no queremos el indice anterior de 0,2,3... con todo eso el indice regresa a ser como era el inicio: 0 a n-1 inplace=True aplica directamente esto al dataframe actual sin inplace=True crea un nuevo dataframe con el nuevo indice

Luego se cambian los espacios vacíos por "no registrado" pero hay un problema. Si en una columna de datos numéricos ingreso el texto "no registrado", entonces esa columna pasará de ser "float64" a "objetc", por lo que ya no se podrán hacer operaciones numéricas con esa columna, lo cual podría ser perjudicial según el API con el que se trabaje más adelante. Así que se usa un código para que llene los vacíos con "no registrado" solo cuando el tipo de datos de la columna sea "object"
Como medida adicional se pasa todo a minúsculas

Para la API Integration se elije la API de nominatim para conseguir las corrdenadas (longitud y latitud) de los países en la columna "Country Presence".  Para esto se usa la biblioteca request que sirve para hacer solicitudes HTTP e interactuar con sitios web y APIs, y conseguir datos de páginas web.
Primero se crea en el dataframe las columnas de latitud y longitud, que las iniciamos todas con el texto "no registrado".
La lógica del codigo para esta parte es tomar el nombre del país e introducir en el API del sitio web cambiando la parte donde va el nombre del país, luego toma los datos de esa API (las coordenadas) y las pone en las filas de las columnas longitud y latitud mientras que el país no esté como "no registrado", de ser así el fila de la coordenada se quedará con su valor inicial de "no registrado".



# Part 2 - Data Visualization & Insights
Se crea un script para mostrar estadísticas y gráfica en el nnismo colab donde estoy trabajando el código de la parte 1.
Además de la librería pandas ya explicada en la parte 1, se usa la librería matplotlib.pyplot, para crear gráficos y visualizar datos en python. Se proponen 3 diferentes formas de visualizar los datos con sus respectivos insights.

 1.Conteo por país: 
Muestra cuantas veces aparece cada país en la columna Country Presence, cada barra representa un pais, su altura son personas o campañas acociadas.
  insight 1: Claramente hay un severo caso falta de registros, esto hace que sea imposible saber en qué país se tiene más presencia realmente, aunque la segunda opción con mayor cantidad es colombia, facilmente podría ese no ser el país con más precencia debido a la gran cantidad de paises no registrados. Se deben implementar medidas para garantizar un registro más completo, como no poder mandar los informes sin la información completa, además de una mejor capacitación de quienes debe llevar los registros para verificar qué datos incluyen y cuales faltan.

 2.Campañas por categoría:
Muestra cuántas campañas hay por cada categoría en un gráfico de barras horizontal
  insight 2: Las campañas de "tech" son las más numerosas con porcentaje de 58.44%, mientras que las de "tech; fundraising" solo llegan a 22.99% siendo menos de la mitad de "tech", pero se ve que hay un 18.58% sin registrar, pero sin importar de cual tipo sean los sin registrar, "tech" seguría siendo la categoría con más registros, aunque sin la información de los no registrados nos estamos perdiendo de otra áreas en las que podríamos tener precensia.

 3.Relación entre registro y asistencia
Muestra tabla de contingencia (crosstab) para cruzar dos columnas: Registered (personas que se registraron) y Attended (personas que asistieron). Esto muestra cuantos registrados realmente asistieron y cuántos no
  insight 3: Se ve que hubo mayor cantidad de gente presente registrada que no registrada, pero de la gente que se llegó a registrar la mayor parte no asistió, lo que nos da un 40.82% de asistencia de parte de los asistentes registrados. Por lo tanto lo recomendable es implementar un sistema de recordatorios por correo o llamada a los participantes registrados.


