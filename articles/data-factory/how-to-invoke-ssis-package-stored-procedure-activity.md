---
title: Executar pacote do SSIS com atividade de procedimento armazenado-Azure
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) em um pipeline de Azure Data Factory usando a atividade de procedimento armazenado.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 063728c03c689c2eafec889bdee8276772ae685a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444025"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Executar um pacote do SSIS com a atividade de procedimento armazenado no Azure Data Factory
Este artigo descreve como executar um pacote do SSIS em um pipeline Azure Data Factory usando uma atividade de procedimento armazenado. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
As instruções neste artigo usam um banco de dados SQL do Azure que hospeda o catálogo do SSIS. Você também pode usar um Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Crie um tempo de execução de integração do Azure-SSIS se você não tiver um seguindo as instruções passo a passo no [tutorial: implantar pacotes do SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interface do usuário do Data Factory (portal do Azure)
Nesta seção, você usará Data Factory interface do usuário para criar um pipeline de Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
A primeira etapa é criar um data factory usando o portal do Azure. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. Navegue para o [portal do Azure](https://portal.azure.com). 
3. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
     ![Nome não disponível - erro](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
     Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que o Data Factory suporta. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras localizações.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.
8. No painel, vai ver o mosaico seguinte com o estado **A implementar fábrica de dados**. 

     ![Mosaico “implementar a fábrica de dados”](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
     ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado
Nesta etapa, você usa a interface do usuário do Data Factory para criar um pipeline. Você adiciona uma atividade de procedimento armazenado ao pipeline e a configura para executar o pacote do SSIS usando o procedimento armazenado sp_executesql. 

1. Na página de introdução, clique em **criar pipeline**: 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na caixa de ferramentas **atividades** , expanda **geral**e arraste e solte a atividade de **procedimento armazenado** para a superfície do designer de pipeline. 

    ![Atividade de procedimento armazenado de arrastar e soltar](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Na janela Propriedades da atividade de procedimento armazenado, alterne para a guia **conta SQL** e clique em **+ novo**. Você cria uma conexão com o banco de dados SQL do Azure que hospeda o catálogo do SSIS (banco de dados banco SSIDB). 
   
    ![Botão Novo serviço ligado](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Selecione **banco de dados SQL do Azure** para **tipo**.
    2. Selecione o Azure Integration Runtime **padrão** para se conectar ao banco de dados SQL do Azure que hospeda o banco de dados do `SSISDB`.
    3. Selecione o banco de dados SQL do Azure que hospeda o banco de dados SSISDB para o campo **nome do servidor** .
    4. Selecione **SSISDB** para **nome do banco de dados**.
    5. Para **nome de usuário**, digite o nome do usuário que tem acesso ao banco de dados.
    6. Para **senha**, insira a senha do usuário. 
    7. Teste a conexão com o banco de dados clicando no botão **testar conexão** .
    8. Salve o serviço vinculado clicando no botão **salvar** . 

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Na janela Propriedades, alterne para a guia **procedimento armazenado** da guia **conta SQL** e execute as seguintes etapas: 

    1. Selecione **Editar**. 
    2. Para o campo **nome do procedimento armazenado** , digite `sp_executesql`. 
    3. Clique em **+ novo** na seção **parâmetros de procedimento armazenado** . 
    4. Para o **nome** do parâmetro, insira **stmt**. 
    5. Para o **tipo** do parâmetro, insira **cadeia de caracteres**. 
    6. Para o **valor** do parâmetro, insira a seguinte consulta SQL:

        Na consulta SQL, especifique os valores corretos para os parâmetros **Folder_Name**, **Project_Name**e **package_name** . 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Para validar a configuração do pipeline, clique em **validar** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>** .

    ![Validar o pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publique o pipeline para Data Factory clicando no botão **publicar tudo** . 

    ![Publicar](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorar o pipeline
Nesta seção, você disparará uma execução de pipeline e a monitorará. 

1. Para disparar uma execução de pipeline, clique em **gatilho** na barra de ferramentas e clique em **disparar agora**. 

    ![Acionar agora](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 
3. Mude para o separador **Monitorizar**, no lado esquerdo. Você vê a execução do pipeline e seu status junto com outras informações (como a hora de início da execução). Para atualizar a vista, clique em **Atualizar**.

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Você vê apenas uma execução de atividade, pois o pipeline tem apenas uma atividade (atividade de procedimento armazenado).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Você pode executar a **consulta** a seguir no banco de dados SSISDB no servidor SQL do Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificar execuções de pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Você também pode criar um gatilho agendado para o pipeline para que o pipeline seja executado em um agendamento (por hora, diariamente, etc.). Para obter um exemplo, consulte [criar uma interface do usuário de data factory data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nesta seção, você usará Azure PowerShell para criar um pipeline de Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS. 

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Você pode usar o mesmo data factory que tem o Azure-SSIS IR ou criar um data factory separado. O procedimento a seguir fornece as etapas para criar um data factory. Você cria um pipeline com uma atividade de procedimento armazenado neste data factory. A atividade de procedimento armazenado executa um procedimento armazenado no banco de dados SSISDB para executar seu pacote SSIS. 

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/management/overview.md) com aspas duplas e execute o comando. Por exemplo: `"adfrg"`. 
   
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

5. Para criar o data factory, execute o seguinte cmdlet **set-AzDataFactoryV2** , usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure
Crie um serviço vinculado para vincular o banco de dados SQL do Azure que hospeda o catálogo do SSIS ao seu data factory. Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um arquivo JSON chamado **AzureSqlDatabaseLinkedService. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;ServerName&gt;, &lt;de nome de usuário&gt;e &lt;senha&gt; com os valores de seu banco de dados SQL do Azure antes de salvar o arquivo.

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

3. Execute o cmdlet **set-AzDataFactoryV2LinkedService** para criar o serviço vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade invoca o procedimento armazenado sp_executesql para executar o pacote SSIS. 

1. Crie um arquivo JSON chamado **RunSSISPackagePipeline. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;nome da pasta&gt;, &lt;nome do projeto&gt;, &lt;nome do pacote&gt; com nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o cmdlet **set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Segue-se o resultado do exemplo:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline
Use o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o pipeline. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Monitorizar a execução do pipeline.

Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie/cole o seguinte script na janela do PowerShell e prima ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Criar um gatilho
Na etapa anterior, você chamou o pipeline sob demanda. Você também pode criar um gatilho de agendamento para executar o pipeline em um agendamento (por hora, diariamente, etc.).

1. Crie um arquivo JSON chamado **mytrigger. JSON** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. Em **Azure PowerShell**, alterne para a pasta **C:\ADF\RunSSISPackage** .
3. Execute o cmdlet **set-AzDataFactoryV2Trigger** , que cria o gatilho. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por padrão, o gatilho está no estado parado. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger** . 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Execute o comando a seguir após a próxima hora. Por exemplo, se a hora atual for 3:25 PM UTC, execute o comando às 4 PM UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Você pode executar a consulta a seguir no banco de dados SSISDB no servidor SQL do Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passos seguintes
Você também pode monitorar o pipeline usando o portal do Azure. Para obter as instruções passo a passo, consulte [monitorar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
