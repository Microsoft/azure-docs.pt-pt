---
title: Invocar programas Spark de Azure Data Factory | Microsoft Docs
description: Saiba como invocar programas Spark de uma data factory do Azure usando a atividade MapReduce.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 08aa1303aeaa0a80f0825f45e037109b98e9771e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135343"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas Spark de pipelines Azure Data Factory

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md)
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de execução de Machine Learning lote](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning atualizar atividade de recurso](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de procedimento armazenado](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics atividade U-SQL](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se você usar a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade Apache Spark no data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introdução
A atividade do Spark é uma das [atividades de transformação de dados](data-factory-data-transformation-activities.md) com suporte pelo data Factory. Esta atividade executa o programa Spark especificado em seu cluster Spark no Azure HDInsight. 

> [!IMPORTANT]
> - A atividade do Spark não dá suporte a clusters HDInsight Spark que usam Azure Data Lake Store como armazenamento primário.
> - A atividade do Spark dá suporte apenas a clusters HDInsight Spark existentes (seus próprios). Ele não dá suporte a um serviço vinculado do HDInsight sob demanda.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Descrição Passo a Passo: Criar um pipeline com uma atividade Spark
Aqui estão as etapas típicas para criar um pipeline de data factory com uma atividade do Spark: 

* Criar uma fábrica de dados.
* Crie um serviço vinculado do armazenamento do Azure para vincular seu armazenamento associado ao seu cluster HDInsight Spark ao data factory.
* Crie um serviço vinculado do HDInsight para vincular seu cluster Spark no HDInsight ao data factory.
* Crie um conjunto de um DataSet que se refere ao serviço vinculado de armazenamento. No momento, você deve especificar um conjunto de uma saída para uma atividade, mesmo que não haja nenhuma saída sendo produzida. 
* Crie um pipeline com a atividade do Spark que se refere ao serviço vinculado do HDInsight que você criou. A atividade é configurada com o conjunto de resultados que você criou na etapa anterior como um conjunto de uma saída. O conjunto de resultados de saída é o que orienta o agendamento (por hora, diariamente). Portanto, você deve especificar o conjunto de resultados de saída, mesmo que a atividade não produza realmente uma saída.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma conta de armazenamento de uso geral seguindo as instruções em [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).

1. Crie um cluster Spark no HDInsight seguindo as instruções no tutorial [criar um cluster do Spark no hdinsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento criada na etapa 1 a este cluster.

1. Baixe e examine o arquivo de script Python **Test.py** localizado [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)em.

1. Carregue o **Test.py** na pasta **pyFiles** no contêiner **adfspark** no armazenamento de BLOBs. Crie o contêiner e a pasta se eles não existirem.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Para criar uma fábrica de dados, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Novo** > **Dados + Análise** > **Data Factory**.

1. Na folha **novo data Factory** , em **nome**, insira **SparkDF**.

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se você vir o erro "o nome do data Factory SparkDF não está disponível", altere o nome do data factory. Por exemplo, use yournameSparkDFdate e crie a data factory novamente. Para obter mais informações sobre regras de nomenclatura [, consulte Data Factory: Regras](data-factory-naming-rules.md)de nomenclatura.

1. Em **Subscrição**, selecione a subscrição do Azure onde pretende que seja criada a fábrica de dados.

1. Selecione um grupo de recursos existente ou crie um grupo de recursos do Azure.

1. Selecione a caixa de verificação **Afixar ao dashboard**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível da subscrição/grupo de recursos.

1. Você vê o data factory conforme ele é criado no painel da portal do Azure.

1. Depois de ter criado a fábrica de dados, é apresentada a página da **fábrica de dados** e o respetivo conteúdo. Se você não vir a página **Data Factory** , selecione o bloco para sua data Factory no painel.

    ![Painel Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços ligados
Nesta etapa, você cria dois serviços vinculados. Um serviço vincula seu cluster Spark ao seu data factory, e o outro serviço vincula seu armazenamento ao seu data factory. 

#### <a name="create-a-storage-linked-service"></a>Criar um serviço ligado ao Armazenamento
Neste passo, vai ligar a sua conta de armazenamento à fábrica de dados. Um conjunto de um DataSet que você criará em uma etapa posteriormente neste passo a passos refere-se a esse serviço vinculado. O serviço vinculado do HDInsight que você define na próxima etapa também se refere a esse serviço vinculado. 

1. Na folha **Data Factory** , selecione **criar e implantar**. O editor de Data Factory é exibido.

1. Selecione **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Novo arquivo de dados](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. O script JSON que você usa para criar um serviço vinculado de armazenamento aparece no editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua o **nome da conta** e a chave de **conta** pelo nome e a chave de acesso da sua conta de armazenamento. Para saber como obter a sua chave de acesso ao armazenamento, veja como visualizar, copiar e regenerar chaves de acesso ao armazenamento em [Gerir a conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

1. Para implantar o serviço vinculado, selecione **implantar** na barra de comandos. Depois de o serviço ligado ser implementado com êxito, a janela Rascunho-1 desaparece. Verá **AzureStorageLinkedService** na vista de árvore à esquerda.

#### <a name="create-an-hdinsight-linked-service"></a>Criar um serviço ligado do HDInsight
Nesta etapa, você cria um serviço vinculado do HDInsight para vincular seu cluster HDInsight Spark ao data factory. O cluster HDInsight é usado para executar o programa Spark especificado na atividade Spark do pipeline neste exemplo. 

1. No editor de data Factory, selecione **mais** > **novo computação** > **cluster HDInsight**.

    ![Criar serviço ligado do HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1. No editor de JSON, execute as seguintes etapas:

    a. Especifique o URI para o cluster HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Especifique o nome do usuário que tem acesso ao cluster do Spark.

    c. Especifique a senha para o usuário.

    d. Especifique o serviço vinculado de armazenamento que está associado ao cluster HDInsight Spark. Neste exemplo, é AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - A atividade do Spark não dá suporte a clusters HDInsight Spark que usam Azure Data Lake Store como armazenamento primário.
    > - A atividade do Spark dá suporte apenas a clusters HDInsight Spark existentes (seus próprios). Ele não dá suporte a um serviço vinculado do HDInsight sob demanda.

    Para obter mais informações sobre o serviço vinculado do HDInsight, consulte [serviço vinculado do hdinsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Para implantar o serviço vinculado, selecione **implantar** na barra de comandos. 

### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
O conjunto de resultados de saída é o que orienta o agendamento (por hora, diariamente). Portanto, você deve especificar um conjunto de uma saída para a atividade do Spark no pipeline, embora a atividade não produza nenhuma saída. A especificação de um conjunto de dados de entrada para a atividade é opcional.

1. No Editor do Data Factory, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

1. Copie e cole o fragmento seguinte na janela Rascunho-1. O trecho JSON define um DataSet chamado **OutputDataset**. Além disso, você especifica que os resultados são armazenados no contêiner de blob chamado **adfspark** e a pasta chamada **pyFiles/output**. Conforme mencionado anteriormente, esse conjunto de um conjunto de um DataSet é fictício. O programa Spark neste exemplo não produz nenhuma saída. A seção **disponibilidade** especifica que o conjunto de resultados de saída é produzido diariamente. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Para implantar o conjunto de um, selecione **implantar** na barra de comandos.


### <a name="create-a-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com uma atividade HDInsightSpark. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados de saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. Portanto, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No Editor do Data Factory, selecione **Mais** > **Novo pipeline**.

1. Substitua o script na janela rascunho-1 pelo seguinte script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Tenha em atenção os seguintes pontos:

    a. A propriedade **Type** é definida como **HDInsightSpark**.

    b. A propriedade **rootPath** é definida como **adfspark\\pyFiles** , em que adfspark é o contêiner de BLOB e pyFiles é a pasta de arquivos nesse contêiner. Neste exemplo, o armazenamento de BLOBs é aquele associado ao cluster do Spark. Você pode carregar o arquivo em uma conta de armazenamento diferente. Se você fizer isso, crie um serviço de armazenamento vinculado para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade **sparkJobLinkedService** . Para obter mais informações sobre essa propriedade e outras propriedades com suporte na atividade do Spark, consulte [Propriedades da atividade do Spark](#spark-activity-properties).

    c. A propriedade **entryFilePath** é definida como **Test.py**, que é o arquivo Python.

    d. A propriedade **getDebugInfo** é definida como **Always**, o que significa que os arquivos de log sempre são gerados (êxito ou falha).

    > [!IMPORTANT]
    > Recomendamos que você não defina essa propriedade como `Always` em um ambiente de produção, a menos que esteja solucionando um problema.

    e. A seção de **saídas** tem um conjunto de uma saída. Você deve especificar um conjunto de uma saída mesmo se o programa Spark não produzir nenhuma saída. O conjunto de resultados de saída orienta o agendamento para o pipeline (por hora, diariamente). 

    Para obter mais informações sobre as propriedades com suporte na atividade do Spark, consulte a seção [Propriedades da atividade do Spark](#spark-activity-properties).

1. Para implantar o pipeline, selecione **implantar** na barra de comandos.

### <a name="monitor-a-pipeline"></a>Monitorizar um pipeline
1. Na folha **Data Factory** , selecione **monitorar & gerenciar** para iniciar o aplicativo de monitoramento em outra guia.

    ![Mosaico Monitorizar e Gerir](media/data-factory-spark/monitor-and-manage-tile.png)

1. Altere o filtro **hora de início** na parte superior para **2/1/2017**e selecione **aplicar**.

1. Somente uma janela de atividade é exibida porque há apenas um dia entre as horas de início (2017-02-01) e de término (2017-02-02) do pipeline. Confirme se a fatia de dados está no estado **pronto** .

    ![Monitorizar o pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. Na lista **janelas de atividades** , selecione uma execução de atividade para ver detalhes sobre ela. Se houver um erro, você verá detalhes sobre ele no painel direito.

### <a name="verify-the-results"></a>Verificar os resultados

1. Inicie o Jupyter Notebook para o cluster HDInsight Spark indo para [este site](https://CLUSTERNAME.azurehdinsight.net/jupyter). Você também pode abrir um painel de cluster para seu cluster HDInsight Spark e iniciar o Jupyter Notebook.

1. Selecione **novo** > **PySpark** para iniciar um novo bloco de anotações.

    ![Novo notebook Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Execute o comando a seguir copiando e colando o texto e pressionando Shift + Enter no final da segunda instrução:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirme que você vê os dados da tabela de HVAC. 

    ![Resultados da consulta do Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Para obter instruções detalhadas, consulte a seção [executar uma consulta SQL do Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Resolução de problemas
Como você define getDebugInfo como **sempre**, você vê uma subpasta log na pasta pyFiles em seu contêiner de BLOB. O arquivo de log na pasta de log fornece informações adicionais. Esse arquivo de log é especialmente útil quando há um erro. Em um ambiente de produção, talvez você queira defini-lo como **falha**.

Para solucionar problemas adicionais, execute as seguintes etapas:


1. Aceda a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicativo de interface do usuário do YARN](media/data-factory-spark/yarnui-application.png)

1. Selecione **logs** para uma das tentativas de execução.

    ![Página do aplicativo](media/data-factory-spark/yarn-applications.png)

1. Você verá as seguintes informações de erro adicionais na página log:

    ![Erro de log](media/data-factory-spark/yarnui-application-error.png)

As seções a seguir fornecem informações sobre as entidades de data factory para usar a atividade de cluster Spark e Spark em seu data factory.

## <a name="spark-activity-properties"></a>Propriedades da atividade do Spark
Aqui está a definição de JSON de exemplo de um pipeline com uma atividade do Spark: 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON.

| Propriedade | Descrição | Requerido |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| description | Texto que descreve o que a atividade faz. | Não |
| type | Essa propriedade deve ser definida como HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço vinculado do HDInsight no qual o programa Spark é executado. | Sim |
| rootPath | O contêiner de BLOB e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do código/pacote do Spark. | Sim |
| className | Classe principal Java/Spark do aplicativo. | Não |
| arguments | Uma lista de argumentos de linha de comando para o programa Spark. | Não |
| proxyUser | A conta de usuário a ser representada para executar o programa Spark. | Não |
| sparkConfig | Especifique valores para as propriedades de configuração do Spark [listadas na configuração do Spark: Propriedades](https://spark.apache.org/docs/latest/configuration.html#available-properties)do aplicativo. | Não |
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados para o armazenamento usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Os valores permitidos são None, Always ou Failure. O valor padrão é None. | Não |
| sparkJobLinkedService | O serviço vinculado de armazenamento que contém o arquivo de trabalho do Spark, dependências e logs. Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

## <a name="folder-structure"></a>Estrutura de pastas
A atividade do Spark não dá suporte a um script embutido, pois as atividades Pig e Hive fazem. Os trabalhos do Spark também são mais extensíveis do que os trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes JAR (colocados no CLASSPATH do Java), arquivos Python (colocados no PYTHONPATH) e quaisquer outros arquivos.

Crie a seguinte estrutura de pastas no armazenamento de BLOBs referenciado pelo serviço vinculado do HDInsight. Em seguida, carregue os arquivos dependentes nas subpastas apropriadas na pasta raiz representada por **entryFilePath**. Por exemplo, carregue arquivos Python na subpasta pyFiles e nos arquivos jar para a subpasta jars da pasta raiz. Em tempo de execução, o serviço Data Factory espera a seguinte estrutura de pasta no armazenamento de BLOBs: 

| Path | Descrição | Requerido | Type |
| ---- | ----------- | -------- | ---- |
| . | O caminho raiz do trabalho do Spark no serviço vinculado de armazenamento. | Sim | Pasta |
| &lt;definido pelo usuário&gt; | O caminho que aponta para o arquivo de entrada do trabalho do Spark. | Sim | Ficheiro |
| ./jars | Todos os arquivos nessa pasta são carregados e colocados no classpath Java do cluster. | Não | Pasta |
| ./pyFiles | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster. | Não | Pasta |
| ./files | Todos os arquivos nessa pasta são carregados e colocados no diretório de trabalho do executor. | Não | Pasta |
| ./archives | Todos os arquivos nesta pasta são descompactados. | Não | Pasta |
| ./logs | A pasta onde os logs do cluster Spark são armazenados.| Não | Pasta |

Aqui está um exemplo de armazenamento que contém dois arquivos de trabalho do Spark no armazenamento de BLOBs referenciados pelo serviço vinculado do HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
