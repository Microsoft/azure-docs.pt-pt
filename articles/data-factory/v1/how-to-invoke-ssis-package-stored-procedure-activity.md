---
title: Invocar pacote SSIS usando Azure Data Factory - Atividade do Procedimento Armazenado
description: Este artigo descreve como invocar um pacote SQL Server Integration Services (SSIS) a partir de um oleoduto Azure Data Factory utilizando a Atividade de Procedimento Armazenado.
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
ms.openlocfilehash: c7a99e7e5f27f8c3503c7fa6124d27cfc4e7f4a4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012838"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocar um pacote SSIS utilizando atividade de procedimento armazenado na Azure Data Factory
Este artigo descreve como invocar um pacote SSIS a partir de um oleoduto Azure Data Factory utilizando uma atividade de procedimento armazenado. 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os pacotes Invoke SSIS utilizando a atividade do procedimento armazenado em](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
A passagem por este artigo utiliza a Base de Dados Azure SQL. Também pode utilizar uma Instância Gerida Azure SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Crie um tempo de integração Azure-SSIS se não tiver um seguindo a instrução passo a passo no [Tutorial: Implementar pacotes SSIS](../tutorial-deploy-ssis-packages-azure.md). Não é possível utilizar a versão 1 da Data Factory para criar um tempo de integração Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
Nesta secção utiliza-se a Azure PowerShell para criar um oleoduto Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
O procedimento a seguir fornece medidas para a criação de uma fábrica de dados. Cria-se um oleoduto com uma atividade de procedimento armazenado nesta fábrica de dados. A atividade de procedimento armazenado executa um procedimento armazenado na base de dados SSISDB para executar o seu pacote SSIS.

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

5. Para criar a fábrica de dados, executar o seguinte cmdlet **New-AzDataFactory,** utilizando a propriedade Localização e Grupo de RecursosName a partir da variável $ResGrp: 
    
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
Crie um serviço ligado para ligar a sua base de dados na Base de Dados Azure SQL que hospeda o catálogo SSIS à sua fábrica de dados. A Data Factory utiliza informações neste serviço ligado para ligar à base de dados SSISDB e executa um procedimento armazenado para executar um pacote SSIS. 

1. Criar um ficheiro JSON nomeado **AzureSqlDatabaseLinkedService.jsna** pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt; o nome do &gt; servidor, o nome de servidor de &lt; nome de utilizador e a &gt; @ &lt; &gt; &lt; palavra-passe &gt; pelos valores da sua Base de Dados Azure SQL antes de guardar o ficheiro.

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
2. No **Azure PowerShell,** mude para a pasta **C:\ADF\RunSSISPackage.**
3. Executar o **cmdlet New-AzDataFactoryLinkedService** para criar o serviço ligado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Este conjunto de dados de saída é um conjunto de dados falso que impulsiona o calendário do pipeline. Note que a frequência está definida para hora e o intervalo está definido para 1. Por isso, o gasoduto funciona uma vez por hora dentro dos tempos de início e fim do gasoduto. 

1. Criar uma OutputDataset.jsem ficheiro com o seguinte conteúdo: 
    
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
2. Executar o **cmdlet New-AzDataFactoryDataset** para criar um conjunto de dados. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um oleoduto com atividade de procedimento armazenado 
Neste passo, cria-se um oleoduto com uma atividade de procedimento armazenado. A atividade invoca o procedimento sp_executesql armazenado para executar o seu pacote SSIS. 

1. Criar um ficheiro JSON nomeado **MyPipeline.jsna** pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt; o nome &gt; da pasta, o nome do &lt; &gt; projeto, o nome do pacote &lt; por &gt; nomes de pasta, projeto e embalagem no catálogo SSIS antes de guardar o ficheiro.

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

2. Para criar o oleoduto: **RunSSISPackagePipeline,** executar o cmdlet **New-AzDataFactoryPipeline.**

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

1. Executar **Get-AzDataFactorySlice** para obter detalhes sobre todas as fatias do conjunto de dados de saída***, que é a tabela de saída do pipeline.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Repare que a StartDateTime que especificar aqui é a mesma hora especificada no JSON do pipeline. 
1. Executar **Get-AzDataFactoryRun** para obter os detalhes da atividade funciona para uma fatia específica.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Pode continuar a executar este cmdlet até ver o setor no estado **Pronto** ou **Falhou**. 

    Pode executar a seguinte consulta com a base de dados SSISDB no seu servidor para verificar se a embalagem foi executada. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a atividade do procedimento armazenado, consulte o artigo [de atividade do Procedimento Armazenado.](data-factory-stored-proc-activity.md)