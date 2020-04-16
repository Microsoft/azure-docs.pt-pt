---
title: Executar pacote SSIS com atividade de procedimento armazenado - Azure
description: Este artigo descreve como executar um pacote sQL Server Integration Services (SSIS) num oleoduto Azure Data Factory utilizando a Atividade de Procedimento Armazenada.
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
ms.openlocfilehash: 95214d797afcef97d974eeba2b4aea941009e1f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413134"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Run an SSIS package with the Stored Procedure activity in Azure Data Factory (Executar um pacote do SSIS com a atividade Procedimento Armazenado no Azure Data Factory)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como executar um pacote SSIS num oleoduto Azure Data Factory utilizando uma atividade de Procedimento Armazenado. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
O walkthrough neste artigo utiliza uma base de dados Azure SQL que acolhe o catálogo SSIS. Também pode utilizar uma instância gerida por base de dados Azure SQL.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Crie um tempo de execução de integração Azure-SSIS se não tiver um seguindo a instrução passo a passo no [Tutorial: Implementar pacotes SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory UI (portal Azure)
Nesta secção, utiliza a Data Factory UI para criar um pipeline data Factory com uma atividade de procedimento armazenada que invoque um pacote SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
O primeiro passo é criar uma fábrica de dados utilizando o portal Azure. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a IU do Data Factory é suportada apenas nos browsers Microsoft Edge e Google Chrome.
2. Navegue para o [portal Azure.](https://portal.azure.com) 
3. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** no **nome**. 
      
     ![Página Nova fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome da fábrica de dados Azure deve ser **globalmente único.** Se vir o erro seguinte no campo de nome, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory). Veja o artigo [Data Factory – Naming Rules](naming-rules.md) (Data Factory – Regras de Nomenclatura) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
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
8. No painel de instrumentos, vê o seguinte azulejo com estatuto: **Implantação da fábrica**de dados . 

     ![Mosaico “implementar a fábrica de dados”](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
     ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um oleoduto com atividade de procedimento armazenado
Neste passo, utiliza-se a UI da Fábrica de Dados para criar um pipeline. Adicione uma atividade de procedimento armazenada ao gasoduto e configure-a para executar o pacote SSIS utilizando o procedimento sp_executesql armazenado. 

1. Na página iniciar, clique em **Criar o pipeline:** 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na caixa de **ferramentas De Atividades,** expanda a atividade **do** **Procedimento Armazenado** de Arrasto para a superfície do designer de gasodutos. 

    ![Atividade de procedimento armazenado de arrasto e queda](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Na janela de propriedades para a atividade do procedimento armazenado, mude para o **separador Conta SQL** e clique **+ Novo**. Cria uma ligação à base de dados Azure SQL que acolhe o Catálogo SSIS (base de dados SSIDB). 
   
    ![Botão Novo serviço ligado](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Selecione **Base de Dados SQL Azure** para **Tipo**.
    2. Selecione o tempo de execução de integração **do Azure Predefinido** para ligar à Base de Dados Azure SQL que acolhe a base de `SSISDB` dados.
    3. Selecione a base de dados Azure SQL que acolhe a base de dados SSISDB para o campo de nome do **Servidor.**
    4. Selecione **SSISDB** para nome base de **dados**.
    5. Para **o nome de utilizador,** insira o nome do utilizador que tenha acesso à base de dados.
    6. Para **obter palavra-passe,** introduza a palavra-passe do utilizador. 
    7. Teste a ligação à base de dados clicando no botão de ligação do **teste.**
    8. Guarde o serviço ligado clicando no botão **Guardar.** 

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Na janela de propriedades, mude para o separador **Procedimento Armazenado** a partir do **separador Conta SQL** e faça os seguintes passos: 

    1. Selecione **Editar**. 
    2. Para o campo de nome `sp_executesql`do **procedimento armazenado,** introduza . 
    3. Clique **+ Novo** na secção de parâmetros de procedimento **armazenado.** 
    4. Para **nome** do parâmetro, introduza **stmt**. 
    5. Para **o tipo** do parâmetro, introduza **string**. 
    6. Para **obter o valor** do parâmetro, insira a seguinte consulta SQL:

        Na consulta SQL, especifique os valores certos para os **parâmetros folder_name**, **project_name**e **package_name.** 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Para validar a configuração do gasoduto, clique em **Validar** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

    ![Validar o pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publique o pipeline na Data Factory clicando em **Publicar Todos os** botões. 

    ![Publicar](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorizar o gasoduto
Nesta secção, aciona-se uma conduta de gasodutos e depois monitoriza-se. 

1. Para acionar uma execução de gasoduto, clique em **Gatilho** na barra de ferramentas e clique agora em **Gatilho**. 

    ![Acionar agora](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 
3. Mude para o separador **Monitorizar**, no lado esquerdo. Você vê o gasoduto executado e o seu estado juntamente com outras informações (como o tempo de início de execução). Para atualizar a vista, clique em **Atualizar**.

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Só se vê uma atividade executada, uma vez que o gasoduto tem apenas uma atividade (atividade de procedimento armazenado).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Pode executar a seguinte **consulta** contra a base de dados SSISDB no seu servidor Azure SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Verificar execuções de pacotes](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Também pode criar um gatilho programado para o seu oleoduto para que o gasoduto corra num horário (hora, diariamente, etc.). Por exemplo, consulte Criar uma fábrica de [dados - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nesta secção, utiliza-se o Azure PowerShell para criar um pipeline data factory com uma atividade de procedimento armazenada que invoque um pacote SSIS. 

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Pode utilizar a mesma fábrica de dados que tem o IR Azure-SSIS ou criar uma fábrica de dados separada. O procedimento seguinte fornece medidas para criar uma fábrica de dados. Cria-se um oleoduto com uma atividade de procedimento armazenada nesta fábrica de dados. A atividade de procedimento armazenado executa um procedimento armazenado na base de dados SSISDB para executar o seu pacote SSIS. 

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

5. Para criar a fábrica de dados, executar o seguinte **Set-AzDataFactoryV2** cmdlet, utilizando a propriedade Localização e ResourceGroupName a partir da variável $ResGrp: 
    
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
Crie um serviço ligado para ligar a sua base de dados Azure SQL que acolhe o catálogo SSIS à sua fábrica de dados. Data Factory utiliza informações neste serviço ligado para ligar à base de dados SSISDB e executa um procedimento armazenado para executar um pacote SSIS. 

1. Crie um ficheiro JSON chamado **AzureSqlDatabaseLinkedService.json** em **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;&gt;o &lt;nome&gt;do &lt;&gt; servidor, o nome de utilizador e a palavra-passe por valores da sua Base de Dados Azure SQL antes de guardar o ficheiro.

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

3. Executar o **set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um oleoduto com atividade de procedimento armazenado 
Neste passo, cria-se um pipeline com uma atividade de procedimento armazenada. A atividade invoca o sp_executesql procedimento armazenado para executar o seu pacote SSIS. 

1. Crie um ficheiro JSON chamado **RunSSISPackagePipeline.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;&gt;o &lt;NOME&gt; &lt;DA&gt; PASTA, NOME DO PROJETO, NOME DO PACOTE por nomes de pasta, projeto e embalagem no catálogo SSIS antes de guardar o ficheiro. 

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

2. Para criar o pipeline: **RunSSISPackagePipeline**, Executar o **set-AzDataFactoryV2Pipeline** cmdlet.

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
Utilize o cmdlet **Invoke-AzDataFactoryV2Pipeline** para executar o gasoduto. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

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
No passo anterior, invocou o oleoduto a pedido. Também pode criar um gatilho de horário para executar o gasoduto num horário (hora, diariamente, etc.).

1. Crie um ficheiro JSON chamado **MyTrigger.json** em **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

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
3. Executar o **set-AzDataFactoryV2Trigger** cmdlet, que cria o gatilho. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por defeito, o gatilho está em estado de paragem. Inicie o gatilho executando o **cmdlet Start-AzDataFactoryV2Trigger.** 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme que o gatilho é iniciado com o funcionamento do cmdlet **Get-AzDataFactoryV2Trigger.** 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Executar o seguinte comando após a próxima hora. Por exemplo, se o tempo atual for 15:25 UTC, dirija o comando às 16:00 UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Pode executar a seguinte consulta contra a base de dados SSISDB no seu servidor Azure SQL para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passos seguintes
Também pode monitorizar o gasoduto utilizando o portal Azure. Para obter instruções passo a passo, consulte [Monitorize o gasoduto](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
