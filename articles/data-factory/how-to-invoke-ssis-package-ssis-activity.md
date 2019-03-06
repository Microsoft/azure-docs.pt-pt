---
title: Execute o pacote do SSIS com a atividade de executar o pacote do SSIS - Azure | Documentos da Microsoft
description: Este artigo descreve como executar um pacote de SQL Server Integration Services (SSIS) no pipeline do Azure Data Factory através da atividade de executar o pacote do SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 846fc5de6470326fbd51d19397503e4eee2ee15b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436090"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Executar um pacote do SSIS com a atividade de executar o pacote do SSIS no Azure Data Factory
Este artigo descreve como executar um pacote do SSIS no pipeline do Azure Data Factory (ADF) através da atividade de executar o pacote do SSIS. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Criar um Azure-SSIS Integration Runtime (IR) se não tiver uma já ao seguir as instruções passo a passo o [Tutorial: Implementar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Executar um pacote no portal do Azure
Nesta secção, é usar a Interface de utilizador do ADF (IU) / aplicação para criar um ADF pipeline com atividade de executar o pacote do SSIS que executa o pacote do SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade de executar o pacote do SSIS
Neste passo, vai utilizar da interface do Usuário do ADF/aplicação para criar um pipeline. Adicionar uma atividade de executar o pacote do SSIS para o pipeline e configurá-lo para executar o seu pacote do SSIS. 

1. Na página de descrição geral/home do ADF no portal do Azure, clique nas **criar e monitorizar** mosaico para iniciar a interface do Usuário do ADF/aplicação num separador à parte. 

   ![Home page da fábrica de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Sobre o **Vamos começar** página, clique em **criar pipeline**: 

   ![Página Introdução](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Na **atividades** caixa de ferramentas, expanda **gerais**, em seguida, arrastar e soltar uma **executar pacotes do SSIS** atividade para a superfície de desenho do pipeline. 

   ![Arraste uma atividade de executar o pacote do SSIS para a superfície de desenho](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Sobre o **gerais** separador para a atividade de executar o pacote do SSIS, forneça um nome e descrição para a atividade. Defina o tempo limite opcional e repita a valores.

   ![Definir as propriedades no separador Geral](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Sobre o **definições** separador para a atividade de executar o pacote do SSIS, selecione o runtime de integração Azure-SSIS que está associado a base de dados SSISDB onde o pacote é implementado. Verifique se o pacote precisar de runtime de 32 bits para executar, o **runtime de 32 bits** caixa de verificação. Para **nível de registo**, selecione um âmbito predefinido do registo para a execução do pacote. Verifique os **personalizado** caixa de verificação, se pretender introduzir o seu nome de registo personalizado em vez disso. Quando o runtime de integração Azure-SSIS está em execução e o **entradas manuais** caixa de verificação estiver desmarcada, pode procurar e selecionar os pastas/projetos/pacotes/ambientes existentes a partir de SSISDB. Clique nas **atualizar** botão para buscar o seu recém-adicionada pastas/projetos/pacotes/ambientes SSISDB, para que estejam disponíveis para navegação e a seleção. 

   ![Definir as propriedades no separador Definições - automático](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Quando não está em execução o runtime de integração de SSIS do Azure ou o **entradas manuais** caixa de verificação, pode inserir seus caminhos de pacote e um ambiente SSISDB nos seguintes formatos: `<folder name>/<project name>/<package name>.dtsx` e `<folder name>/<environment name>`.

   ![Definir as propriedades no separador Definições - Manual](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. Na **parâmetros do SSIS** separador para executar o pacote do SSIS atividade, quando está a executar o runtime de integração Azure-SSIS e o **entradas manuais** caixa de seleção **definições** separador estiver desmarcado, o parâmetros SSIS existentes de SSISDB no pacote/projeto selecionado serão exibidos para que possa atribuir valores a elas. Caso contrário, pode inseri-los um por um atribuir valores a elas manualmente – Certifique-se que existe e forem introduzidos corretamente para a execução do pacote com êxito. Também pode adicionar conteúdo dinâmico para os valores usando expressões, funções, variáveis de sistema do ADF e parâmetros de pipeline ADF, as variáveis.

   ![Definir propriedades na guia Parameters do SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na **gerenciadores de conexão** separador para executar o pacote do SSIS atividade, quando está a executar o runtime de integração Azure-SSIS e o **entradas manuais** caixa de seleção **definições** separador estiver desmarcado, Os gerenciadores de conexão existente no seu projeto/pacote selecionado do SSISDB serão exibidas para que possa atribuir valores a elas. Caso contrário, pode inseri-los um por um atribuir valores a elas manualmente – Certifique-se que existe e forem introduzidos corretamente para a execução do pacote com êxito. Também pode adicionar conteúdo dinâmico para os valores usando expressões, funções, variáveis de sistema do ADF e parâmetros de pipeline ADF, as variáveis.

   ![Definir as propriedades no separador de gerenciadores de conexão](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Sobre o **propriedade substitui** separador para a atividade de executar o pacote do SSIS, pode introduzir os caminhos das propriedades existentes no seu pacote selecionado do SSISDB individualmente para atribuir valores a elas manualmente – Certifique-se eles existem e estão foi introduzido corretamente para a execução do pacote ter êxito, por exemplo, para substituir o valor da variável de utilizador, introduza o caminho no seguinte formato: `\Package.Variables[User::YourVariableName].Value`. Também pode adicionar conteúdo dinâmico para os valores usando expressões, funções, variáveis de sistema do ADF e parâmetros de pipeline ADF, as variáveis.

   ![Definir as propriedades no separador de substituições de propriedade](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. Para validar a configuração do pipeline, clique em **Validate** na barra de ferramentas. Para fechar o **Relatório de Validação do Pipeline**, clique em **>>**.

9. Publicar o pipeline no ADF clicando **publicar tudo** botão. 

### <a name="run-the-pipeline"></a>Executar o pipeline
Neste passo, vai acionar uma execução de pipeline. 

1. Para acionar uma execução de pipeline, clique em **acionador** na barra de ferramentas e clique em **acionar agora**. 

   ![Acionar agora](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Na janela **Executar Pipeline**, selecione **Concluir**. 

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

1. Mude para o separador **Monitorizar**, no lado esquerdo. Verá a execução do pipeline e o respetivo estado, juntamente com outras informações (por exemplo, a hora de início da execução). Para atualizar a vista, clique em **Atualizar**.

   ![Execuções de pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Clique na ligação **Ver Execuções de Atividades**, na coluna **Ações**. Pode ver apenas uma atividade executar conforme o pipeline tem apenas uma atividade (atividade a executar o pacote do SSIS).

   ![Execuções de atividade](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Pode executar o seguinte procedimento **consulta** contra o SSISDB de base de dados no seu servidor SQL do Azure para verificar se o pacote executado. 

   ```sql
   select * from catalog.executions
   ```

   ![Certifique-se de execuções de pacote](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Também pode obter o ID de execução do SSISDB a partir da saída da execução de atividade do pipeline e utilize o ID para verificar os registos de execução mais abrangentes e mensagens de erro no SSMS.

   ![Obter o ID de execução.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>O pipeline com um acionador de agenda

Também pode criar um acionador agendado para o seu pipeline, para que o pipeline é executado com base numa agenda (hora a hora, diária, etc.). Por exemplo, veja [criar uma fábrica de dados - IU do Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Executar um pacote com o PowerShell
Nesta secção, utilizar o Azure PowerShell para criar um pipeline do ADF com atividade de executar o pacote do SSIS que executa o pacote do SSIS. 

Instalar os módulos Azure PowerShell mais recentes ao seguir as instruções passo a passo [como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Criar um ADF com o runtime de integração Azure-SSIS
Pode utilizar um ADF existente que já tenha o IR Azure-SSIS aprovisionado ou criar um ADF novo com o IR Azure-SSIS, siga as instruções passo a passo o [Tutorial: Implementar pacotes do SSIS no Azure através do PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Criar um pipeline com uma atividade de executar o pacote do SSIS 
Neste passo, vai criar um pipeline com uma atividade de executar o pacote do SSIS. A atividade executa seu pacote do SSIS. 

1. Crie um ficheiro JSON com o nome **RunSSISPackagePipeline.json** no **C:\ADF\RunSSISPackage** pasta com conteúdo semelhante ao seguinte exemplo:

   > [!IMPORTANT]
   > Substitua os nomes de objetos, descrições e caminhos, propriedade e valores de parâmetros, as palavras-passe e outros valores das variáveis antes de guardar o ficheiro. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "def"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. No Azure PowerShell, mude para o `C:\ADF\RunSSISPackage` pasta.

3. Para criar o pipeline **RunSSISPackagePipeline**, execute o **conjunto AzDataFactoryV2Pipeline** cmdlet.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Segue-se o resultado do exemplo:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Executar o pipeline
Utilize o **Invoke-AzDataFactoryV2Pipeline** cmdlet para executar o pipeline. O cmdlet devolve o ID de execução do pipeline para monitorização futura.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorizar o pipeline

Execute o seguinte script do PowerShell para verificar continuamente o estado de execução do pipeline até que este termine de copiar os dados. Copie/cole o seguinte script na janela do PowerShell e prima ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

Também pode monitorizar o pipeline com o portal do Azure. Para obter instruções passo a passo, consulte [monitorizar o pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>O pipeline com um acionador de agenda
No passo anterior, executou a pipeline a pedido. Também pode criar um acionador de agenda para executar o pipeline com base numa agenda (hora a hora, diária, etc.).

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
           }]
       }
   }    
   ```
2. Na **do Azure PowerShell**, mude para o **C:\ADF\RunSSISPackage** pasta.
3. Executar o **Set-AzDataFactoryV2Trigger** cmdlet, que cria o acionador. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Por predefinição, o acionador está no estado parado. Inicie o acionador ao executar o **Start-AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Confirme que o acionador é iniciado ao executar o **Get-AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Após a hora seguinte, execute o seguinte comando. Por exemplo, se a hora atual é de 3:25 PM UTC, execute o comando 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Pode executar a consulta seguinte na base de dados SSISDB no seu servidor SQL do Azure para verificar se o pacote executado. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Passos Seguintes
Consulte a seguinte mensagem de blogue:
-   [Modernize e expandir os fluxos de trabalho ETL/ELT com atividades SSIS no ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
