---
title: Criar a primeira fábrica de dados (REST) | Microsoft Docs
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com a API REST do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 7e0a2465-2d85-4143-a4bb-42e03c273097
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 384294dfcd443f0bdbb7a915069d2563bcc35ae4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533890"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Tutorial: Criar a primeira fábrica de dados do Azure com a API REST do Data Factory
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [início rápido: Criar uma fábrica de dados com o Azure Data Factory](../quickstart-create-data-factory-rest-api.md).

Neste artigo, utiliza a API REST do Data Factory para criar a sua primeira fábrica de dados do Azure. Para fazer o tutorial com outras ferramentas/SDKs, selecione uma das opções na lista pendente.

O pipeline neste tutorial tem uma atividade: **Atividade do Hive do HDInsight**. Esta atividade executa um script de ramo de registo num cluster do Azure HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas.

> [!NOTE]
> Este artigo não abrange toda a API REST. Para obter a documentação completa sobre a API REST, veja [Data Factory REST API Reference](/rest/api/datafactory/) (Referência da API REST do Data Factory).
> 
> Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (agendamento e execução no Data Factory).


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**.
* Instale o [Curl](https://curl.haxx.se/dlwiz/) no seu computador. Utilize a ferramenta CURL com comandos REST para criar uma fábrica de dados.
* Siga as instruções [neste artigo](../../active-directory/develop/howto-create-service-principal-portal.md) para:
  1. Criar uma aplicação Web com o nome **ADFGetStartedApp** no Azure Active Directory.
  2. Obter o **ID de cliente** e a **chave secreta**.
  3. Obter o **ID de inquilino**.
  4. Atribuir a aplicação **ADFGetStartedApp** à função para o **Contribuinte do Data Factory**.
* Instale o [Azure PowerShell](/powershell/azure/overview).
* Inicie o **PowerShell** e execute o seguinte comando. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
  1. Execute **Connect-AzAccount** e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão no portal do Azure.
  2. Execute **Get-AzSubscription** para ver todas as subscrições para esta conta.
  3. Executar **Get AzSubscription - SubscriptionName NameOfAzureSubscription | Conjunto AzContext** para selecionar a subscrição que pretende trabalhar com. Substitua **NameOfAzureSubscription** pelo nome da sua subscrição do Azure.
* Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** ao executar o comando seguinte no PowerShell:

    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Alguns dos passos deste tutorial pressupõe que utiliza o grupo de recursos com o nome ADFTutorialResourceGroup. Se utilizar um grupo de recursos diferente, terá de utilizar o nome do grupo de recursos em vez de ADFTutorialResourceGroup neste tutorial.

## <a name="create-json-definitions"></a>Criar definições JSON
Crie os seguintes ficheiros JSON na pasta onde está localizado curl.exe.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> O Nome tem de ser globalmente exclusivo, pelo que poderá querer atribuir um prefixo/sufixo ao ADFCopyTutorialDF para torná-lo um nome exclusivo.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Substitua **accountname** e **accountkey** pelo nome e chave da sua conta de armazenamento do Azure. Para saber como obter a sua chave de acesso ao armazenamento, veja as informações sobre como ver, copiar e regenerar as chaves de acesso de armazenamento em [Gerir a conta do Armazenamento](../../storage/common/storage-account-manage.md#access-keys).
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

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

A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

| Propriedade | Descrição |
|:--- |:--- |
| ClusterSize |O tamanho do cluster do HDInsight. |
| TimeToLive |Especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
| linkedServiceName |Especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo HDInsight |

Tenha em atenção os seguintes pontos:

* O Data Factory cria um cluster do HDInsight **baseado no Linux** com o JSON anterior. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
* Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
* O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que é processado um setor, exceto se houver um cluster em direto (**timeToLive**), que será eliminado no fim do processamento.

    À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](https://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

### <a name="inputdatasetjson"></a>inputdataset.json

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

O JSON define um conjunto de dados com o nome **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no contentor do blob denominado **adfgetstarted** e na pasta denominada **inputdata**.

A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

| Propriedade | Descrição |
|:--- |:--- |
| tipo |O tipo de propriedade é definido para AzureBlob, uma vez que os dados estão contidos no armazenamento de blobs do Azure. |
| linkedServiceName |Refere-se ao StorageLinkedService que criou anteriormente. |
| fileName |Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste caso, apenas o input.log é processado. |
| tipo |Os ficheiros de registo estão no formato de texto, pelo que vamos utilizar TextFormat. |
| columnDelimiter |as colunas nos ficheiros de registo são delimitadas por uma vírgula (,) |
| frequência/intervalo |A frequência definida para o Mês, sendo o intervalo 1, o que significa que os setores de entrada estão disponíveis mensalmente. |
| externo |Esta propriedade é definida como verdadeira se os dados de entrada não forem gerados pelo serviço Data Factory. |

### <a name="outputdatasetjson"></a>outputdataset.json

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

O JSON define um conjunto de dados com o nome **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, especifica que os dados de saída estão armazenados no contentor do blob denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Substitua **storageaccountname** pelo nome da sua conta de armazenamento do Azure.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

No fragmento JSON, está a criar um pipeline que consiste numa única atividade que utiliza o Hive para processar dados num cluster do HDInsight.

O ficheiro do script do Hive **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificado pelo scriptLinkedService, denominado **StorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.

A secção **define** especifica as definições do tempo de execução que são transmitidas ao script de Hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline.

No JSON de atividade, especifique que o Script de ramo de registo é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [!NOTE]
> Veja "JSON do Pipeline" em [Pipelines and activities in Azure Data Factory (Pipelines e atividades no Azure Data Factory)](data-factory-create-pipelines.md) para obter detalhes das propriedades JSON utilizadas no exemplo anterior.
>
>

## <a name="set-global-variables"></a>Definir variáveis globais
No Azure PowerShell, execute os seguintes comandos após substituir os valores pelos seus:

> [!IMPORTANT]
> Consulte a secção [Pré-requisitos](#prerequisites) para instruções sobre como obter o ID de cliente, o segredo do cliente, o ID de inquilino e o ID da subscrição.
>
>

```PowerShell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Autenticar com o AAD

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, irá criar uma fábrica de dados do Azure com o nome **FirstDataFactoryREST**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do HDInsight Hive para executar um script do Hive para transformar dados. Execute os seguintes comandos para criar a fábrica de dados:

1. Atribua o comando à variável com o nome **cmd**.

    Confirme que o nome da fábrica de dados que especificar aqui (ADFCopyTutorialDF) corresponde ao nome especificado em **datafactory.json**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se a fábrica de dados tiver sido criada com êxito, irá ver o JSON da fábrica de dados em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se vir o erro nos resultados: **Nome da fábrica de dados "FirstDataFactoryREST" não está disponível**, siga os passos abaixo:
  1. Altere o nome (por exemplo, oseunomeFirstDataFactoryREST) no ficheiro **datafactory.json**. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
  2. No primeiro comando em que está atribuído um valor à variável **$cmd**, substitua FirstDataFactoryREST pelo novo nome e execute o comando.
  3. Execute os dois comandos seguintes para invocar a API REST, para criar a fábrica de dados e imprimir os resultados da operação.
* Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente:

  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado:
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Comece por criar os serviços ligados para ligar os arquivos/computações de dados ao seu arquivo de dados e definir os conjuntos de dados de entrada e saída para representar os dados em arquivos de dados ligados.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai ligar a sua conta de Armazenamento do Azure e um cluster do Azure HDInsight a pedido à fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight serve para executar um script do Hive especificado na atividade do pipeline.

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Com este tutorial, utilize a mesma conta de Armazenamento do Azure para armazenar os dados de entrada/saída e o ficheiro de script HQL.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o serviço ligado tiver sido criado com êxito, consulte o JSON do serviço ligado em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço ligado do Azure HDInsight
Neste passo, irá ligar um cluster do HDInsight a pedido à sua fábrica de dados. O cluster do HDInsight é criado automaticamente no tempo de execução e eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado. Também pode utilizar o seu próprio cluster do HDInsight em vez de utilizar um cluster do HDInsight a pedido. Veja [Compute Linked Services (Serviços Ligados de Computação)](data-factory-compute-linked-services.md) para obter detalhes.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o serviço ligado tiver sido criado com êxito, consulte o JSON do serviço ligado em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao **StorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Neste passo, vai criar o conjunto de dados de entrada para representar os dados de entrada armazenados no armazenamento de Blobs do Azure.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Neste passo, vai criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de Blobs do Azure.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, irá criar o seu primeiro pipeline com uma atividade **HDInsightHive**. Setor de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), o setor de saída é produzido mensalmente e a propriedade do agendador da atividade também está definida como mensal. As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada.

Confirme se vê o ficheiro **input.log** na pasta **adfgetstarted/inputdata** na armazenamento de blobs do Azure e execute o seguinte comando para implementar o pipeline. Uma vez que as horas de **início** e **fim** são definidas no passado e **isPaused** é definido como falso, o pipeline (atividade no pipeline) é executado imediatamente depois da implementação.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o conjunto de dados tiver sido criado com êxito, consulte o JSON do conjunto de dados em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell
    Write-Host $results
    ```
4. Parabéns, criou com êxito o seu primeiro pipeline com o Azure PowerShell!

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, utilize a API REST do Data Factory para monitorizar os setores produzidos pelo pipeline.

```PowerShell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Assim, prevê-se que o pipeline demore **aproximadamente 30 minutos** a processar o setor.
>
>

Execute o comando Invoke-Command e o seguinte, até ver o setor no estado **Pronto** ou no estado **Falhou**. Quando estiver no estado Pronto, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do armazenamento de blobs dos dados de saída.  A criação de um cluster do HDInsight a pedido costuma ser um pouco demorada.

![dados de saída](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.
>
>

Também pode utilizar o portal do Azure para monitorizar setores e resolver quaisquer problemas. Consulte os detalhes em [Monitorizar pipelines com o portal do Azure](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline).

## <a name="summary"></a>Resumo
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o Script de ramo de registo num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no portal do Azure para realizar os seguintes passos:

1. Criou uma **fábrica de dados** do Azure.
2. Criar dois **serviços ligados**:
   1. O serviço ligado do **Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
   2. O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída.
3. Criar dois **conjuntos de dados**, que descrevem os dados de entrada e de saída da atividade do HDInsight Hive no pipeline.
4. Criar um **pipeline** com uma atividade do **Ramo de Registo do HDInsight**.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um Script de ramo de registo num cluster do Azure HDInsight a pedido. Para ver como utilizar uma atividade de cópia para copiar dados de um Blob do Azure para SQL do Azure, consulte [Tutorial: Copiar dados de um Blob do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Consultar Também
| Tópico | Descrição |
|:--- |:--- |
| [Referência da API REST do Data Factory](/rest/api/datafactory/) |Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e Execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |
