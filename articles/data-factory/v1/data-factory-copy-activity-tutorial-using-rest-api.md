---
title: 'Tutorial: Utilizar a REST API para criar um pipeline do Azure Data Factory | Documentos da Microsoft'
description: Neste tutorial, vai utilizar a API REST para criar um pipeline do Azure Data Factory com uma Atividade de Cópia, para copiar dados de um armazenamento de blobs do Azure e uma base de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d83b659cc04218fad66ea95216e69682b265dc83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077807"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Tutorial: Utilizar a API REST para criar um pipeline do Azure Data Factory para copiar dados 
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-rest-api.md). 

Neste artigo, vai aprender a utilizar a API REST para criar uma fábrica de dados com um pipeline que copia dados de um armazenamento de blobs do Azure para uma base de dados do SQL Azure. Se não estiver familiarizado com o Azure Data Factory, leia o artigo [Introduction to Azure Data Factory](data-factory-introduction.md) (Introdução ao Azure Data Factory) antes de fazer este tutorial.   

Neste tutorial, vai criar um pipeline com uma atividade no mesmo: Atividade de cópia. A Atividade de Cópia copia dados de um arquivo de dados suportado para um arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados suportados como origens e sinks, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados). A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Para obter mais informações sobre a Atividade de Cópia, veja [Data Movement Activities](data-factory-data-movement-activities.md) (Atividades de Movimento de Dados).

Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Multiple activities in a pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Várias atividades num pipeline).

> [!NOTE]
> Este artigo não abrange toda a API REST do Data Factory. Veja [Referência da API REST do Data Factory](/rest/api/datafactory/) para obter a documentação completa sobre os cmdlets do Data Factory.
>  
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Descrição Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua os passos de **pré-requisitos**.
* Instale o [Curl](https://curl.haxx.se/dlwiz/) no seu computador. Utilize a ferramenta Curl com comandos REST para criar uma fábrica de dados. 
* Siga as instruções [neste artigo](../../active-directory/develop/howto-create-service-principal-portal.md) para: 
  1. Criar uma aplicação Web com o nome **ADFCopyTutorialApp** no Azure Active Directory.
  2. Obter o **ID de cliente** e a **chave secreta**. 
  3. Obter o **ID de inquilino**. 
  4. Atribuir a aplicação **ADFCopyTutorialApp** à função **Contribuinte do Data Factory**.  
* Instale o [Azure PowerShell](/powershell/azure/overview).  
* Inicie o **PowerShell** e execute os seguintes passos. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
  
  1. Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Execute o comando seguinte para ver todas as subscrições desta conta:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** ao executar o comando seguinte no PowerShell:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Se o grupo de recursos já existir, especifique se pretende atualizá-lo (Y) ou mantê-lo como está (N). 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Substitua **accountname** e **accountkey** pelo nome e chave da sua conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../../storage/common/storage-account-manage.md#access-keys).

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

Para obter detalhes sobre as propriedades JSON, veja [Azure Storage linked service](data-factory-azure-blob-connector.md#azure-storage-linked-service) (Serviço ligado do Armazenamento do Azure).

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> Substitua **servername**, **databasename**, **username** e **password** pelos nomes do seu servidor SQL do Azure, nome da base de dados SQL, conta de utilizador e palavra-passe da conta.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Para obter detalhes sobre as propriedades JSON, veja [Azure SQL linked service](data-factory-azure-sql-connector.md#linked-service-properties) (Serviço ligado SQL do Azure).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

| Propriedade | Descrição |
|:--- |:--- |
| tipo | O tipo de propriedade é definido como **AzureBlob**, uma vez que os dados residem num armazenamento de blobs do Azure. |
| linkedServiceName | Refere-se ao **AzureStorageLinkedService** que criou anteriormente. |
| folderPath | Especifica o **contentor** de blobs e a **pasta** que contém os blobs de entrada. Neste tutorial, adftutorial é o contentor de blobs e a pasta é a pasta raiz. | 
| fileName | Esta propriedade é opcional. Se omitir esta propriedade, são escolhidos todos os ficheiros em folderPath. Neste tutorial, **emp.txt** é especificado para fileName, pelo que apenas esse ficheiro é escolhido para processamento. |
| formato -> tipo |O ficheiro de entrada está em formato de texto, pelo que utilizamos **TextFormat**. |
| columnDelimiter | As colunas do ficheiro de entrada são delimitadas pelo caráter de **vírgula (`,`)**. |
| frequência/intervalo | A frequência está definida como **Hora** e o intervalo como **1**, o que significa que os setores de entrada estão disponíveis **à hora**. Por outras palavras, o serviço do Data Factory procura dados de entrada a cada hora na pasta raiz do contentor de blobs (**adftutorial**) que especificou. Procura os dados entre as horas de início e fim do pipeline e não antes ou depois.  |
| externo | Esta propriedade está definida como **verdadeira** se os dados de entrada não forem gerados por este pipeline. Os dados de entrada deste tutorial estão no ficheiro empt.txt, que não é gerado por este pipeline, pelo que vamos definir esta propriedade como verdadeira. |

Para obter mais informações sobre estas propriedades JSON, veja [Azure Blob connector](data-factory-azure-blob-connector.md#dataset-properties) (Conector de Blobs do Azure).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

| Propriedade | Descrição |
|:--- |:--- |
| tipo | A propriedade de tipo está definida como **AzureSqlTable**, porque os dados são copiados para uma tabela numa base de dados SQL do Azure. |
| linkedServiceName | Refere-se ao **AzureSqlLinkedService** que criou anteriormente. |
| tableName | Especificou a **tabela** para a qual os dados são copiados. | 
| frequência/intervalo | A frequência está definida como **Hora** e o intervalo é **1**, o que significa que os setores de saída são produzidos **à hora** entre as horas de início e fim do pipeline, não antes ou depois.  |

Existem três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp da base de dados. O ID é uma coluna de identidade, por isso terá de especificar apenas **FirstName** e **LastName** aqui.

Para obter mais informações sobre estas propriedades JSON, veja o artigo [Azure SQL connector](data-factory-azure-sql-connector.md#dataset-properties) (Conector do SQL do Azure).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Tenha em atenção os seguintes pontos:

- Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**. Para obter mais informações sobre a atividade de cópia, veja [Data movement activities](data-factory-data-movement-activities.md) (Atividades de movimento de dados). Nas soluções do Data Factory, também pode utilizar [Data transformation activities](data-factory-data-transformation-activities.md) (Atividades de transformação de dados).
- A entrada da atividade está definida como **AzureBlobInput** e a saída da atividade está definida como **AzureSqlOutput**. 
- Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Para obter uma lista completa dos arquivos de dados que a atividade de cópia suporta como origens e sinks, veja [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (arquivos de dados suportados). Para saber como utilizar um arquivo de dados suportado específico como origem/sink, clique na ligação na tabela.  
 
Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, "2017-02-03", que é equivalente a "2017-02-03T00:00:00Z"
 
Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
 
Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
 
No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.

Para obter descrições das propriedades JSON na definição de um pipeline, veja o artigo [create pipelines](data-factory-create-pipelines.md) (criar pipelines). Para obter descrições das propriedades JSON na definição de uma atividade de cópia, veja [data movement activities](data-factory-data-movement-activities.md) (atividades de movimento de dados). Para obter descrições das propriedades JSON que BlobSource suporta, veja o artigo [Azure Blob connector](data-factory-azure-blob-connector.md) (Conector de Blobs do Azure). Para obter descrições das propriedades JSON que SqlSink suporta, veja o artigo [Azure SQL Database connector](data-factory-azure-sql-connector.md) (Conector da Base de Dados SQL do Azure).

## <a name="set-global-variables"></a>Definir variáveis globais
No Azure PowerShell, execute os seguintes comandos após substituir os valores pelos seus:

> [!IMPORTANT]
> Consulte a secção [Pré-requisitos](#prerequisites) para instruções sobre como obter o ID de cliente, o segredo do cliente, o ID de inquilino e o ID da subscrição.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Execute o comando seguinte depois de atualizar o nome da fábrica de dados que está a utilizar: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Autenticar com o AAD
Execute o seguinte comando para autenticar com o Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Criar fábrica de dados
Neste passo, irá criar uma fábrica de dados do Azure com o nome **ADFCopyTutorialDF**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino. Uma atividade de Hive do HDInsight para executar um script do Hive, de modo a transformar dados de entrada para produzir dados de saída. Execute os seguintes comandos para criar a fábrica de dados: 

1. Atribua o comando à variável com o nome **cmd**. 
   
    > [!IMPORTANT]
    > Confirme que o nome da fábrica de dados que especificar aqui (ADFCopyTutorialDF) corresponde ao nome especificado em **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se a fábrica de dados tiver sido criada com êxito, irá ver o JSON da fábrica de dados em **resultados**; caso contrário, verá uma mensagem de erro.  
   
    ```
    Write-Host $results
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se vir o erro nos resultados: **Nome da fábrica de dados "ADFCopyTutorialDF" não está disponível**, siga os passos abaixo:  
  
  1. Altere o nome (por exemplo, oseunomeADFCopyTutorialDF) no ficheiro **datafactory.json**.
  2. No primeiro comando em que está atribuído um valor à variável **$cmd**, substitua ADFCopyTutorialDF pelo novo nome e execute o comando. 
  3. Execute os dois comandos seguintes para invocar a API REST, para criar a fábrica de dados e imprimir os resultados da operação. 
     
     Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
* Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente: 
  
  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Esta ação regista automaticamente o fornecedor por si.

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Primeiro, crie serviços ligados para ligar os arquivos de dados de origem e de destino ao arquivo de dados. Em seguida, defina conjuntos de dados de entrada e de saída para representar dados nos arquivos de dados ligados. Por fim, crie o pipeline com uma atividade que utiliza estes conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste tutorial, não vai utilizar serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Vai utilizar dois arquivos de dados do tipo Armazenamento do Azure (origem) e Base de Dados SQL do Azure (destino). Portanto, cria dois serviços ligados com o nome AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Esta conta de armazenamento é aquela em que criou um contentor e para a qual carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou a tabela emp nesta base de dados.  

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta secção. Veja [Azure Storage linked service (Serviço ligado de Armazenamento do Azure)](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado de Armazenamento do Azure.  

1. Atribua o comando à variável com o nome **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Execute o comando com **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Veja os resultados. Se o serviço ligado tiver sido criado com êxito, consulte o JSON do serviço ligado em **resultados**; caso contrário, verá uma mensagem de erro.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Criar serviço ligado SQL do Azure
Neste passo, vai ligar a sua base de dados SQL do Azure à fábrica de dados. Especifique o nome do servidor SQL do Azure, o nome da base de dados, o nome de utilizador e a palavra-passe do utilizador nesta secção. Consulte o [Azure SQL linked service (Serviço ligado SQL do Azure)](data-factory-azure-sql-connector.md#linked-service-properties) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado SQL do Azure.

1. Atribua o comando à variável com o nome **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
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
No passo anterior, criou serviços ligados para ligar a sua conta de Armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai definir dois conjuntos de dados, com os nomes AzureBlobInput e AzureSqlOutput, que representam os dados de entrada e saída que estão armazenados nos arquivos de dados referenciados por AzureStorageLinkedService e AzureSqlLinkedService, respetivamente.

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada (AzureBlobInput) especifica o contentor e a pasta que contém os dados de entrada.  

Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída (OututDataset) especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados. 

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome AzureBlobInput que aponta para um ficheiro de blobs (emp.text) na pasta raiz de um contentor de blobs (adftutorial) no Armazenamento do Azure, representado pelo serviço ligado AzureStorageLinkedService. Se não especificar um valor para fileName (ou se o ignorar), os dados de todos os blobs da pasta de entrada são copiados para o destino. Neste tutorial, vai especificar um valor para fileName. 

1. Atribua o comando à variável com o nome **cmd**. 

    ```PowerSHell   
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
O serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. O conjunto de dados da tabela SQL de saída (OututDataset) que vai criar neste passo especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.

1. Atribua o comando à variável com o nome **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
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
Neste passo, vai criar um pipeline com uma **atividade de cópia** que utiliza **AzureBlobInput** como entrada e **AzureSqlOutput** como saída.

Atualmente, é o conjunto de dados de saída que controla a agenda. Neste tutorial, o conjunto de dados de saída está configurado para produzir um setor uma vez por hora. As horas de início e de fim do pipeline têm um dia de diferença, ou seja, 24 horas. Desta forma, o pipeline produz 24 setores de conjuntos de dados de saída. 

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

**Parabéns!** Criou com êxito uma fábrica de dados do Azure, com um pipeline que copia dados do armazenamento de blobs do Azure para a base de dados SQL do Azure.

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
Neste passo, utilize a API REST do Data Factory para monitorizar os setores produzidos pelo pipeline.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Confirme que as horas de início e de fim especificadas no comando seguinte correspondem às horas de início e de fim do pipeline. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Execute o comando Invoke-Command e o seguinte, até ver um setor no estado **Pronto** ou **Falhou**. Quando o setor estiver no estado Pronto, verifique a tabela **emp** na base de dados SQL do Azure para os dados de saída. 

Para cada setor, duas linhas de dados do ficheiro de origem são copiadas para a tabela emp na base de dados SQL do Azure. Por conseguinte, verá 24 novos registos na tabela emp quando todos os setores tiverem sido processados com êxito (no estado Pronto). 

## <a name="summary"></a>Resumo
Neste tutorial, utilizou uma API REST para criar uma fábrica de dados do Azure para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
2. Criou **serviços ligados**:
   1. Um serviço ligado do Armazenamento do Azure para ligar a sua conta de Armazenamento do Azure que contém os dados de entrada.     
   2. Um serviço ligado SQL do Azure para ligar a sua base de dados SQL do Azure que contém os dados de saída. 
3. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
4. Criou um **pipeline** com uma Atividade de Cópia com BlobSource como origem e SqlSink como sink. 

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o armazenamento de blobs do Azure como arquivo de dados de origem e uma base de dados SQL do Azure como arquivo de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber como copiar dados de/para um arquivo de dados, clique na ligação relativa a esse arquivo na tabela.
