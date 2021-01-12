---
title: Visualizações
description: Utilize os cadernos Azure Synapse para visualizar os seus dados
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: f11693b34048b11c02668e086561b9a6521a5213
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121530"
---
# <a name="visualize-data"></a>Visualizar os dados
O Azure Synapse é um serviço de análise integrado que acelera o tempo de perspicácia, através de armazéns de dados e sistemas de análise de big data. A visualização de dados é um componente chave para ser capaz de obter informações sobre os seus dados. Ajuda a tornar os dados grandes e pequenos mais fáceis de entender pelos humanos. Também facilita a deteção de padrões, tendências e outliers em grupos de dados. 

Ao utilizar o Apache Spark in Azure Synapse Analytics, existem várias opções incorporadas para o ajudar a visualizar os seus dados, incluindo opções de gráficos de cadernos Synapse, acesso a bibliotecas de código aberto populares e integração com o Synapse SQL e Power BI.

## <a name="notebook-chart-options"></a>Opções de gráfico de caderno
Ao utilizar um portátil Azure Synapse, pode transformar a sua visualização de resultados tabulares num gráfico personalizado utilizando opções de gráficos. Aqui, pode visualizar os seus dados sem ter de escrever qualquer código. 

### <a name="displaydf-function"></a>função exibição(df)
A ```display``` função permite transformar consultas DE SQL e dataframes e RDDs apache spark em visualizações de dados ricas. A ```display``` função pode ser utilizada em dataframes ou RDDs criados em PySpark, Scala, Java e .NET.

Para aceder às opções do gráfico:
1. A saída dos ```%%sql``` comandos mágicos aparece na vista da mesa por defeito. Também pode recorrer ```display(df)``` à função Spark DataFrames ou Resilient Distributed Datasets (RDD) para produzir a vista de tabela renderizada. 
   
2. Uma vez que tenha uma vista de mesa renderizada, mude para a Vista de Gráficos.
   ![incorporado em gráficos](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Pode agora personalizar a sua visualização especificando os seguintes valores:
   | Configuração | Descrição |
   |--|--| 
   | Tipo de Gráfico | A ```display``` função suporta uma ampla gama de tipos de gráficos, incluindo gráficos de barras, enredos de dispersão, gráficos de linha, e muito mais |
   | Chave | Especificar a gama de valores para o eixo x|
   | Valor | Especificar a gama de valores para os valores do eixo y |
   | Grupo série | Usado para determinar os grupos para a agregação | 
   | Agregação | Método para agregar dados na sua visualização| 
   
   
    > [!NOTE]
    > Por predefinição, a ```display(df)``` função só levará as primeiras 1000 linhas dos dados para render as tabelas. Verifique a **agregação sobre todos os resultados** e clique no botão **Aplicar,** aplicará a geração de gráficos a partir de todo o conjunto de dados. Um trabalho de Faísca será ativado quando a definição do gráfico for muda. Por favor, note que pode levar vários minutos para completar o cálculo e renderizar o gráfico.
   
4. Uma vez feito, pode ver e interagir com a sua visualização final!

### <a name="displaydf-statistic-details"></a>display(df) detalhes estatísticos
Pode utilizar <code>display(df, summary = true)</code> para verificar o resumo estatístico de um dado Apache Spark DataFrame que inclua o nome da coluna, tipo de coluna, valores únicos e valores em falta para cada coluna. Também pode selecionar em coluna específica para ver o seu valor mínimo, valor máximo, valor médio e desvio padrão.
    ![incorporado em gráficos-resumo](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>opção displayHTML (df)
Os cadernos Azure Synapse Analytics suportam gráficos HTML utilizando a ```displayHTML``` função.

A imagem a seguir é um exemplo de criação de visualizações utilizando [D3.js](https://d3js.org/).

   ![d3-js-exemplo](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Executar o seguinte código para criar a visualização acima.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Bibliotecas Populares
No que diz respeito à visualização de dados, python oferece várias bibliotecas de gráficos que vêm repletas de muitas funcionalidades diferentes. Por padrão, cada Piscina de Faíscas Apache em Azure Synapse Analytics contém um conjunto de bibliotecas de código aberto com curadoria e populares. Também pode adicionar ou gerir bibliotecas adicionais & versões utilizando as capacidades de gestão da biblioteca Azure Synapse Analytics. 

### <a name="bokeh"></a>Bokeh
Pode renderizar bibliotecas HTML ou interativas, como **bokeh,** utilizando o ```displayHTML(df)``` . 

A imagem a seguir é um exemplo de conspiração de glifos sobre um mapa usando **bokeh**.

   ![bokeh-exemplo](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Executar o seguinte código de amostra para desenhar a imagem acima.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
Pode tornar as bibliotecas padrão de conspiração, como o Matplotlib, utilizando as funções de renderização incorporadas para cada biblioteca.

A imagem a seguir é um exemplo de criação de um gráfico de barras utilizando **Matplotlib**.
   ![Exemplo de gráfico de linha.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Executar o seguinte código de amostra para desenhar a imagem acima.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Bibliotecas adicionais 
Além destas bibliotecas, o Azure Synapse Analytics Runtime também inclui o seguinte conjunto de bibliotecas que são frequentemente usadas para visualização de dados:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

Pode visitar a [documentação](./spark/../apache-spark-version-support.md) do Runtime Azure Synapse Analytics para obter as informações mais atualizadas sobre as bibliotecas e versões disponíveis.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Ligue-se ao Power BI utilizando o Apache Spark & SQL On-Demand
O Azure Synapse Analytics integra-se profundamente com o Power BI permitindo aos engenheiros de dados construir soluções de análise.

O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e tabelas entre as suas piscinas Spark e piscina SQL sem servidor. Utilizando o [modelo de metadados partilhados,](../metadata/overview.md)pode consultar as suas tabelas Apache Spark utilizando SQL a pedido. Uma vez feito, pode ligar o seu ponto final a pedido do SQL ao Power BI para consultar facilmente as suas tabelas Spark sincronizadas.


## <a name="next-steps"></a>Passos seguintes
- Para mais informações sobre como configurar o Conector SPARK SQL DW: [Conector SQL Synapse](./spark/../synapse-spark-sql-pool-import-export.md)
- Ver as bibliotecas predefinidas: [Azure Synapse Analytics tempo de execução](../spark/apache-spark-version-support.md)