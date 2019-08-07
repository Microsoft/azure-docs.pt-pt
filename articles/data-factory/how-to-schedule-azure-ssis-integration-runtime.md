---
title: Como agendar o Azure-SSIS Integration Runtime | Microsoft Docs
description: Este artigo descreve como agendar o início e a interrupção do Integration Runtime do SSIS do Azure usando Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d7a4a54f979cd4b14e12c5a57792241f1b2388d2
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734710"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Como iniciar e parar o Azure-SSIS Integration Runtime em uma agenda
Este artigo descreve como agendar o início e a interrupção do Azure-SSIS Integration Runtime (IR) usando Azure Data Factory (ADF). O IR do Azure-SSIS é um recurso de computação do ADF dedicado para a execução de pacotes SQL Server Integration Services (SSIS). Executar o IR do Azure-SSIS tem um custo associado a ele. Portanto, normalmente você desejará executar o IR somente quando precisar executar pacotes do SSIS no Azure e parar o IR quando não precisar mais dele. Você pode usar a interface do usuário do ADF/app ou Azure PowerShell para [Iniciar ou parar manualmente o ir](manage-azure-ssis-integration-runtime.md)).

Como alternativa, você pode criar atividades da Web em pipelines do ADF para iniciar/parar o IR no agendamento, por exemplo, iniciando-o na manhã antes de executar suas cargas de trabalho de ETL diárias e interrompê-lo à tarde após a conclusão.  Você também pode encadear uma atividade executar pacote SSIS entre duas atividades da Web que iniciam e param o IR, para que o IR inicie/pare sob demanda, just-in-time antes/após a execução do pacote. Para obter mais informações sobre a atividade executar pacote do SSIS, consulte [o artigo executar um pacote do SSIS usando a atividade executar pacote do SSIS no pipeline do ADF](how-to-invoke-ssis-package-ssis-activity.md) .

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não provisionou o IR do Azure-SSIS, provisione-o seguindo as instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Criar e agendar pipelines do ADF que iniciam e ou param o IR do Azure-SSIS
Esta seção mostra como usar as atividades da Web em pipelines do ADF para iniciar/parar o IR do Azure-SSIS no agendamento ou iniciar & interrompê-lo sob demanda. Iremos orientá-lo para criar três pipelines: 

1. O primeiro pipeline contém uma atividade da Web que inicia o IR do Azure-SSIS. 
2. O segundo pipeline contém uma atividade da Web que interrompe o IR do Azure-SSIS.
3. O terceiro pipeline contém uma atividade executar pacote do SSIS encadeada entre duas atividades da Web que iniciam/param o IR do Azure-SSIS. 

Depois de criar e testar esses pipelines, você pode criar um gatilho de agendamento e associá-lo a qualquer pipeline. O gatilho de agendamento define uma agenda para executar o pipeline associado. 

Por exemplo, você pode criar dois gatilhos, o primeiro é agendado para execução diária às 18h e associado ao primeiro pipeline, enquanto o segundo é agendado para execução diária às 18:00 e associado ao segundo pipeline.  Dessa forma, você tem um período entre 18:00 e 18:00 todos os dias em que o IR está em execução, pronto para executar suas cargas de trabalho de ETL diárias.  

Se você criar um terceiro gatilho agendado para execução diária à meia-noite e associado ao terceiro pipeline, esse pipeline será executado à meia-noite todos os dias, iniciando o IR logo antes da execução do pacote, executando posteriormente o pacote e imediatamente interromper o IR logo após a execução do pacote, de modo que o IR não estará executando inocioso.

### <a name="create-your-adf"></a>Criar seu ADF

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na página **novo data Factory** , digite **MyAzureSsisDataFactory** para o **nome**. 
      
   ![Página Nova fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do ADF deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome do ADF (por exemplo, yournameMyAzureSsisDataFactory) e tente criá-lo novamente. Consulte o artigo [regras de nomenclatura de data Factory](naming-rules.md) para saber mais sobre regras de nomenclatura para artefatos do ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecione sua **assinatura** do Azure sob a qual você deseja criar o ADF. 
5. Para **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **criar novo**e insira o nome do novo grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte [o artigo usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md) .
   
6. Para **versão**, selecione **v2** .
7. Para **local**, selecione um dos locais com suporte para a criação do ADF na lista suspensa.
8. Selecione **Afixar ao dashboard**.     
9. Clique em **Criar**.
10. No painel do Azure, você verá o seguinte bloco com o status: **Implantando data Factory**. 

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Depois que a criação for concluída, você poderá ver a página do ADF, conforme mostrado abaixo.
   
    ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Clique em **criar & monitor** para iniciar o aplicativo/interface do usuário do ADF em uma guia separada.

### <a name="create-your-pipelines"></a>Criar seus pipelines

1. Na página de **introdução** , selecione **criar pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Na caixa de ferramentas **atividades** , expanda o menu **geral** e arraste & soltar uma atividade **da Web** na superfície do designer de pipeline. Na guia **geral** da janela Propriedades da atividade, altere o nome da atividade para **startMyIR**. Alterne para a guia **configurações** e execute as ações a seguir.

    1. Para **URL**, insira a seguinte URL para a API REST que inicia o ir do Azure- `{subscriptionId}`SSIS, `{factoryName}`substituindo `{integrationRuntimeName}` , `{resourceGroupName}`, e pelos valores reais para o ir: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`Como alternativa, você também pode copiar & colar a ID de recurso do IR de sua página de monitoramento no aplicativo/interface do usuário do ADF para substituir a seguinte parte da URL acima:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID de recurso de IR do SSIS do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Para **método**, selecione **post**. 
    3. Para **corpo**, insira `{"message":"Start my IR"}`. 
    4. Para **autenticação**, selecione **MSI** para usar a identidade gerenciada para o ADF, consulte o artigo [identidade gerenciada para data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) para obter mais informações.
    5. Para **recurso**, insira `https://management.azure.com/`.
    
       ![IR do SSIS agendamento de atividade da Web do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clone o primeiro pipeline para criar um segundo, alterando o nome da atividade para **stopMyIR** e substituindo as propriedades a seguir.

    1. Para **URL**, insira a seguinte URL para a API REST que interrompe o ir do Azure- `{subscriptionId}`SSIS, `{factoryName}`substituindo `{integrationRuntimeName}` , `{resourceGroupName}`, e pelos valores reais para o ir:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Para **corpo**, insira `{"message":"Stop my IR"}`. 

4. Crie um terceiro pipeline, arraste & descartar uma atividade **executar pacote do SSIS** da caixa de ferramentas **atividades** para a superfície do designer de pipeline e configure-a seguindo as instruções em [invocar um pacote do SSIS usando executar pacote SSIS atividade no ADF](how-to-invoke-ssis-package-ssis-activity.md) artigo.  Como alternativa, você pode usar uma atividade de **procedimento armazenado** em vez disso e configurá-la seguindo as instruções em [invocar um pacote do SSIS usando a atividade de procedimento armazenado no ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) .  Em seguida, encadeamento a atividade executar pacote SSIS/procedimento armazenado entre duas atividades da Web que iniciam/interrompem o IR, semelhante às atividades da Web nos pipelines do primeiro/segundo.

   ![IR do SSIS de atividade da Web por demanda do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Atribua a identidade gerenciada para o ADF uma função de **colaborador** para si mesma, para que as atividades da Web em seus pipelines possam chamar a API REST para iniciar/parar a IRS do Azure-SSIS provisionada nela.  Na página do ADF no portal do Azure, clique em **controle de acesso (iam)** , clique em **+ Adicionar atribuição de função**e, em seguida, na folha **Adicionar atribuição de função** , execute as ações a seguir.

    1. Para **função**, selecione **colaborador**. 
    2. Para **atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure ad**. 
    3. Para **selecionar**, pesquise o nome do ADF e selecione-o. 
    4. Clique em **Guardar**.
    
   ![Atribuição de função de identidade gerenciada do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valide o ADF e todas as configurações de pipeline clicando em **validar tudo/validar** na barra de ferramentas fábrica/pipeline. Feche a **saída de validação de fábrica/pipeline** clicando no **>>** botão.  

   ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Executar teste em seus pipelines

1. Selecione **execução de teste** na barra de ferramentas para cada pipeline e veja janela de **saída** no painel inferior. 

   ![Execução de teste](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para testar o terceiro pipeline, inicie o SQL Server Management Studio (SSMS). Na janela **conectar ao servidor** , execute as ações a seguir. 

    1. Para **nome do servidor**, insira  **&lt;o nome&gt;do servidor do banco de dados SQL do Azure. Database.Windows.net**.
    2. Selecione **opções > >** .
    3. Para **conectar ao banco de dados**, selecione **SSISDB**.
    4. Selecione **Ligar**. 
    5. Expanda **Integration Services catálogos** -> **SSISDB** -> seus **projetos** de > de pasta-> seus **pacotes**de > de projeto do SSIS. 
    6. Clique com o botão direito do mouse no pacote SSIS especificado para executar e selecione **relatórios** -> **Standard relata** -> **todas as execuções**. 
    7. Verifique se ele foi executado. 

   ![Verificar a execução do pacote SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Agendar seus pipelines

Agora que seus pipelines funcionam conforme o esperado, você pode criar gatilhos para executá-los em cadências especificadas. Para obter detalhes sobre como associar gatilhos com pipelines, consulte [disparar o pipeline em um](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artigo de agendamento.

1. Na barra de ferramentas do pipeline, selecione **gatilho** e selecione **novo/editar**. 

   ![Gatilho-> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. No painel **Adicionar gatilhos** , selecione **+ novo**.

   ![Adicionar gatilhos-novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. No **novo** painel de gatilho, execute as seguintes ações: 

    1. Para **nome**, insira um nome para o gatilho. No exemplo a seguir, **executar diariamente** é o nome do gatilho. 
    2. Para **tipo**, selecione **agenda**. 
    3. Para **data de início (UTC)** , insira uma data e hora de início em UTC. 
    4. Para **recorrência**, insira uma cadência para o gatilho. No exemplo a seguir, ele é **diário** uma vez. 
    5. Para **fim**, selecione **não terminar** ou insira uma data e hora de término depois **de selecionar na data**. 
    6. Selecione **ativado** para ativar o gatilho imediatamente após a publicação de todas as configurações do ADF. 
    7. Selecione **Seguinte**.

   ![Gatilho-> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na página **parâmetros de execução de gatilho** , examine qualquer aviso e selecione **concluir**. 
5. Publique as configurações do ADF inteira selecionando **publicar tudo** na barra de ferramentas da fábrica. 

   ![Publicar tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitore seus pipelines e gatilhos no portal do Azure

1. Para monitorar execuções de gatilho e execuções de pipeline, use a guia **monitorar** à esquerda da interface do usuário do ADF/aplicativo. Para obter etapas detalhadas, consulte [o artigo monitorar o pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline) .

   ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para exibir as execuções de atividade associadas a uma execução de pipeline, selecione o primeiro link (**Exibir execuções de atividade**) na coluna **ações** . Para o terceiro pipeline, você verá três execuções de atividade, uma para cada atividade encadeada no pipeline (atividade da Web para iniciar o IR, a atividade de procedimento armazenado para executar o pacote e a atividade da Web para parar o IR). Para exibir o pipeline novamente, selecione o link pipelines na parte superior.

   ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para exibir as execuções do gatilho, selecione gatilhos de **execução** na lista suspensa em execuções de **pipeline** na parte superior. 

   ![Execuções de acionador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorar seus pipelines e gatilhos com o PowerShell

Use scripts como os exemplos a seguir para monitorar seus pipelines e gatilhos.

1. Obtenha o status de uma execução de pipeline.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obter informações sobre um gatilho.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Obter o status de uma execução de gatilho.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Criar e agendar o runbook de automação do Azure que inicia/interrompe o IR do Azure-SSIS

Nesta seção, você aprenderá a criar um runbook de automação do Azure que executa o script do PowerShell, Iniciando/interrompendo seu IR do Azure-SSIS em um agendamento.  Isso é útil quando você deseja executar scripts adicionais antes/depois de iniciar/parar o IR para o pré-processamento/pós-processamento.

### <a name="create-your-azure-automation-account"></a>Criar sua conta de automação do Azure

Se você ainda não tiver uma conta de automação do Azure, crie uma seguindo as instruções nesta etapa. Para obter etapas detalhadas, consulte [o artigo criar uma conta de automação do Azure](../automation/automation-quickstart-create-account.md) . Como parte desta etapa, você cria uma conta **Executar como do Azure** (uma entidade de serviço em seu Azure Active Directory) e atribui a ela uma função **colaborador** em sua assinatura do Azure. Verifique se é a mesma assinatura que contém o ADF com o IR do Azure SSIS. A automação do Azure usará essa conta para se autenticar em Azure Resource Manager e operar em seus recursos. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário/aplicativo do ADF só tem suporte nos navegadores da Web Microsoft Edge e Google Chrome.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
3. Selecione **novo** no menu à esquerda, selecione **monitoramento + gerenciamento**e clique em **automação**. 

   ![Automação de > de Monitoramento + Gerenciamento New->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. No painel **adicionar conta de automação** , execute as ações a seguir.

    1. Para **nome**, insira um nome para sua conta de automação do Azure. 
    2. Para **assinatura**, selecione a assinatura que tem o ADF com o ir do Azure-SSIS. 
    3. Para **grupo de recursos**, selecione **criar novo** para criar um novo grupo de recursos ou **usar existente** para selecionar um existente. 
    4. Para **local**, selecione um local para sua conta de automação do Azure. 
    5. Confirme **criar conta Executar como do Azure** como **Sim**. Uma entidade de serviço será criada em seu Azure Active Directory e atribuída uma função de **colaborador** em sua assinatura do Azure.
    6. Selecione **fixar no painel** para exibi-lo permanentemente no painel do Azure. 
    7. Selecione **Criar**. 

   ![Automação de > de Monitoramento + Gerenciamento New->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Você verá o status de implantação da sua conta de automação do Azure no painel do Azure e as notificações. 
    
   ![Implantando a automação](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Você verá a Home Page da sua conta de automação do Azure depois que ela for criada com êxito. 

   ![home page de automação](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importar módulos do ADF

1. Selecione **módulos** na seção **recursos compartilhados** no menu à esquerda e verifique se você tem **AzureRM. DataFactoryV2** + **AzureRM. Profile** na lista de módulos.

   ![Verificar os módulos necessários](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se você não tiver **AzureRM. DataFactoryV2**, vá para o Galeria do PowerShell para o [módulo AzureRM. DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), selecione **implantar na automação do Azure**, selecione sua conta de automação do Azure e, em seguida, selecione **OK**. Volte para exibir **módulos** na seção **recursos compartilhados** no menu à esquerda e aguarde até ver o **status** do módulo **AzureRM. DataFactoryV2** alterado para **disponível**.

    ![Verificar o módulo de Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se você não tiver o **AzureRM. Profile**, vá para o Galeria do PowerShell para o [módulo AzureRM. Profile](https://www.powershellgallery.com/packages/AzureRM.profile/), selecione **implantar na automação do Azure**, selecione sua conta de automação do Azure e, em seguida, selecione **OK**. Volte para exibir **módulos** na seção **recursos compartilhados** no menu à esquerda e aguarde até ver o **status** do módulo **AzureRM. Profile** alterado para **disponível**.

    ![Verificar o módulo de perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Criar seu runbook do PowerShell

A seção a seguir fornece as etapas para criar um runbook do PowerShell. O script associado ao seu runbook inicia/interrompe o IR do Azure-SSIS com base no comando especificado para o parâmetro **Operation** . Esta seção não fornece os detalhes completos para a criação de um runbook. Para obter mais informações, consulte [o artigo criar um runbook](../automation/automation-quickstart-create-runbook.md) .

1. Alterne para a guia **Runbooks** e selecione **+ Adicionar um runbook** da barra de ferramentas. 

   ![Adicionar um botão de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecione **criar um novo runbook** e execute as seguintes ações: 

    1. Para **nome**, insira **StartStopAzureSsisRuntime**.
    2. Para **tipo de runbook**, selecione **PowerShell**.
    3. Selecione **Criar**.
    
   ![Adicionar um botão de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie & Cole o seguinte script do PowerShell em sua janela de script do runbook. Salve e publique seu runbook usando os botões **salvar** e **publicar** na barra de ferramentas. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Editar runbook do PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Teste seu runbook selecionando o botão **Iniciar** na barra de ferramentas. 

   ![Botão iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. No painel **Iniciar runbook** , execute as seguintes ações: 

    1. Para **nome do grupo de recursos**, insira o nome do grupo de recursos que tem o ADF com o ir do Azure-SSIS. 
    2. Para **nome do data Factory**, insira o nome do ADF com o ir do Azure-SSIS. 
    3. Para **AZURESSISNAME**, insira o nome do ir do Azure-SSIS. 
    4. Para **operação**, digite **Iniciar**. 
    5. Selecione **OK**.  

   ![Iniciar janela do runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Na janela do trabalho, selecione bloco de **saída** . Na janela de saída, aguarde a mensagem **# # # # # Completed # # # # #** depois de ver **# # # # # Starting # # # # #** . Iniciar o IR do Azure-SSIS leva aproximadamente 20 minutos. Feche a janela do **trabalho** e volte para a janela do **runbook** .

   ![IR do Azure SSIS-iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita as duas etapas anteriores usando **parar** como o valor para a **operação**. Inicie o runbook novamente selecionando o botão **Iniciar** na barra de ferramentas. Insira seu grupo de recursos, ADF e nomes de IR do Azure-SSIS. Para **operação**, digite **parar**. Na janela de saída, aguarde a mensagem **# # # # # Completed # # # # #** depois de ver **# # # # # parando # # #** # #. Parar o IR do Azure-SSIS não leva desde que seja iniciado. Feche a janela do **trabalho** e volte para a janela do **runbook** .

8. Você também pode disparar seu runbook por meio de um webhook que pode ser criado selecionando o item de menu WebHooks ou uma agenda que pode ser criada selecionando o item de menu **agendas** , conforme especificado abaixo.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Criar agendas para seu runbook para iniciar/parar o IR do Azure-SSIS

Na seção anterior, você criou o runbook de automação do Azure que pode iniciar ou parar o IR do Azure-SSIS. Nesta seção, você criará dois agendamentos para o runbook. Ao configurar a primeira agenda, você especifica **Iniciar** para a **operação**. Da mesma forma, ao configurar a segunda, você especifica **Stop** for **Operation**. Para obter etapas detalhadas para criar agendas, consulte [o artigo criar um agendamento](../automation/shared-resources/schedules.md#creating-a-schedule) .

1. Na janela do **runbook** ,selecione agendas e selecione **+ Adicionar um agendamento** na barra de ferramentas. 

   ![IR do Azure SSIS-iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. No painel **agendar runbook** , execute as seguintes ações: 

    1. Selecione **vincular um agendamento ao seu runbook**. 
    2. Selecione **criar um novo agendamento**.
    3. No painel **novo agendamento** , insira **Iniciar ir diariamente** para o **nome**. 
    4. Para **iniciado**, insira um horário que seja de alguns minutos após a hora atual. 
    5. Para **recorrência**,selecione recorrente. 
    6. Para **repetir a cada**, insira **1** e selecione **dia**. 
    7. Selecione **Criar**. 

   ![Agenda para início do IR do Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Alterne para a guia **parâmetros e configurações de execução** . Especifique o grupo de recursos, o ADF e os nomes de IR do Azure-SSIS. Para **operação**, digite **Iniciar** e selecione **OK**. Selecione **OK** novamente para ver a página agenda em agendas do seu runbook. 

   ![Agendar a estrela do IR do Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita as duas etapas anteriores para criar um agendamento chamado **parar ir diariamente**. Insira uma hora que seja pelo menos 30 minutos após a hora especificada para **Iniciar agendamento diário de ir** . Para **operação**, digite **parar** e selecione **OK**. Selecione **OK** novamente para ver a página agenda em agendas do seu runbook. 

5. Na janela do **runbook** , selecione **trabalhos** no menu à esquerda. Você deve ver os trabalhos criados por suas agendas nos horários especificados e seus status. Você pode ver os detalhes do trabalho, como sua saída, semelhante ao que você viu depois de testar o runbook. 

   ![Agendar a estrela do IR do Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Depois de concluir o teste, desabilite suas agendas editando-as. Selecione **agendamentos** no menu à esquerda, selecione **Iniciar ir diariamente/parar ir diariamente**e selecione **não** para **habilitado**. 

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte postagem no blog:
-   [Modernizar e estender seus fluxos de trabalho ETL/ELT com atividades do SSIS em pipelines do ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Veja os artigos seguintes da documentação do SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Implementar, executar e monitorizar pacotes do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Ligar ao catálogo do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Agendar a execução de pacotes no Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
