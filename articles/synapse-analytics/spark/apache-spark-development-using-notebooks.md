---
title: Cadernos do Estúdio De Sinaapse
description: Neste artigo, aprende-se a criar e desenvolver cadernos do Azure Synapse Studio para fazer a preparação e visualização de dados.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6859a2f8571c11e6ef93a5e5b1635cdbe39ad001
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737675"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Criar, desenvolver e manter os cadernos do Estúdio Synapse em Azure Synapse Analytics

Um notebook Do Synapse Studio é uma interface web para criar ficheiros que contenham código ao vivo, visualizações e texto narrativo. Os cadernos são um bom local para validar ideias e usar experiências rápidas para obter informações dos seus dados. Os cadernos também são amplamente utilizados na preparação de dados, visualização de dados, machine learning e outros cenários de Big Data.

Com um caderno Azure Synapse Studio, você pode:

* Começa com nenhum esforço de preparação.
* Mantenha os dados seguros com funcionalidades de segurança da empresa incorporadas.
* Analise os dados em formatos brutos (CSV, txt, JSON, etc.), formatos de ficheiros processados (parquet, Delta Lake, ORC, etc.), e ficheiros de dados tabulares SQL contra Spark e SQL.
* Seja produtivo com capacidades de autoria melhoradas e visualização de dados incorporados.

Este artigo descreve como usar cadernos no Azure Synapse Studio.

## <a name="preview-of-the-new-notebook-experience"></a>Pré-visualização da nova experiência do caderno
A equipa da Synapse trouxe a nova componente de cadernos para o Synapse Studio para fornecer uma experiência de caderno consistente para os clientes da Microsoft e maximizar a descoberta, produtividade, partilha e colaboração. A nova experiência do caderno está pronta para pré-visualização. Verifique o botão **Funcionalidades de Pré-visualização** na barra de ferramentas do portátil para o ligar. A tabela abaixo captura a comparação de características entre o caderno existente (o chamado "notebook clássico") com o novo pré-visualização.  

|Funcionalidade|Caderno Clássico|Caderno de pré-visualização|
|--|--|--|
|%run| Não suportado | &#9745;|
|%história| Não suportado |&#9745;
|%carga| Não suportado |&#9745;|
|%%html| Não suportado |&#9745;|
|Arrastar e largar para mover uma célula| Não suportado |&#9745;|
|Célula de texto de formato com botões de barra de ferramentas|&#9745;| Não disponível |
|Desfazer a operação celular| &#9745;| Não disponível |


## <a name="create-a-notebook"></a>Criar um bloco de notas

Há duas maneiras de criar um caderno. Pode criar um novo caderno ou importar um caderno existente para um espaço de trabalho Azure Synapse do Explorador de **Objetos.** Os portáteis do Estúdio Azure Synapse podem reconhecer ficheiros IPYNB de Caderno Jupyter padrão.

![criar caderno de importação](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Desenvolver blocos de notas

Os cadernos são de células, que são blocos individuais de código ou texto que podem ser funcionadas independentemente ou como um grupo.

### <a name="add-a-cell"></a>Adicione uma célula

Há várias formas de adicionar uma nova célula ao seu caderno.

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

1. Expanda o botão superior esquerdo **+ célula** e selecione Adicionar célula **de código** ou adicionar célula **de texto**.

    ![botão adicionar célula com célula](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Pairar sobre o espaço entre duas células e selecionar **Adicionar código** ou **adicionar texto**.

    ![adicionar células entre o espaço](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Utilize [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Prima **B** para inserir uma célula abaixo da célula atual.


# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

1. Expanda o botão superior esquerdo **+ célula** e selecione a célula **de código** ou a **célula Markdown**.
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Selecione o sinal de mais no início de uma célula e selecione **a célula Code** ou a célula **Markdown**.

    ![add-azure-notebook-cell-entre-espaço](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Utilize [teclas aznb atalhos no modo de comando](#shortcut-keys-under-command-mode). Pressione **A** para inserir uma célula acima da célula atual. Prima **B** para inserir uma célula abaixo da célula atual.

---

### <a name="set-a-primary-language"></a>Definir uma língua primária

Os cadernos do Azure Synapse Studio suportam quatro línguas Apache Spark:

* pySpark (Python)
* Apache Spark (Scala)
* Sparksql
* .NET para Apache Spark (C#)

Pode definir a linguagem primária para novas células adicionadas da lista de dropdown na barra de comando superior.

   ![linguagem padrão-sinapse](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Use várias línguas

Pode utilizar várias línguas num só caderno especificando o comando mágico da linguagem correto no início de uma célula. A tabela que se segue lista os comandos mágicos para mudar as línguas celulares.

|Comando mágico |Linguagem | Description |  
|---|------|-----|
|%%pyspark| Python | Execute uma consulta **python** contra o contexto de faísca.  |
|%%faísca| Scala | Execute uma consulta **scala** contra o Contexto de Faíscas.  |  
|%%sql| Sparksql | Execute uma consulta **SparkSQL** contra o Contexto de Faísca.  |
|%%csharp | .NET para Faísca C # | Execute uma consulta **.NET para Spark C#** contra o Contexto de Faísca. |

A imagem a seguir é um exemplo de como você pode escrever uma consulta PySpark usando o comando mágico **%%pyspark** ou uma consulta SparkSQL com o comando mágico **de %%sql** em um caderno **Spark (Scala).** Note que a língua primária para o caderno está definida para pySpark.

   ![Comandos mágicos de faíscas sinapse](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Use tabelas temporárias para fazer referência a dados em todos os idiomas

Não é possível fazer referência a dados ou variáveis diretamente em diferentes línguas num caderno do Synapse Studio. Em Spark, uma tabela temporária pode ser referenciada através de línguas. Aqui está um exemplo de como ler um `Scala` DataFrame `PySpark` dentro e usar uma tabela de temperatura spark como uma `SparkSQL` solução alternativa.

1. Na Célula 1, leia um DataFrame a partir de um conector de piscina SQL usando Scala e crie uma tabela temporária.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
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

As características do IntelliSense estão em diferentes níveis de maturidade para diferentes línguas. Use a seguinte tabela para ver o que é suportado.

|Idiomas| Destaque de sintaxe | Marcador de erro de sintaxe  | Conclusão do Código sintaxe | Conclusão do código variável| Conclusão do código de função do sistema| Conclusão do código de função do utilizador| Avanço Inteligente | Dobragem de código|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Yes|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Apache Spark (Scala)|Yes|Yes|Yes|Yes|-|-|-|Yes|
|Sparksql|Yes|Yes|-|-|-|-|-|-|
|.NET for Spark (C#)|Yes|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Fragmentos de Código

Os cadernos do Azure Synapse Studio fornecem fragmentos de código que facilitam a entrada de padrões de código usados comuns, tais como configurar a sua sessão Spark, ler dados como um Spark DataFrame, ou desenhar gráficos com matplotlib etc.

Snippets aparecem no [IntelliSense](#ide-style-intellisense) misturado com outras sugestões. O código corta o conteúdo alinha-se com a linguagem da célula de código. Pode ver os snippets disponíveis digitando **Snippet** ou quaisquer palavras-chave aparecem no título do snippet no editor de células de código. Por exemplo, ao escrever **a leitura** pode ver a lista de excertos para ler dados de várias fontes de dados.

![Corte de código de sinapse](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Célula de texto de formato com botões de barra de ferramentas

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Pode utilizar os botões de formato na barra de ferramentas das células de texto para fazer ações comuns de marcação. Inclui texto arrojado, texto em itálico, inserção de códigos de corte, inserção de lista não ordenada, inserção da lista ordenada e inserção da imagem a partir de URL.

  ![Barra de ferramentas de célula de texto sinaapse](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

A barra de ferramentas do botão de formato ainda não está disponível para a experiência do portátil de pré-visualização. 

---

### <a name="undo-cell-operations"></a>Desfazer operações celulares

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Selecione o botão **de desfazer** ou pressione **Ctrl+Z** para revogar a operação celular mais recente. Agora pode desfazer as últimas 20 ações históricas de células. 

   ![Sinaapse desfazer células](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

A operação de células de desfazer ainda não está disponível para a experiência do caderno de pré-visualização. 

---

### <a name="move-a-cell"></a>Mover uma célula

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Selecione as elipses (...) para aceder ao menu de outras ações celulares na extrema-direita. Em seguida, **selecione Mover a célula para cima** ou mover a célula para **baixo** para mover a célula atual. 

Também pode utilizar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **ctrl+Alt+↑** para subir a célula atual. Prima **Ctrl+Alt+↓** para mover a célula atual para baixo.

   ![mover-a-célula](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Clique no lado esquerdo de uma célula e arraste-a para a posição desejada. 
    ![Células de movimento de sinapse](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Apagar uma célula

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Para eliminar uma célula, selecione as elipses (...) para aceder ao menu de outras ações celulares na extrema direita e, em seguida, **selecione Delete cell**. 

Também pode utilizar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). Pressione **D,D** para apagar a célula atual.
  
   ![apagar-a-célula](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Para eliminar uma célula, selecione o botão de exclusão na mão direita da célula. 

Também pode utilizar [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). **Prima Shift+D** para eliminar a célula atual. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Colapso de uma entrada de células

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Selecione o botão de seta na parte inferior da célula atual para o colapsar. Para expandi-lo, selecione o botão de seta enquanto a célula está colapsada.

   ![entrada de células em colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Selecione as **elipses mais comandos** (...) na barra de ferramentas celular e **entrada** para colapsar a entrada da célula atual. Para expandi-la, selecione a **entrada escondida** enquanto a célula está em colapso.

   ![azure-notebook-colapso-célula-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Colapso de uma saída celular

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Selecione o botão **de saída** de colapso na parte superior esquerda da saída da célula atual para o colapsar. Para expandi-la, selecione a **saída da célula Show** enquanto a saída da célula é colapsada.

   ![saída de células em colapso](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Selecione as **elipses mais comandos** (...) na barra de ferramentas celular e **saída** para colapsar a saída da célula atual. Para expandi-lo, selecione o mesmo botão enquanto a saída da célula está escondida.

   ![azure-notebook-colapso-célula-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Executar blocos de notas

Pode executar as células de código no seu caderno individualmente ou todas de uma só vez. O estado e o progresso de cada célula estão representados no caderno.

### <a name="run-a-cell"></a>Executar uma célula

Há várias maneiras de executar o código numa cela.

1. Passe pela célula que pretende executar e selecione o botão **'Célula de Execução'** ou prima **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Utilize [teclas de atalho no modo de comando](#shortcut-keys-under-command-mode). **Prima Shift+Enter** para executar a célula atual e selecione a célula abaixo. Prima **Alt+Enter** para executar a célula atual e insira uma nova célula abaixo.

---

### <a name="run-all-cells"></a>Executar todas as células
Selecione o botão **Executar Todas para** executar todas as células no caderno atual em sequência.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Executar todas as células acima ou abaixo

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Para aceder ao menu de outras ações celulares na extrema-direita, selecione as elipses **(...**). Em seguida, selecione **Executar as células acima** para executar todas as células acima da corrente em sequência. Selecione **Executar as células abaixo** para executar todas as células abaixo da corrente em sequência.

   ![células de execução acima ou abaixo](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Expanda a lista de dropdown a partir de **executar todos os** botões e, em seguida, selecione executar as **células acima** para executar todas as células acima da corrente em sequência. Selecione **Executar as células abaixo** para executar todas as células abaixo da corrente em sequência.

   ![azure-notebook-run-cells-acima-ou-abaixo](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Cancelar todas as células de execução

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)
Selecione o botão **Cancelar Todos** para cancelar as células de funcionamento ou as células que aguardam na fila. 
   ![cancelar todas as células](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Selecione o botão **Cancelar Todos** para cancelar as células de funcionamento ou as células que aguardam na fila. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Referência de caderno

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Não suportado.

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Pode usar ```%run <notebook path>``` o comando mágico para fazer referência a outro caderno no contexto do caderno atual. Todas as variáveis definidas no caderno de referência estão disponíveis no caderno atual. ```%run``` o comando mágico suporta chamadas aninhadas, mas não suporta chamadas recursivas. Receberá uma exceção se a profundidade da declaração for maior do que cinco. ```%run``` comando atualmente apenas suporta para passar um caminho de caderno como parâmetro. 

Exemplo: ``` %run /path/notebookA ```.

> [!NOTE]
> A referência ao portátil não é suportada no gasoduto Synapse.
>
>

---


### <a name="cell-status-indicator"></a>Indicador de estado celular

Um estado de execução de células passo a passo é exibido sob a célula para ajudá-lo a ver o seu progresso atual. Uma vez concluída a execução da célula, é apresentado um resumo de execução com a duração total e o tempo final e mantidos lá para referência futura.

![estado celular](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Indicador de progresso de faísca

O caderno do Estúdio Azure Synapse é puramente baseado em Faíscas. As células de código são executadas na piscina Apache Spark sem servidor remotamente. Um indicador de progresso de emprego spark é fornecido com uma barra de progresso em tempo real parece ajudá-lo a entender o estado de execução do trabalho.
O número de tarefas por cada trabalho ou etapa ajuda-o a identificar o nível paralelo do seu trabalho de faísca. Também pode perfurar mais profundamente a UI de uma tarefa específica (ou fase) através da seleção do link no nome de trabalho (ou estágio).


![indicador de faísca-progresso](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Config da sessão de faísca

Pode especificar a duração do tempo limite, o número e o tamanho dos executores para dar à atual sessão de Faísca na **sessão de Configuração**. Reiniciar a sessão Spark destina-se a alterações de configuração a produzir efeito. Todas as variáveis de cadernos em cache estão limpas.

[![gestão de sessão](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Sessão de faísca config comando mágico
Também pode especificar as definições de sessão de faíscas através de um comando mágico **%%configure**. A sessão de faíscas tem de ser reiniciada para fazer o efeito de definições. Recomendamos que faça a **configuração %%** no início do seu caderno. Aqui está uma amostra, consulte para a https://github.com/cloudera/livy#request-body lista completa de parâmetros válidos 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```
> [!NOTE]
> O comando mágico config da sessão de faíscas não é suportado no oleoduto synapse.
>
>

## <a name="bring-data-to-a-notebook"></a>Levar dados a um caderno

Pode carregar dados de Azure Blob Storage, Azure Data Lake Store Gen 2 e sql pool como mostrado nas amostras de código abaixo.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Leia um CSV da Azure Data Lake Store Gen2 como um Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Leia um CSV do Azure Blob Storage como um Spark DataFrame

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Ler dados da conta de armazenamento primário

Pode aceder diretamente aos dados na conta de armazenamento primário. Não há necessidade de fornecer as chaves secretas. No Data Explorer, clique com o botão direito num ficheiro e selecione **Novo caderno** para ver um novo caderno com o extrator de dados autogerido.

![dados para célula](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Guardar blocos de notas

Pode guardar um único caderno ou todos os cadernos no seu espaço de trabalho.

1. Para guardar as alterações efetuadas num único bloco de notas, selecione o botão **Publicar** na barra de comando do portátil.

   ![publicar caderno](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Para guardar todos os cadernos no seu espaço de trabalho, selecione o botão **Publicar todos na** barra de comando do espaço de trabalho. 

   ![publicar tudo](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Nas propriedades do portátil, pode configurar se deve incluir a saída da célula ao poupar.

   ![portátil-propriedades](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Comandos magic
Você pode usar comandos mágicos jupyter familiares em cadernos do Estúdio Azure Synapse. Reveja a seguinte lista como os comandos mágicos disponíveis atuais. Diga-nos [os seus casos de uso no GitHub](https://github.com/MicrosoftDocs/azure-docs/issues/new) para que possamos continuar a construir mais comandos mágicos para satisfazer as suas necessidades.

> [!NOTE]
> Apenas os comandos mágicos são suportados no gasoduto Synapse : %%pyspark, %%spark, %%csharp, %%sql. 
>
>

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Magias de linha disponíveis: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time) [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Magias celulares disponíveis: [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%captura,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages)[%%configure](#spark-session-config-magic-command) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)



# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Magias de linha disponíveis: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#notebook-reference), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Magias celulares disponíveis: [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%captura,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql,](#use-multiple-languages) [%%pyspark,](#use-multiple-languages) [%%spark,](#use-multiple-languages) [%%csharp,](#use-multiple-languages) [%%html,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html) [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrar um caderno

### <a name="add-a-notebook-to-a-pipeline"></a>Adicione um caderno a um oleoduto

Selecione o botão **de gasoduto Add to pipeline** no canto superior direito para adicionar um caderno a um gasoduto existente ou criar um novo gasoduto.

![Adicione caderno ao pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Designar uma célula de parâmetros

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

Para parametrizar o seu caderno, selecione as elipses (...) para aceder ao menu de outras ações celulares na extrema-direita. Em seguida, selecione **a célula de parâmetros Toggle** para designar a célula como célula de parâmetros.

![parâmetro de alternância](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

Para parametrizar o seu caderno, selecione as elipses (...) para aceder aos **comandos na** barra de ferramentas celular. Em seguida, selecione **a célula de parâmetros Toggle** para designar a célula como célula de parâmetros.

![azure-notebook-toggle-parâmetro](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

A Azure Data Factory procura a célula de parâmetros e trata esta célula como padrão para os parâmetros passados no tempo de execução. O motor de execução adicionará uma nova célula sob a célula de parâmetros com parâmetros de entrada, a fim de substituir os valores predefinidos. Quando uma célula de parâmetros não é designada, a célula injetada será inserida na parte superior do caderno.


### <a name="assign-parameters-values-from-a-pipeline"></a>Atribuir valores de parâmetros a partir de um oleoduto

Uma vez criado um caderno com parâmetros, pode executá-lo a partir de um oleoduto com a atividade do Caderno Azure Synapse. Depois de adicionar a atividade à sua tela de pipeline, poderá definir os valores dos parâmetros na secção **parâmetros Base** no **separador Definições.** 

![Atribuir um parâmetro](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Ao atribuir valores de parâmetros, pode utilizar a linguagem de expressão do [gasoduto](../../data-factory/control-flow-expression-language-functions.md) ou [as variáveis do sistema](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Teclas de atalho

Semelhante aos Cadernos Jupyter, os portáteis do Azure Synapse Studio têm uma interface de utilizador modal. O teclado faz coisas diferentes dependendo do modo em que a célula de portátil está. Os cadernos Synapse Studio suportam os seguintes dois modos para uma determinada célula de código: modo de comando e modo de edição.

1. Uma célula está no modo de comando quando não há cursor de texto que lhe ordene a escrever. Quando uma célula está no modo Comando, pode editar o caderno como um todo, mas não digitar em células individuais. Introduza o modo de comando premindo `ESC` ou utilizando o rato para selecionar fora da área de editor de uma célula.

   ![modo de comando](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. O modo de edição é indicado por um cursor de texto que o leva a escrever na área do editor. Quando uma célula está em modo de edição, pode digitar na célula. Introduza o modo de edição premindo `Enter` ou utilizando o rato para selecionar na área de editor de uma célula.
   
   ![modo-de-edição](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Teclas de atalho no modo de comando

# <a name="classical-notebook"></a>[Caderno Clássico](#tab/classical)

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

# <a name="preview-notebook"></a>[Caderno de pré-visualização](#tab/preview)

| Ação |Atalhos de cadernos do Estúdio De Sinaapse  |
|--|--|
|Executar a célula atual e selecione abaixo | Shift+Enter |
|Executar a célula atual e inserir abaixo | Alt+Entrar |
|Executar a célula atual| Ctrl+Enter |
|Selecione a célula acima| Cima |
|Selecione a célula abaixo| Baixo |
|Selecione célula anterior| K |
|Selecione a próxima célula| J |
|Insira a célula acima| A |
|Insira a célula abaixo| B |
|Eliminar células selecionadas| Turno+D |
|Mude para o modo de edição| ENTER |

---

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
|Travessão| Ctrl +]|
|Dedent|Ctrl +|
|Mudar para o modo de comando| Esc |

---

## <a name="next-steps"></a>Passos seguintes
- [Confira os cadernos da amostra da Sinapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Criar uma piscina Apache Spark em Azure Synapse Analytics usando ferramentas web](../quickstart-apache-spark-notebook.md)
- [O que é Apache Spark em Azure Synapse Analytics](apache-spark-overview.md)
- [Utilizar o .NET para Apache Spark com o Azure Synapse Analytics](spark-dotnet.md)
- [.NET para documentação Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)