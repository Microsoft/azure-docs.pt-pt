---
title: Execute o pacote do SSIS com a atividade de procedimento armazenado - Azure | Documentos da Microsoft
description: Este artigo descreve como executar um pacote de SQL Server Integration Services (SSIS) num pipeline do Azure Data Factory através da atividade de procedimento armazenado.
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
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: daf3ebec00d81488c100c51bc95b03c313dba391
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432843"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Executar um pacote do SSIS com a atividade de procedimento armazenado no Azure Data Factory
Este artigo descreve como executar um pacote do SSIS num pipeline do Azure Data Factory com uma atividade de procedimento armazenado. 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure 
As instruções neste artigo utilizam uma base de dados SQL do Azure que aloja o catálogo do SSIS. Também pode utilizar uma instância de gerida de base de dados do Azure SQL.

## <a name="create-an-azure-ssis-integration-runtime"></a>Criar um integration runtime do Azure-SSIS
Criar um runtime de integração Azure-SSIS se não tiver uma ao seguir as instruções passo a passo no [Tutorial: Implementar pacotes do SSIS](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>IU do Data Factory (portal do Azure)
Nesta secção, vai utilizar IU do Data Factory para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS.

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Primeiro passo é criar uma fábrica de dados com o portal do Azure. 

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
         
    Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Só aparecem na lista pendente as localizações que o Data Factory suporta. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras localizações.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.
8. No dashboard, verá o mosaico seguinte com o estado: **Implementar fábrica de dados**. 

    ![Mosaico “implementar a fábrica de dados”](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Depois de concluída a criação, vai ver a página **Data Factory**, conforme mostrado na imagem.
   
    ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no mosaico **Criar e Monitorizar** para iniciar a aplicação de interface de utilizador (IU) do Azure Data Factory num separador à parte. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado
Neste passo, vai utilizar a IU do Data Factory para criar um pipeline. Adicionar uma atividade de procedimento armazenado para o pipeline e configurá-lo para executar o pacote SSIS ao utilizar o procedimento armazenado de sp_executesql. 

1. Na página de introdução, clique em **criar pipeline**: 

    ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Na **atividades** caixa de ferramentas, expanda **gerais**e arraste e largue **Stored Procedure** atividade para a superfície de desenho do pipeline. 

    ![Atividade de procedimento armazenado de arrastar e largar](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Na janela de propriedades para a atividade de procedimento armazenado, mude para o **conta de SQL** separador e clique em **+ novo**. Criar uma ligação à base de dados SQL do Azure que aloja o catálogo de SSIS (base de dados SSIDB). 
   
    ![Botão Novo serviço ligado](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Na janela **Novo Serviço Ligado**, siga os passos abaixo: 

    1. Selecione **base de dados SQL do Azure** para **tipo**.
    2. Selecione o **predefinido** Runtime de integração do Azure para ligar à base de dados de SQL do Azure que aloja o `SSISDB` base de dados.
    3. Selecione a base de dados de SQL do Azure que aloja a base de dados do SSISDB para o **nome do servidor** campo.
    4. Selecione **SSISDB** para **nome da base de dados**.
    5. Para **nome de utilizador**, introduza o nome de utilizador que tenha acesso à base de dados.
    6. Para **palavra-passe**, introduza a palavra-passe do utilizador. 
    7. Testar a ligação à base de dados ao clicar em **Testar ligação** botão.
    8. Guardar o serviço ligado ao clicar o **guardar** botão. 

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Na janela Propriedades, mude para o **Stored Procedure** separador da **conta de SQL** separador e siga os passos abaixo: 

    1. Selecione **Editar**. 
    2. Para o **nome do procedimento armazenado** campo, Enter `sp_executesql`. 
    3. Clique em **+ novo** no **parâmetros do procedimento armazenado** secção. 
    4. Para **name** do parâmetro, introduza **stmt**. 
    5. Para **tipo** do parâmetro, introduza **cadeia de caracteres**. 
    6. Para **valor** do parâmetro, introduza a seguinte consulta SQL:

        Na consulta SQL, especifique os valores certos para o **nome_da_pasta**, **project_name**, e **package_name** parâmetros. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Serviço ligado da Base de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Para validar a configuração do pipeline, clique em **Validate** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

    ![Validar o pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publicar o pipeline no Data Factory clicando **publicar tudo** botão. 

    ![Publicar](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Executar e monitorizar o pipeline
Nesta secção, acionar uma execução de pipeline e, em seguida, monitorizá-lo. 

1. Para acionar uma execução de pipeline, clique em **acionador** na barra de ferramentas e clique em **acionar agora**. 

    ![Acionar agora](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 
3. Mude para o separador **Monitorizar**, no lado esquerdo. Verá a execução do pipeline e o respetivo estado, juntamente com outras informações (por exemplo, a hora de início da execução). Para atualizar a vista, clique em **Atualizar**.

    ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Pode ver apenas uma atividade executar conforme o pipeline tem apenas uma atividade (atividade de procedimento armazenado).

    ![Execuções de atividade](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Pode executar o seguinte procedimento **consulta** contra o SSISDB de base de dados no seu servidor SQL do Azure para verificar se o pacote executado. 

    ```sql
    select * from catalog.executions
    ```

    ![Certifique-se de execuções de pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Também pode criar um acionador agendado para o seu pipeline, para que o pipeline é executado com base numa agenda (hora a hora, diária, etc.). Por exemplo, veja [criar uma fábrica de dados - IU do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nesta secção, utilizar o Azure PowerShell para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote do SSIS. 

Instale os módulos do Azure PowerShell mais recentes ao seguir as instruções em [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Pode utilizar a fábrica de dados mesmo que tenha o IR Azure-SSIS ou criar uma fábrica de dados separado. O procedimento seguinte fornece passos para criar uma fábrica de dados. Criar um pipeline com uma atividade de procedimento armazenado nesta fábrica de dados. A atividade de procedimento armazenado executa um procedimento armazenado na base de dados SSISDB para executar o seu pacote do SSIS. 

1. Defina uma variável para o nome do grupo de recursos que vai utilizar nos comandos do PowerShell mais tarde. Copie o texto do comando seguinte para o PowerShell, especifique um nome para o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com aspas duplas e execute o comando. Por exemplo: `"adfrg"`. 
   
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

5. Para criar a fábrica de dados, execute o seguinte procedimento **Set-AzDataFactoryV2** cmdlet, usando a propriedade de Location e ResourceGroupName da variável de $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenha em atenção os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro seguinte, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias do Data Factory, a conta de utilizador que utiliza para iniciar sessão no Azure tem de ser um membro das funções **contribuidor** ou **proprietário**, ou um **administrador** da subscrição do Azure.
* Para obter uma lista de regiões do Azure em que a fábrica de dados está atualmente disponível, selecione as regiões que lhe interessam, na página seguinte e, em seguida, expanda **Analytics** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço ligado da Base de Dados SQL do Azure
Crie um serviço ligado para ligar a sua base de dados SQL do Azure que aloja o catálogo SSIS à fábrica de dados. Data Factory utiliza informações neste serviço ligado para ligar à base de dados do SSISDB e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um ficheiro JSON com o nome **Azuresqldatabaselinkedservice** na **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;nome de utilizador&gt;, e &lt;palavra-passe&gt; pelos valores da sua base de dados do SQL do Azure antes de guardar o ficheiro.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. Na **do Azure PowerShell**, mude para o **C:\ADF\RunSSISPackage** pasta.

3. Executar o **Set-AzDataFactoryV2LinkedService** cmdlet para criar o serviço ligado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Neste passo, vai criar um pipeline com uma atividade de procedimento armazenado. A atividade invoca o procedimento de sp_executesql armazenados para executar o seu pacote do SSIS. 

1. Crie um ficheiro JSON com o nome **RunSSISPackagePipeline.json** no **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua &lt;nome da pasta&gt;, &lt;nome do PROJETO&gt;, &lt;nome do pacote&gt; com nomes de pasta, o projeto e o pacote no catálogo de SSIS antes de guardar o ficheiro. 

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

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o **conjunto AzDataFactoryV2Pipeline** cmdlet.

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
Utilize o **Invoke-AzDataFactoryV2Pipeline** cmdlet para executar o pipeline. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

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

### <a name="create-a-trigger"></a>Criar um acionador
No passo anterior, invocado a pipeline a pedido. Também pode criar um acionador de agenda para executar o pipeline com base numa agenda (hora a hora, diária, etc.).

1. Crie um ficheiro JSON com o nome **MyTrigger.json** na **C:\ADF\RunSSISPackage** pasta com o seguinte conteúdo: 

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
2. Na **do Azure PowerShell**, mude para o **C:\ADF\RunSSISPackage** pasta.
3. Executar o **Set-AzDataFactoryV2Trigger** cmdlet, que cria o acionador. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Por predefinição, o acionador está no estado parado. Inicie o acionador ao executar o **Start-AzDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme que o acionador é iniciado ao executar o **Get-AzDataFactoryV2Trigger** cmdlet. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Após a hora seguinte, execute o seguinte comando. Por exemplo, se a hora atual é de 3:25 PM UTC, execute o comando 4 PM UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Pode executar a consulta seguinte na base de dados SSISDB no seu servidor SQL do Azure para verificar se o pacote executado. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Passos Seguintes
Também pode monitorizar o pipeline com o portal do Azure. Para obter instruções passo a passo, consulte [monitorizar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
