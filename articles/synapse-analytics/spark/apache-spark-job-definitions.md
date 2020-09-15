---
title: 'Tutorial: Criar definição de emprego apache spark no Estúdio Synapse'
description: Tutorial - Use o Azure Synapse Analytics para criar definições de emprego spark, e submetê-las a uma piscina Apache Spark for Azure Synapse Analytics.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 787e9efa8ef11a83b63719cad51f9b26e055620d
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562170"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Criar definição de emprego apache spark no Estúdio Synapse

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de emprego apache spark, e depois submetê-los a uma piscina Apache Spark.

Este tutorial abrange as seguintes tarefas:
> [!div class="checklist"]
>
> - Crie uma definição de trabalho apache spark para PySpark (Python)
> - Criar uma definição de emprego apache spark para Spark (Scala)
> - Crie uma definição de trabalho apache spark para .NET Spark(C#/F#)
> - Submeta uma definição de trabalho apache spark como um trabalho de lote
> - Adicione uma definição de trabalho apache spark em pipeline

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que cumpre os seguintes requisitos:

* Um espaço de trabalho Azure Synapse Analytics. Para obter instruções, consulte [Criar um espaço de trabalho Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Uma piscina Apache Spark.
* Uma conta de armazenamento da ADLS Gen2. Tem de ser o Proprietário de **Dados blob** de armazenamento do sistema de ficheiros ADLS Gen2 com o que pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.
* Se não quiser utilizar o armazenamento padrão do espaço de trabalho, ligue a conta de armazenamento ADLS Gen2 necessária no Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Crie uma definição de trabalho apache spark para PySpark (Python)

Nesta secção, você cria uma definição de trabalho Apache Spark para PySpark (Python).

1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Pode ir aos [ficheiros Sample para criar definições de emprego do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) para descarregar **ficheiros de amostras para python.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.py** e **shakespeare.txt.** 

     ![ficheiros de amostra](./media/apache-spark-job-definitions/sample-files.png)

3. Clique em **Dados**   ->  **Linked**   ->  **Ligados Azure Data Lake Storage Gen2**, e faça o upload **wordcount.py**   e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2. 

     ![carregar arquivo python](./media/apache-spark-job-definitions/upload-python-file.png)

4. Clique em **Desenvolver** o hub, clique no ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. 

     ![criar nova definição para pitão](./media/apache-spark-job-definitions/create-new-definition.png)

5. Selecione **PySpark (Python)** da lista de drop down da linguagem na janela principal de definição de emprego Apache Spark.

     ![selecionar python](./media/apache-spark-job-definitions/select-python.png)

6. Preencha informações para a definição de emprego da Apache Spark. 

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. <br> Amostra: `job definition sample`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro PY do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. <br> Amostra: `abfss://…/path/to/wordcount.py`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Amostra: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Nota: Dois argumentos para a definição de trabalho de amostra são separados por um espaço.*|
     |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. |
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para Python](./media/apache-spark-job-definitions/create-py-definition.png)

7. Clique **em Publicar** para salvar a definição de emprego apache spark.

     ![publicar py definição](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Criar uma definição de emprego apache spark para Apache Spark (Scala)

Nesta secção, você cria uma definição de trabalho Apache Spark para Apache Spark (Scala).

 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode ir aos [ficheiros Sample para criar definições de emprego apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) para descarregar **ficheiros de amostras para scala.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.jar** e **shakespeare.txt.** 
 
     ![amostra ficheiros scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Clique em **Dados**   ->  **Linked**   ->  **Ligados Azure Data Lake Storage Gen2**, e carreque **wordcount.jar**   e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2.
 
     ![preparar estrutura scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Clique em **Desenvolver** o hub, clique no ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. (A imagem da amostra é a mesma do passo 4 de Criar uma definição de **trabalho apache spark (Python) para PySpark**.)

 5. Selecione **Spark (Scala)** da lista de drop down da linguagem na janela principal de definição de emprego Apache Spark.

     ![selecionar scala](./media/apache-spark-job-definitions/select-scala.png)

 6. Preencha informações para a definição de emprego da Apache Spark. Pode copiar a informação da amostra.

     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. <br> Amostra: `scala`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro JAR do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. <br> Amostra: `abfss://…/path/to/wordcount.jar`|
     |Nome da classe principal| O identificador totalmente qualificado ou a classe principal que está no ficheiro de definição principal. <br> Amostra: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Amostra: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Nota: Dois argumentos para a definição de trabalho de amostra são separados por um espaço.* |
     |Ficheiros de referência| Ficheiros adicionais utilizados para referência no ficheiro de definição principal. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para escala](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Clique **em Publicar** para salvar a definição de emprego apache spark.

      ![publicar a definição de scala](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Crie uma definição de trabalho apache spark para .NET Spark(C#/F#)

Nesta secção, você cria uma definição de trabalho Apache Spark para .NET Spark (C#/F#).
 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode ir aos [ficheiros Sample para criar definições de emprego do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) para descarregar **ficheiros de amostras para dotnet.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.zip** e **shakespeare.txt.** 

     ![amostra dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Clique em **Dados**   ->  **Linked**   ->  **Ligados Azure Data Lake Storage Gen2**, e faça o upload **wordcount.zip**   e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2.
 
     ![preparar estrutura de dotnet](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Clique em **Desenvolver** o hub, clique no ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. (A imagem da amostra é a mesma do passo 4 de Criar uma definição de **trabalho apache spark (Python) para PySpark**.)

 5. Selecione **.NET Spark(C#/F#)** da lista de drop down da linguagem na janela principal da Definição de Emprego de Faísca Apache.

     ![selecionar dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Preencha informações para a Definição de Emprego de Apache Spark. Pode copiar a informação da amostra.
    
     |  Propriedade   | Descrição   |  
     | ----- | ----- |  
     |Nome da definição de emprego| Insira um nome para a definição de emprego apache spark. Este nome pode ser atualizado a qualquer momento até ser publicado. <br> Amostra: `dotnet`|
     |Ficheiro de definição principal| O ficheiro principal usado para o trabalho. Selecione um ficheiro ZIP que contenha a sua aplicação .NET para Apache Spark (isto é, o ficheiro executável principal, DLLs que contenham funções definidas pelo utilizador e outros ficheiros necessários) a partir do seu armazenamento. Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento. <br> Amostra: `abfss://…/path/to/wordcount.zip`|
     |Arquivo executável principal| O ficheiro executável principal no ficheiro ZIP de definição principal. <br> Amostra: `WordCount`|
     |Argumentos de linha de comando| Argumentos opcionais para o trabalho. <br> Amostra: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Nota: Dois argumentos para a definição de trabalho de amostra são separados por um espaço.* |
     |Ficheiros de referência| Ficheiros adicionais necessários para a execução da aplicação .NET para Apache Spark que não está incluída na definição principal do ficheiro ZIP (isto é, frascos dependentes, DLLs de função adicional definidos pelo utilizador e outros ficheiros config). Pode selecionar **o ficheiro Upload** para fazer o upload do ficheiro para uma conta de armazenamento.|
     |Piscina de faíscas| O trabalho será submetido à piscina apache spark selecionada.|
     |Versão spark| Versão do Apache Spark que a piscina Apache Spark está a funcionar.|
     |Executores| Número de executores a serem administrados na piscina apache spark especificada para o trabalho.|  
     |Tamanho do executor| Número de núcleos e memória a utilizar para executores indicados na piscina apache spark especificada para o trabalho.|
     |Tamanho do condutor| Número de núcleos e memória a utilizar para o condutor dado na piscina apache spark especificada para o trabalho.|

     ![Definir o valor da definição de trabalho spark para dotnet](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Clique **em Publicar** para salvar a definição de emprego apache spark.

      ![publicar definição de dotnet](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Submeta uma definição de trabalho apache spark como um trabalho de lote

Depois de criar uma definição de emprego Apache Spark, você pode submetê-lo a uma piscina Apache Spark. Certifique-se de que é o Proprietário de **Dados blob** de armazenamento do sistema de ficheiros ADLS Gen2 com o qual pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Cenário 1: Submeter a definição de emprego da Apache Spark
 1. Abra uma janela de definição de trabalho de faísca Apache clicando nela.

      ![Abrir definição de trabalho de faísca para submeter ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Clique **em Enviar** o botão submeter o seu projeto ao Conjunto de Faíscas Apache Selecionados. Pode clicar no **separador URL de monitorização do Spark** para ver o LogQuery da aplicação Apache Spark.

    ![Clique em enviar botão para submeter definição de trabalho de faísca](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Cenário 2: Veja o trabalho de Apache Spark a correr o progresso

 1. Clique **em Monitor**e, em seguida, selecione a opção de **aplicações Apache Spark.** Pode encontrar o pedido de Apache Spark submetido.

     ![Ver aplicação Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Em seguida, clique numa aplicação Apache Spark, a janela de trabalho **SparkJobDefinition** exibe. Pode ver o progresso da execução do trabalho a partir daqui.
     
     ![Ver aplicação de faísca LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: Verifique o ficheiro de saída

 1. Clique em **Dados**  ->  **Ligados**  ->  **Azure Data Lake Storage Gen2** (hozhaobdbj), abra a pasta **de resultados** criada anteriormente, pode ir à pasta de resultados e verificar se a saída é gerada.

     ![Ver ficheiro de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Adicione uma definição de trabalho apache spark em pipeline

Nesta secção, adicione uma definição de trabalho apache spark em pipeline.

 1. Abra uma definição de emprego apache spark existente.

 2. Clique no ícone no canto superior direito da definição de trabalho apache spark, escolha **O Pipeline Existente**ou Novo **pipeline**. Pode consultar a página pipeline para mais informações.

     ![adicionar ao pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![adicionar ao pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Passos seguintes

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de emprego apache spark, e depois submetê-los a uma piscina Apache Spark. Em seguida, pode utilizar o Azure Synapse Studio para criar conjuntos de dados Power BI e gerir dados do Power BI.

