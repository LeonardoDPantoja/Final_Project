
# Principales resultados del proyecto

Este archivo contiene los principales resultados del proyecto, destacando las visualizaciones y gráficos creados con librerías como Altair. Además, se incluyen los resultados de consultas de información en redes sociales y/o en la web utilizando técnicas como web scraping.

## Visualizaciones y Gráficos

Se han creado visualizaciones impactantes utilizando diversas herramientas, entre ellas `Altair`. Estos gráficos proporcionan una representación clara y efectiva de los datos analizados durante el proyecto.

`Altair` es una biblioteca de visualización de datos declarativa para Python que permite crear gráficos interactivos de manera sencilla y concisa. Por ejemplo, para hacer el siguiente gráfico sobre la distribución de una determinada variable:

```python
#Creación de una grafica con marcador de promedio anual de variable con Altair.
Ventas=alt.Chart(data_venta).mark_bar().encode(
    x='Tienda',
    y='suma'
).interactive()

linea = alt.Chart(data_venta).mark_rule(color='red').encode(
    y='mean(suma)'
)

(Ventas + linea).properties(width=600)
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Pobla_Local.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Pobla_Local.png)

O esta otra sobre los diferentes tipos de tiendas:

```python
#visualización y creación de grafica de los diferentes tipos de tiendas.
tipos = alt.Chart(conjunto).mark_bar().encode(
    x=alt.X('count(Tienda):Q',stack='zero'),
    y=alt.Y('Tipo:N'),
    color=alt.Color('Tipo')
)

tipos
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Tipo_Tiendas_1.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Tipo_Tiendas_1.png)

Acá segmentamos según el género y la edad de manera muy sencilla:

```python
#creación de grafica apilada de la cantidad y tipo de población.
grafica=pd.DataFrame({
    'Genero':['mujeres','mujeres','hombres','hombres'],
    'Edad':['menor_edad','mayor_edad','menor_edad','mayor_edad'],
    'Poblacion_Total':[9941647,27117573,10232485,24693752]
})

genero=alt.Chart(grafica).mark_bar().encode(
    x='Poblacion_Total',
    y='Edad',
    color='Genero'
)

genero
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Dist_Pobla.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Dist_Pobla.png)

O este último ejemplo en donde creamos una gráfica apilada por estado y situación:

```python
#creación de grafica apilada de la cantidad y tipo de situación.

nac_def=alt.Chart(grafica).mark_bar().encode(
    x='sum(Cantidad):Q',
    y='Estado_Rep:N',
    color='Situacion:N'
).interactive()

nac_def
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Nacimiento_def_est.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Nacimiento_def_est.png)

Como se podrá notar, el uso de `Altair` está diseñado para ser relativamente fácil de usar. Se basa en el principio de "gramática de gráficos" (inspirado en la gramática del lenguaje), lo que significa que proporciona una sintaxis clara y declarativa para crear visualizaciones. 


## Análisis de Texto

El análisis de texto ha sido realizado utilizando una variedad de técnicas, desde los métodos de procesamiento de cadenas (string methods) y expresiones regulares (regex) hasta el uso de bibliotecas avanzadas como `spaCy` y `NLTK`. Además, se ha llevado a cabo un preprocesamiento exhaustivo para mejorar la calidad de los resultados.

Herramientas como *n-grams* y *wordclouds* han sido empleadas para obtener insights valiosos a partir de los datos de texto. Un ejemplo de ello:

De manera concreta, se realizo la consulta de 10 noticias diferentes, en diferentes portales y de diferentes años sobre Coppel, ya fuera sobre sus tiendas, temas de servicios al cliente o nuevas herramientas que tiene, para brindar un mejor servicio a todos sus usuarios. En general seguimos los siguientes tres pasos para las diez noticias.

### 1. Hacer 'encoding'

```python
url = "https://lasillarota.com/dinero/2023/1/28/eres-cliente-coppel-checa-estos-nuevos-beneficios-411831.html"
response = requests.get(url)
response.encoding='utf-8'
html = response.text
soup = BeautifulSoup(html, 'html.parser')

# Identifica las etiquetas que contienen el texto de la noticia
noticias_tags = soup.find_all('p')  # Puedes ajustar esto según la estructura HTML

# Extrae el texto de las etiquetas identificadas
texto_noticia = ' '.join([tag.get_text() for tag in noticias_tags])
```

### 2. Tokenizamos con `NLTK`

```python
# Normalizar caracteres usando unidecode
normalized_text = unidecode(texto_noticia)

# Tokenizar el texto normalizado
tokens = nltk.word_tokenize(normalized_text)

# Filtrar palabras que no son alfanuméricas y convertirlas a minúsculas
words = [word.lower() for word in tokens if word.isalpha()]

```

### 3. Eliminamos palabras con stopwords

```python
palabras_filtradas1 = [palabra for palabra in tokens if palabra.lower() not in stopwords.words('spanish')]
```

----

### Wordcloud

Después de hacer esto, realizamos el **WordCloud** con los resultados:

```python
# Calcular la frecuencia de cada palabra
freq_dist = FreqDist(palabras_filtradas)

mask = np.array(Image.open('Coppel7.png').resize((800, 400)))

# Crear un WordCloud
wordcloud = WordCloud(width=1332, height=680, random_state=11, max_font_size=300, background_color='white', max_words=500, collocations=False, mode='RGBA',mask=mask).generate_from_frequencies(freq_dist)

# Visualizar la WordCloud
plt.figure(figsize=(30, 15))
image_colors = ImageColorGenerator(mask)
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis('off')
plt.show()
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Wordcloud.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/Wordcloud.png)

## Desarrollo de Mapas

El desarrollo de mapas interactivos ha sido llevado a cabo con la ayuda de `Folium` y `GeoPandas`. Estos mapas proporcionan una representación geoespacial de la información, facilitando la comprensión de patrones y distribuciones espaciales.

### Mapas de GeoPandas

`GeoPandas` es una biblioteca de Python que extiende las capacidades de pandas para trabajar con datos geoespaciales. Permite trabajar con datos geoespaciales de manera fácil y eficiente al proporcionar estructuras de datos geoespaciales y funciones para realizar operaciones espaciales y análisis.

Para esta parte, primero cargamos los datos:

```python
# Cargamos librerías
import pandas as pd

# Ruta del archivo CSV
file_path = '3. Data/Datos_Estados.csv'

# Leemos el archivo
df_estados = pd.read_csv(file_path)

# Le agregamos un 0 cuando la variable estado es de un dígito y lo 
# convertimos a string
df_estados['id_ent'] = df_estados['estado'].apply(lambda x: f'{x:02d}').astype(str)
df_estados.head()
```
Después los convertimos a un tipo de `DataFrame` espacial, utilizando los polígonos que usa el INEGI en su [Marco Geoestadístico](https://www.inegi.org.mx/temas/mg/):

```python
# Importamos librería
import geopandas as gpd

# Ruta del archivo shapefile (.shp) del INEGI
shapefile_path = '3. Data/nacional_dic22/mg2022_integrado/conjunto_de_datos/00ent.shp'

# Cargar el Shapefile en un GeoDataFrame
gdf_mex_ent = gpd.read_file(shapefile_path)
gdf_mex_ent.head()
```
Luego, podemos definir un mapa de la siguiente manera:

```python
# Definimos la paleta de colores
num_colors = 250
custom_colors = plt.cm.Reds(range(num_colors))

# Creamos el mapa de colores personalizado
custom_cmap = ListedColormap(custom_colors)

# Configuración del gráfico
fig, ax = plt.subplots(figsize=(10, 10))  

# Para hacer el mapa
plot = gdf_estados.plot(
    column='def_x_est',
    cmap=custom_cmap,
    legend=True,
    legend_kwds={'label': "Defunciones totales", 'shrink': 0.35},  
    ax=ax  
)

# Quitamos los ejes
ax.set_axis_off()

# Agrega etiquetas de valores en cada polígono
#for x, y, label in zip(gdf_estados.geometry.centroid.x, gdf_estados.geometry.centroid.y, gdf_estados['def_x_est']):
#    ax.annotate(text=label, xy=(x, y), xytext=(3, 3), textcoords='offset points', fontsize=8, color='black')

# Título
plt.title('Mapa de defunciones por estado', fontsize=16)

# Guardamos el mapa como PNG
plt.savefig('mapa_defunciones.png', bbox_inches='tight')

# Vemos el mapa
plt.show()
```

El cual muestra la siguiente imagen:

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_defunciones.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_defunciones.png)

También lo podemos ver para los nacimientos por estados, cambiando esta parte:

```python
plot = gdf_estados.plot(
    column='nacim_x_est',
    cmap=custom_cmap,
    legend=True,
    legend_kwds={'label': "Nacimientos totales", 'shrink': 0.35},  
    ax=ax  
)

```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_nacimientos.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_nacimientos.png)

O los ingresos trimestrates promedio:

```python
plot = gdf_estados.plot(
    column='prom_ingr_trim',
    cmap=custom_cmap,
    legend=True,
    legend_kwds={'label': "Ingreso promedio trimestral", 'shrink': 0.35},  
    ax=ax  
)
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_ingresos.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_ingresos.png)

O un mapa para el gasto en alimentos promedio por estados:

```python
plot = gdf_estados.plot(
    column='gast_alim_esta_trim',
    cmap=custom_cmap,
    legend=True,
    legend_kwds={'label': "Gasto en alimentos trimestral", 'shrink': 0.35},  
    ax=ax  
)
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_gasto_alimentos.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_gasto_alimentos.png)

### Mapas de folium

`Folium` es una biblioteca de Python que se utiliza para crear mapas interactivos. Facilita la creación de mapas interactivos y su visualización en un entorno web. También es posible usar la librería `folium` para poder navegar en los mapas. En el siguiente código estamos creando un mapa **base** en donde vamos a mostrar la ubicación de las tiendas posteriormente.

```python
# Importamos folium
import folium

# Creamos un mapa centrado en la ubicación media de los polígonos
map_center = [gdf_gastos.geometry.centroid.y.mean(), gdf_gastos.geometry.centroid.x.mean()]
m = folium.Map(location=map_center, zoom_start=5)
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_general.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_general.png)

A continuación, se muestran las ubicaciones de las tiendas y sus respectivos valores de gasto en diciembre de 2022. **Nota:** dado que el mapa está en HTML no es posible visualizarlo de archivos tipo _markdown_ por lo que solo estamos utilizando una imagen a manera ilustratita:

```python
# Agregamos los puntos al mapa
for idx, row in gdf_gastos.iterrows():
    folium.Marker(
        location=[row.geometry.y, row.geometry.x],
        tooltip=f"Gasto: ${idx}",
        popup=f"Gasto en\ndic de 2022: ${row['dic-22']}"  
    ).add_to(m)
    
m
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_gasto_dic_22.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_gasto_dic_22.png)

Por último, sin mayor diferencia, está el mapa con las ubicaciones de las tiendas y niveles de venta promedio en 2022:

```python
# Agregamos los puntos al mapa
for idx, row in gdf_ventas.iterrows():
    folium.Marker(
        location=[row.geometry.y, row.geometry.x],
        tooltip=f"Venta promedio: ${idx}",
        popup=f"Venta promedio\nen 2022: ${row['promedio']}"  
    ).add_to(m)
    
m
```

![](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_venta_dic_22.png)

Fuente: [Elaboración propia](https://github.com/LeonardoDPantoja/Final_Project/blob/main/doc/IMAGES/mapa_folium_venta_dic_22.png)


