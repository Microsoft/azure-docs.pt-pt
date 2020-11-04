---
title: 'Tutorial: Criar um caderno em Azure Cosmos DB para analisar e visualizar os dados'
description: 'Tutorial: Saiba como usar cadernos Jupyter incorporados para importar dados para Azure Cosmos DB, analisar os dados e visualizar a saída.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: e16a738264e64e37cfa42722832dac7e34fee899
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339501"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Tutorial: Criar um caderno em Azure Cosmos DB para analisar e visualizar os dados
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como usar cadernos Jupyter incorporados para importar dados de retalho de amostras para Azure Cosmos DB. Você verá como usar os comandos mágicos SQL e Azure Cosmos DB para executar consultas, analisar os dados e visualizar os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* [Ativar o suporte de cadernos ao criar a conta Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Criar os recursos e os dados de importação
 
Nesta secção, irá criar a base de dados, o contentor Azure Cosmos e importar os dados de retalho para o contentor.

1. Navegue na sua conta Azure Cosmos e abra o **Data Explorer.**

1. Vá ao **separador Cadernos,** selecione `…` ao lado dos Meus **Cadernos** e crie um **Novo Caderno.** Selecione **Python 3** como o Kernel padrão.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Criar um novo Notebook":::

1. Depois de ser criado um novo caderno, pode renomeá-lo para algo como **VisualizeRetailData.ipynb.**

1. Em seguida, irá criar uma base de dados chamada "RetailDemo" e um contentor chamado "WebsiteData" para armazenar os dados de retalho. Pode utilizar /CartID como chave de partição. Copie e cole o seguinte código para uma nova célula no seu caderno e execute-o:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Para executar uma célula, `Shift + Enter` selecione ou selecione a célula e escolha a opção **'Executar Célula Ativa'** na barra de navegação do explorador de dados.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Executar a célula ativa":::

   A base de dados e o contentor são criados na sua conta atual da Azure Cosmos. O contentor é a provisionado com 400 RU/s. Verá a seguinte saída após a criação da base de dados e do recipiente. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   Também pode atualizar o separador **Dados** e ver os recursos recém-criados:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Atualizar o separador de dados para ver o novo recipiente":::

1. Em seguida, importará os dados de varejo da amostra para o contentor Azure Cosmos. Aqui está o formato de um item a partir dos dados de retalho:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Para o efeito tutorial, os dados de varejo da amostra são armazenados no armazenamento de bolhas Azure. Pode importá-lo para o contentor Azure Cosmos colando o seguinte código numa nova célula. Pode confirmar que os dados são importados com sucesso através de uma consulta para selecionar o número de itens.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Quando executam a consulta anterior, retorna a seguinte saída:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Coloque os seus dados num DataFrame

Antes de executar consultas para analisar os dados, pode ler os dados do recipiente para um [DataFrame pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) para análise. Utilize o seguinte comando mágico sql para ler os dados num DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Para saber mais, consulte os [comandos e funcionalidades do caderno incorporado no artigo da Azure Cosmos DB.](use-python-notebook-features-and-commands.md) Vai fazer a `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c` consulta. Os resultados serão guardados num DataFrame pandas nomeado df_cosmos. Cole o seguinte comando numa nova célula de caderno e execute-o:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

Numa nova célula de portátil, execute o seguinte código para ler os primeiros 10 itens da saída:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Faça consulta para obter os 10 melhores itens":::

## <a name="run-queries-and-analyze-your-data"></a>Executar consultas e analisar os seus dados

Nesta secção, irá executar algumas consultas sobre os dados recuperados.

* **Consulta1:** Executar um Grupo por consulta no DataFrame para obter a soma do total das receitas de vendas de cada país/região e apresentar 5 itens dos resultados. Numa nova célula de caderno, execute o seguinte código:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Produção total de receitas de vendas":::

* **Consulta2:** Para obter uma lista dos cinco principais itens adquiridos, abra uma nova célula de portátil e execute o seguinte código:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Top 5 itens comprados":::

## <a name="visualize-your-data"></a>Visualizar os seus dados  

1. Agora que temos os nossos dados sobre as receitas do contentor Azure Cosmos, pode visualizar os seus dados com uma biblioteca de visualização à sua escolha. Neste tutorial, usaremos a biblioteca Bokeh. Abra uma nova célula de portátil e execute o seguinte código para instalar a biblioteca Bokeh. Depois de todos os requisitos estarem preenchidos, a biblioteca será instalada.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Em seguida, prepare-se para traçar os dados num mapa. Junte os dados em Azure Cosmos DB com informações de país/região localizadas no armazenamento de Azure Blob e converta o resultado no formato GeoJSON. Copie o seguinte código para uma nova célula de portátil e execute-o.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualizar as receitas de vendas de diferentes países/regiões num mapa mundial executando o seguinte código numa nova célula portátil:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   A saída exibe o mapa do mundo com cores diferentes. As cores mais escuras para mais claras representam os países/regiões com maiores receitas para as receitas mais baixas.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualização do mapa de receitas dos países/regiões":::

1. Vamos ver outro caso de visualização de dados. O contentor WebsiteData tem registo de utilizadores que visualizaram um item, adicionados ao seu carrinho, e compraram o item. Vamos traçar a taxa de conversão dos artigos comprados. Executar o seguinte código numa nova célula para visualizar a taxa de conversão de cada item:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Visualizar taxa de conversão de compra":::

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os comandos de caderno python, veja [como usar comandos e funcionalidades de cadernos incorporados no artigo da Azure Cosmos DB.](use-python-notebook-features-and-commands.md)
