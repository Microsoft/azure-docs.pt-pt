---
title: Invocar programas Spark da Azure Data Factory
description: Aprenda a invocar programas Spark a partir de uma fábrica de dados Azure utilizando a atividade MapReduce.
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
ms.openlocfilehash: 97e2be64818888040b7e6ac3bc8861da24ebdbbd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359956"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas Spark dos oleodutos Azure Data Factory

> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da colmeia](data-factory-hive-activity.md)
> * [Atividade do porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de faísca](data-factory-spark.md)
> * [Azure Machine Learning Studio (clássico) Atividade de execução de lote](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (clássico) Atualização De Recursos](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de procedimento armazenado](data-factory-stored-proc-activity.md)
> * [Atividade U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, consulte [os dados da Transform utilizando a atividade Apache Spark na Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introdução
A atividade Spark é uma das atividades de transformação de [dados](data-factory-data-transformation-activities.md) apoiadas pela Data Factory. Esta atividade executa o programa Spark especificado no seu cluster Spark em Azure HDInsight.

> [!IMPORTANT]
> - A atividade Spark não suporta clusters HDInsight Spark que usam a Azure Data Lake Store como armazenamento primário.
> - A atividade Spark suporta apenas clusters HDInsight Spark existentes (seus) hdinsight. Não suporta um serviço on-demand HDInsight ligado.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Walkthrough: Criar um oleoduto com uma atividade de Faísca
Aqui estão os passos típicos para criar um oleoduto de fábrica de dados com uma atividade spark:

* Criar uma fábrica de dados.
* Crie um serviço ligado ao Azure Storage para ligar o seu armazenamento que está associado ao seu cluster HDInsight Spark à fábrica de dados.
* Crie um serviço ligado ao HDInsight para ligar o seu cluster Spark em HDInsight à fábrica de dados.
* Crie um conjunto de dados que se refira ao serviço ligado ao Armazenamento. Atualmente, deve especificar um conjunto de dados de saída para uma atividade, mesmo que não seja produzida nenhuma produção.
* Crie um pipeline com atividade Spark que se refira ao serviço de ligação HDInsight que criou. A atividade está configurada com o conjunto de dados que criou no passo anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que impulsiona o horário (de hora em hora, diariamente). Portanto, deve especificar o conjunto de dados de saída, mesmo que a atividade não produza realmente uma saída.

### <a name="prerequisites"></a>Pré-requisitos
1. Crie uma conta de armazenamento para fins gerais seguindo as instruções na [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

1. Crie um cluster spark em HDInsight seguindo as instruções no tutorial [Crie um cluster de faíscas em HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento que criou no passo 1 com este cluster.

1. Faça o download e reveja o ficheiro de script Python **test.py** localizado em [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Faça **o upload test.py** para a pasta **pyFiles** no recipiente **adfspark** no seu armazenamento de bolhas. Crie o recipiente e a pasta se não existirem.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Para criar uma fábrica de dados, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Novos**  >  Dados + Fábrica de Dados**Analíticos.**  >  **Data Factory**

1. Na nova lâmina da **fábrica de dados,** em **Nome,** insira **a SparkDF**.

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser globalmente exclusivo. Se vir o erro "O nome da fábrica de dados SparkDF não está disponível", altere o nome da fábrica de dados. Por exemplo, use o seu nomeSparkDFdate e crie novamente a fábrica de dados. Para obter mais informações sobre regras de nomenclatura, veja [Data Factory: regras de nomenclatura](data-factory-naming-rules.md).

1. Em **Subscrição**, selecione a subscrição do Azure onde pretende que seja criada a fábrica de dados.

1. Selecione um grupo de recursos existente ou crie um grupo de recursos Azure.

1. Selecione a caixa de verificação **Afixar ao dashboard**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias data factory, você deve ser um membro da [função de contribuinte](../../role-based-access-control/built-in-roles.md#data-factory-contributor) data factory ao nível do grupo de subscrição/recursos.

1. Vê a fábrica de dados tal como é criada no painel de instrumentos do portal Azure.

1. Depois de ter criado a fábrica de dados, é apresentada a página da **fábrica de dados** e o respetivo conteúdo. Se não vir a página da **fábrica de dados,** selecione o azulejo para a sua fábrica de dados no painel de instrumentos.

    ![Painel Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, cria-se dois serviços ligados. Um serviço liga o seu cluster Spark à sua fábrica de dados, e o outro serviço liga o seu armazenamento à sua fábrica de dados.

#### <a name="create-a-storage-linked-service"></a>Criar um serviço ligado ao Armazenamento
Neste passo, vai ligar a sua conta de armazenamento à fábrica de dados. Um conjunto de dados que cria num passo mais tarde neste walkthrough refere-se a este serviço ligado. O serviço de ligação HDInsight que define no passo seguinte refere-se também a este serviço ligado.

1. Na lâmina da **fábrica de dados,** selecione **Author e implemente**. O Editor da Fábrica de Dados aparece.

1. Selecione **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Nova loja de dados](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. O script JSON que usa para criar um serviço ligado ao Armazenamento aparece no editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua **o nome da conta** e a chave de **conta** pelo nome e chave de acesso da sua conta de armazenamento. Para aprender a obter a chave de acesso ao armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).

1. Para implementar o serviço ligado, selecione **Implementar** na barra de comando. Depois de o serviço ligado ser implementado com êxito, a janela Rascunho-1 desaparece. Verá **AzureStorageLinkedService** na vista de árvore à esquerda.

#### <a name="create-an-hdinsight-linked-service"></a>Criar um serviço ligado do HDInsight
Neste passo, cria um serviço ligado ao HDInsight para ligar o seu cluster HDInsight Spark à fábrica de dados. O cluster HDInsight é utilizado para executar o programa Spark especificado na atividade spark do pipeline nesta amostra.

1. No Data Factory Editor, selecione **Mais**  >  **Novos clusters de computação**  >  **HDInsight**.

    ![Criar serviço ligado do HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1. No editor da JSON, tome os seguintes passos:

    a. Especifique o URI para o cluster HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Especifique o nome do utilizador que tem acesso ao cluster Spark.

    c. Especifique a palavra-passe do utilizador.

    d. Especifique o serviço ligado ao armazenamento que está associado ao cluster HDInsight Spark. Neste exemplo, é AzureStorageLinkedService.

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
    > - A atividade Spark não suporta clusters HDInsight Spark que usam a Azure Data Lake Store como armazenamento primário.
    > - A atividade Spark suporta apenas clusters HDInsight Spark existentes (seus) hdinsight. Não suporta um serviço on-demand HDInsight ligado.

    Para obter mais informações sobre o serviço ligado ao HDInsight, consulte o [serviço ligado ao HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Para implementar o serviço ligado, selecione **Implementar** na barra de comando.

### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
O conjunto de dados de saída é o que impulsiona o horário (de hora em hora, diariamente). Portanto, deve especificar um conjunto de dados de saída para a atividade spark no pipeline, mesmo que a atividade não produza qualquer saída. Especificar um conjunto de dados de entrada para a atividade é opcional.

1. No Editor de Fábrica de Dados, selecione **Mais**  >  **novos conjuntos de dados**  >  **Azure Blob armazenamento**.

1. Copie e cole o fragmento seguinte na janela Rascunho-1. O snippet JSON define um conjunto de dados chamado **OutputDataset**. Além disso, especifica que os resultados são armazenados no recipiente blob chamado **adfspark** e na pasta chamada **pyFiles/output**. Como mencionado anteriormente, este conjunto de dados é um conjunto de dados falso. O programa Spark neste exemplo não produz qualquer saída. A secção **de disponibilidade** especifica que o conjunto de dados de saída é produzido diariamente.

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
1. Para implementar o conjunto de dados, selecione **Implementar** na barra de comando.


### <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, cria-se um oleoduto com uma atividade HDInsightSpark. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados de saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. Portanto, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No Editor de Fábrica de Dados, selecione **Mais**  >  **Novo pipeline**.

1. Substitua o guião na janela Draft-1 pelo seguinte script:

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

    a. A propriedade **tipo** está definida para **HDInsightSpark.**

    b. A propriedade **rootPath** está definida para **adfspark \\ pyFiles** onde adfspark é o recipiente blob e pyFiles é pasta de arquivo nesse recipiente. Neste exemplo, o armazenamento de bolhas é o que está associado ao cluster Spark. Pode fazer o upload do ficheiro para uma conta de armazenamento diferente. Se o fizer, crie um serviço ligado ao Armazenamento para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para a propriedade **sparkJobLinkedService.** Para mais informações sobre esta propriedade e outros imóveis suportados pela atividade Spark, consulte [as propriedades da atividade spark.](#spark-activity-properties)

    c. A **propriedade in entryFilePath** está definida para **test.py**, que é o ficheiro Python.

    d. A propriedade **getDebugInfo** está definida para **Always**, o que significa que os ficheiros de registo são sempre gerados (sucesso ou falha).

    > [!IMPORTANT]
    > Recomendamos que não coloque esta propriedade num ambiente de produção a `Always` menos que esteja a resolver um problema.

    e. A secção **de saídas** tem um conjunto de dados de saída. Tem de especificar um conjunto de dados de saída mesmo que o programa Spark não produza qualquer saída. O conjunto de dados de saída impulsiona o calendário do gasoduto (de hora em hora, diariamente).

    Para obter mais informações sobre os imóveis suportados pela atividade Spark, consulte a secção [Propriedades de atividade spark](#spark-activity-properties).

1. Para implantar o gasoduto, selecione **Implementar** na barra de comando.

### <a name="monitor-a-pipeline"></a>Monitorizar um pipeline
1. Na lâmina da **fábrica de dados,** selecione **Monitor & Conseguir** iniciar a aplicação de monitorização em outro separador.

    ![Mosaico Monitorizar e Gerir](media/data-factory-spark/monitor-and-manage-tile.png)

1. Mude o filtro **de tempo de início** na parte superior para **2/1/2017**e selecione **Aplicar**.

1. Apenas uma janela de atividade aparece porque só há um dia entre o início (2017-02-01) e os tempos finais (2017-02-02) do oleoduto. Confirme se a fatia de dados está no estado **de Ready.**

    ![Monitorizar o pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. Na lista de **janelas de atividade,** selecione uma corrida de atividade para ver detalhes sobre o mesmo. Se houver um erro, vê detalhes sobre isso no painel certo.

### <a name="verify-the-results"></a>Verificar os resultados

1. Inicie o Caderno Jupyter para o seu cluster HDInsight Spark indo para `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Também pode abrir um painel de cluster para o seu cluster HDInsight Spark e, em seguida, iniciar o Caderno Jupyter.

1. Selecione **New**  >  **PySpark** para iniciar um novo caderno.

    ![Novo caderno jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Executar o seguinte comando copiando e colando o texto e pressionando Shift+Enter no final da segunda declaração:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Confirme que vê os dados da tabela de avac.

    ![Resultados da consulta de Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Para obter instruções detalhadas, consulte a secção [Executar uma consulta Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Resolução de problemas
Como configurar getDebugInfo para **Always**, vê uma sub-dobradeira de registo na pasta pyFiles no seu recipiente blob. O ficheiro de registo na pasta de registo fornece informações adicionais. Este ficheiro de registo é especialmente útil quando há um erro. Num ambiente de produção, é melhor defini-lo para **o Fracasso.**

Para uma maior resolução de problemas, tome os seguintes passos:


1. Aceda a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicação YARN UI](media/data-factory-spark/yarnui-application.png)

1. Selecione **Registos** para uma das tentativas de execução.

    ![Página de aplicação](media/data-factory-spark/yarn-applications.png)

1. Veja as seguintes informações adicionais de erro na página de registo:

    ![Erro de registo](media/data-factory-spark/yarnui-application-error.png)

As secções seguintes fornecem informações sobre as entidades da fábrica de dados para utilizar a atividade spark cluster e spark na sua fábrica de dados.

## <a name="spark-activity-properties"></a>Propriedades de atividade de faísca
Aqui está a definição JSON de uma amostra de um oleoduto com uma atividade de Faísca:

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

A tabela seguinte descreve as propriedades JSON utilizadas na definição JSON.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | O nome da atividade no oleoduto. | Yes |
| descrição | Texto que descreve o que a atividade faz. | No |
| tipo | Esta propriedade deve ser definida para HDInsightSpark. | Yes |
| linkedServiceName | Nome do serviço ligado hdInsight no qual o programa Spark é executado. | Yes |
| rootPath | O recipiente blob e a pasta que contém o ficheiro Spark. O nome do ficheiro é sensível ao caso. | Yes |
| ingressoFilePata | Caminho relativo para a pasta raiz do código/embalagem Spark. | Yes |
| nome de classeName | A classe principal java/faísca da candidatura. | No |
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | No |
| proxyUser | A conta de utilizador a fazer-se passar para executar o programa Spark. | No |
| sparkConfig | Especifique os valores das propriedades de configuração Spark listadas na [configuração Spark: Propriedades da aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No |
| obterDebugInfo | Especifica quando os ficheiros de registo spark são copiados para o armazenamento utilizado pelo cluster HDInsight (ou) especificado pelo sparkJobLinkedService. Os valores permitidos são Nenhum, Sempre ou Fracasso. O valor predefinido é Nenhum. | No |
| sparkJobLinkedService | O serviço de armazenamento ligado que detém o ficheiro de trabalho spark, dependências e registos. Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster HDInsight é utilizado. | No |

## <a name="folder-structure"></a>Estrutura de pasta
A atividade Spark não suporta um script inline como as atividades de Pig e Hive fazem. Os trabalhos de faísca são também mais extensíveis do que os empregos de Porco/Colmeia. Para trabalhos com Spark, pode fornecer várias dependências, tais como pacotes de frascos (colocados no java CLASSPATH), ficheiros Python (colocados no PYTHONPATH) e quaisquer outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento blob referenciado pelo serviço ligado hdInsight. Em seguida, faça o upload de ficheiros dependentes para as subpastas apropriadas na pasta raiz representada pela **entradaFilePath**. Por exemplo, faça o upload dos ficheiros Python para o sub-dobrador de pyFiles e para os ficheiros de frascos da pasta raiz. No tempo de funcionaamento, o serviço Data Factory espera a seguinte estrutura de pasta no armazenamento do blob:

| Caminho | Descrição | Necessário | Tipo |
| ---- | ----------- | -------- | ---- |
| . | O caminho principal do trabalho spark no serviço ligado ao armazenamento. | Sim | Pasta |
| &lt;utilizador definido &gt; | O caminho que aponta para o ficheiro de entrada do trabalho de Faísca. | Yes | Ficheiro |
| ./frascos | Todos os ficheiros desta pasta são carregados e colocados no classe Java do cluster. | No | Pasta |
| ./pyFiles | Todos os ficheiros desta pasta são carregados e colocados no PYTHONPATH do cluster. | No | Pasta |
| ./ficheiros | Todos os ficheiros desta pasta são carregados e colocados no diretório de funcionamento do executor. | No | Pasta |
| ./arquivos | Todos os ficheiros desta pasta não estão reprimidos. | No | Pasta |
| ./logs | A pasta onde são armazenados os registos do cluster Spark.| No | Pasta |

Aqui está um exemplo para armazenamento que contém dois ficheiros de trabalho Spark no armazenamento blob referenciado pelo serviço ligado hdInsight:

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
