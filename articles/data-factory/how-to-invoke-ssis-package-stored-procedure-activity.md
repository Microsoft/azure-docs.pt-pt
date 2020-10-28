---
title: Executar pacote SSIS com atividade de procedimento armazenado - Azure
description: Este artigo descreve como executar um pacote SQL Server Integration Services (SSIS) num oleoduto Azure Data Factory utilizando a Atividade de Procedimento Armazenado.
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
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6cb3bf53db14c6c01290abea7a5c48c332a07632
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634883"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Run an SSIS package with the Stored Procedure activity in Azure Data Factory (Executar um pacote do SSIS com a atividade Procedimento Armazenado no Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SSIS num oleoduto Azure Data Factory utilizando uma atividade do Procedimento Armazenado. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
A passagem por este artigo utiliza a Base de Dados Azure SQL para acolher o catálogo SSIS. Também pode utilizar a Azure SQL Managed Instance.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Crie um tempo de integração Azure-SSIS se não tiver um seguindo a instrução passo a passo no [Tutorial: Implementar pacotes SSIS](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory UI (portal Azure)
Nesta secção, utiliza uI da Data Factory para criar um oleoduto data factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
O primeiro passo é criar uma fábrica de dados utilizando o portal Azure. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome** . Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. Navegue até ao [portal Azure.](https://portal.azure.com) 
3. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory** . 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados** , introduza **ADFTutorialDataFactory** no **nome** . 
      
     ![Página Nova fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome da fábrica de dados Azure deve ser **globalmente único.** Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
     ![Nome não disponível - erro](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos** , siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
     Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/management/overview.md).  
4. Selecione **V2** para a **versão** .
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que o Data Factory suporta. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras localizações.
6. Selecione **Afixar ao dashboard** .     
7. Clique em **Criar** .
8. No painel de instrumentos, vê-se o seguinte azulejo com estado: **Implantação da fábrica de dados** . 

     ![Mosaico “implementar a fábrica de dados”](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory** , conforme mostrado na imagem.
   
     ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um oleoduto com atividade de procedimento armazenado
Neste passo, você usa a UI data factory para criar um oleoduto. Adicione uma atividade de procedimento armazenada ao gasoduto e configuure-a para executar a embalagem SSIS utilizando o procedimento sp_executesql armazenado. 

1. Na página get start, clique em **Criar pipeline** : 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na caixa de **ferramentas Atividades,** expanda a atividade do **Procedimento Armazenado** **Geral** e drag-drop para a superfície do designer de gasodutos. 

    ![Atividade do procedimento armazenado de arrasto e queda](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Na janela de propriedades para a atividade de procedimento armazenado, mude para o separador **Conta SQL** e clique **em + Novo** . Cria uma ligação à base de dados na Base de Dados Azure SQL que acolhe o Catálogo SSIS (base de dados SSIDB). 
   
    ![Botão Novo serviço ligado](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Na janela **Novo Serviço Ligado** , siga os passos abaixo: 

    1. Selecione **Azure SQL Database** for **Type** .
    2. Selecione o tempo de execução de integração Azure **padrão** para ligar à Base de Dados Azure SQL que acolhe a `SSISDB` base de dados.
    3. Selecione a Base de Dados Azure SQL que acolhe a base de dados SSISDB para o campo **de nomes do Servidor.**
    4. Selecione **SSISDB** para **o nome da base de dados** .
    5. Para **o nome de utilizador,** insira o nome do utilizador que tem acesso à base de dados.
    6. Para **palavra-passe,** introduza a palavra-passe do utilizador. 
    7. Teste a ligação à base de dados clicando no botão **de ligação de teste.**
    8. Guarde o serviço ligado clicando no botão **Guardar.** 

        ![Screenshot que mostra o processo de adicionar um novo serviço ligado.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Na janela propriedades, mude para o **separador Procedimento Armazenado** a partir do separador **Conta SQL** e faça os seguintes passos: 

    1. Selecione **Editar** . 
    2. Para o campo **de nome do procedimento armazenado,** introduza `sp_executesql` . 
    3. Clique **+ Novo** na secção de **parâmetros de procedimento armazenado.** 
    4. Para **o nome** do parâmetro, **insira stmt** . 
    5. Para **o tipo** de parâmetro, **introduza a corda** . 
    6. Para **o valor** do parâmetro, insira a seguinte consulta SQL:

        Na consulta SQL, especifique os valores certos para os **folder_name,** **project_name** e **package_name** parâmetros. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Para validar a configuração do pipeline, clique em **Validar** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline** , clique em **>>** .

    ![Validar o pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publique o pipeline na Data Factory clicando no botão **Publicar Tudo.** 

    ![Publicar](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorizar o gasoduto
Nesta secção, desencadeia-se uma conduta e depois monitoriza-a. 

1. Para ativar uma corrida de gasoduto, clique em **Trigger** na barra de ferramentas e clique em **Trigger agora** . 

    ![Acionar agora](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Na janela **Executar Pipeline** , selecione **Concluir** . 
3. Mude para o separador **Monitorizar** , no lado esquerdo. Vê o gasoduto a funcionar e o seu estado juntamente com outras informações (como o tempo de início de funcionação). Para atualizar a vista, clique em **Atualizar** .

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Clique na ligação **Ver Execuções de Atividades** , na coluna **Ações** . Vê-se apenas uma atividade a decorrer, uma vez que o gasoduto tem apenas uma atividade (atividade de procedimento armazenado).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Pode executar a seguinte **consulta** com a base de dados SSISDB na Base de Dados SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificar execuções de pacotes](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Também pode criar um gatilho programado para o seu oleoduto para que o gasoduto seja executado num horário (hora, dia, etc.). Por exemplo, consulte [Criar uma fábrica de dados - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nesta secção, utiliza-se a Azure PowerShell para criar um oleoduto Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS. 

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Pode utilizar a mesma fábrica de dados que tem o Azure-SSIS IR ou criar uma fábrica de dados separada. O procedimento a seguir fornece medidas para a criação de uma fábrica de dados. Cria-se um oleoduto com uma atividade de procedimento armazenado nesta fábrica de dados. A atividade de procedimento armazenado executa um procedimento armazenado na base de dados SSISDB para executar o seu pacote SSIS. 

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

5. Para criar a fábrica de dados, executar o seguinte **Set-AzDataFactoryV2** cmdlet, utilizando a propriedade Localização e Grupo de RecursosName a partir da variável $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário** , ou um **administrador** da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que o Data Factory está atualmente disponível, selecione as regiões que lhe interessam na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory** : [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure
Crie um serviço ligado para ligar a sua base de dados que hospeda o catálogo SSIS à sua fábrica de dados. A Data Factory utiliza informações neste serviço ligado para ligar à base de dados SSISDB e executa um procedimento armazenado para executar um pacote SSIS. 

1. Criar um ficheiro JSON nomeado **AzureSqlDatabaseLinkedService.jsna** pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt; o nome de &gt; servidor, o nome de utilizador &lt; e a &gt; &lt; palavra-passe &gt; pelos valores da sua Base de Dados Azure SQL antes de guardar o ficheiro.

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

3. Executar o **set-AzDataFactoryV2LinkedService** para criar o serviço ligado: **AzureSqlDatabaseLinkedService** . 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um oleoduto com atividade de procedimento armazenado 
Neste passo, cria-se um oleoduto com uma atividade de procedimento armazenado. A atividade invoca o procedimento sp_executesql armazenado para executar o seu pacote SSIS. 

1. Criar um ficheiro JSON nomeado **RunSSISPackagePipeline.jsna** pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt; o nome da &gt; pasta, o NOME DO &lt; &gt; PROJETO, o NOME DO PACOTE com &lt; &gt; nomes de pasta, projeto e embalagem no catálogo SSIS antes de guardar o ficheiro. 

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

2. Para criar o oleoduto: **RunSSISPackagePipeline** , executar o **cmdlet Set-AzDataFactoryV2Pipeline.**

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
Utilize o **cmdlet Invoke-AzDataFactoryV2Pipeline** para executar o gasoduto. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

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
No passo anterior, invocou o gasoduto a pedido. Também pode criar um gatilho de horário para executar o gasoduto num horário (hora, dia, etc.).

1. Criar um ficheiro JSON nomeado **MyTrigger.jsna** pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

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
2. No **Azure PowerShell,** mude para a pasta **C:\ADF\RunSSISPackage.**
3. Executar o **cmdlet Set-AzDataFactoryV2Trigger,** que cria o gatilho. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por defeito, o gatilho está em estado de paragem. Inicie o gatilho executando o cmdlet **Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme se o gatilho é iniciado executando o cmdlet **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Executar o seguinte comando depois da hora seguinte. Por exemplo, se a hora atual for 3:25 PM UTC, executar o comando às 16:00 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Pode executar a seguinte consulta com a base de dados SSISDB na Base de Dados SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passos seguintes
Também pode monitorizar o gasoduto utilizando o portal Azure. Para obter instruções passo a passo, consulte [o gasoduto](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).