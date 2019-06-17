---
title: Invocar programas do Spark no Azure Data Factory | Documentos da Microsoft
description: Saiba como invocar programas do Spark a partir de uma fábrica de dados do Azure através da atividade de MapReduce.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 95c49eec6964984894f75ecd0a9e50c9c947683b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61257647"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas do Spark no Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do Hive](data-factory-hive-activity.md)
> * [Atividade PIg](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de execução de lotes de Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de recursos de atualização de Aprendizado de máquina](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de procedimento armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, veja [transformar dados com a atividade do Apache Spark no Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introdução
A atividade do Spark é um da [atividades de transformação de dados](data-factory-data-transformation-activities.md) suportado pelo Data Factory. Esta atividade executa o programa Spark especificado num cluster do Spark no HDInsight do Azure. 

> [!IMPORTANT]
> - A atividade do Spark não suporta clusters do Spark do HDInsight que utilizam o Azure Data Lake Store como armazenamento primário.
> - A atividade do Spark suporta apenas utilizadores existentes (seus próprio) os clusters do Spark do HDInsight. Ele não dá suporte um serviço de ligado de HDInsight a pedido.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Descrição Passo a Passo: Criar um pipeline com uma atividade Spark
Aqui estão as etapas típicas para criar um pipeline de fábrica de dados com uma atividade do Spark: 

* Criar uma fábrica de dados.
* Crie um serviço ligado do armazenamento do Azure para ligar o seu armazenamento que está associado ao seu cluster do Spark do HDInsight para a fábrica de dados.
* Crie um serviço ligado do HDInsight para ligar o seu cluster do Spark no HDInsight a fábrica de dados.
* Crie um conjunto de dados refere-se ao serviço ligado do armazenamento. Atualmente, tem de especificar um conjunto de dados de saída para uma atividade, mesmo que não existe nenhuma saída a ser produzida. 
* Crie um pipeline com a atividade do Spark que se refere ao serviço ligado do HDInsight que criou. A atividade está configurada com o conjunto de dados que criou no passo anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que pauta a agenda (por hora, diariamente). Por conseguinte, tem de especificar o conjunto de dados de saída, mesmo que a atividade realmente não produz um resultado.

### <a name="prerequisites"></a>Pré-requisitos
1. Criar uma conta de armazenamento para fins gerais, seguindo as instruções em [criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).

1. Criar um cluster do Spark no HDInsight ao seguir as instruções do tutorial [criar um cluster do Spark no HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento que criou no passo 1, com este cluster.

1. Transfira e reveja o ficheiro de script de Python **test.py** localizado na [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Carregue **test.py** para o **pyFiles** pasta na **adfspark** contentor no armazenamento de Blobs. Crie o contentor e a pasta, caso não existam.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Para criar uma fábrica de dados, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Novo** > **Dados + Análise** > **Data Factory**.

1. Sobre o **nova fábrica de dados** painel, em **nome**, introduza **SparkDF**.

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se vir o erro "o nome da fábrica de dados SparkDF não está disponível", altere o nome da fábrica de dados. Por exemplo, utilize yournameSparkDFdate e volte a criar a fábrica de dados. Para obter mais informações sobre regras de nomenclatura, consulte [fábrica de dados: Regras de nomenclatura](data-factory-naming-rules.md).

1. Em **Subscrição**, selecione a subscrição do Azure onde pretende que seja criada a fábrica de dados.

1. Selecione um grupo de recursos existente ou crie um grupo de recursos do Azure.

1. Selecione a caixa de verificação **Afixar ao dashboard**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível da subscrição/grupo de recursos.

1. Verá a fábrica de dados, já que é criado no dashboard do portal do Azure.

1. Depois de ter criado a fábrica de dados, é apresentada a página da **fábrica de dados** e o respetivo conteúdo. Se não vir a **fábrica de dados** , selecione o mosaico da sua fábrica de dados no dashboard.

    ![Painel Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai criar dois serviços ligados. Um serviço liga o seu cluster do Spark para a fábrica de dados e o outro serviço liga o seu armazenamento à fábrica de dados. 

#### <a name="create-a-storage-linked-service"></a>Criar um serviço ligado ao Armazenamento
Neste passo, vai ligar a sua conta de armazenamento à fábrica de dados. Um conjunto de dados que criar num passo mais à frente nestas instruções se refere a este serviço ligado. O serviço ligado do HDInsight que definir no próximo passo refere-se demasiado para este serviço ligado. 

1. Sobre o **fábrica de dados** painel, selecione **autor e implementar**. É apresentado o Editor do Data Factory.

1. Selecione **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Novo arquivo de dados](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. O que utilizar para criar um armazenamento de serviço ligado é apresentado no editor de script JSON.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua **nome da conta** e **chave de conta** com a chave de acesso e o nome da conta de armazenamento. Para saber como obter a sua chave de acesso ao armazenamento, veja como visualizar, copiar e regenerar chaves de acesso ao armazenamento em [Gerir a conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

1. Para implementar o serviço ligado, selecione **Deploy** na barra de comandos. Depois de o serviço ligado ser implementado com êxito, a janela Rascunho-1 desaparece. Verá **AzureStorageLinkedService** na vista de árvore à esquerda.

#### <a name="create-an-hdinsight-linked-service"></a>Criar um serviço ligado do HDInsight
Neste passo, vai criar um serviço ligado do HDInsight para ligar o seu cluster do HDInsight Spark à fábrica de dados. O cluster do HDInsight é utilizado para executar o programa Spark especificado na atividade Spark do pipeline neste exemplo. 

1. No Editor do Data Factory, selecione **mais** > **nova computação** > **cluster do HDInsight**.

    ![Criar serviço ligado do HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1. No editor de JSON, siga os passos seguintes:

    a. Especifique o URI para o cluster do HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Especifique o nome do utilizador que tem acesso para o cluster do Spark.

    c. Especifique a palavra-passe do utilizador.

    d. Especifique o serviço ligado de armazenamento que está associado ao cluster do HDInsight Spark. Neste exemplo, é AzureStorageLinkedService.

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
    > - A atividade do Spark não suporta clusters do Spark do HDInsight que utilizam o Azure Data Lake Store como armazenamento primário.
    > - A atividade do Spark suporta apenas utilizadores existentes (seus próprio) os clusters do Spark do HDInsight. Ele não dá suporte um serviço de ligado de HDInsight a pedido.

    Para obter mais informações sobre o serviço ligado do HDInsight, consulte [serviço ligado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Para implementar o serviço ligado, selecione **Deploy** na barra de comandos. 

### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
O conjunto de dados de saída é o que pauta a agenda (por hora, diariamente). Por conseguinte, tem de especificar um conjunto de dados de saída para a atividade do Spark no pipeline, apesar da atividade não produza dados. Especificar um conjunto de dados de entrada para a atividade é opcional.

1. No Editor do Data Factory, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

1. Copie e cole o fragmento seguinte na janela Rascunho-1. O fragmento JSON define um conjunto de dados chamado **OutputDataset**. Além disso, especifica que os resultados são armazenados no contentor de BLOBs denominado **adfspark** e na pasta denominada **pyFiles/saída**. Como mencionado anteriormente, este conjunto de dados é um conjunto de dados fictício. O programa Spark neste exemplo não produzir qualquer saída. O **disponibilidade** secção especifica que o conjunto de dados de saída é produzido diariamente. 

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
1. Para implementar o conjunto de dados, selecione **Deploy** na barra de comandos.


### <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma atividade de HDInsightSpark. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados de saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. Portanto, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No Editor do Data Factory, selecione **Mais** > **Novo pipeline**.

1. Substitua o script na janela rascunho-1 com o seguinte script:

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

    a. O **tipo** estiver definida como **HDInsightSpark**.

    b. O **rootPath** estiver definida como **adfspark\\pyFiles** onde adfspark é o contentor de BLOBs e pyFiles é a pasta de ficheiros nesse contentor. Neste exemplo, o armazenamento de BLOBs é o que está associado ao cluster do Spark. Pode carregar o ficheiro para outra conta de armazenamento. Se fizer isso, crie um serviço ligado do armazenamento para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para o **sparkJobLinkedService** propriedade. Para obter mais informações sobre esta propriedade e outras propriedades compatíveis com a atividade do Spark, consulte [propriedades da atividade do Spark](#spark-activity-properties).

    c. O **entryFilePath** estiver definida como **test.py**, que é o ficheiro de Python.

    d. O **getDebugInfo** estiver definida como **sempre**, que significa que os ficheiros de registo são sempre gerado (êxito ou falha).

    > [!IMPORTANT]
    > Recomendamos que não defina esta propriedade para `Always` num ambiente de produção, a menos que esteja a resolver um problema.

    e. O **produz** secção tem um conjunto de dados de saída. Tem de especificar um conjunto de dados de saída, mesmo que o programa Spark não produz quaisquer dados. O conjunto de dados de saída controla a agenda para o pipeline (hora a hora, diariamente). 

    Para obter mais informações sobre as propriedades suportadas pela atividade Spark, consulte a secção [propriedades da atividade do Spark](#spark-activity-properties).

1. Para implementar o pipeline, selecione **Deploy** na barra de comandos.

### <a name="monitor-a-pipeline"></a>Monitorizar um pipeline
1. Sobre o **fábrica de dados** painel, selecione **monitorizar e gerir** para iniciar a aplicação de monitorização no outro separador.

    ![Mosaico Monitorizar e Gerir](media/data-factory-spark/monitor-and-manage-tile.png)

1. Alteração da **hora de início** filtro na parte superior para **2/1/2017**e selecione **aplicar**.

1. Apenas uma atividade é apresentada a janela porque existe apenas um dia entre o início (2017-02-01) e horas de fim (2017-02-02) do pipeline. Confirme se o setor de dados está no **pronto** estado.

    ![Monitorizar o pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. Na **janelas de atividade** , selecione uma execução de atividade para ver detalhes sobre ele. Se existir um erro, verá detalhes sobre o mesmo no painel da direita.

### <a name="verify-the-results"></a>Verificar os resultados

1. Inicie o bloco de notas do Jupyter para o seu cluster do Spark do HDInsight ao aceder [este Web site](https://CLUSTERNAME.azurehdinsight.net/jupyter). Também pode abrir um dashboard do cluster para o Spark do HDInsight cluster e, em seguida, inicie o bloco de notas do Jupyter.

1. Selecione **New** > **PySpark** para iniciar um novo bloco de notas.

    ![Novo bloco de notas Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Execute o seguinte comando ao copiar e colar o texto e prima Shift + Enter no final da segunda instrução:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirme que vê os dados da tabela de ar-condicionado. 

    ![Resultados de consulta do Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Para obter instruções detalhadas, consulte a secção [executar uma consulta do Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Resolução de problemas
Porque definiu o getDebugInfo **sempre**, pode ver uma subpasta de log na pasta pyFiles no contentor de blob. O ficheiro de registo na pasta de registo fornece informações adicionais. Este ficheiro de registo é especialmente útil quando existe um erro. Num ambiente de produção, convém configurá-lo para **falha**.

Para resolução de problemas ainda mais, siga os passos seguintes:


1. Aceda a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicação da IU do YARN](media/data-factory-spark/yarnui-application.png)

1. Selecione **registos** para um da execução tenta.

    ![Página de aplicativo](media/data-factory-spark/yarn-applications.png)

1. Consulte as seguintes informações de erro adicionais na página de registo:

    ![Erro do registo](media/data-factory-spark/yarnui-application-error.png)

As secções seguintes fornecem informações sobre as entidades da fábrica de dados para utilizar o cluster do Spark e a atividade do Spark na sua fábrica de dados.

## <a name="spark-activity-properties"></a>Propriedades de atividade do Spark
Eis a definição de JSON de exemplo de um pipeline com uma atividade do Spark: 

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
                    }
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

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON.

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| description | Texto que descreve o que faz a atividade. | Não |
| type | Esta propriedade tem de ser definida para HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço ligado do HDInsight em que o programa Spark é executado. | Sim |
| rootPath | O contentor de BLOBs e a pasta que contém o ficheiro de Spark. O nome de ficheiro diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do código/pacote Spark. | Sim |
| className | Classe de principal de Java/Spark do aplicativo. | Não |
| arguments | Uma lista de argumentos da linha de comandos para o programa Spark. | Não |
| proxyUser | A conta de utilizador para representar a execução do programa Spark. | Não |
| sparkConfig | Especifique valores para as propriedades de configuração de Spark listadas na [configuração de Spark: Propriedades da aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não |
| getDebugInfo | Especifica quando os ficheiros de registo do Spark são copiados para o armazenamento utilizado pelo cluster do HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos são nenhum, sempre ou falha. O valor predefinido é nenhum. | Não |
| sparkJobLinkedService | O armazenamento ligado do serviço que detém o Spark, o ficheiro de tarefa, dependências e registos. Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster do HDInsight é utilizado. | Não |

## <a name="folder-structure"></a>estrutura de pastas
A atividade do Spark não suporta um script inline de como o Pig e Hive atividades fazer. Tarefas do Spark também são mais extensíveis das tarefas do Pig/Hive. Para tarefas do Spark, pode fornecer múltiplas dependências, tais como jar pacotes (colocados no java CLASSPATH), ficheiros de Python (colocados sobre o PYTHONPATH) e outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento de BLOBs referenciado pelo serviço ligado do HDInsight. Em seguida, carregue ficheiros dependentes para as subpastas apropriadas na pasta raiz representado pelo **entryFilePath**. Por exemplo, carregar ficheiros de Python para a subpasta pyFiles e jar ficheiros para a subpasta jars da pasta raiz. No tempo de execução, o serviço Data Factory espera que a seguinte estrutura de pasta no armazenamento de BLOBs: 

| Caminho | Descrição | Necessário | Type |
| ---- | ----------- | -------- | ---- |
| . | O caminho de raiz da tarefa do Spark no serviço ligado do armazenamento. | Sim | Pasta |
| &lt;definido pelo utilizador &gt; | O caminho que aponta para o ficheiro de entrada da tarefa do Spark. | Sim | Ficheiro |
| ./jars | Todos os ficheiros nessa pasta são carregados e colocados no caminho da classe de Java do cluster. | Não | Pasta |
| ./pyFiles | Todos os ficheiros nessa pasta são carregados e colocados em PYTHONPATH do cluster. | Não | Pasta |
| ./files | Todos os ficheiros nessa pasta são carregados e colocados no diretório de trabalho de executor. | Não | Pasta |
| . / arquiva | Todos os ficheiros nessa pasta são descomprimidos. | Não | Pasta |
| ./logs | A pasta onde estão armazenados os registos do cluster do Spark.| Não | Pasta |

Eis um exemplo para o armazenamento que contém dois arquivos de tarefa do Spark no armazenamento de BLOBs referenciada pelo serviço ligado do HDInsight:

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
