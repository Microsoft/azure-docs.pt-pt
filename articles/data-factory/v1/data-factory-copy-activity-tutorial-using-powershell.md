---
title: 'Tutorial: Criar um pipeline para mover dados com o Azure PowerShell | Documentos da Microsoft'
description: Neste tutorial, vai utilizar o Azure PowerShell para criar um pipeline do Azure Data Factory com a Atividade de Cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7031e003ad05d647ccfaebf9239f26ef0af00a7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110720"
---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: Criar um pipeline do Data Factory que move os dados com o Azure PowerShell
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [tutorial de atividade de cópia](../quickstart-create-data-factory-powershell.md). 

Neste artigo, irá aprender a utilizar o PowerShell para criar uma fábrica de dados com um pipeline que copia dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure. Se não estiver familiarizado com o Azure Data Factory, leia o artigo [Introduction to Azure Data Factory](data-factory-introduction.md) (Introdução ao Azure Data Factory) antes de fazer este tutorial.   

Neste tutorial, vai criar um pipeline com uma atividade no mesmo: Atividade de cópia. A Atividade de Cópia copia dados de um arquivo de dados suportado para um arquivo de dados sink suportado. Para obter uma lista dos arquivos de dados suportados como origens e sinks, veja [Supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Arquivos de dados suportados). A atividade utiliza a tecnologia de um serviço globalmente disponível que pode copiar dados entre vários arquivos de dados de uma forma segura, fiável e dimensionável. Para obter mais informações sobre a Atividade de Cópia, veja [Data Movement Activities](data-factory-data-movement-activities.md) (Atividades de Movimento de Dados).

Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [Multiple activities in a pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Várias atividades num pipeline).

> [!NOTE]
> Este artigo não abrange todos os cmdlets do Data Factory. Veja [Data Factory Cmdlet Reference (Referência aos Cmdlets do Data Factory)](/powershell/module/az.datafactory) para obter a documentação completa sobre estes cmdlets.
> 
> O pipeline de dados neste tutorial copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Para obter um tutorial sobre como transformar dados com o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Conclua os pré-requisitos listados no artigo [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Instale o **Azure PowerShell**. Siga as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

## <a name="steps"></a>Passos
Veja a seguir os passos que deve executar como parte deste tutorial:

1. Crie uma **fábrica de dados** do Azure. Neste passo, irá criar uma fábrica de dados com o nome ADFTutorialDataFactoryPSH. 
1. Crie **serviços ligados** na fábrica de dados. Neste passo, vai criar dois serviços ligados dos tipos: Armazenamento do Azure e Azure base de dados SQL. 
    
    O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou um contentor e carregou dados para esta conta de armazenamento.   

    O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou uma tabela SQL nesta base de dados.   
1. Crie **conjuntos de dados** de entrada e saída na fábrica de dados.  
    
    O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada especifica o contentor e a pasta que contém os dados de entrada.  

    Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados.
1. Crie um **pipeline** na fábrica de dados. Neste passo, vai criar um pipeline com uma atividade de cópia.   
    
    A atividade de cópia copia dados de um blob no armazenamento de blobs do Azure para uma tabela na base de dados SQL do Azure. Pode utilizar uma atividade de cópia num pipeline para copiar dados de qualquer origem suportada para qualquer destino suportado. Para ver uma lista dos arquivos de dados suportados, veja o artigo [Data movement activities](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (Atividades de movimento de dados). 
1. Monitorize o pipeline. Neste passo, irá **monitorizar** os setores dos conjuntos de entrada e saída com o PowerShell.

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
> [!IMPORTANT]
> Conclua os [pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), se ainda não o tiver feito.   

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do Ramo de Registo do HDInsight para executar um Script de Ramo de Registo para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados.

1. Inicie o **Azure PowerShell**. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.

    Execute o comando seguinte e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure:

    ```PowerShell
    Connect-AzAccount
    ```   
   
    Execute o comando seguinte para ver todas as subscrições desta conta:

    ```PowerShell
    Get-AzSubscription
    ```

    Execute o comando seguinte para selecionar a subscrição com a qual pretende trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua subscrição do Azure:

    ```PowerShell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```
1. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup** ao executar o seguinte comando:

    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    Alguns dos passos deste tutorial partem do princípio de que utiliza o grupo de recursos com o nome **ADFTutorialResourceGroup**. Se utiliza um grupo de recursos diferente, terá de utilizá-lo em vez de ADFTutorialResourceGroup neste tutorial.
1. Executar o **New-AzDataFactory** cmdlet para criar uma fábrica de dados com o nome **ADFTutorialDataFactoryPSH**:  

    ```PowerShell
    $df=New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
    Este nome pode já ter sido atribuído. Por conseguinte, que o nome da fábrica de dados exclusivo, adicionando um prefixo ou sufixo (por exemplo: ADFTutorialDataFactoryPSH05152017) e execute o comando novamente.  

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o seguinte erro, altere o nome (por exemplo, oseunomeADFTutorialDataFactory). Utilize este nome em vez de ADFTutorialFactoryPSH ao realizar os passos deste tutorial. Veja [Data Factory - Naming Rules (Data Factory - Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Para criar instâncias do Data Factory, tem de ser contribuidor ou administrador da subscrição do Azure.
* O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
* Poderá receber o erro seguinte: "**Esta subscrição não está registada para utilizar o espaço de nomes DataFactory.** " Efetue um dos seguintes procedimentos e tente publicar novamente:

  * No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Execute o seguinte comando para confirmar que o fornecedor do Data Factory está registado:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Utilize a subscrição do Azure para iniciar sessão no [portal do Azure](https://portal.azure.com). Aceda a um painel do Data Factory ou crie uma fábrica de dados no portal do Azure. Esta ação regista automaticamente o fornecedor por si.

## <a name="create-linked-services"></a>Criar serviços ligados
Os serviços ligados são criados numa fábrica de dados para ligar os seus arquivos de dados e serviços de computação a essa fábrica de dados. Neste tutorial, não vai utilizar serviços de computação, como o Azure HDInsight ou o Azure Data Lake Analytics. Vai utilizar dois arquivos de dados do tipo Armazenamento do Azure (origem) e Base de Dados SQL do Azure (destino). 

Portanto, cria dois serviços ligados com o nome AzureStorageLinkedService e AzureSqlLinkedService dos tipos: AzureStorage e AzureSqlDatabase.  

O AzureStorageLinkedService liga a sua conta do Armazenamento do Azure à fábrica de dados. Esta conta de armazenamento é aquela em que criou um contentor e para a qual carregou os dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

O AzureSqlLinkedService liga a sua base de dados SQL do Azure à fábrica de dados. Os dados copiados do armazenamento de blobs são armazenados nesta base de dados. Como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), criou a tabela emp nesta base de dados. 

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Criar um serviço ligado para uma conta de armazenamento do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados.

1. Crie um ficheiro JSON com o nome **azurestoragelinkedservice. JSON** na **C:\ADFGetStartedPSH** pasta com o seguinte conteúdo: (Se ainda não existir, crie a pasta ADFGetStartedPSH.)

    > [!IMPORTANT]
    > Substitua &lt;accountname&gt; e &lt;accountkey&gt; pelo nome e chave da sua conta de armazenamento do Azure antes de guardar o ficheiro. 

    ```json
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
1. No **Azure PowerShell**, mude para a pasta **ADFGetStartedPSH**.
1. Executar o **New-AzDataFactoryLinkedService** cmdlet para criar o serviço ligado: **AzureStorageLinkedService**. Este cmdlet e os outros cmdlets do Data Factory que utilizar neste tutorial requerem que passe os valores para os parâmetros **ResourceGroupName** e **DataFactoryName**. Em alternativa, pode passar o objeto de DataFactory devolvido pelo cmdlet New-AzDataFactory sem escrever ResourceGroupName e DataFactoryName sempre que executar um cmdlet. 

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\AzureStorageLinkedService.json
    ```
    Segue-se o resultado do exemplo:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ``` 

    Outra forma de criar este serviço ligado é especificar o nome do grupo de recursos e o nome da fábrica de dados, em vez de especificar o objeto DataFactory.  

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName <Name of your data factory> -File .\AzureStorageLinkedService.json
    ```

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Criar um serviço ligado para uma base de dados SQL do Azure
Neste passo, vai ligar a sua base de dados SQL do Azure à fábrica de dados.

1. Crie um ficheiro JSON com o nome AzureSqlLinkedService.json na pasta C:\ADFGetStartedPSH com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;databasename&gt;, &lt;username@servername&gt; e &lt;password&gt; pelos nomes do seu servidor, base de dados, conta de utilizador e palavra-passe do SQL do Azure.
    
    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
1. Execute o seguinte comando para criar um serviço ligado:

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```
    
    Segue-se o resultado do exemplo:

    ```
    LinkedServiceName : AzureSqlLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.LinkedServiceProperties
    ProvisioningState : Succeeded
    ```

   Certifique-se de que a definição **Permitir acesso aos serviços do Azure** está ativada no seu servidor da base de dados SQL do Azure. Para verificar e ativá-la, siga os seguintes passos:

    1. Inicie sessão no [Portal do Azure](https://portal.azure.com)
    1. Clique em **Mais serviços >** à esquerda e clique em **Servidores SQL** na categoria **BASES DE DADOS**.
    1. Selecione o servidor na lista de servidores SQL.
    1. No painel do servidor SQL, clique na ligação **Mostrar configurações da firewall**.
    1. No painel **Definições da firewall**, clique em **ATIVAR** para **Permitir acesso aos serviços do Azure**.
    1. Clique em **Guardar** na barra de ferramentas. 

## <a name="create-datasets"></a>Criar conjuntos de dados
No passo anterior, criou serviços ligados para ligar a sua conta de Armazenamento do Azure e uma base de dados SQL do Azure à fábrica de dados. Neste passo, vai definir dois conjuntos de dados, com os nomes InputDataset e OutputDataset, que representam os dados de entrada e saída que estão armazenados nos arquivos de dados referenciados por AzureStorageLinkedService e AzureSqlLinkedService, respetivamente.

O serviço ligado do armazenamento do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua conta de armazenamento do Azure. Além disso, o conjunto de dados de blobs de entrada (InputDataset) especifica o contentor e a pasta que contém os dados de entrada.  

Do mesmo modo, o serviço ligado Base de Dados SQL do Azure especifica a cadeia de ligação que o serviço Data Factory utiliza no tempo de execução para ligar à sua base de dados SQL do Azure. E o conjunto de dados da tabela SQL de saída (OututDataset) especifica a tabela na base de dados para a qual os dados do armazenamento de blobs vão ser copiados. 

### <a name="create-an-input-dataset"></a>Criar um conjunto de dados de entrada
Neste passo, vai criar um conjunto de dados com o nome InputDataset que aponta para um ficheiro de blobs (emp.text) na pasta raiz de um contentor de blobs (adftutorial) no Armazenamento do Azure, representado pelo serviço ligado AzureStorageLinkedService. Se não especificar um valor para fileName (ou se o ignorar), os dados de todos os blobs da pasta de entrada são copiados para o destino. Neste tutorial, vai especificar um valor para fileName.  

1. Crie um ficheiro JSON com o nome **InputDataset.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "InputDataset",
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
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
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
1. Execute o seguinte comando para criar o conjunto de dados do Data Factory.

    ```PowerShell  
    New-AzDataFactoryDataset $df -File .\InputDataset.json
    ```
    Segue-se o resultado do exemplo:

    ```
    DatasetName       : InputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureBlobDataset
    Policy            : Microsoft.Azure.Management.DataFactories.Common.Models.Policy
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Nesta parte do passo, vai criar um conjunto de dados de saída com o nome **OutputDataset**. Este conjunto de dados aponta para uma tabela SQL na Base de Dados SQL do Azure, representada por **AzureSqlLinkedService**. 

1. Crie um ficheiro JSON com o nome **OutputDataset.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "OutputDataset",
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

    Para obter mais informações sobre estas propriedades JSON, veja [Azure SQL connector](data-factory-azure-sql-connector.md#dataset-properties) (Conector do SQL do Azure).
1. Execute o comando seguinte para criar o conjunto de dados da fábrica de dados.

    ```PowerShell   
    New-AzDataFactoryDataset $df -File .\OutputDataset.json
    ```

    Segue-se o resultado do exemplo:

    ```
    DatasetName       : OutputDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Availability      : Microsoft.Azure.Management.DataFactories.Common.Models.Availability
    Location          : Microsoft.Azure.Management.DataFactories.Models.AzureSqlTableDataset
    Policy            :
    Structure         : {FirstName, LastName}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DatasetProperties
    ProvisioningState : Succeeded
    ```

## <a name="create-a-pipeline"></a>Criar um pipeline
Neste passo, vai criar um pipeline com uma **atividade de cópia** que utiliza **InputDataset** como entrada e **OutputDataset** como saída.

Atualmente, é o conjunto de dados de saída que controla a agenda. Neste tutorial, o conjunto de dados de saída está configurado para produzir um setor uma vez por hora. As horas de início e de fim do pipeline têm um dia de diferença, ou seja, 24 horas. Desta forma, o pipeline produz 24 setores de conjuntos de dados de saída. 


1. Crie um ficheiro JSON com o nome **ADFTutorialPipeline.json** na pasta **C:\ADFGetStartedPSH** com o conteúdo seguinte:

    ```json   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
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
   - A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**. 
   - Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink. Para obter uma lista completa dos arquivos de dados que a atividade de cópia suporta como origens e sinks, veja [supported data stores](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (arquivos de dados suportados). Para saber como utilizar um arquivo de dados suportado específico como origem/sink, clique na ligação na tabela.  
     
     Substitua o valor da propriedade **start** com o dia atual e o valor **end** com o dia seguinte. Pode especificar apenas a parte da data e ignorar a parte da hora em data e hora. Por exemplo, "2016-02-03", que é equivalente a "2016-02-03T00:00:00Z"
     
     Tanto o datetime de início como de fim têm de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. O tempo **end** é opcional, mas iremos utilizá-lo neste tutorial. 
     
     Se não especificar valor da propriedade **end**, esta é calculada como “**início + 48 horas**”. Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**.
     
     No exemplo anterior existem 24 setores de dados, uma vez que estes são produzidos de hora a hora.

     Para obter descrições das propriedades JSON na definição de um pipeline, veja o artigo [create pipelines](data-factory-create-pipelines.md) (criar pipelines). Para obter descrições das propriedades JSON na definição de uma atividade de cópia, veja [data movement activities](data-factory-data-movement-activities.md) (atividades de movimento de dados). Para obter descrições das propriedades JSON que BlobSource suporta, veja o artigo [Azure Blob connector](data-factory-azure-blob-connector.md) (Conector de Blobs do Azure). Para obter descrições das propriedades JSON que SqlSink suporta, veja o artigo [Azure SQL Database connector](data-factory-azure-sql-connector.md) (Conector da base de dados SQL do Azure).
1. Execute o comando seguinte para criar a tabela da fábrica de dados.

    ```PowerShell   
    New-AzDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

    Segue-se o resultado do exemplo: 

    ```
    PipelineName      : ADFTutorialPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    Properties        : Microsoft.Azure.Management.DataFactories.Models.PipelinePropertie
    ProvisioningState : Succeeded
    ```

**Parabéns!** Criou com êxito uma fábrica de dados do Azure, com um pipeline para copiar dados de um armazenamento de blobs do Azure para uma base de dados SQL do Azure. 

## <a name="monitor-the-pipeline"></a>Monitorizar o pipeline
Neste passo, irá utilizar o Azure PowerShell para monitorizar os acontecimentos de uma fábrica de dados do Azure.

1. Substitua &lt;DataFactoryName&gt; pelo nome da sua fábrica de dados e execute **Get-AzDataFactory**e atribua o resultado a uma variável $df.

    ```PowerShell  
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name <DataFactoryName>
    ```

    Por exemplo:
    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH0516
    ```
    
    Em seguida, execute os conteúdos de $df para ver a seguinte saída: 
    
    ```
    PS C:\ADFGetStartedPSH> $df
    
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DataFactoryId     : 6f194b34-03b3-49ab-8f03-9f8a7b9d3e30
    ResourceGroupName : ADFTutorialResourceGroup
    Location          : West US
    Tags              : {}
    Properties        : Microsoft.Azure.Management.DataFactories.Models.DataFactoryProperties
    ProvisioningState : Succeeded
    ```
1. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todos os setores do **OutputDataset**, que é o conjunto de dados de saída do pipeline.  

    ```PowerShell   
    Get-AzDataFactorySlice $df -DatasetName OutputDataset -StartDateTime 2017-05-11T00:00:00Z
    ```

   Esta definição deve corresponder ao valor **Start** no JSON do pipeline. Deve ver 24 setores, um para cada hora, desde as 12:00 do dia atual até as 12:00 do dia seguinte.

   Seguem-se os três setores de exemplo da saída: 

    ``` 
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 11:00:00 PM
    End               : 5/12/2017 12:00:00 AM
    RetryCount        : 0
    State             : Ready
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 9:00:00 PM
    End               : 5/11/2017 10:00:00 PM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0   

    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFTutorialDataFactoryPSH0516
    DatasetName       : OutputDataset
    Start             : 5/11/2017 8:00:00 PM
    End               : 5/11/2017 9:00:00 PM
    RetryCount        : 0
    State             : Waiting
    SubState          : ConcurrencyLimit
    LatencyStatus     :
    LongRetryCount    : 0
    ```
1. Execute **Get-AzDataFactoryRun** para obter os detalhes de atividade é executada para um **específico** setor. Copie o valor de data e hora da saída do comando anterior para especificar o valor para o parâmetro StartDateTime. 

    ```PowerShell  
    Get-AzDataFactoryRun $df -DatasetName OutputDataset -StartDateTime "5/11/2017 09:00:00 PM"
    ```

   Segue-se o resultado do exemplo: 

    ```
    Id                  : c0ddbd75-d0c7-4816-a775-704bbd7c7eab_636301332000000000_636301368000000000_OutputDataset
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH0516
    DatasetName         : OutputDataset
    ProcessingStartTime : 5/16/2017 8:00:33 PM
    ProcessingEndTime   : 5/16/2017 8:01:36 PM
    PercentComplete     : 100
    DataSliceStart      : 5/11/2017 9:00:00 PM
    DataSliceEnd        : 5/11/2017 10:00:00 PM
    Status              : Succeeded
    Timestamp           : 5/16/2017 8:00:33 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy  
    ```

Para obter a documentação completa sobre os cmdlets do Data Factory, veja [Referência dos Cmdlets do Data Factory](/powershell/module/az.datafactory).

## <a name="summary"></a>Resumo
Neste tutorial, vai criar um Azure Data Factory para copiar dados de um blob do Azure para uma base de dados SQL do Azure. Utilizou o PowerShell para criar a fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Eis os passos de alto nível que realizou neste tutorial:  

1. Criou uma **fábrica de dados** do Azure.
1. Criou **serviços ligados**:

   a. Um serviço ligado do **Armazenamento do Azure** para ligar a sua conta de Armazenamento do Azure que contém os dados de entrada.     
   b. Um serviço ligado do **SQL do Azure** para ligar a sua base de dados SQ que contém os dados de saída.
1. Criou **conjuntos de dados** que descrevem dados de entrada e saída para pipelines.
1. Criou um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como a origem e **SqlSink** como o sink.

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, utilizou o armazenamento de blobs do Azure como arquivo de dados de origem e uma base de dados SQL do Azure como arquivo de dados de destino numa operação de cópia. A tabela seguinte disponibiliza uma lista dos arquivos de dados que a atividade de cópia suporta como origens e destinos: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber como copiar dados de/para um arquivo de dados, clique na ligação relativa a esse arquivo na tabela. 

