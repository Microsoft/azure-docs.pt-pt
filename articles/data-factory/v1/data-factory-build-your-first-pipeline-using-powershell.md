---
title: Construa a sua primeira fábrica de dados (PowerShell)
description: Neste tutorial, irá criar um exemplo de pipeline do Azure Data Factory com o Azure PowerShell.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 3f388937c43c9c6a2b9e4700768d4af9cdcb39de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543117"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Tutorial: Criar a primeira fábrica de dados do Azure com o Azure PowerShell
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo de gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja o [Início Rápido: criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-powershell.md).

Neste artigo, vai utilizar o Azure PowerShell para criar a sua primeira fábrica de dados do Azure. Para fazer o tutorial com outras ferramentas/SDKs, selecione uma das opções na lista pendente.

O pipeline neste tutorial tem uma atividade: **atividade do HDInsight Hive**. Esta atividade executa um script de ramo de registo num cluster do Azure HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Não copia dados de um de dados de origem para um arquivo de dados de destino. Para um tutorial sobre como copiar dados com o Azure Data Factory, consulte [Tutorial: Copy data from Blob Storage to SQL Database (Tutorial: copiar dados do Armazenamento de Blobs para a Base de Dados SQL)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (agendamento e execução no Data Factory).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**.
* Siga as instruções no artigo [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/) para instalar a versão mais recente do Azure PowerShell no computador.
* (opcional) Este artigo não abrange todos os cmdlets do Data Factory. Veja [Referência dos Cmdlets do Data Factory](/powershell/module/az.datafactory) para obter a documentação completa sobre os cmdlets do Data Factory.

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, utiliza o Azure PowerShell para criar um Azure Data Factory com o nome **FirstDataFactoryPSH**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade Hive do HDInsight para executar um script de Hive para transformar dados de entrada. Comecemos este passo com a criação da fábrica de dados.

1. Inicie o Azure PowerShell e execute o seguinte comando. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
   * Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Execute o comando seguinte para ver todas as subscrições para esta conta.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Esta subscrição deve ser idêntica à que utilizou no Portal do Azure.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup** ao executar o seguinte comando:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Alguns dos passos deste tutorial pressupõe que utiliza o grupo de recursos com o nome ADFTutorialResourceGroup. Se utiliza um grupo de recursos diferente, terá de utilizá-lo em vez de ADFTutorialResourceGroup neste tutorial.
3. Executar o cmdlet **New-AzDataFactory** que cria uma fábrica de dados chamada **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro **Nome "FirstDataFactoryPSH" não disponível para a fábrica de dados**, altere o nome (por exemplo, seunomeFirstDataFactoryPSH). Utilize este nome em vez de ADFTutorialFactoryPSH ao realizar os passos deste tutorial. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
* Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente:

  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Deve criar primeiro os serviços ligados para ligar os arquivos/computações de dados ao seu arquivo de dados, definir os conjuntos de dados de entrada e saída para representar os dados de entrada e saída em arquivos de dados ligados e criar o pipeline com uma atividade que utiliza estes conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai ligar a sua conta de Armazenamento do Azure e um cluster do Azure HDInsight a pedido à fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight serve para executar um script do Hive especificado na atividade do pipeline. Identifique os dados de arquivo de dados/serviços de computação que são utilizados no seu cenário e ligue esses serviços à fábrica de dados, criando serviços ligados.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Utilize a mesma conta de Armazenamento do Azure para armazenar dados de entrada/saída e o ficheiro de script HQL.

1. Crie um ficheiro JSON com o nome StorageLinkedService.json na pasta C:\ADFGetStarted com o seguinte conteúdo. Se ainda não existir, crie a pasta ADFGetStarted.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Substitua o nome da **conta** pelo nome da sua conta de Armazenamento Azure e **chave de conta** pela chave de acesso da conta Azure Storage. Para aprender a obter a chave de acesso ao armazenamento, consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
2. No Azure PowerShell, mude para a pasta ADFGetStarted.
3. Pode utilizar o **cmdlet New-AzDataFactoryLinkedService** que cria um serviço ligado. Este cmdlet e outros cmdlets da Data Factory que utiliza neste tutorial requer que você passe valores para os parâmetros *DataGroupName* e *DataFactoryName.* Em alternativa, pode utilizar **o Get-AzDataFactory** para obter um objeto **DataFactory** e passar o objeto sem digitar *o ResourceGroupName* e *DataFactoryName* cada vez que executar um cmdlet. Executar o seguinte comando para atribuir a saída do cmdlet **Get-AzDataFactory** a uma variável **$df.**

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Agora, gere o **cmdlet New-AzDataFactoryLinkedService** que cria o serviço **de StorageLinkedService** ligado.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Se não tivesse executado o cmdlet **Get-AzDataFactory** e atribuído a saída à variável **$df,** teria de especificar os valores para os parâmetros *Do Nome do Grupo de Recursos* e *dataFactoryName* da seguinte forma.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Se fechar o Azure PowerShell no meio do tutorial, terá de executar o cmdlet **Get-AzDataFactory** da próxima vez que iniciar o Azure PowerShell para completar o tutorial.

### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço ligado do Azure HDInsight
Neste passo, irá ligar um cluster do HDInsight a pedido à sua fábrica de dados. O cluster do HDInsight é criado automaticamente no tempo de execução e eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado. Também pode utilizar o seu próprio cluster do HDInsight em vez de utilizar um cluster do HDInsight a pedido. Veja [Compute Linked Services (Serviços Ligados de Computação)](data-factory-compute-linked-services.md) para obter detalhes.

1. Crie um ficheiro JSON com o nome **HDInsightOnDemandLinkedService**.json na pasta **C:\ADFGetStarted** com o seguinte conteúdo.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

   | Propriedade | Descrição |
   |:--- |:--- |
   | clusterSize |Especifica o tamanho do cluster HDInsight. |
   | timeToLive |Especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
   | linkedServiceName |Especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo HDInsight |

    Tenha em atenção os seguintes pontos:

   * O Data Factory cria um cluster do HDInsight **baseado no Linux** com o JSON. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
   * Você poderia usar **o seu próprio cluster HDInsight** em vez de usar um cluster HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
   * O cluster HDInsight cria um **recipiente predefinido** no armazenamento de bolhas especificado no JSON **(linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com um serviço on-demand HDInsight ligado, um cluster HDInsight é criado sempre que uma fatia é processada, a menos que exista um cluster vivo existente **(timeToLive**). O cluster é eliminado automaticamente quando o processamento é concluído.

       À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes recipientes seguem um padrão: "adf**yourdatafactoryname** - **linkedservicename**-datetimestamp". Utilize ferramentas como o [Microsoft Azure Storage Explorer](https://storageexplorer.com/) para eliminar recipientes no seu armazenamento de bolhas Azure.

     Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
2. Executar o **cmdlet New-AzDataFactoryLinkedService** que cria o serviço ligado chamado HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, irá criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao **StorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. Crie um ficheiro JSON com o nome **InputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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
    O JSON define um conjunto de dados com o nome **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no contentor do blob denominado **adfgetstarted** e na pasta denominada **inputdata**.

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

   | Propriedade | Descrição |
   |:--- |:--- |
   | tipo |O tipo de propriedade é definido para AzureBlob, uma vez que os dados estão contidos no armazenamento de blobs do Azure. |
   | linkedServiceName |Refere-se ao StorageLinkedService que criou anteriormente. |
   | fileName |Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste caso, apenas o input.log é processado. |
   | tipo |Os ficheiros de registo estão no formato de texto, pelo que vamos utilizar TextFormat. |
   | columnDelimiter |As colunas nos ficheiros de registo são delimitadas por vírgula (,). |
   | frequência/intervalo |A frequência definida para o Mês, sendo o intervalo 1, o que significa que os setores de entrada estão disponíveis mensalmente. |
   | externo |Esta propriedade é definida como verdadeira se os dados de entrada não forem gerados pelo serviço Data Factory. |
2. Execute o seguinte comando no Azure PowerShell para criar o conjunto de dados do Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Agora, vai criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. Crie um ficheiro JSON com o nome **OutputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
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
    O JSON define um conjunto de dados com o nome **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, especifica que os dados de saída estão armazenados no contentor do blob denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.
2. Execute o seguinte comando no Azure PowerShell para criar o conjunto de dados do Data Factory:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, irá criar o seu primeiro pipeline com uma atividade **HDInsightHive**. O setor de entrada está disponível mensalmente (frequência: mês, intervalo: 1), o setor de saída é produzido mensalmente e a propriedade do agendador da atividade também está definida como mensal. As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. No final desta secção, encontrará uma explicação sobre as propriedades utilizadas no seguinte JSON.

1. Crie um ficheiro JSON com o nome MyFirstPipelinePSH.json na pasta C:\ADFGetStarted com o seguinte conteúdo:

   > [!IMPORTANT]
   > Substitua o **nome do número de armazenamento** pelo nome da sua conta de armazenamento no JSON.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
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
    No fragmento JSON, está a criar um pipeline que consiste numa única atividade que utiliza o Ramo de Registo para processar Dados num cluster do HDInsight.

    O ficheiro de scripts Da Colmeia, **partitionweblogs.hql,** está armazenado na conta de Armazenamento Azure (especificada pelo scriptLinkedService, chamado **StorageLinkedService),** e na pasta de **scripts** no recipiente **adfgetstared**.

    A secção **define** é utilizada para especificar as definições do tempo de execução que são transmitidas ao script do Hive como valores de configuração do Hive(por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline.

    No JSON de atividade, especifique que o Script de ramo de registo é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Veja "JSON do Pipeline" em [Pipelines and activities in Azure Data Factory (Pipelines e atividades no Azure Data Factory)](data-factory-create-pipelines.md) para obter detalhes das propriedades JSON utilizadas no exemplo.

2. Confirme se vê o ficheiro **input.log** na pasta **adfgetstarted/inputdata** na armazenamento de blobs do Azure e execute o seguinte comando para implementar o pipeline. Uma vez que as horas de **início** e **fim** são definidas no passado e **isPaused** é definido como falso, o pipeline (atividade no pipeline) é executado imediatamente depois da implementação.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Parabéns, criou com êxito o seu primeiro pipeline com o Azure PowerShell!

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o Azure PowerShell para monitorizar os acontecimentos de uma fábrica de dados do Azure.

1. Executar **Get-AzDataFactory** e atribuir a saída a uma variável **$df.**

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Executar **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Repare que a StartDateTime que especificar aqui é a mesma hora especificada no JSON do pipeline. Segue-se o resultado do exemplo:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Executar **Get-AzDataFactoryRun** para obter os detalhes da atividade funciona para uma fatia específica.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Segue-se o resultado do exemplo: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Pode continuar a executar este cmdlet até ver o setor no estado **Pronto** ou **Falhou**. Quando estiver no estado Pronto, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do armazenamento de blobs dos dados de saída.  A criação de um cluster do HDInsight a pedido costuma ser um pouco demorada.

    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Por isso, espere que o gasoduto desemque **aproximadamente 30 minutos** para processar a fatia.
>
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.
>
>

## <a name="summary"></a>Resumo
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o Script de ramo de registo num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no portal do Azure para realizar os seguintes passos:

1. Criou uma **fábrica de dados** do Azure.
2. Criar dois **serviços ligados**:
   1. O serviço ligado do **Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
   2. O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída.
3. Criei dois **conjuntos de dados**, que descrevem dados de entrada e saída para a atividade da Colmeia HDInsight no pipeline.
4. Criar um **pipeline** com uma atividade do **Ramo de Registo do HDInsight**.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um Script de ramo de registo num cluster do Azure HDInsight a pedido. Para ver como utilizar uma Atividade de Cópia para copiar dados de um Blob do Azure para o Azure SQL, veja o [Tutorial: Copiar dados de um Blob do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consulte também

| Tópico | Descrição |
|:--- |:--- |
| [Referência dos Cmdlets do Data Factory](/powershell/module/az.datafactory) |Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e Execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |
