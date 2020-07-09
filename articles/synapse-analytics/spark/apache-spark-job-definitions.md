---
title: 'Tutorial: Criar definição de emprego apache spark no Estúdio Synapse'
description: Tutorial - Use o Azure Synapse Analytics para criar definições de emprego spark, e submetê-las a uma piscina Apache Spark for Azure Synapse Analytics.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 3311a9a92cc5e63a6fa20e4dd0d2af00fdacc95c
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194489"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Criar definição de emprego apache spark no Estúdio Synapse

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de emprego apache spark, e depois submetê-los a uma piscina Apache Spark.

Este tutorial abrange as seguintes tarefas:

* Crie uma definição de trabalho apache spark para PySpark (Python)
* Criar uma definição de emprego apache spark para Spark (Scala)
* Crie uma definição de trabalho apache spark para .NET Spark(C#)
* Submeta uma definição de trabalho apache spark como um trabalho de lote
* Adicione uma definição de trabalho apache spark em pipeline

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* Um espaço de trabalho Azure Synapse Analytics. Para obter instruções, consulte [Criar um espaço de trabalho Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Uma piscina de faíscas Apache
* Uma conta de armazenamento da ADLS Gen2. Tem de ser o Proprietário de Dados blob de armazenamento do sistema de ficheiros ADLS Gen2 com o que pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Crie uma definição de trabalho apache spark para PySpark (Python)

Nesta secção, você cria uma definição de trabalho Apache Spark para PySpark (Python).

1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Pode recorrer a [ficheiros Sample para criar definições de emprego apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) para baixar **wordcount.jar** e **shakespear.txt**. E, em seguida, faça o upload destes ficheiros para o Azure Storage: Clique em **Dados,** selecione contas de Armazenamento e carreve **ficheiros relacionados**para o seu sistema de ficheiros ADLS Gen2. Ignore este passo se os seus ficheiros já estiverem no armazenamento do Azure. 

     ![carregar arquivo python](./media/apache-spark-job-definitions/upload-python-file.png)

3. Clique **em Desenvolver** o hub, selecione **definições** de trabalho spark a partir do painel esquerdo, clique em '...' Nó de ação ao lado da **definição de trabalho spark,** em seguida, selecione **a definição de trabalho de New Spark** no menu de contexto.

     ![criar nova definição para pitão](./media/apache-spark-job-definitions/create-new-definition.png)

4. Selecione **PySpark (Python)** da lista de drop down da linguagem na janela principal de definição de emprego Apache Spark.

5. Preencha informações para a definição de emprego da Apache Spark. Pode copiar a informação da amostra.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. Amostra:`job definition sample`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro PY do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. Amostra:`abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/fileexists.py`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Amostra:`shakespeare.txt`|
     |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. Amostra:`abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/shakespeare.txt`|
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. **Selecione Publicar** para salvar a definição de emprego apache spark.

     ![publicar py definição](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Criar uma definição de emprego apache spark para Apache Spark (Scala)

Nesta secção, você cria uma definição de trabalho Apache Spark para Apache Spark (Scala).

 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode recorrer a [ficheiros Sample para criar definições de emprego apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) para baixar **wordcount.jar** e **shakespear.txt**. E, em seguida, faça o upload destes ficheiros para o Azure Storage: Clique em **Dados,** selecione contas de Armazenamento e carreve **ficheiros relacionados**para o seu sistema de ficheiros ADLS Gen2. Ignore este passo se os seus ficheiros já estiverem no armazenamento do Azure. 
 
     ![preparar estrutura scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Clique **em Desenvolver** o hub, selecione **definições** de trabalho spark a partir do painel esquerdo, clique em '...' Nó de ação ao lado da **definição de trabalho spark,** em seguida, selecione **a definição de trabalho de New Spark** no menu de contexto.
     ![criar nova definição para scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selecione **Spark (Scala)** da lista de drop down da linguagem na janela principal de definição de emprego Apache Spark.

 5. Preencha informações para a definição de emprego da Apache Spark. Pode copiar a informação da amostra.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. Amostra:`job definition sample`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro JAR do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. Amostra:`abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/wordcount.jar`|
     |Nome da classe principal| O identificador totalmente qualificado ou a classe principal que está no ficheiro de definição principal. Amostra:`WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Amostra:`abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/result`|
     |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para escala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. **Selecione Publicar** para salvar a definição de emprego apache spark.

     ![publicar a definição de scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkc"></a>Crie uma definição de trabalho apache spark para .NET Spark(C#)

Nesta secção, você cria uma definição de trabalho Apache Spark para .NET Spark(C#).
 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode recorrer a [ficheiros Sample para criar definições de emprego apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) para descarregar **wordcount.zip** e **shakespear.txt**. E, em seguida, faça o upload destes ficheiros para o Azure Storage: Clique em **Dados,** selecione contas de Armazenamento e carreve **ficheiros relacionados**para o seu sistema de ficheiros ADLS Gen2. Ignore este passo se os seus ficheiros já estiverem no armazenamento do Azure. 

     ![preparar estrutura de dotnet](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Clique **em Desenvolver** o hub, selecione **definições** de trabalho spark a partir do painel esquerdo, clique em '...' Nó de ação ao lado da **definição de trabalho spark,** em seguida, selecione **a definição de trabalho de New Spark** no menu de contexto.

     ![criar uma nova definição para dotnet](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selecione **.NET Spark(C#/F#)** da lista de drop down da linguagem na janela principal da Definição de Emprego de Faísca Apache.

 5. Preencha informações para a Definição de Emprego de Apache Spark. Pode copiar a informação da amostra.
     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. Amostra:`job definition sample`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro ZIP que contenha a sua aplicação .NET para a aplicação Apache Spark (ou seja, o ficheiro executável principal, DLLs que contenham funções definidas pelo utilizador e outros ficheiros necessários) a partir do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. Amostra:`abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/wordcount.zip`|
     |Arquivo executável principal| O ficheiro executável principal no ficheiro ZIP de definição principal. Amostra:`WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. Amostra:`abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/result`|
     |Ficheiros de referência| Ficheiros adicionais necessários para a execução da aplicação .NET para Apache Spark que não esteja incluída na definição principal de ficheiro ZIP (isto é, frascos dependentes, DLLs de função adicional definidos pelo utilizador e outros ficheiros config). Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para dotnet](./media/apache-spark-job-definitions/create-net-definition.png)

 6. **Selecione Publicar** para salvar a definição de emprego apache spark.

      ![publicar definição de dotnet](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Submeta uma definição de trabalho apache spark como um trabalho de lote

Depois de criar uma definição de emprego Apache Spark, você pode submetê-lo a uma piscina Apache Spark. Certifique-se de que é o Proprietário de Dados blob de armazenamento do sistema de ficheiros ADLS Gen2 com o qual pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Cenário 1: Submeter a definição de emprego da Apache Spark
 1. Abra uma janela de definição de trabalho de faísca Apache clicando nela.

      ![Abrir definição de trabalho de faísca para submeter ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Clique **em submeter** o seu projeto ao Apache Spark Pool selecionado. Pode clicar no **separador URL de monitorização do Spark** para ver o LogQuery da aplicação Apache Spark.

    ![Clique em enviar botão para submeter definição de trabalho de faísca](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Cenário 2: Veja o trabalho de Apache Spark a correr o progresso

 1. Clique **em Monitor**e, em seguida, selecione a opção de **aplicações Spark.** Pode encontrar o pedido de Apache Spark submetido.

     ![Ver aplicação Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Em seguida, clique na aplicação Apache Spark, os visões **LogQuery.** Pode ver o progresso da execução de emprego a partir de **LogQuery.**
     
     ![Ver aplicação de faísca LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: Verifique o ficheiro de saída

 1. Clique em **Dados**e, em seguida, selecione **contas de Armazenamento.** Depois de uma corrida bem sucedida, você pode ir ao armazenamento ADLS Gen2 e verificar saídas são geradas.

     ![Ver ficheiro de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Adicione uma definição de trabalho apache spark em pipeline

Nesta secção, adicione uma definição de trabalho apache spark em pipeline.

 1. Abra uma definição de emprego apache spark existente.

 2. Clique no ícone no canto superior direito da definição de trabalho apache spark, escolha **O Pipeline Existente**ou Novo **pipeline**. Pode consultar a página pipeline para mais informações.

     ![adicionar ao oleoduto](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![adicionar ao oleoduto](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Passos seguintes

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de emprego apache spark, e depois submetê-los a uma piscina Apache Spark. Em seguida, pode utilizar o Azure Synapse Studio para criar conjuntos de dados Power BI e gerir dados do Power BI.

