---
title: Criar a primeira fábrica de dados (portal do Azure) | Microsoft Docs
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com o Editor do Data Factory no portal do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 11e92b4c6b8799cde489369a202f8f7c8c05ca6c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535999"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Tutorial: Criar a primeira fábrica de dados com o portal do Azure
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory, que está geralmente disponível. Se utilizar a versão atual do serviço Data Factory, veja [início rápido: Criar uma fábrica de dados com o Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste artigo, ficará a saber como utilizar o [portal do Azure](https://portal.azure.com/) para criar a sua primeira fábrica de dados. Para fazer o tutorial com outras ferramentas/SDKs, selecione uma das opções na lista pendente. 

O pipeline neste tutorial tem uma atividade: uma atividade do Azure HDInsight Hive. Esta atividade executa um script do Hive num cluster do HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Para obter um tutorial sobre como copiar dados com o Data Factory, veja [Tutorial: Copiar dados de armazenamento de Blobs do Azure para a base de dados do Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Pré-requisitos
Leia a [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) e siga os passos na secção “Pré-requisitos”.

Este artigo não fornece uma descrição geral conceptual do serviço Data Factory. Para obter mais informações sobre o serviço, leia a [Introdução ao Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Um exemplo é uma atividade Copiar que copia dados de uma origem para um arquivo de dados de destino. Outro exemplo é uma atividade do HDInsight Hive que executa um script do Hive para transformar os dados de entrada para produzir os dados de saída. 

Para criar uma fábrica de dados, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Novo** > **Dados + Análise** > **Data Factory**.

   ![Criar painel](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. No painel **Nova fábrica de dados**, em **Nome**, introduza **GetStartedDF**.

   ![Painel Nova fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > O nome da fábrica de dados tem de ser globalmente exclusivo. Se receber o erro «O nome da fábrica de dados “GetStartedDF” não está disponível», altere o nome da fábrica de dados. Por exemplo, utilize oseunomeGetStartedDF e crie a fábrica de dados novamente. Para obter mais informações sobre regras de nomenclatura, consulte [fábrica de dados: Regras de nomenclatura](data-factory-naming-rules.md).
   >
   > O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
   >
   >
1. Em **Subscrição**, selecione a subscrição do Azure onde pretende que seja criada a fábrica de dados.

1. Selecione um grupo de recursos existente ou crie um grupo de recursos. Para este tutorial, crie um grupo de recursos com o nome **ADFGetStartedRG**.

1. Em **Localização**, selecione uma localização para a fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.

1. Selecione a caixa de verificação **Afixar ao dashboard**.

1. Selecione **Criar**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) ao nível da subscrição/grupo de recursos.
   >
   >
1. No dashboard, é apresentado o mosaico seguinte, com o estado **A Implementar o Data Factory**:    

   ![Estado A implementar o Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Depois de ter criado a fábrica de dados, é apresentada a página da **fábrica de dados** e o respetivo conteúdo.     

    ![Painel Fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de criar um pipeline na fábrica de dados, deve primeiro criar algumas entidades da fábrica de dados. Em primeiro lugar, crie serviços ligados para ligar arquivos/computações de dados ao seu arquivo de dados. Em seguida, defina conjuntos de dados de entrada e de saída para representar dados de entrada/saída nos arquivos de dados ligados. Por fim, crie o pipeline com uma atividade que utiliza estes conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai ligar a sua conta de Armazenamento do Azure e um cluster do HDInsight a pedido à fábrica de dados. A conta de armazenamento possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight serve para executar um script do Hive especificado na atividade do pipeline. Identifique o [arquivo de dados](data-factory-data-movement-activities.md)/[serviços de computação](data-factory-compute-linked-services.md) que são utilizados no seu cenário. Em seguida, ligue esses serviços à fábrica de dados, criando serviços ligados.  

### <a name="create-a-storage-linked-service"></a>Criar um serviço ligado ao Armazenamento
Neste passo, vai ligar a sua conta de armazenamento à fábrica de dados. Neste tutorial, vai utilizar a mesma conta de armazenamento para guardar os dados de entrada/saída e o ficheiro de script HQL.

1. N painel **Fábrica de dados** de **GetStartedDF**, selecione **Criar e implementar**. Será apresentado o Editor do Data Factory.

   ![Mosaico Criar e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Selecione **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Painel Novo arquivo de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Verá o script JSON para criar um serviço ligado de Armazenamento no editor.

   ![Serviço ligado de armazenamento](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua a **chave da conta** pela chave de acesso da sua conta de armazenamento. Para saber como obter a sua chave de acesso ao armazenamento, veja como visualizar, copiar e regenerar chaves de acesso ao armazenamento em [Gerir a conta de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

    ![Botão Implementar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Depois de o serviço ligado ser implementado com êxito, a janela Rascunho-1 desaparece. Verá **AzureStorageLinkedService** na vista de árvore à esquerda.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Criar um serviço ligado do HDInsight
Neste passo, irá ligar um cluster do HDInsight a pedido à sua fábrica de dados. O cluster do HDInsight é criado automaticamente no runtime. Depois de ter sido processado e ficado inativo pelo período de tempo especificado, o cluster é eliminado.

1. No Editor do Data Factory, selecione **Mais** > **Nova computação** > **Cluster do HDInsight a pedido**.

    ![Nova computação](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1. O fragmento JSON descreve as propriedades que são utilizadas para criar o cluster do HDInsight a pedido.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    A tabela seguinte fornece descrições das propriedades JSON utilizadas no fragmento.

   | Propriedade | Descrição |
   |:--- |:--- |
   | clusterSize |Especifica o tamanho do cluster HDInsight. |
   | timeToLive | Especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
   | linkedServiceName | Especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo HDInsight. |

    Tenha em atenção os seguintes pontos:

     a. A fábrica de dados cria um cluster do HDInsight baseado no Linux, com as propriedades JSON. Para obter mais informações, veja [Serviço ligado do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Pode utilizar o seu próprio cluster do HDInsight, em vez de utilizar um cluster do HDInsight a pedido. Para obter mais informações, veja [Serviço ligado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. O cluster do HDInsight cria um contentor predefinido no armazenamento de blobs especificado na propriedade JSON (**linkedServiceName**). O HDInsight não elimina este contentor quando o cluster é eliminado. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que é processado um setor, exceto se houver um cluster em direto (**timeToLive**). O cluster é eliminado automaticamente quando o processamento é concluído.

     À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: “adf**nomedasuafábricadedados**-**nomedoserviçoligado**-carimbodedataehora”. Utilize ferramentas como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/) para eliminar contentores do armazenamento de blobs.

     Para obter mais informações, veja [Serviço ligado do HDInsight a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Selecione **Implementar** na barra de comandos para implementar o serviço ligado.

    ![Opção Implementar](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Confirme se vê **AzureStorageLinkedService** e **HDInsightOnDemandLinkedService** na vista de árvore à esquerda.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao AzureStorageLinkedService que criou anteriormente neste tutorial. O serviço ligado aponta para uma conta de armazenamento. Os conjuntos de dados especificam o contentor, a pasta e o nome do ficheiro no armazenamento que contém os dados de entrada e saída.   

### <a name="create-the-input-dataset"></a>Criar o conjunto de dados de entrada
1. No Editor do Data Factory, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

    ![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1. No fragmento JSON, crie um conjunto de dados com o nome **AzureBlobInput**, que representa os dados de entrada de uma atividade no pipeline. Além disso, especifique que os dados de entrada estão no contentor de blobs com o nome **adfgetstarted** e na pasta com o nome **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    A tabela seguinte fornece descrições das propriedades JSON utilizadas no fragmento.

   | Propriedade | Descrição |
   |:--- |:--- |
   | tipo |O tipo de propriedade está definido como **AzureBlob**, porque os dados estão contidos no armazenamento de blobs. |
   | linkedServiceName |Refere-se ao AzureStorageLinkedService que criou anteriormente. |
   | folderPath | Especifica o contentor de blobs e a pasta que contém os blobs de entrada. | 
   | fileName |Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste tutorial, apenas o ficheiro input.log é processado. |
   | tipo |Os ficheiros de registo estão no formato de texto, pelo que utilizamos **TextFormat**. |
   | columnDelimiter |As colunas nos ficheiros de registo são delimitadas por vírgula (`,`). |
   | frequência/intervalo |A frequência está definida para **Mês**, sendo o intervalo **1**, o que significa que os setores de entrada estão disponíveis mensalmente. |
   | externo | Esta propriedade é definida como **verdadeira** se os dados de entrada não forem gerados por este pipeline. Neste tutorial, o ficheiro input.log não é gerado por este pipeline, por isso definimos a propriedade como **verdadeira**. |

    Para obter mais informações sobre estas propriedades JSON, veja [Conector de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties).

1. Selecione **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado. Verá o conjunto de dados na vista de árvore à esquerda.

### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Agora, crie o conjunto de dados de saída para representar os dados de saída guardados no armazenamento de blobs.

1. No Editor do Data Factory, selecione **Mais** > **Novo conjunto de dados** > **Armazenamento de Blobs do Azure**.

1. Copie e cole o fragmento seguinte na janela Rascunho-1. No fragmento JSON, crie um conjunto de dados com o nome **AzureBlobOutput** para especificar a estrutura dos dados que são produzidos pelo script do Hive. Especifique também que os resultados são armazenados no contentor de blobs com o nome **adfgetstarted** e na pasta com o nome **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Para obter descrições destas propriedades, veja a secção “Criar o conjunto de dados de entrada”. Não defina a propriedade externa num conjunto de dados de saída, pois o conjunto de dados é produzido pelo serviço Data Factory.

1. Selecione **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado.

1. Verifique se o conjunto de dados foi criado com êxito.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar o seu primeiro pipeline com uma atividade do HDInsight Hive. O setor de entrada está disponível mensalmente (a frequência é Mês, o intervalo é 1). O setor de saída é produzido mensalmente. A propriedade do agendador da atividade também está definida para mensal. As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados de saída. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. No final desta secção, encontrará uma explicação sobre as propriedades utilizadas no seguinte fragmento JSON.

1. No Editor do Data Factory, selecione **Mais** > **Novo pipeline**.

    ![Opção Novo pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Copie e cole o fragmento seguinte na janela Rascunho-1.

   > [!IMPORTANT]
   > Substitua **storageaccountname** pelo nome da sua conta de armazenamento no fragmento JSON.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    No fragmento JSON, vai criar um pipeline que consiste numa única atividade que utiliza o Hive para processar dados num cluster do HDInsight.

    O ficheiro de script do Hive **partitionweblogs.hql** é armazenado na conta de armazenamento, que é especificada pelo scriptLinkedService, que tem o nome **AzureStorageLinkedService1**. Pode encontrá-lo na pasta de **scripts** do contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições de runtime que são transmitidas ao script do Hive como valores de configuração do Hive. Os exemplos são ${hiveconf:inputtable} e ${hiveconf}.

    As propriedades de **início** e **fim** do pipeline especificam o período ativo do pipeline.

    No JSON de atividade, especifique que o script do Hive é executado na computação especificada pelo **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Para obter mais informações sobre as propriedades JSON utilizadas no exemplo, veja a secção “JSON do Pipeline” em [Pipelines e atividades no Data Factory](data-factory-create-pipelines.md).
   >
   >
1. Confirme se:

   a. O ficheiro **input.log** existe na pasta **inputdata** do contentor **adfgetstarted** no armazenamento de blobs.

   b. O ficheiro **partitionweblogs.hql** existe na pasta **script** do contentor **adfgetstarted** no armazenamento de blobs. Se não vir estes ficheiros, siga os passos na secção “Pré-requisitos” na [Descrição geral do tutorial](data-factory-build-your-first-pipeline.md).

   c. Substituiu **storageaccountname** pelo nome da sua conta de armazenamento no pipeline JSON.

1. Selecione **Implementar** na barra de comandos para implementar o pipeline. Uma vez que as horas de **início** e **fim** estão definidas no passado e **isPaused** está definido como **falso**, o pipeline (atividade no pipeline) é executado imediatamente depois de implementá-lo.

1. Verifique se vê o pipeline na vista de árvore.

    ![Vista de árvore com o pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Monitorizar um pipeline
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Monitorizar um pipeline com a vista de Diagrama
1. No painel **Fábrica de dados**, selecione **Diagrama**.

    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. Na Vista **Diagrama**, verá uma descrição geral dos pipelines e conjuntos de dados utilizados neste tutorial.

    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Para ver todas as atividades do pipeline, clique com o botão direito do rato no pipeline no diagrama e selecione **Abrir pipeline**.

    ![Menu Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Confirme se vê **Atividade do Hive** no pipeline.

    ![Vista Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Para voltar à vista anterior, selecione **Fábrica de dados** no menu na parte superior.

1. Na vista **Diagrama**, faça duplo clique no conjunto de dados **AzureBlobInput**. Verifique se o setor está no estado **Pronto**. Poderá demorar alguns minutos até o setor aparecer como **Pronto**. Se não aparecer ao fim de um certo tempo, verifique se colocou o ficheiro de entrada (**input.log**) no contentor (**adfgetstarted**) e na pasta (**inputdata**) adequados.

   ![Setor de entrada no estado Pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Feche o painel **AzureBlobInput**.

1. Na Vista **Diagrama**, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.

   ![Processamento de conjunto de dados em curso](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. Depois de o processamento estar concluído, verá o setor no estado **Pronto**.

   ![Conjunto de dados no estado Pronto](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > A criação de um cluster do HDInsight a pedido demora aproximadamente 20 minutos. Prevê-se que o pipeline demore aproximadamente 30 minutos a processar o setor.
   >
   >

1. Quando estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do armazenamento de blobs dos dados de saída.  

   ![Dados de saída](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Selecione o setor para ver mais informações sobre o mesmo num painel **Setor de dados**.

    ![Informações de Setor de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Na lista **Execuções de atividades**, selecione uma execução de atividade para ver mais informações sobre a mesma. (Neste cenário, é uma atividade do Hive.) As informações são apresentadas num painel **Detalhes da execução da atividade**.   

    ![Janela Detalhes da execução da atividade](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Nos ficheiros de registo, pode ver a consulta do Hive que foi executada e as informações sobre o estado. Estes registos são úteis para resolver eventuais problemas.
   Para obter mais informações, veja [Monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (**input.log**) para a pasta **inputdata** do contentor **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Monitorizar um pipeline com a aplicação Monitorizar e Gerir
Pode utilizar a aplicação Monitorizar e Gerir para monitorizar os seus pipelines. Para obter mais informações sobre como utilizar esta aplicação, veja [Monitorizar e gerir pipelines do Data Factory com a aplicação Monitorizar e Gerir](data-factory-monitor-manage-app.md).

1. Selecione o mosaico **Monitorizar e Gerir** na home page da fábrica de dados.

    ![Mosaico Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. Na aplicação Monitorizar e Gerir, altere a **Hora de início** e a **Hora de fim** para corresponderem às horas de início e fim do seu pipeline. Selecione **Aplicar**.

    ![Aplicação Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Selecione uma janela de atividades na lista **Janelas de Atividades** para ver as respetivas informações.

    ![Lista Janelas de Atividades](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Resumo
Neste tutorial, criou uma fábrica de dados para processar dados, ao executar o script do Hive num cluster de Hadoop do HDInsight. Utilizou o Editor do Data Factory no portal do Azure para o seguinte:  

* Criar uma fábrica de dados.
* Criar dois serviços ligados:
   * O serviço ligado de Armazenamento para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída à fábrica de dados.
   * Um serviço ligado do Azure HDInsight a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída.
* Criar dois conjuntos de dados, que descrevem os dados de entrada e de saída de uma atividade do HDInsight Hive no pipeline.
* Criar um pipeline com uma atividade do HDInsight Hive.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script do Hive num cluster do HDInsight a pedido. Para ver como utilizar uma atividade de cópia para copiar dados de armazenamento de BLOBs para base de dados SQL, consulte [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo de aplicação do Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação Monitorizar e Gerir](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a aplicação Monitorizar e Gerir. |
