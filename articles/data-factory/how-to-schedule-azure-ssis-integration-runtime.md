---
title: Como agendar o Runtime de integração Azure-SSIS | Documentos da Microsoft
description: Este artigo descreve como agendar a iniciar e parar de Runtime de integração Azure-SSIS ao utilizar o Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 0b84f02d11e278950e4e44874e7b1af9da58f83f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092451"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Como iniciar e parar o Runtime de integração Azure-SSIS com base numa agenda
Este artigo descreve como agendar a iniciar e parar do Azure-SSIS Integration Runtime (IR) com o Azure Data Factory (ADF). Runtime de integração Azure-SSIS é dedicado para a execução de pacotes do SQL Server Integration Services (SSIS) de recurso de computação do ADF. Executar o IR Azure-SSIS tem um custo associado ele. Portanto, normalmente pretende executar o runtime de integração apenas quando tiver de executar pacotes do SSIS no Azure e parar o runtime de integração, quando não precisa mais. Pode usar a Interface de utilizador do ADF (IU) / aplicação ou o Azure PowerShell para [manualmente iniciar ou parar o runtime de integração](manage-azure-ssis-integration-runtime.md)).

Em alternativa, pode criar atividades Web no ADF pipelines para iniciar/parar o runtime de integração numa agenda, por exemplo, a partir da manhã antes de executar as suas cargas de trabalho ETL diárias e pará-la à tarde depois que elas são feitas.  Também pode encadear uma atividade de executar o pacote do SSIS entre duas atividades Web que iniciar e parar o runtime de integração, para que o runtime de integração irá iniciar/parar a pedido e just-in-time anteriores e posteriores a execução do pacote. Para mais informações sobre a atividade de executar o pacote do SSIS, veja [executar um pacote do SSIS com a atividade de executar o pacote do SSIS no pipeline do ADF](how-to-invoke-ssis-package-ssis-activity.md) artigo.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se não aprovisionou o runtime de integração Azure-SSIS já, aprovisioná-lo ao seguir as instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Criar e agendar pipelines do ADF que comecem e ou parar o IR Azure-SSIS
Esta secção mostra-lhe como utilizar atividades Web no ADF pipelines para iniciar/parar o runtime de integração Azure-SSIS numa agenda ou iniciar e pará-la a pedido. Vamos guiá-lo para criar pipelines de três: 

1. O primeiro pipeline contém uma atividade Web que inicia o ir Azure-SSIS. 
2. O segundo pipeline contém uma atividade Web que pára o ir Azure-SSIS.
3. O terceiro pipeline contém uma atividade de executar o pacote do SSIS em cadeia entre duas atividades Web que iniciar/parar o ir Azure-SSIS. 

Depois de criar e testar esses pipelines, pode criar um acionador de agenda e associá-lo a qualquer pipeline. O acionador de agenda define uma agenda para a execução de pipeline associado. 

Por exemplo, pode criar dois acionadores, primeiro é agendado para ser executada diariamente às 6 da Manhã e associado com o primeiro pipeline, enquanto a segunda é agendada para ser executada diariamente para as 18:00 e associado com o segundo pipeline.  Dessa forma, tem um período entre 6 da Manhã às 18:00 todos os dias quando o runtime de integração está em execução, pronto para executar as suas cargas de trabalho ETL diárias.  

Se cria um acionador de terceiro que esteja agendado para ser executada diariamente à meia-noite e associadas do pipeline de terceiro, esse pipeline será executada à meia-noite todos os dias, a iniciar o runtime de integração apenas antes da execução do pacote, em seguida, executar o seu pacote e imediatamente a parar o runtime de integração apenas após a execução do pacote, por isso, o runtime de integração não será executado vinha.

### <a name="create-your-adf"></a>Criar o ADF

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na **nova fábrica de dados** página, introduza **MyAzureSsisDataFactory** para **nome**. 
      
   ![Página Nova fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome do seu ADF tem de ser globalmente exclusivo. Se receber o seguinte erro, altere o nome do seu ADF (por exemplo, Oseunomemyazuressisdatafactory) e tente criar novamente. Ver [Data Factory – regras de nomenclatura](naming-rules.md) artigo para saber mais sobre regras de nomenclatura para artefactos do ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecione o seu Azure **subscrição** sob a qual pretende criar o ADF. 
5. Para **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **criar novo**e introduza o nome do seu novo grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, veja [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md) artigo.
   
6. Para **versão**, selecione **V2** .
7. Para **localização**, selecione uma das localizações suportadas para a criação do ADF na lista pendente.
8. Selecione **Afixar ao dashboard**.     
9. Clique em **Criar**.
10. No dashboard do Azure, verá o mosaico seguinte com o estado: **Implementar o Data Factory**. 

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Depois de concluída a criação, pode ver a página do ADF conforme mostrado abaixo.
   
    ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Clique em **criar e monitorizar** para iniciar a interface do Usuário do ADF/aplicação num separador à parte.

### <a name="create-your-pipelines"></a>Criar os seus pipelines

1. Na **Vamos começar** página, selecione **criar pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Na **atividades** caixa de ferramentas, expanda **gerais** menu e arrastar e largar um **Web** atividade para a superfície de desenho do pipeline. Na **gerais** separador da janela de propriedades de atividade, altere o nome da atividade para **startMyIR**. Mude para o **definições** separador e fazer as seguintes ações.

    1. Para **URL**, introduza o seguinte URL para a API REST, que inicia o IR Azure-SSIS, substituindo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, e `{integrationRuntimeName}` com os valores reais para o runtime de integração: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Em alternativa, também pode copiar e colar o ID de recurso do Runtime de integração da sua página de monitorização na interface do Usuário do ADF/aplicação para substituir a parte seguinte do URL acima: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID de recurso do Runtime de integração de SSIS do ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Para **método**, selecione **POST**. 
    3. Para **corpo**, introduza `{"message":"Start my IR"}`. 
    4. Para **autenticação**, selecione **MSI** para utilizar a identidade gerida para o ADF, consulte [identiy gerida do Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artigo para obter mais informações.
    5. Para **Resource**, introduza `https://management.azure.com/`.
    
       ![Agenda de atividades do ADF Web IR do SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clonar o primeiro pipeline para criar uma segunda, o nome da atividade para a alteração **stopMyIR** e substituir as seguintes propriedades.

    1. Para **URL**, introduza o seguinte URL para a API REST que pára o IR Azure-SSIS, substituindo `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, e `{integrationRuntimeName}` com os valores reais para o runtime de integração: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Para **corpo**, introduza `{"message":"Stop my IR"}`. 

4. Criar um pipeline de terceiro, arrastar e soltar uma **executar o pacote do SSIS** atividade a partir de **atividades** caixa de ferramentas para o desenho do pipeline de superfície e configure-o seguindo as instruções em [ Invocar um pacote do SSIS com a atividade de executar o pacote do SSIS no ADF](how-to-invoke-ssis-package-ssis-activity.md) artigo.  Em alternativa, pode utilizar um **Stored Procedure** atividade em vez disso e configurá-lo a seguir as instruções [invocar um pacote do SSIS com a atividade de procedimento armazenado no ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) artigo.  Em seguida, encadear a atividade executar procedimento armazenado/pacote do SSIS entre duas atividades Web que iniciar/parar o runtime de integração, semelhante a essas atividades Web nos pipelines primeiro/segundo.

   ![Atividade de Web do ADF sob demanda IR do SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Atribuir a identidade gerida para o ADF uma **contribuinte** função para si próprio para que as atividades Web no respetivos pipelines podem chamar a API REST para iniciar/parar IRs Azure-SSIS aprovisionado no mesmo.  Na página do ADF no portal do Azure, clique em **controlo de acesso (IAM)**, clique em **+ adicionar atribuição de função**e, em seguida, na **adicionar atribuição de função** painel, fazer as seguintes ações.

    1. Para **função**, selecione **contribuinte**. 
    2. Para **atribuir acesso aos**, selecione **utilizador, grupo ou principal de serviço do Azure AD**. 
    3. Para **selecione**, procure o nome do ADF e selecioná-lo. 
    4. Clique em **Guardar**.
    
   ![ADF geridos atribuição de função de identidade](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Validar o ADF e todas as definições do pipeline ao clicar em **validar se todos os / validar** na barra de ferramentas do factory/pipeline. Fechar **saída da validação do Pipeline de fábrica/** ao clicar em **>>** botão.  

   ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Os pipelines de execução de teste

1. Selecione **execução de teste** na barra de ferramentas para cada pipeline e consulte **saída** janela no painel inferior. 

   ![Execução de teste](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para testar o pipeline de terceiro, inicie o SQL Server Management Studio (SSMS). Na **ligar ao servidor** janela, fazer as seguintes ações. 

    1. Para **nome do servidor**, introduza  **&lt;o nome do servidor de base de dados do Azure SQL&gt;. database.windows.net**.
    2. Selecione **Opções >>**.
    3. Para **ligar à base de dados**, selecione **SSISDB**.
    4. Selecione **Ligar**. 
    5. Expanda **catálogos de serviços de integração** -> **SSISDB** -> sua pasta -> **projetos** -> projeto do Your SSIS -> **pacotes** . 
    6. Com o pacote especificado do SSIS para executar e selecione o botão direito **relatórios** -> **relatórios padrão** -> **todas as execuções**. 
    7. Certifique-se de que foi executado. 

   ![Certifique-se a execução de pacotes do SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Agende os pipelines

Agora que os seus pipelines de funcionam conforme esperado, pode criar acionadores para executá-los em cadences especificados. Para obter detalhes sobre a associação de acionadores com pipelines, consulte [acionar o pipeline com base numa agenda](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artigo.

1. Na barra de ferramentas da pipeline, selecione **acionador** e selecione **novo/editar**. 

   ![Acionador -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Na **adicionar Acionadores** painel, selecione **+ novo**.

   ![Adicionar Acionadores - novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Na **novo acionador** painel, fazer as seguintes ações: 

    1. Para **nome**, introduza um nome para o acionador. No exemplo a seguir **ser executada diariamente,** é o nome do acionador. 
    2. Para **tipo**, selecione **agenda**. 
    3. Para **data de início (UTC)**, introduza uma data de início e hora em UTC. 
    4. Para **periodicidade**, introduza uma cadência para o acionador. No exemplo a seguir, é **diária** depois. 
    5. Para **final**, selecione **sem fim** ou introduza uma data e hora depois de selecionar **na data**. 
    6. Selecione **Activated** para ativar o acionador imediatamente depois de publicar as configurações de ADF inteiras. 
    7. Selecione **Seguinte**.

   ![Acionador -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na **parâmetros da execução do acionador** página, reveja os avisos e selecione **concluir**. 
5. Publicar as configurações de ADF todos selecionando **publicar tudo** na barra de ferramentas da factory. 

   ![Publicar Tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorize os pipelines e acionadores no portal do Azure

1. Para monitorizar as execuções de Acionador e execuções de pipeline, utilize **Monitor** separador no lado esquerdo da interface do Usuário do ADF/aplicação. Para obter passos detalhados, consulte [monitorizar o pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline) artigo.

   ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para ver as execuções de atividades associadas com uma execução de pipeline, selecione a primeira ligação (**ver execuções de atividades**) na **ações** coluna. Para o pipeline de terceiro, verá três execuções de atividades, um para cada atividade em cadeia no pipeline (atividade Web para iniciar o runtime de integração, atividade de procedimento armazenado para executar seu pacote e a atividade da Web para parar o runtime de integração). Para ver o pipeline é executado novamente, selecione **Pipelines** link na parte superior.

   ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para ver as execuções do acionador, selecione **execuções do acionador** na lista pendente em **execuções do Pipeline** na parte superior. 

   ![Execuções de acionador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorize os pipelines e acionadores com o PowerShell

Utilize scripts como nos exemplos seguintes para monitorizar os pipelines e acionadores.

1. Obtenha o estado de uma execução de pipeline.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obter informações sobre um acionador.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Obtenha o estado de uma execução de Acionador.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Criar e agendar o runbook de automatização do Azure que é iniciada/interrompida IR Azure-SSIS

Nesta seção, aprenderá a criar o runbook de automatização do Azure que executa o script do PowerShell, iniciar/parar o runtime de integração Azure-SSIS com base numa agenda.  Isto é útil quando quiser executar scripts adicionais anteriores e posteriores a iniciar/parar o runtime de integração para pré/pós-processamento.

### <a name="create-your-azure-automation-account"></a>Criar a sua conta de automatização do Azure

Se já não tem uma conta de automatização do Azure, crie uma ao seguir as instruções neste passo. Para obter passos detalhados, consulte [criar uma conta de automatização do Azure](../automation/automation-quickstart-create-account.md) artigo. Como parte deste passo, vai criar uma **Run As do Azure** conta (um principal de serviço no Azure Active Directory) e atribuí-lo um **contribuinte** função na sua subscrição do Azure. Certifique-se de que é a mesma subscrição que contém o ADF com ir de SSIS do Azure. A automatização do Azure utiliza esta conta para autenticar para o Azure Resource Manager e operar nos seus recursos. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do Usuário do ADF/aplicação só é suportada no Microsoft Edge e navegadores da web Google Chrome.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
3. Selecione **New** no menu da esquerda, selecione **monitorização + gestão**e selecione **automatização**. 

   ![Novo -> monitorização + gestão -> a automatização](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Na **adicionar conta de automatização** painel, fazer as seguintes ações.

    1. Para **nome**, introduza um nome para a sua conta de automatização do Azure. 
    2. Para **subscrição**, selecione a subscrição que tem o ADF com o ir Azure-SSIS. 
    3. Para **grupo de recursos**, selecione **criar nova** para criar um novo grupo de recursos ou **utilizar existente** para selecionar um já existente. 
    4. Para **localização**, selecione uma localização para a sua conta de automatização do Azure. 
    5. Confirme **criar conta Run do Azure** como **Sim**. Um principal de serviço será criado no Azure Active Directory e atribuído uma **contribuinte** função na sua subscrição do Azure.
    6. Selecione **afixar ao dashboard** exibi-los permanentemente no dashboard do Azure. 
    7. Selecione **Criar**. 

   ![Novo -> monitorização + gestão -> a automatização](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Verá o estado de implementação da sua conta de automatização do Azure no dashboard do Azure e notificações. 
    
   ![Implementar a automatização](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Verá a página inicial da sua conta de automatização do Azure depois de ser criado com êxito. 

   ![Página inicial de automatização](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importar módulos ADF

1. Selecione **módulos** na **recursos PARTILHADOS** secção no menu da esquerda e verifique se tem **AzureRM.DataFactoryV2**  +   **Azurerm. profile** na lista de módulos.

   ![Verifique se os módulos necessários](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se não tiver **AzureRM.DataFactoryV2**, aceda à galeria do PowerShell para [AzureRM.DataFactoryV2 módulo](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), selecione **implementar na automatização do Azure**, selecione o seu Azure Conta de automatização e, em seguida, selecione **OK**. Regresse para ver **módulos** na **recursos PARTILHADOS** secção no menu da esquerda e aguarde até ver **estado** de **AzureRM.DataFactoryV2** módulo foi alterado para **disponível**.

    ![Verifique se o módulo de fábrica de dados](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se não tiver **azurerm. profile**, aceda à galeria do PowerShell para [módulo azurerm. profile](https://www.powershellgallery.com/packages/AzureRM.profile/), selecione **implementar na automatização do Azure**, selecione a automatização do Azure conta e, em seguida, selecione **OK**. Regresse para ver **módulos** na **recursos PARTILHADOS** secção no menu da esquerda e aguarde até ver **estado** do **azurerm. profile** módulo foi alterado para **disponível**.

    ![Verifique se o módulo de perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Criar o seu runbook do PowerShell

A secção seguinte fornece passos para criar um runbook do PowerShell. O script associado a seu runbook seja é iniciada/interrompida IR Azure-SSIS com base no comando especificou para **operação** parâmetro. Esta secção não fornece os detalhes completos para a criação de um runbook. Para obter mais informações, consulte [criar um runbook](../automation/automation-quickstart-create-runbook.md) artigo.

1. Mude para o **Runbooks** separador e selecione **+ adicionar um runbook** da barra de ferramentas. 

   ![Adicione um botão de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecione **criar um runbook novo** e fazer as seguintes ações: 

    1. Para **Name**, introduza **StartStopAzureSsisRuntime**.
    2. Para **tipo de Runbook**, selecione **PowerShell**.
    3. Selecione **Criar**.
    
   ![Adicione um botão de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie e cole o seguinte script do PowerShell para a janela de script do runbook. Guardar e, em seguida, publicar o runbook utilizando **salvar** e **Publish** botões na barra de ferramentas. 

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
    
4. Testar o runbook, selecionando **iniciar** botão na barra de ferramentas. 

   ![Botão de runbook de iniciar](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Na **iniciar Runbook** painel, fazer as seguintes ações: 

    1. Para **nome do grupo de recursos**, introduza o nome do grupo de recursos com o ADF com o ir Azure-SSIS. 
    2. Para **nome da fábrica de dados**, introduza o nome do seu ADF com o ir Azure-SSIS. 
    3. Para **AZURESSISNAME**, introduza o nome do ir Azure-SSIS. 
    4. Para **operação**, introduza **iniciar**. 
    5. Selecione **OK**.  

   ![Iniciar a janela de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Na janela de tarefas, selecione **saída** mosaico. Na janela de saída, aguarde que a mensagem **# # # concluído # # #** depois de confirmar **# # # inicial # # #**. A partir de IR Azure-SSIS demora, aproximadamente 20 minutos. Fechar **tarefa** janela e fazer uma cópia de get para **Runbook** janela.

   ![Azure SSIS IR - iniciada](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita os dois passos anteriores utilizando **parar** como o valor de **operação**. Inicie o runbook novamente selecionando **iniciar** botão na barra de ferramentas. Introduza o seu grupo de recursos, o ADF e o IR Azure-SSIS nomes. Para **operação**, introduza **parar**. Na janela de saída, aguarde que a mensagem **# # # concluído # # #** depois de confirmar **# # # parar # # #**. A parar o IR Azure-SSIS não demorar até a reativá-la. Fechar **tarefa** janela e fazer uma cópia de get para **Runbook** janela.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Criar agendas para o runbook para iniciar/parar IR Azure-SSIS

Na secção anterior, criou o runbook de automatização do Azure que pode iniciar ou parar o ir Azure-SSIS. Nesta secção, irá criar duas agendas para o runbook. Ao configurar o primeiro agendamento, especifique **começar** para **operação**. Da mesma forma, quando configurar a segunda, especifique **parar** para **operação**. Para obter passos detalhados para criar agendas, consulte [criar uma agenda](../automation/automation-schedules.md#creating-a-schedule) artigo.

1. Na **Runbook** janela, selecione **agendas**e selecione **+ adicionar uma agenda** na barra de ferramentas. 

   ![Azure SSIS IR - iniciada](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. Na **agenda Runbook** painel, fazer as seguintes ações: 

    1. Selecione **ligar uma agenda ao runbook**. 
    2. Selecione **criar uma nova agenda**.
    3. Na **nova agenda** painel, introduza **iniciar IR diariamente** para **nome**. 
    4. Para **começa**, introduza um tempo em que está a poucos minutos posterior à hora atual. 
    5. Para **periodicidade**, selecione **periódico**. 
    6. Para **Repetir cada**, introduza **1** e selecione **dia**. 
    7. Selecione **Criar**. 

   ![Agenda de início de IR de SSIS do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Mude para o **parâmetros e definições de execução** separador. Especifique o grupo de recursos, o ADF e o IR Azure-SSIS nomes. Para **operação**, introduza **iniciar** e selecione **OK**. Selecione **OK** novamente para ver a agenda na **agendas** página do runbook. 

   ![Agenda de olhar o runtime de integração de SSIS do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita os dois passos anteriores para criar uma agenda com o nome **IR parar diariamente**. Introduza uma hora que é, pelo menos, 30 minutos após a hora que especificou para **iniciar o runtime de integração diariamente** agenda. Para **operação**, introduza **parar** e selecione **OK**. Selecione **OK** novamente para ver a agenda na **agendas** página do runbook. 

5. Na **Runbook** janela, selecione **tarefas** no menu da esquerda. Deverá ver as tarefas criadas por seus cronogramas especificado em horas e os respetivos Estados. Pode ver os detalhes da tarefa, como sua saída, semelhante ao que tem visto após testou seu runbook. 

   ![Agenda de olhar o runtime de integração de SSIS do Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Depois de terminar de teste, desativar seus cronogramas por edição dos mesmos. Selecione **agendas** no menu da esquerda, selecione **iniciar o runtime de integração diariamente/parar IR diariamente**e selecione **não** para **ativado**. 

## <a name="next-steps"></a>Passos Seguintes
Consulte a seguinte mensagem de blogue:
-   [Modernize e expandir os fluxos de trabalho ETL/ELT com atividades SSIS no ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Veja os artigos seguintes da documentação do SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Implementar, executar e monitorizar pacotes do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Ligar ao catálogo do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Agendar a execução de pacotes no Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
