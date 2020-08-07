---
title: Cadernos do Estúdio De Sinaapse
description: Neste artigo, aprende-se a criar e desenvolver cadernos do Azure Synapse Studio (pré-visualização) para fazer a preparação e visualização de dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6e0062450889a2bbbdfcd47137ffbe36b83cae57
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849103"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Criar, desenvolver e manter cadernos do Synapse Studio (pré-visualização) em Azure Synapse Analytics

Um notebook Synapse Studio (pré-visualização) é uma interface web para criar ficheiros que contenham código ao vivo, visualizações e texto narrativo. Os cadernos são um bom local para validar ideias e usar experiências rápidas para obter informações dos seus dados. Os cadernos também são amplamente utilizados na preparação de dados, visualização de dados, machine learning e outros cenários de Big Data.

Com um caderno Azure Synapse Studio, você pode:

* Começa com nenhum esforço de preparação.
* Mantenha os dados seguros com funcionalidades de segurança da empresa incorporadas.
* Analise os dados em formatos brutos (CSV, txt, JSON, etc.), formatos de ficheiros processados (parquet, Delta Lake, ORC, etc.), e ficheiros de dados tabulares SQL contra Spark e SQL.
* Seja produtivo com capacidades de autoria melhoradas e visualização de dados incorporados.

Este artigo descreve como usar cadernos no Azure Synapse Studio.

## <a name="create-a-notebook"></a>Criar um bloco de notas

Há duas maneiras de criar um caderno. Pode criar um novo caderno ou importar um caderno existente para um espaço de trabalho Azure Synapse do Explorador de **Objetos.** Os portáteis do Estúdio Azure Synapse podem reconhecer ficheiros IPYNB de Caderno Jupyter padrão.

![sinapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Desenvolver blocos de notas

Os cadernos são de células, que são blocos individuais de código ou texto que podem ser funcionadas independentemente ou como um grupo.

### <a name="add-a-cell"></a>Adicione uma célula

Há várias formas de adicionar uma nova célula ao seu caderno.

1. Expanda o botão superior esquerdo **+ célula** e selecione Adicionar célula **de código** ou adicionar célula **de texto**.

    ![botão adicionar célula com célula](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Pairar sobre o espaço entre duas células e selecionar **Adicionar código** ou **adicionar texto**.

    ![adicionar células entre o espaço](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Utilize [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Prima **B** para inserir uma célula abaixo da célula atual.

### <a name="set-a-primary-language"></a>Definir uma língua primária

Os cadernos do Azure Synapse Studio suportam quatro línguas Apache Spark:

* pySpark (Python)
* Faísca (Scala)
* Sparksql
* .NET para Apache Spark (C#)

Pode definir a linguagem primária para novas células adicionadas da lista de dropdown na barra de comando superior.

   ![linguagem padrão-sinapse](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Use várias línguas

Pode utilizar várias línguas num só caderno especificando o comando mágico da linguagem correto no início de uma célula. A tabela que se segue lista os comandos mágicos para mudar as línguas celulares.

|Comando mágico |Linguagem | Descrição |  
|---|------|-----|
|%%pyspark| Python | Execute uma consulta **python** contra o contexto de faísca.  |
|%%faísca| Scala | Execute uma consulta **scala** contra o Contexto de Faíscas.  |  
|%%sql| Sparksql | Execute uma consulta **SparkSQL** contra o Contexto de Faísca.  |
|%%csharp | .NET para Faísca C # | Execute uma consulta **.NET para Spark C#** contra o Contexto de Faísca. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark usando o comando mágico **%%pyspark** ou uma consulta SparkSQL com o comando mágico **de %%sql** em um caderno **Spark (Scala).** Note que a língua primária para o caderno está definida para pySpark.

   ![sinapse-faísca-magia](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Use tabelas temporárias para fazer referência a dados em todos os idiomas

Não é possível fazer referência a dados ou variáveis diretamente em diferentes línguas num caderno do Synapse Studio. Em Spark, uma tabela temporária pode ser referenciada através de línguas. Aqui está um exemplo de como ler um `Scala` DataFrame `PySpark` dentro e usar uma tabela de temperatura spark como uma `SparkSQL` solução alternativa.

1. Na Cell 1, leia um DataFrame do conector de piscina SQL usando o Scala e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Na Célula 2, consultar os dados utilizando o Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Na cela 3, use os dados em PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense estilo IDE

Os cadernos do Azure Synapse Studio estão integrados com o editor do Mónaco para levar o IntelliSense ao editor de células ao estilo IDE. O destaque da sintaxe, o marcador de erro e as conclusões automáticas do código ajudam-no a escrever código e a identificar problemas mais rapidamente.

As características do IntelliSense estão em diferentes níveis de maturidade para diferentes línguas. Use a tabela abaixo para ver o que é suportado.

|Linguagens| Destaque de sintaxe | Marcador de erro de sintaxe  | Conclusão do Código sintaxe | Conclusão do código variável| Conclusão do código de função do sistema| Conclusão do código de função do utilizador| Avanço Inteligente | Dobragem de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Faísca (Scala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|Sparksql|Sim|Sim|-|-|-|-|-|-|
|.NET for Spark (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Célula de texto de formato com botões de barra de ferramentas

Pode utilizar os botões de formato na barra de ferramentas das células de texto para fazer ações comuns de marcação. Inclui texto arrojado, texto em itálico, inserção de códigos de corte, inserção de lista não ordenada, inserção da lista ordenada e inserção da imagem a partir de URL.

  ![sinapse-texto-célula-barra](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Desfazer operações celulares
Clique no botão **de desfazer** ou pressione **Ctrl+Z** para revogar a operação celular mais recente. Agora pode desfazer as últimas 20 ações históricas de células. 

   ![sinapse-desfazer-células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Mover uma célula

Selecione as elipses (...) para aceder ao menu adicional de ações celulares na extrema-direita. Em seguida, **selecione Mover a célula para cima** ou mover a célula para **baixo** para mover a célula atual. 

Também pode utilizar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **ctrl+Alt+↑** para subir a célula atual. Prima **Ctrl+Alt+↓** para mover a célula atual para baixo.

   ![mover-a-célula](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Apagar uma célula

Para eliminar uma célula, selecione as elipses (...) para aceder ao menu de ações celulares adicionais na extrema direita e, em seguida, **selecione Delete cell**. 

Também pode utilizar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para apagar a célula atual.
  
   ![apagar-a-célula](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Colapso de uma entrada de células
Clique no botão de seta na parte inferior da célula atual para o colapsar. Para expandi-lo, clique no botão de seta enquanto a célula está colapsada.

   ![entrada de células em colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Colapso de uma saída celular

Clique no botão **de saída** de colapso na parte superior esquerda da saída da célula atual para o colapsar. Para expandi-la, clique na saída da **célula Show** enquanto a saída da célula é colapsada.

   ![saída de células em colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Executar blocos de notas

Pode executar as células de código no seu caderno individualmente ou todas de uma só vez. O estado e o progresso de cada célula estão representados no caderno.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código numa cela.

1. Passe pela célula que pretende executar e selecione o botão **'Célula de Execução'** ou prima **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Para aceder ao menu adicional de ações celulares na extrema-direita, selecione as elipses **(...**). Em seguida, selecione **Executar a célula**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Utilize [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). **Prima Shift+Enter** para executar a célula atual e selecione a célula abaixo. Prima **Alt+Enter** para executar a célula atual e insira uma nova célula abaixo.


### <a name="run-all-cells"></a>Executar todas as células
Clique no botão **Executar Todas** para executar todas as células no caderno atual em sequência.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

Para aceder ao menu adicional de ações celulares na extrema-direita, selecione as elipses **(...**). Em seguida, selecione **Executar as células acima** para executar todas as células acima da corrente em sequência. Selecione **Executar as células abaixo** para executar todas as células abaixo da corrente em sequência.

   ![células de execução acima ou abaixo](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Cancelar todas as células de execução
Clique no botão **Cancelar Todos** para cancelar as células de funcionamento ou as células que aguardam na fila. 
   ![cancelar todas as células](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Indicador de estado celular

Um estado de execução de células passo a passo é exibido sob a célula para ajudá-lo a ver o seu progresso atual. Uma vez concluída a execução da célula, é apresentado um resumo de execução com a duração total e o tempo final e mantidos lá para referência futura.

![estado celular](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso de faísca

O caderno do Estúdio Azure Synapse é puramente baseado em Faíscas. As células de código são executadas na piscina Spark remotamente. Um indicador de progresso de emprego spark é fornecido com uma barra de progresso em tempo real parece ajudá-lo a entender o estado de execução do trabalho.
O número de tarefas por cada trabalho ou etapa ajuda-o a identificar o nível paralelo do seu trabalho de faísca. Também pode perfurar mais profundamente a UI de uma tarefa específica (ou estágio) clicando no link no nome de trabalho (ou estágio).


![indicador de faísca-progresso](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Config da sessão de faísca

Pode especificar a duração do tempo limite, o número e o tamanho dos executores para dar à atual sessão de Faísca na **sessão de Configuração**. Reiniciar a sessão Spark destina-se a alterações de configuração a produzir efeito. Todas as variáveis de cadernos em cache estão limpas.

[![gestão de sessão](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Um recomendador de sessão de faíscas agora disponível no painel config da sessão de faíscas. Pode selecionar um pool de faíscas diretamente do painel de configuração da sessão e ver quantos nós estão a usar e quantos executores restantes estão disponíveis. Estas informações podem ajudá-lo a definir o tamanho da sessão adequadamente, em vez de modificá-lo para trás e para a frente.

![sessão-recomendar](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Levar dados a um caderno

Pode carregar dados de Azure Blob Storage, Azure Data Lake Store Gen 2 e sql pool como mostrado nas amostras de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leia um CSV da Azure Data Lake Store Gen2 como um Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leia um CSV do Azure Blob Storage como um Spark DataFrame

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Ler dados da conta de armazenamento primário

Pode aceder diretamente aos dados na conta de armazenamento primário. Não há necessidade de fornecer as chaves secretas. No Data Explorer, clique com o botão direito num ficheiro e selecione **Novo caderno** para ver um novo caderno com o extrator de dados autogerido.

![dados para célula](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizar dados num caderno

### <a name="produce-rendered-table-view"></a>Produzir vista de mesa renderizada

Uma vista de resultados tabulares é fornecida com a opção de criar um gráfico de barras, gráfico de linha, gráfico de tortas, gráfico de dispersão e gráfico de área. Pode visualizar os seus dados sem ter de escrever código. Os gráficos podem ser personalizados nas **Opções de Gráfico.** 

A saída de comandos mágicos **%%sql** aparece na vista de mesa renderizada por padrão. Pode recorrer <code>display(df)</code> à função Spark DataFrames ou Resilient Distributed Datasets (RDD) para produzir a vista de tabela renderizada.

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Visualizar gráficos incorporados a partir de conjuntos de dados em larga escala 

Por predefinição, a <code>display(df)</code> função só levará as primeiras 1000 linhas dos dados para render as tabelas. Verifique a **agregação sobre todos os resultados** e clique em **Aplicar** o botão, aplicará a geração de gráficos a partir de todo o conjunto de dados. Uma faísca será ativada quando a definição do gráfico mudar, demora um pouco a concluir o cálculo e a renderizar o gráfico. 
    [![builtin-charts-agregação-todos](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>Visualizar informações estatísticas de dados
Pode utilizar <code>display(df, summary = true)</code> para verificar o resumo estatístico de um dado DataFrame de faísca que inclua o nome da coluna, tipo de coluna, valores únicos e valores em falta para cada coluna. Também pode selecionar em coluna específica para ver o seu valor mínimo, valor máximo, valor médio e desvio padrão.
    [![builtin-charts-resumo ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>Renderização DE HTML ou bibliotecas interativas

Pode renderizar bibliotecas HTML ou interativas, como **o bokeh,** utilizando o **displayHTML()**.

A imagem a seguir é um exemplo de conspiração de glifos sobre um mapa usando **bokeh**.

   ![bokeh-exemplo](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

## <a name="save-notebooks"></a>Guardar cadernos

Pode guardar um único caderno ou todos os cadernos no seu espaço de trabalho.

1. Para guardar as alterações efetuadas num único bloco de notas, selecione o botão **Publicar** na barra de comando do portátil.

   ![publicar caderno](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para guardar todos os cadernos no seu espaço de trabalho, selecione o botão **Publicar todos na** barra de comando do espaço de trabalho. 

   ![publicar tudo](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do portátil, pode configurar se deve incluir a saída da célula ao poupar.

   ![portátil-propriedades](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos mágicos
Você pode usar seus comandos mágicos Jupyter familiares em cadernos do Estúdio Azure Synapse. Consulte a lista abaixo como os comandos mágicos disponíveis atuais. Diga-nos os seus casos de uso no GitHub para que possamos continuar a construir mais comandos mágicos para satisfazer as suas necessidades.

Magias de linha disponíveis: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magias celulares disponíveis: [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>Caderno orquestrado

### <a name="add-a-notebook-to-a-pipeline"></a>Adicione um caderno a um oleoduto

Clique no botão **adicionar ao gasoduto** no canto superior direito para adicionar um caderno a um gasoduto existente ou criar um novo gasoduto.

![adicionar ao gasoduto](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designar uma célula de parâmetros

Para parametrizar o seu caderno selecione as elipses (...) para aceder ao menu adicional de ações celulares na extrema-direita. Em seguida, selecione **a célula de parâmetros Toggle** para designar a célula como célula de parâmetros.

![parâmetro de alternância](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

A Azure Data Factory procura a célula de parâmetros e trata esta célula como padrão para os parâmetros passados no tempo de execução. O motor de execução adicionará uma nova célula sob a célula de parâmetros com parâmetros de entrada, a fim de substituir os valores predefinidos. Quando não houver parâmetros, a célula injetada será inserida na parte superior do caderno.

### <a name="assign-parameters-values-from-a-pipeline"></a>Atribuir valores de parâmetros a partir de um oleoduto

Uma vez criado um caderno com parâmetros, pode executá-lo a partir de um oleoduto com a atividade do Caderno Azure Synapse. Depois de adicionar a atividade à sua tela de pipeline, poderá definir os valores dos parâmetros na secção **parâmetros Base** no **separador Definições.** 

![atribuir parâmetro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Ao atribuir valores de parâmetros, pode utilizar a linguagem de expressão do [gasoduto](../../data-factory/control-flow-expression-language-functions.md) ou [as variáveis do sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Chaves de atalho

Semelhante aos Cadernos Jupyter, os portáteis do Azure Synapse Studio têm uma interface de utilizador modal. O teclado faz coisas diferentes dependendo do modo em que a célula de portátil está. Os cadernos Synapse Studio suportam os seguintes dois modos para uma determinada célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há cursor de texto que lhe ordene a escrever. Quando uma célula está no modo Comando, pode editar o caderno como um todo, mas não digitar em células individuais. Introduza o modo de comando premindo `ESC` ou utilizando o rato para clicar fora da área de editor de uma célula.

   ![modo de comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. O modo de edição é indicado por um cursor de texto que o leva a escrever na área do editor. Quando uma célula está em modo de edição, pode digitar na célula. Introduza o modo de edição premindo `Enter` ou utilizando o rato para clicar na área de editor de uma célula.
   
   ![modo-de-edição](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

Utilizando os seguintes atalhos de teclas, pode navegar e executar código mais facilmente em cadernos Azure Synapse.

| Ação |Atalhos de cadernos do Estúdio De Sinaapse  |
|--|--|
|Executar a célula atual e selecione abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Entrar |
|Selecione a célula acima| Cima |
|Selecione a célula abaixo| Baixo |
|Insira a célula acima| A |
|Insira a célula abaixo| B |
|Estender as células selecionadas acima| Shift+Up |
|Estender as células selecionadas abaixo| Shift+Down|
|Mover a célula para cima| Ctrl+Alt+↑ |
|Mover a célula para baixo| Ctrl+Alt+↓ |
|Eliminar células selecionadas| D, D |
|Mude para o modo de edição| ENTER |

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho em modo de edição

Utilizando os seguintes atalhos de teclas, pode navegar e executar código mais facilmente em cadernos Azure Synapse quando estiver no modo Editar.

| Ação |Atalhos de cadernos do Estúdio de Synapse  |
|--|--|
|Mover cursor para cima | Cima |
|Mover cursor para baixo|Baixo|
|Anular|Ctrl + Z|
|Refazer|Ctrl + Y|
|Comentário/Desinteressamento|Ctrl + /|
|Apagar palavra antes|Ctrl + Backspace|
|Apagar palavra depois|Ctrl + Eliminar|
|Vá para o início da célula|Ctrl + Casa|
|Ir para o fim da cela |Ctrl + Fim|
|Vai uma palavra à esquerda|Ctrl + Esquerda|
|Vai uma palavra à direita|Ctrl + Direito|
|Selecionar tudo|Ctrl + A|
|Travessão| Ctrl + ]|
|Dedent|Ctrl +|
|Mudar para o modo de comando| Esc |

## <a name="next-steps"></a>Passos seguintes
- [Confira os cadernos da amostra da Sinapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Criar uma piscina Apache Spark (pré-visualização) em Azure Synapse Analytics usando ferramentas web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark em Azure Synapse Analytics](apache-spark-overview.md)
- [Utilize .NET para Apache Spark com Azure Synapse Analytics](spark-dotnet.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
