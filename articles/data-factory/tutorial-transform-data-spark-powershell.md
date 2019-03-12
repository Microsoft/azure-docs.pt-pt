---
title: Transformar dados com o Spark no Azure Data Factory | Microsoft Docs
description: Este tutorial fornece instruções passo a passo para transformar dados através da Atividade do Spark no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f273237431373aa69423ba244d4e7c509ffe7bfe
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57577114"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformar dados na cloud através da atividade do Spark no Azure Data Factory
Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Data Factory que transforma dados com a Atividade do Spark e um serviço ligado do HDInsight a pedido. Vai executar os seguintes passos neste tutorial:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e implementar serviços ligados.
> * Criar e implementar um pipeline. 
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Conta de Armazenamento do Azure**. Tem de criar um script Python e um ficheiro de entrada, e carregá-los para o armazenamento do Azure. A saída do programa Spark é armazenada nesta conta de armazenamento. O cluster do Spark a pedido utiliza a mesma conta de armazenamento como o respetivo armazenamento primário.  
* **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Carregar o script Python para a conta de Armazenamento de Blobs
1. Crie um ficheiro Python com o nome **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Substitua **&lt;storageAccountName&gt;** pelo nome da sua conta de Armazenamento do Azure. Em seguida, guarde o ficheiro. 
3. No Armazenamento de Blobs do Azure, crie um contentor com o nome **adftutorial**, caso ainda não exista. 
4. Crie uma pasta com o nome **spark**.
5. Crie uma subpasta com o nome **script** na pasta **spark**. 
6. Carregue o ficheiro **WordCount_Spark.py** para a subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o ficheiro de entrada
1. Crie um ficheiro com o nome **minecraftstory.txt** com algum texto. O programa Spark conta o número de palavras neste texto. 
2. Crie uma subpasta com o nome `inputfiles` na pasta `spark`. 
3. Carregue o ficheiro `minecraftstory.txt` para a subpasta `inputfiles`. 

## <a name="author-linked-services"></a>Criar serviços ligados
Nesta secção, vai criar dois Serviços Ligados: 
    
- Um Serviço Ligado do Armazenamento do Azure que liga uma conta de Armazenamento do Azure à fábrica de dados. Este armazenamento é utilizado pelo cluster do HDInsight a pedido. Também contém o script Spark que vai ser executado. 
- Um Serviço Ligado do HDInsight a Pedido. O Azure Data Factory cria automaticamente um cluster do HDInsight, executa o programa Spark e, em seguida, elimina o cluster do HDInsight depois de estar inativo durante um período de tempo pré-configurado. 

### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
Crie um ficheiro JSON com o seu editor preferencial, copie a seguinte definição JSON de um serviço ligado do Armazenamento do Azure e, em seguida, guarde o ficheiro como **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
Atualize os parâmetros &lt;storageAccountName&gt; e &lt;storageAccountKey&gt; com o nome e a chave da sua conta de Armazenamento do Azure. 


### <a name="on-demand-hdinsight-linked-service"></a>Serviço Ligado do HDInsight a pedido
Crie um ficheiro JSON com o seu editor preferencial, copie a seguinte definição JSON de um serviço ligado do Azure HDInsight e guarde o ficheiro como **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Atualize os valores para as seguintes propriedades na definição de serviço ligado: 

- **hostSubscriptionId**. Substitua &lt;subscriptionID&gt; pelo ID da sua subscrição do Azure. O cluster do HDInsight a pedido é criado nesta subscrição. 
- **tenant**. Substitua &lt;tenantID&gt; pelo ID do inquilino do Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Substitua &lt;servicePrincipalID&gt; e &lt;servicePrincipalKey&gt; pelo ID e chave do seu principal de serviço no Azure Active Directory. Este principal de serviço tem de ser membro da função de Contribuinte da subscrição ou do Grupo de recursos no qual o cluster é criado. Veja [Criar uma aplicação e um principal de serviço do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md) para obter mais detalhes. 
- **clusterResourceGroup**. Substitua &lt;resourceGroupOfHDICluster&gt; pelo nome do grupo de recursos no qual o cluster do HDInsight tem de ser criado. 

> [!NOTE]
> O Azure HDInsight tem limitação do número total de núcleos que pode utilizar em cada região do Azure que suporta. Para o Serviço Ligado do HDInsight a Pedido, o cluster do HDInsight será criado na mesma localização do Armazenamento do Azure utilizado como o respetivo armazenamento primário. Certifique-se de que tem um número suficiente de quotas de núcleo para o cluster ser criado com êxito. Para obter mais informações, veja [Configurar clusters no HDInsight com o Hadoop, Spark, Kafka e muito mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Criar um pipeline 
Neste passo, vai criar um novo pipeline com uma atividade do Spark. A atividade utiliza o exemplo de **contagem de palavras**. Transfira os conteúdos a partir desta localização, caso ainda não o tenha feito.

Crie um ficheiro JSON no seu editor preferencial, copie a seguinte definição JSON de uma definição de pipeline e guarde-o como **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Tenha em atenção os seguintes pontos: 

- rootPath aponta para a pasta spark do contentor adftutorial. 
- entryFilePath aponta para o ficheiro WordCount_Spark.py na subpasta script da pasta spark. 


## <a name="create-a-data-factory"></a>Criar uma fábrica de dados 
Criou definições de serviço ligado e de pipeline em ficheiros JSON. Agora, vamos criar uma fábrica de dados e implementar os ficheiros JSON do serviço ligado e do pipeline através de cmdlets do PowerShell. Execute os seguintes comandos do PowerShell um a um: 

1. Defina as variáveis uma a uma.

    **Nome do Grupo de Recursos**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Nome do Data Factory. Tem de ser globalmente exclusivo** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Nome do Pipeline**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste início rápido. Se o fechar e reabrir, terá de executar os comandos novamente. Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
        
    ```powershell
    Connect-AzAccount
    ```        
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```powershell
    Get-AzSubscription
    ```
    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **SubscriptionId** pelo ID da sua subscrição do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Crie o grupo de recursos: ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Crie a fábrica de dados. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Execute o seguinte comando para ver a saída: 

    ```powershell
    $df
    ```
5. Mude para a pasta onde criou os ficheiros JSON e execute o seguinte comando para implementar o serviço ligado do Armazenamento do Azure: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Execute o seguinte comando implementar um serviço ligado do Spark a pedido: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Execute o seguinte comando para implementar um pipeline: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Iniciar e monitorizar uma execução de pipeline  

1. Iniciar uma execução de pipeline. Também captura o ID de execução do pipeline para monitorização futura.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Execute o script seguinte para verificar continuamente o estado de execução do pipeline até terminar.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Eis o resultado da execução de exemplo: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Confirme que uma pasta com o nome `outputfiles` é criada na pasta `spark` do contentor adftutorial com a saída do programa spark. 


## <a name="next-steps"></a>Passos Seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Aprendeu a: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e implementar serviços ligados.
> * Criar e implementar um pipeline. 
> * Iniciar uma execução de pipeline.
> * Monitorizar a execução do pipeline.

Avance para o tutorial seguinte para saber como transformar dados executando o script Hive num cluster do Azure HDInsight que se encontra numa rede virtual. 

> [!div class="nextstepaction"]
> [Tutorial: transformar dados com o Hive na Rede Virtual do Azure](tutorial-transform-data-hive-virtual-network.md).





