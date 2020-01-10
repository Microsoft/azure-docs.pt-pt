---
title: Invocar pacote SSIS usando Azure Data Factory atividade de procedimento armazenado
description: Este artigo descreve como invocar um pacote SQL Server Integration Services (SSIS) de um pipeline de Azure Data Factory usando a atividade de procedimento armazenado.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438788"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocar um pacote do SSIS usando a atividade de procedimento armazenado no Azure Data Factory
Este artigo descreve como invocar um pacote do SSIS de um pipeline Azure Data Factory usando uma atividade de procedimento armazenado. 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [invocar pacotes SSIS usando a atividade de procedimento armazenado no](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
As instruções neste artigo usam um banco de dados SQL do Azure que hospeda o catálogo do SSIS. Você também pode usar um Instância Gerenciada do Banco de Dados SQL do Azure.

### <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Crie um tempo de execução de integração do Azure-SSIS se você não tiver um seguindo as instruções passo a passo no [tutorial: implantar pacotes do SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Você não pode usar Data Factory versão 1 para criar um tempo de execução de integração do Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você usará Azure PowerShell para criar um pipeline de Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
O procedimento a seguir fornece as etapas para criar um data factory. Você cria um pipeline com uma atividade de procedimento armazenado neste data factory. A atividade de procedimento armazenado executa um procedimento armazenado no banco de dados SSISDB para executar seu pacote SSIS.

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../../azure-resource-manager/management/overview.md) com aspas duplas e execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$ResourceGroupName` e execute novamente o comando
2. Para criar o grupo de recursos do Azure, execute o comando abaixo: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se o grupo de recursos já existir, pode não substituí-lo. Atribua outro valor à variável `$ResourceGroupName` e execute novamente o comando. 
3. Defina uma variável para o nome da fábrica de dados. 

    > [!IMPORTANT]
    >  Atualize o nome da fábrica de dados para que seja globalmente exclusivo. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para criar o data factory, execute o seguinte cmdlet **New-AzDataFactory** , usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure
Crie um serviço vinculado para vincular o banco de dados SQL do Azure que hospeda o catálogo do SSIS ao seu data factory. Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um arquivo JSON chamado **AzureSqlDatabaseLinkedService. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;ServerName&gt;, &lt;nome de usuário&gt;@&lt;ServerName&gt; e &lt;senha&gt; com os valores de seu banco de dados SQL do Azure antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. Em **Azure PowerShell**, alterne para a pasta **C:\ADF\RunSSISPackage** .
3. Execute o cmdlet **New-AzDataFactoryLinkedService** para criar o serviço vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Esse conjunto de resultados de saída é um conjunto de uma cópia que orienta o agendamento do pipeline. Observe que a frequência é definida como hora e o intervalo é definido como 1. Portanto, o pipeline é executado uma vez a cada hora nas horas de início e término do pipeline. 

1. Crie um arquivo OutputDataset. JSON com o seguinte conteúdo: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Execute o cmdlet **New-AzDataFactoryDataset** para criar um conjunto de um DataSet. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade invoca o procedimento armazenado sp_executesql para executar o pacote SSIS. 

1. Crie um arquivo JSON chamado **Mypipee. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;nome da pasta&gt;, &lt;nome do projeto&gt;, &lt;nome do pacote&gt; com nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o cmdlet **New-AzDataFactoryPipeline** .

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias do conjunto de informações de saída * *, que é a tabela de saída do pipeline.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Repare que a StartDateTime que especificar aqui é a mesma hora especificada no JSON do pipeline. 
1. Execute **Get-AzDataFactoryRun** para obter os detalhes das execuções de atividade para uma fatia específica.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Pode continuar a executar este cmdlet até ver o setor no estado **Pronto** ou **Falhou**. 

    Você pode executar a consulta a seguir no banco de dados SSISDB no servidor SQL do Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter detalhes sobre a atividade de procedimento armazenado, consulte o artigo [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) .

