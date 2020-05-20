---
title: Criar, desenvolver e manter os cadernos azure Synapse Studio (pré-visualização)
description: Neste artigo, aprende-se a criar e desenvolver cadernos do Azure Synapse Studio (pré-visualização) para fazer a preparação e visualização de dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 21e3ba8cbf60cbbdc6480719016fc48db4fe390c
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702109"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Criar, desenvolver e manter os cadernos azure Synapse Studio (pré-visualização)

Um notebook Azure Synapse Studio (pré-visualização) é uma interface web para você criar ficheiros que contenham código vivo, visualizações e texto narrativo. Os cadernos são um bom local para validar ideias e usar experiências rápidas para obter informações dos seus dados. Os cadernos também são amplamente utilizados na preparação de dados, visualização de dados, machine learning e outros cenários de Big Data.

Com um caderno do Estúdio Azure Synapse, pode:

* Começa com zero esforço de preparação.
* Mantenha os dados seguros com funcionalidades de segurança da empresa incorporadas.
* Analise os dados em formatos brutos (CSV, txt, JSON, etc.), formatos de ficheiros processados (parquet, Delta Lake, ORC, etc.) e ficheiros de dados tabular SQL contra a Spark e a SQL.
* Seja produtivo com capacidades de autor e visualização de dados incorporados.

Este artigo descreve como usar cadernos no Estúdio Azure Synapse.

## <a name="create-a-notebook"></a>Criar um bloco de notas

Há duas maneiras de criar um caderno. Pode criar um novo caderno ou importar um caderno existente para um espaço de trabalho Azure Synapse a partir do **Object Explorer**. Os cadernos do Estúdio Azure Synapse podem reconhecer ficheiros IPYNB do Jupyter Notebook padrão.

![sinapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Desenvolver cadernos

Os cadernos consistem em células, que são blocos individuais de código ou texto que podem ser recorridos independentemente ou em grupo.

### <a name="add-a-cell"></a>Adicione uma célula

Existem várias formas de adicionar uma nova célula ao seu caderno.

1. Expanda o botão superior esquerdo **+ célula** e selecione Adicionar célula **de código** ou adicionar célula **de texto**.

    ![adicionar célula-com-botão de célula](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Pairar sobre o espaço entre duas células e selecionar **Adicionar código** ou **adicionar texto**.

    ![adicionar célula-entre-espaço](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Utilize [teclas de atalho no modo](#shortcut-keys-under-command-mode)de comando . Pressione **A** para inserir uma célula acima da célula atual. Pressione **B** para inserir uma célula abaixo da célula atual.

### <a name="set-a-primary-language"></a>Definir uma língua primária

Os cadernos do Estúdio Azure Synapse suportam quatro línguas Apache Spark:

* pySpark (Python)
* Faísca (Scala)
* Sparksql
* .NET para Apache Spark (C#)

Pode definir a língua primária para novas células adicionadas da lista de dropdown na barra de comando superior.

   ![padrão-sinapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Use várias línguas

Pode utilizar várias línguas num caderno especificando o comando mágico da linguagem correta no início de uma célula. A tabela seguinte lista os comandos mágicos para mudar as línguas celulares.

|Comando mágico |Idioma | Descrição |  
|---|------|-----|
|%%pyspark| Python | Execute uma consulta **python** contra o Contexto de Faíscas.  |
|%%faísca| Scala | Execute uma consulta **de Scala** contra o Contexto de Faíscas.  |  
|%%sql| Sparksql | Execute uma consulta **SparkSQL** contra o Contexto de Faíscas.  |
|%%csharp | .NET para Faísca C # | Execute uma **consulta .NET para Spark C#** contra o Contexto de Faísca. |

A imagem que se segue é um exemplo de como pode escrever uma consulta pySpark usando o comando mágico **%%pyspark** ou uma consulta SparkSQL com o comando mágico **%%sql** em um caderno **Spark(Scala).** Note que a língua primária para o caderno está definida para pySpark.

   ![sinapse-faísca-magias](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Utilize tabelas temporárias para dados de referência em todos os idiomas

Não é possível fazer referência a dados ou variáveis diretamente em diferentes idiomas num caderno do Estúdio Synapse. Em Spark, uma tabela temporária pode ser referenciada através de línguas. Aqui está um exemplo de como ler um `Scala` DataFrame `PySpark` e usar uma tabela de temperatura spark como uma `SparkSQL` suver.

1. Na Célula 1, leia um DataFrame do conector de piscina SQL utilizando o Scala e crie uma tabela temporária.

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

3. Na Célula 3, utilize os dados no PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IntelliSense estilo IDE

Os cadernos do Estúdio Azure Synapse estão integrados com o editor do Mónaco para levar o IDE ao estilo IntelliSense para o editor de células. O destaque da sintaxe, o fabricante de erros e as conclusões automáticas de código ajudam-no a escrever código e a identificar problemas mais rapidamente.

As funcionalidades IntelliSense estão em diferentes níveis de maturidade para diferentes idiomas. Use a tabela abaixo para ver o que é suportado.

|Idiomas| Destaque da sintaxe | Marcador de erro de sintaxe  | Conclusão do Código sintaxe | Conclusão de Código Variável| Conclusão do código de função do sistema| Conclusão do código de função do utilizador| Avanço Inteligente | Dobragem de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Faísca (Scala)|Sim|Sim|Sim|Sim|-|-|-|Sim|
|Sparksql|Sim|Sim|-|-|-|-|-|-|
|.NET para Faísca (C#)|Sim|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Célula de texto de formato com botões de barra de ferramentas

Pode utilizar os botões de formato na barra de ferramentas das células de texto para fazer ações comuns de marcação. Inclui texto arrojado, texto em itálico, inserção de código, inserção de lista não ordenada, inserção da lista ordenada e inserção de imagem a partir de URL.

  ![sinapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Desfazer operações celulares
Clique no botão **desfazer** ou prima **Ctrl+Z** para revogar a operação celular mais recente. Agora pode desfazer até as últimas 20 ações históricas de células. 

   ![sinapse-undo-células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Mover uma célula

Selecione as elipses (...) para aceder ao menu de ações adicionais de células na extrema-direita. Em seguida, selecione **Mover a célula para cima** ou mover a célula para **baixo** para mover a célula atual. 

Também pode utilizar teclas de [atalho no modo de comando](#shortcut-keys-under-command-mode). Prima **Ctrl+Alt+↑** para subir a célula atual. Prima **Ctrl+Alt+)** para mover a célula atual para baixo.

   ![mover-a-célula](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Apagar uma célula

Para eliminar uma célula, selecione as elipses (...) para aceder ao menu de ações adicionais de células na extrema-direita e, em seguida, **selecione Apagar a célula**. 

Também pode utilizar teclas de [atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para apagar a célula atual.
  
   ![delete-a-célula](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Colapso de uma entrada celular
Clique no botão de seta na parte inferior da célula atual para o colapsar. Para expandi-lo, clique no botão de seta enquanto a célula está em colapso.

   ![colapso-entrada célula](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Colapso de uma saída celular

Clique no botão de saída de **colapso** na parte superior esquerda da saída da célula atual para desfazê-lo. Para expandi-lo, clique na saída da **célula Mostrar** enquanto a saída da célula está em colapso.

   ![saída de células de colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Executar cadernos

Pode executar as células de código no seu caderno individualmente ou tudo de uma vez. O estado e o progresso de cada célula estão representados no caderno.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código numa cela.

1. Pairar sobre a célula que pretende executar e selecionar o botão **'Célula executar'** ou **premir Ctrl+Enter**.

   ![célula seleção 1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Para aceder ao menu de ações adicionais de células na extrema-direita, selecione as elipses (**...**). Em seguida, selecione **célula executar**.

   ![célula seleção 2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Utilize [teclas de atalho no modo](#shortcut-keys-under-command-mode)de comando . Pressione **Shift+Enter** para executar a célula atual e selecione a célula abaixo. Prima **Alt+Enter** para executar a célula atual e inserir uma nova célula abaixo.


### <a name="run-all-cells"></a>Executar todas as células
Clique no botão **Executar Todos** para executar todas as células do caderno atual em sequência.

   ![executar todas as células](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

Para aceder ao menu de ações adicionais de células na extrema-direita, selecione as elipses (**...**). Em seguida, selecione **as células run acima** para executar todas as células acima da corrente em sequência. Selecione **as células run abaixo** para executar todas as células abaixo da corrente em sequência.

   ![células-de-execução-acima ou abaixo](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Indicador de estado celular

Um estado de execução celular passo a passo é apresentado sob a célula para ajudá-lo a ver o seu progresso atual. Uma vez concluída a execução da célula, um resumo de execução com a duração total e o tempo final são mostrados e mantidos lá para referência futura.

![estatuto celular](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso de faíscas

O caderno do Estúdio Azure Synapse é puramente baseado em Spark. As células de código são executadas remotamente na piscina de Spark. Um indicador de progresso do trabalho da Spark é fornecido com uma barra de progresso em tempo real parece ajudá-lo a entender o estado de execução do emprego.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Config de sessão de faísca

Pode especificar a duração do tempo, o número e o tamanho dos executores para dar à atual sessão Spark na **sessão de Configuração**. Reiniciar a sessão Spark é para que as alterações de configuração entrem em vigor. Todas as variáveis de caderno saem.

![sessão-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Trazer dados para um caderno

Pode carregar dados do Armazenamento Azure Blob, azure Data Lake Store Gen 2 e sQL pool, como mostram as amostras de código abaixo.

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leia um CSV do Armazenamento de Blob Azure como um Spark DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Leia os dados da conta de armazenamento primário

Pode aceder diretamente aos dados na conta de armazenamento primário. Não há necessidade de fornecer as chaves secretas. No Data Explorer, clique à direita num ficheiro e selecione **Novo caderno** para ver um novo caderno com extrator de dados autogerado.

![dados-a-célula](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Visualizar dados num caderno

### <a name="display"></a>Exibição()

Uma vista de resultados tabular é fornecida com a opção de criar um gráfico de barras, gráfico de linha, gráfico de tortas, gráfico de dispersão e gráfico de área. Pode visualizar os seus dados sem ter de escrever código. Os gráficos podem ser personalizados nas **Opções de Gráficos.** 

A saída de comandos mágicos **%%sql** aparece na vista da tabela renderizada por padrão. Pode ligar para **o display( `<DataFrame name>` )** em Spark DataFrames ou Resilient Distributed Datasets (RDD) para produzir a vista de mesa renderizada.

   ![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

Pode renderizar bibliotecas HTML ou interativas, como **bokeh,** utilizando o **displayHTML()**.

A imagem que se segue é um exemplo de conspiração de glifos sobre um mapa usando **bokeh**.

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

## <a name="save-notebooks"></a>Salvar cadernos

Pode guardar um único caderno ou todos os cadernos no seu espaço de trabalho.

1. Para evitar alterações feitas num único bloco de notas, selecione o botão **Publicar** na barra de comando do portátil.

   ![caderno editorial](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para guardar todos os cadernos no seu espaço de trabalho, selecione **todos os** botões Publicar todos os botões na barra de comando do espaço de trabalho. 

   ![publicar-tudo](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do portátil, pode configurar se deve incluir a saída da célula ao poupar.

   ![propriedades de caderno](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos mágicos
Podes usar os teus comandos mágicos jupyter familiares nos cadernos do Estúdio Azure Synapse. Verifique a lista abaixo como os comandos mágicos disponíveis. Diga-nos os seus casos de uso no GitHub para que possamos continuar a construir mais comandos mágicos para satisfazer as suas necessidades.

Magias de linha disponíveis: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magias celulares disponíveis: [%%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%sql,](#use-multiple-languages) [%%pyspark,](#use-multiple-languages) [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Teclas de atalho

Semelhantes aos Cadernos Jupyter, os cadernos do Estúdio Azure Synapse têm uma interface de utilizador modal. O teclado faz coisas diferentes dependendo do modo em que a célula do portátil está. Os portáteis do Estúdio Synapse suportam os dois modos seguintes para uma determinada célula de código: modo de comando e modo de edição.

1. Uma célula está em modo de comando quando não há um cursor de texto que o levou a escrever. Quando uma célula está no modo Comando, pode editar o caderno como um todo, mas não escrever em células individuais. Introduza o modo de comando premindo `ESC` ou utilizando o rato para clicar fora da área de editor de uma célula.

   ![modo comando](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. O modo de edição é indicado por um cursor de texto que o leva a escrever na área do editor. Quando uma célula está em modo de edição, não se pode escrever na célula. Introduza o modo de edição premindo `Enter` ou utilizando o rato para clicar na área do editor de uma célula.
   
   ![modo-de-edição](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho sob modo de comando

Utilizando os seguintes atalhos de teclas, pode navegar e executar código mais facilmente nos cadernos Azure Synapse.

| Ação |Atalhos de caderno do Estúdio Synapse  |
|--|--|
|Executar a célula atual e selecionar abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Enter |
|Selecione célula acima| Cima |
|Selecione a célula abaixo| Baixo |
|Inserir célula acima| A |
|Inserir célula abaixo| B |
|Estender as células selecionadas acima| Shift+Up |
|Estender as células selecionadas abaixo| Shift+Down|
|Mover célula para cima| Ctrl+Alt+↑ |
|Mover célulapara baixo| Ctrl+Alt+≤ |
|Eliminar células selecionadas| D, D |
|Mude para o modo de edição| ENTER |

### <a name="shortcut-keys-under-edit-mode"></a>Teclas de atalho em modo de edição

Utilizando os seguintes atalhos de teclas, pode navegar e executar código mais facilmente nos cadernos Azure Synapse quando estiver no modo Editar.

| Ação |Atalhos de caderno do Estúdio Synapse  |
|--|--|
|Mover cursor para cima | Cima |
|Mova o cursor para baixo|Baixo|
|Anular|Ctrl + Z|
|Refazer|Ctrl + Y|
|Comentário/Não Comentar|CTRL + /|
|Apagar palavra antes|Ctrl + Backspace|
|Apagar palavra depois|CTRL + Eliminar|
|Ir para o início da cela|CTRL + Casa|
|Vá para o fim da cela |Ctrl + Fim|
|Vai uma palavra à esquerda|CTRL + Esquerda|
|Vai com uma palavra à direita.|Ctrl + Direito|
|Selecionar tudo|Ctrl + A|
|Travessão| Ctrl + ]|
|Dent|CTRL + [|
|Mude para o modo de comando| Esc |

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Crie uma piscina Apache Spark (pré-visualização) no Azure Synapse Analytics usando ferramentas web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark em Azure Synapse Analytics](apache-spark-overview.md)
- [Use .NET para Apache Spark com Azure Synapse Analytics](spark-dotnet.md)
- [.NET para documentação Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
