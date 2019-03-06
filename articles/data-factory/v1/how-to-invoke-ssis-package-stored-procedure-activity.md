---
title: Invocar pacotes SSIS com o Azure Data Factory - atividade de procedimento armazenado | Documentos da Microsoft
description: Este artigo descreve como invocar um pacote de SQL Server Integration Services (SSIS) a partir de um pipeline de fábrica de dados do Azure com a atividade de procedimento armazenado.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 859cd6cfd3db68dad2607f1dc8905facb43dd290
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453784"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocar um pacote do SSIS com a atividade de procedimento armazenado no Azure Data Factory
Este artigo descreve como invocar um pacote do SSIS de um pipeline do Azure Data Factory através de uma atividade de procedimento armazenado. 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [pacotes de SSIS invocar com a atividade de procedimento armazenado no](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
As instruções neste artigo utilizam uma base de dados SQL do Azure que aloja o catálogo do SSIS. Também pode utilizar uma instância de gerida de base de dados do Azure SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Criar um runtime de integração Azure-SSIS se não tiver uma ao seguir as instruções passo a passo no [Tutorial: Implementar pacotes do SSIS](../tutorial-create-azure-ssis-runtime-portal.md). Não é possível utilizar a versão 1 do Data Factory para criar um runtime de integração Azure-SSIS. 

## <a name="azure-portal"></a>Portal do Azure
Esta secção utiliza o portal do Azure para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Primeiro passo é criar uma fábrica de dados com o portal do Azure. 

1. Navegue para o [portal do Azure](https://portal.azure.com). 
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Veja o artigo [Data Factory – Naming Rules](data-factory-naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.

    `Data factory name ADFTutorialDataFactory is not available`
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos**, siga um destes passos:
     
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
    Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V1** para o **versão**.
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que o Data Factory suporta. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras localizações.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.
8. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

    ![Mosaico “implementar a fábrica de dados”](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
    ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique em **autor e implementar** mosaico para iniciar o Editor do Data Factory.

    ![Editor do Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure
Crie um serviço ligado para ligar a sua base de dados SQL do Azure que aloja o catálogo SSIS à fábrica de dados. Data Factory utiliza informações neste serviço ligado para ligar à base de dados do SSISDB e executa um procedimento armazenado para executar um pacote do SSIS. 

1. No Editor do Data Factory, clique em **novo arquivo de dados** no menu e clique em **base de dados do Azure SQL**. 

    ![Novo arquivo de dados -> SQL Database do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. No painel da direita, siga os passos abaixo:

    1. Substitua `<servername>` com o nome do servidor SQL do Azure. 
    2. Substitua `<databasename>` com **SSISDB** (nome da base de dados do catálogo de SSIS). 
    3. Substitua `<username@servername>` com o nome de utilizador que tem acesso ao servidor SQL do Azure. 
    4. Substitua `<password>` com a palavra-passe do utilizador. 
    5. Implementar o serviço ligado ao clicar o **Deploy** botão na barra de ferramentas. 

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Criar um conjunto de dados fictício para saída
Este conjunto de dados de saída é um conjunto de dados fictício que pauta a agenda do pipeline. Tenha em atenção que a frequência está definida como hora e intervalo está definida como 1. Por conseguinte, o pipeline é executado depois de uma hora no pipeline de começar e terminar vezes. 

1. No painel esquerdo do Editor do Data Factory, clique em **... Obter mais** -> **novo conjunto de dados** -> **SQL do Azure**.

    ![Mais -> novo conjunto de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Copie o seguinte fragmento JSON para o editor de JSON no painel da direita. 
    
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
3. Clique em **Implementar**, na barra de ferramentas. Esta ação implementa o conjunto de dados para o serviço Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Neste passo, vai criar um pipeline com uma atividade de procedimento armazenado. A atividade invoca o procedimento de sp_executesql armazenados para executar o seu pacote do SSIS. 

1. No painel esquerdo, clique em **... Mais** e clique em **Novo pipeline**.
2. Copie o seguinte fragmento JSON para o editor de JSON: 

    > [!IMPORTANT]
    > Substitua &lt;nome da pasta&gt;, &lt;nome do projeto&gt;, &lt;nome do pacote&gt; com nomes de pasta, o projeto e o pacote no catálogo de SSIS antes de guardar o ficheiro.

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
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Clique em **Implementar**, na barra de ferramentas. Esta ação implementa o pipeline para o serviço Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.
A agenda no conjunto de dados de saída é definida como hora a hora. A hora de fim do pipeline é cinco horas a partir da hora de início. Por conseguinte, verá as execuções de pipeline cinco. 

1. Feche as janelas do editor, para que consulte a home page da fábrica de dados. Clique em **monitorizar e gerir** mosaico. 

    ![Mosaico do diagrama](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Atualização do **hora de início** e **hora de fim** para **18/01/2018 08 às 9:30h** e **20/01/2018 08 às 9:30h**e clique em **aplicar**. Deverá ver o **janelas de atividade** associadas com a execução do pipeline. 

    ![Janelas de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Para obter mais informações sobre a monitorização de pipelines, consulte [monitorizar e gerir pipelines do Azure Data Factory com a aplicação de gestão e monitorização](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
Nesta secção utilizar o Azure PowerShell para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
O procedimento seguinte fornece passos para criar uma fábrica de dados. Criar um pipeline com uma atividade de procedimento armazenado nesta fábrica de dados. A atividade de procedimento armazenado executa um procedimento armazenado na base de dados SSISDB para executar o seu pacote do SSIS.

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Por exemplo: `"adfrg"`. 
   
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

5. Para criar a fábrica de dados, execute o seguinte procedimento **New-AzDataFactory** cmdlet, usando a propriedade de Location e ResourceGroupName da variável de $ResGrp: 
    
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
Crie um serviço ligado para ligar a sua base de dados SQL do Azure que aloja o catálogo SSIS à fábrica de dados. Data Factory utiliza informações neste serviço ligado para ligar à base de dados do SSISDB e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um ficheiro JSON com o nome **Azuresqldatabaselinkedservice** na **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;nome de utilizador&gt;@&lt;servername&gt; e &lt;palavra-passe&gt; pelos valores da sua base de dados do SQL do Azure antes de a guardar o ficheiro.

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
2. Na **do Azure PowerShell**, mude para o **C:\ADF\RunSSISPackage** pasta.
3. Executar o **New-AzDataFactoryLinkedService** cmdlet para criar o serviço ligado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Este conjunto de dados de saída é um conjunto de dados fictício que pauta a agenda do pipeline. Tenha em atenção que a frequência está definida como hora e intervalo está definida como 1. Por conseguinte, o pipeline é executado depois de uma hora no pipeline de começar e terminar vezes. 

1. Crie um ficheiro de outputdataset. JSON com o seguinte conteúdo: 
    
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
2. Executar o **New-AzDataFactoryDataset** cmdlet para criar um conjunto de dados. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Neste passo, vai criar um pipeline com uma atividade de procedimento armazenado. A atividade invoca o procedimento de sp_executesql armazenados para executar o seu pacote do SSIS. 

1. Crie um ficheiro JSON com o nome **MyPipeline.json** no **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;nome da pasta&gt;, &lt;nome do projeto&gt;, &lt;nome do pacote&gt; com nomes de pasta, o projeto e o pacote no catálogo de SSIS antes de guardar o ficheiro.

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

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o **New-AzDataFactoryPipeline** cmdlet.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

2. Execute **Get-AzDataFactorySlice** para obter detalhes sobre todos os setores da saída conjunto de dados * *, que é a tabela de saída do pipeline.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Repare que a StartDateTime que especificar aqui é a mesma hora especificada no JSON do pipeline. 
3. Execute **Get-AzDataFactoryRun** para obter os detalhes das execuções de atividades de um setor específico.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Pode continuar a executar este cmdlet até ver o setor no estado **Pronto** ou **Falhou**. 

    Pode executar a consulta seguinte na base de dados SSISDB no seu servidor SQL do Azure para verificar se o pacote executado. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Passos Seguintes
Para obter detalhes sobre a atividade de procedimento armazenado, consulte a [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) artigo.

