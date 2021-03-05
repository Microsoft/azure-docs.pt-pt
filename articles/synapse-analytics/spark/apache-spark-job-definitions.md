---
title: 'Tutorial: Criar definição de emprego apache spark no Estúdio Synapse'
description: Tutorial - Use o Azure Synapse Analytics para criar definições de emprego spark, e submetê-las a uma piscina Apache Spark for Azure Synapse Analytics.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: d125bca5ed67476897eec7cd32a586776d8b1ea8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176625"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Criar definição de emprego apache spark no Estúdio Synapse

Este tutorial demonstra como usar o Azure Synapse Studio para criar definições de emprego Apache Spark e, em seguida, submetê-los a uma piscina Apache Spark sem servidor.

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

* Um espaço de trabalho Azure Synapse Analytics. Para obter instruções, consulte [Criar um espaço de trabalho Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md).
* Uma piscina Apache Spark sem servidor.
* Uma conta de armazenamento da ADLS Gen2. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.
* Se não quiser utilizar o armazenamento padrão do espaço de trabalho, ligue a conta de armazenamento ADLS Gen2 necessária no Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Crie uma definição de trabalho apache spark para PySpark (Python)

Nesta secção, você cria uma definição de trabalho Apache Spark para PySpark (Python).

1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Pode ir aos [ficheiros Sample para criar definições de emprego do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) para descarregar **ficheiros de amostras para python.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.py** e **shakespeare.txt.** 

     ![ficheiros de amostra](./media/apache-spark-job-definitions/sample-files.png)

3. Selecione **Dados**  ->  **Ligados**  ->  **Azure Data Lake Storage Gen2**, e faça o upload **wordcount.py** e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2. 

     ![carregar arquivo python](./media/apache-spark-job-definitions/upload-python-file.png)

4. Selecione **Develop** hub, selecione o ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. 

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

7. **Selecione Publicar** para salvar a definição de emprego apache spark.

     ![publicar py definição](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Criar uma definição de emprego apache spark para Apache Spark (Scala)

Nesta secção, você cria uma definição de trabalho Apache Spark para Apache Spark (Scala).

 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode ir aos [ficheiros Sample para criar definições de emprego do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) para descarregar **ficheiros de amostras para scala.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.jar** e **shakespeare.txt.** 
 
     ![amostra ficheiros scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Selecione **Dados**  ->  **Ligados**  ->  **Azure Data Lake Storage Gen2**, e carrema **o contador de palavras.jar** e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2.
 
     ![preparar estrutura scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Selecione **Develop** hub, selecione o ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. (A imagem da amostra é a mesma do passo 4 de Criar uma definição de **trabalho apache spark (Python) para PySpark**.)

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

 7. **Selecione Publicar** para salvar a definição de emprego apache spark.

      ![publicar a definição de scala](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Crie uma definição de trabalho apache spark para .NET Spark(C#/F#)

Nesta secção, você cria uma definição de trabalho Apache Spark para .NET Spark (C#/F#).
 1. Open [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Pode ir aos [ficheiros Sample para criar definições de emprego do Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) para descarregar **ficheiros de amostras para dotnet.zip**, em seguida, desapertar o pacote comprimido e extrair os ficheiros **wordcount.zip** e **shakespeare.txt.** 

     ![amostra dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Selecione **Dados**  ->  **Ligados**  ->  **Azure Data Lake Storage Gen2**, e faça o upload **wordcount.zip** e **shakespeare.txt** no seu sistema de ficheiros ADLS Gen2.
 
     ![preparar estrutura de dotnet](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Selecione **Develop** hub, selecione o ícone '+' e selecione **a definição de trabalho spark** para criar uma nova definição de trabalho spark. (A imagem da amostra é a mesma do passo 4 de Criar uma definição de **trabalho apache spark (Python) para PySpark**.)

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

 7. **Selecione Publicar** para salvar a definição de emprego apache spark.

      ![publicar definição de dotnet](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Submeta uma definição de trabalho apache spark como um trabalho de lote

Depois de criar uma definição de emprego Apache Spark, você pode submetê-lo a uma piscina Apache Spark. Certifique-se de que é o Colaborador de **Dados blob** de armazenamento do sistema de ficheiros ADLS Gen2 com o qual pretende trabalhar. Se não estiver, tem de adicionar a permissão manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Cenário 1: Submeter a definição de emprego da Apache Spark
 1. Abra uma janela de definição de trabalho de faísca Apache selecionando-a.

      ![Abrir definição de trabalho de faísca para submeter ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. **Selecione Enviar** o botão para submeter o seu projeto ao Conjunto de Faíscas Apache Selecionados. Pode selecionar **o separador URL de monitorização do Spark** para ver o LogQuery da aplicação Apache Spark.

    ![Selecione botão de submissão para submeter definição de trabalho de faísca](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Cenário 2: Veja o trabalho de Apache Spark a correr o progresso

 1. Selecione **Monitor** e, em seguida, selecione a opção **de aplicações Apache Spark.** Pode encontrar o pedido de Apache Spark submetido.

     ![Ver aplicação Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Em seguida, selecione uma aplicação Apache Spark, a janela de trabalho **SparkJobDefinition** exibe. Pode ver o progresso da execução do trabalho a partir daqui.
     
     ![Ver aplicação de faísca LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Cenário 3: Verifique o ficheiro de saída

 1. Selecione **Dados**  ->    ->  **Ligados Azure Data Lake Storage Gen2** (hozhaobdbj), abra a pasta **de resultados** criada anteriormente, pode ir à pasta de resultados e verificar se a saída é gerada.

     ![Ver ficheiro de saída](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Adicione uma definição de trabalho apache spark em pipeline

Nesta secção, adicione uma definição de trabalho apache spark em pipeline.

 1. Abra uma definição de emprego apache spark existente.

 2. Selecione o ícone no canto superior direito da definição de trabalho Apache Spark, escolha **O Pipeline Existente** ou Novo **pipeline**. Pode consultar a página pipeline para mais informações.

     ![adicionar ao pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![adicionar ao pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode utilizar o Azure Synapse Studio para criar conjuntos de dados Power BI e gerir dados do Power BI. Avance para o [espaço de trabalho De Ligação a Um espaço de trabalho Power BI para um artigo do espaço de trabalho da Sinapse](../quickstart-power-bi.md) para saber mais. 

