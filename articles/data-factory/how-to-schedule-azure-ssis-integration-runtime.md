---
title: Como agendar o tempo de execução da integração Azure-SSIS
description: Este artigo descreve como agendar o início e paragem do Tempo de Execução da Integração Azure-SSIS utilizando a Azure Data Factory.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384372"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Como iniciar e parar o Azure-SSIS Integration Runtime numa agenda

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve como agendar o início e paragem do Tempo de Execução da Integração Azure-SSIS (IR) utilizando a Azure Data Factory (ADF). Azure-SSIS IR é um recurso computacional da ADF dedicado à execução de pacotes SQL Server Integration Services (SSIS). Executar A Azure-SSIS IR tem um custo associado a ele. Por isso, normalmente, só pretende executar o seu IR quando precisa de executar pacotes SSIS em Azure e parar o seu IR quando já não precisa dele. Pode utilizar a Interface do Utilizador ADF (UI)/app ou a Azure PowerShell para [iniciar ou parar manualmente o seu IR](manage-azure-ssis-integration-runtime.md)).

Em alternativa, pode criar atividades Web em oleodutos ADF para iniciar/parar o seu IR no horário previsto, por exemplo, começando pela manhã antes de executar as suas cargas de trabalho diárias da ETL e impedi-la à tarde depois de terminarem.  Também pode acorrentar uma atividade do Pacote SSIS executo entre duas atividades Web que iniciam e param o seu IR, para que o seu IR comece/pare a pedido, logo antes/após a execução do seu pacote. Para obter mais informações sobre a atividade do Pacote SSIS Execute, consulte [executar um pacote SSIS utilizando a atividade do Pacote SSIS executou em artigo de pipeline ADF.](how-to-invoke-ssis-package-ssis-activity.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não ademca o seu Azure-SSIS IR, forneça-o seguindo as instruções do [tutorial](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Criar e agendar gasodutos ADF que iniciem e ou impeçam o Azure-SSIS IR
Esta secção mostra-lhe como utilizar as atividades web em oleodutos ADF para iniciar/parar o seu Azure-SSIS IR no horário ou começar & impedi-lo a pedido. Vamos guiá-lo a criar três oleodutos: 

1. O primeiro oleoduto contém uma atividade Web que inicia o seu Azure-SSIS IR. 
2. O segundo oleoduto contém uma atividade Web que para o seu Azure-SSIS IR.
3. O terceiro oleoduto contém uma atividade do Pacote SSIS executada acorrentada entre duas atividades Web que iniciam/param o seu Azure-SSIS IR. 

Depois de criar e testar esses oleodutos, pode criar um gatilho de agenda e associá-lo a qualquer oleoduto. O gatilho de horário define um calendário para a execução do gasoduto associado. 

Por exemplo, pode criar dois gatilhos, o primeiro está programado para funcionar diariamente às 6 da manhã e associado ao primeiro oleoduto, enquanto o segundo está programado para funcionar diariamente às 18:00 e associado ao segundo oleoduto.  Desta forma, tem um período entre as 6:00 e as 18:00 horas todos os dias quando o seu IR está em funcionamento, pronto para executar as suas cargas de trabalho diárias da ETL.  

Se criar um terceiro gatilho que esteja programado para funcionar diariamente à meia-noite e associado ao terceiro oleoduto, esse oleoduto funcionará à meia-noite todos os dias, iniciando o seu IR pouco antes da execução do pacote, executando posteriormente o seu pacote, e interrompendo imediatamente o seu IR logo após a execução do pacote, para que o seu IR não esteja a funcionar de braços dado.

### <a name="create-your-adf"></a>Crie o seu ADF

1. Inscreva-se no [portal Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na nova página de **fábrica de dados,** **insira myAzureSsisDataFactory** para **nome**. 
      
   ![Página Nova fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome da sua ADF deve ser globalmente único. Se receber o seguinte erro, altere o nome do seu ADF (por exemplo, o seu nome MyAzureSsisDataFactory) e tente criá-lo novamente. Consulte Data Factory - Artigo [de Regras de Nomeação](naming-rules.md) para saber sobre as regras de nomeação de artefactos ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecione a sua **Assinatura** Azure sob a qual pretende criar o seu ADF. 
5. Para **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - **Selecione Criar novo** e insira o nome do seu novo grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte utilizar grupos de recursos para gerir o seu artigo [de recursos Azure.](../azure-resource-manager/management/overview.md)
   
6. Para **a versão**, selecione **V2** .
7. Para **localização**, selecione um dos locais suportados para a criação de ADF a partir da lista de drop-down.
8. Selecione **Afixar ao dashboard**.     
9. Clique em **Criar**.
10. No painel Azure, verá o seguinte azulejo com estado: **Implantação da Fábrica de Dados**. 

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Depois de concluída a criação, pode ver a sua página ADF como mostrado abaixo.
   
    ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Clique **em Author & Monitor** para lançar ADF UI/app num separador.

### <a name="create-your-pipelines"></a>Crie os seus oleodutos

1. In **Vamos começar a** página, selecione Criar o **pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Em **Activity** toolbox, expanda o menu **geral** e arraste & deixe cair uma atividade **web** na superfície do designer de pipeline. No separador **geral** da janela propriedades da atividade, altere o nome da atividade para **iniciar OMYIR**. Mude para o separador Definições e faça as **seguintes** ações.

    1. Para **URL**, introduza o seguinte URL para REST API que inicia Azure-SSIS IR, `{subscriptionId}` substituindo, , , e com os `{resourceGroupName}` `{factoryName}` `{integrationRuntimeName}` valores reais para o seu IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Em alternativa, também pode copiar & colar o ID de recurso do seu IR a partir da sua página de monitorização na ADF UI/app para substituir a seguinte parte do URL acima: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID de recursos ir da ADF SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Para **método**, selecione **POST**. 
    3. Para **Corpo,** insira `{"message":"Start my IR"}` . 
    4. Para **autenticação**, selecione **MSI** para utilizar a identidade gerida para o seu ADF, consulte identidade gerida para artigo [de Fábrica de Dados](./data-factory-service-identity.md) para obter mais informações.
    5. Para **recurso,** insira `https://management.azure.com/` .
    
       ![ADF Agenda de Atividade Web SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clone o primeiro oleoduto para criar um segundo, alterando o nome de atividade para **parar OMyIR** e substituindo as seguintes propriedades.

    1. Para **URL**, insira o seguinte URL para REST API que pare a Azure-SSIS IR, `{subscriptionId}` substituindo, , , e com os `{resourceGroupName}` `{factoryName}` `{integrationRuntimeName}` valores reais para o seu IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Para **Corpo,** insira `{"message":"Stop my IR"}` . 

4. Crie um terceiro oleoduto, arraste & deixe cair uma atividade do **Pacote SSIS executo** a partir de uma caixa de **ferramentas de atividades** para a superfície do designer do gasoduto e configuure-o seguindo as instruções em [Invocar um pacote SSIS utilizando a atividade do Pacote Executar SSIS em artigo ADF.](how-to-invoke-ssis-package-ssis-activity.md)  Em alternativa, pode utilizar uma atividade **do Procedimento Armazenado** e configurá-la seguindo as instruções da [Invocação de um pacote SSIS utilizando a atividade do Procedimento Armazenado em artigo da ADF.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Em seguida, acorr um bloco de execução/procedimento armazenado entre duas atividades Web que iniciam/param o seu IR, semelhantes às atividades web nos oleodutos de primeira/segunda.

   ![Atividade web da ADF a pedido SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Atribua a identidade gerida para o seu ADF um papel **contribuinte** para si mesmo, para que as atividades web nos seus oleodutos possam chamar a API REST para iniciar/parar os IRs Azure-SSIS previstos no mesmo.  Na sua página ADF no portal Azure, clique em **Access control (IAM)**, clique **+ Adicionar atribuição de funções**, e, em seguida, na lâmina **de atribuição de funções Adicionar,** faça as seguintes ações.

    1. Para **função**, **selecione Contribuinte**. 
    2. Para **atribuir acesso a**, selecione **utilizador, grupo ou principal de serviço Azure.** 
    3. Para **Selecione,** procure o seu nome ADF e selecione-o. 
    4. Clique em **Guardar**.
    
   ![Atribuição de função de identidade gerida pela ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valide as definições do seu ADF e de todos os gasodutos clicando em **Validar tudo/Validar** na barra de ferramentas de fábrica/gasoduto. Feche a **saída de validação de fábrica/gasoduto** clicando **>>** no botão.  

   ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Teste executar os seus oleodutos

1. Selecione **Test Run** na barra de ferramentas para cada oleoduto e veja a janela **de saída** no painel inferior. 

   ![Teste de Corrida](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para testar o terceiro oleoduto, lance o SQL Server Management Studio (SSMS). Na janela **'Ligar ao Servidor',** faça as seguintes ações. 

    1. Para **o nome do Servidor,** insira o nome do seu servidor **&lt; &gt; .database.windows.net**.
    2. Selecione **Opções >>**.
    3. Para **ligar à base de dados,** selecione **SSISDB**.
    4. Selecione **Ligar**. 
    5. Expandir **catálogos de serviços** de integração  ->  **SSISDB** -> A sua pasta -> **Projetos** -> O seu projeto SSIS -> **Pacotes**. 
    6. Clique com o direito no pacote SSIS especificado para executar e selecione **Relatórios**  ->  **De Relatórios**  ->  **Normais Todas as Execuções**. 
    7. Verifique se correu. 

   ![Verifique a execução do pacote SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Agende os seus oleodutos

Agora que os seus oleodutos funcionam como esperava, pode criar gatilhos para executá-los em cadências especificadas. Para obter detalhes sobre a associação de gatilhos com oleodutos, consulte Trigger the pipeline em um artigo [de agenda.](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)

1. Na barra de ferramentas do gasoduto, selecione **Trigger** e selecione **Novo/Editar**. 

   ![Screenshot que realça a opção de menu Trigger-> Novo/Editar.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. No **painel Add Triggers,** selecione **+ Novo**.

   ![Adicionar Gatilhos - Novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. No **painel New Trigger,** faça as seguintes ações: 

    1. Para **nome,** insira um nome para o gatilho. No exemplo seguinte, **Executar diariamente** é o nome do gatilho. 
    2. Para **tipo**, selecione **Agenda**. 
    3. Para **data de início (UTC)**, insira uma data e hora de início na UTC. 
    4. Para **Recorrência,** introduza uma cadência para o gatilho. No exemplo seguinte, é **diário** uma vez. 
    5. Para **terminar**, selecione **No End** ou introduza uma data e hora de fim após a seleção de **Data**. 
    6. **Selecione Ativado** para ativar o gatilho imediatamente após a publicação de toda a definição de ADF. 
    7. Selecione **Seguinte**.

   ![Gatilho -> Novo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na página **De parâmetros de execução do gatilho,** reveja qualquer aviso e selecione **Terminar**. 
5. Publique todas as definições ADF selecionando **Publicar Tudo** na barra de ferramentas de fábrica. 

   ![Publicar Tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorize os seus oleodutos e gatilhos no portal Azure

1. Para monitorizar as correções do gatilho e as correções do gasoduto, utilize o **separador Monitor** à esquerda da ADF UI/app. Para obter etapas detalhadas, consulte Monitor do artigo [do gasoduto.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para visualizar as operações executadas associadas a uma corrida de gasoduto, selecione o primeiro link **(Ver Operações Executadas)** na coluna **Actions.** Para o terceiro oleoduto, você verá três operações executadas, uma para cada atividade acorrentada no pipeline (atividade Web para iniciar a sua atividade de IR, Procedimento Armazenado para executar o seu pacote, e atividade Web para parar o seu IR). Para ver o gasoduto funcionar novamente, selecione **Pipelines** link na parte superior.

   ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para visualizar as correções do gatilho, selecione **Trigger Runs** a partir da lista de drop-down sob **Pipeline Runs** no topo. 

   ![Execuções de acionador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorize os seus oleodutos e gatilhos com o PowerShell

Utilize scripts como os seguintes exemplos para monitorizar os seus oleodutos e gatilhos.

1. Obtenha o estado de uma corrida de oleoduto.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Obtenha informações sobre um gatilho.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Obtenha o estado de um gatilho.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Criar e agendar o runbook da Automatização do Azure que inicia/para o Azure-SSIS IR

Nesta secção, você aprenderá a criar o runbook Azure Automation que executa o script PowerShell, iniciando/interrompendo o seu Azure-SSIS IR em uma programação.  Isto é útil quando pretende executar scripts adicionais antes/depois de iniciar/parar o seu IR para pré/pós-processamento.

### <a name="create-your-azure-automation-account"></a>Crie a sua conta Azure Automation

Se ainda não tiver uma conta Azure Automation, crie uma seguindo as instruções deste passo. Para obter etapas detalhadas, consulte Criar um artigo [de conta Azure Automation.](../automation/automation-quickstart-create-account.md) Como parte deste passo, cria uma conta **Azure Run As** (um diretor de serviço no seu Diretório Ativo Azure) e atribui-lhe um papel **de Contribuinte** na sua subscrição Azure. Certifique-se de que é a mesma subscrição que contém o seu ADF com Azure SSIS IR. A Azure Automation utilizará esta conta para autenticar o Gestor de Recursos da Azure e operar os seus recursos. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a ADF UI/app só é suportada nos navegadores web Microsoft Edge e Google Chrome.
2. Inscreva-se no [portal Azure](https://portal.azure.com/).    
3. Selecione **Novo** no menu esquerdo, selecione **Monitoring + Management**, e selecione **Automation**. 

   ![Screenshot que destaca a opção De Monitorização + Gestão > Automação.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. No painel **de Conta De Adicionar Automação,** faça as seguintes ações.

    1. Para **nome,** insira um nome para a sua conta Azure Automation. 
    2. Para **Subscrição**, selecione a subscrição que tem o seu ADF com Azure-SSIS IR. 
    3. Para **o grupo de recursos,** selecione Criar **novo** para criar um novo grupo de recursos ou utilizar **o existente** para selecionar um existente. 
    4. Para **localização,** selecione uma localização para a sua conta Azure Automation. 
    5. Confirme **Criar Azure Run Como conta** como **Sim**. Será criado um diretor de serviço no seu Diretório Ativo Azure e atribuído um papel **de Contribuinte** na sua subscrição Azure.
    6. Selecione **Pin para painel de instrumentos** para exibi-lo permanentemente no painel Azure. 
    7. Selecione **Criar**. 

   ![Nova Monitorização de -> + Gestão -> Automação](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Verá o estado de implantação da sua conta Azure Automation no painel de instrumentos Escasse e notificações. 
    
   ![Implantação da automatização](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Verá a página inicial da sua conta Azure Automation depois de ter sido criada com sucesso. 

   ![Página inicial da automação](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importar módulos ADF

1. Selecione **Módulos** na secção **RECURSOS PARTILHADOS** no menu esquerdo e verifique se tem **Az.DataFactory**  +  **Az.Profile** na lista de módulos.

   ![Verifique os módulos necessários](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se não tiver **Az.DataFactory,** vá à PowerShell Gallery para módulo [Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), selecione **Implementar para Azure Automation,** selecione a sua conta Azure Automation e, em seguida, selecione **OK**. Volte a ver **os Módulos** na secção RECURSOS **PARTILHADOS** no menu esquerdo e aguarde até ver **o status** do módulo **Az.DataFactory** alterado para **Disponível**.

    ![Verifique o módulo data factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se não tiver **Az.Profile**, vá à PowerShell Gallery para o [módulo Az.Profile](https://www.powershellgallery.com/packages/Az.profile/), selecione **Implementar para Azure Automation,** selecione a sua conta Azure Automation e, em seguida, selecione **OK**. Volte a ver **os Módulos** na secção RECURSOS **PARTILHADOS** no menu esquerdo e aguarde até ver o **status** do módulo **Az.Profile** alterado para **Disponível**.

    ![Verifique o módulo perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Crie o seu livro de execução PowerShell

A secção seguinte fornece passos para a criação de um livro de bordo PowerShell. O script associado ao seu runbook ou começa/para O Azure-SSIS IR com base no comando que especifica para o parâmetro **DE FUNCIONAMENTO.** Esta secção não fornece todos os detalhes para a criação de um livro de recortes. Para obter mais informações, consulte Criar um artigo [de livro de recortes.](../automation/automation-quickstart-create-runbook.md)

1. Mude para **o separador Runbooks** e selecione + Adicione um livro de **execução** da barra de ferramentas. 

   ![Screenshot que realça o botão +Adicionar um botão de runbook.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecione **Criar um novo livro de bordo** e fazer as seguintes ações: 

    1. Para **nomear**, insira **StartStopAzureSsisRuntime**.
    2. Para **o tipo Runbook**, selecione **PowerShell**.
    3. Selecione **Criar**.
    
   ![Adicione um botão de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie & cole o seguinte script PowerShell na janela do script do seu guião. Guarde e publique o seu runbook utilizando botões **Save** and **Publish** na barra de ferramentas. 

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

   ![Editar o runbook PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Teste o seu runbook selecionando o botão **Iniciar** na barra de ferramentas. 

   ![Botão de runbook de início](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. No **painel Start Runbook,** faça as seguintes ações: 

    1. Para **denominação de grupo de recursos,** insira o nome do grupo de recursos que tem o seu ADF com Azure-SSIS IR. 
    2. Para **data factory name**, insira o nome do seu ADF com Azure-SSIS IR. 
    3. Para **AZURESSISNAME,** insira o nome de Azure-SSIS IR. 
    4. Para **OPERAÇÃO**, insira **START**. 
    5. Selecione **OK**.  

   ![Inicie a janela do runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Na janela de trabalho, selecione o azulejo **de saída.** Na janela de saída, aguarde a mensagem **##### Completed #####** depois de ver **#### Starting ####**# . Iniciar o Azure-SSIS IR demora aproximadamente 20 minutos. Feche a janela **job** e volte para a janela **Runbook.**

   ![Screenshot que destaca o azulejo de saída.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita os dois passos anteriores utilizando **STOP** como o valor para **o FUNCIONAMENTO**. Volte a ligar o seu runbook selecionando o botão **Iniciar** na barra de ferramentas. Insira os nomes do seu grupo de recursos, ADF e Azure-SSIS IR. Para **OPERAÇÃO**, insira **STOP**. Na janela de saída, aguarde a mensagem **##### Completed #####** depois de ver **#### Stop ####**# . Parar o Azure-SSIS IR não demora tanto tempo como iniciá-lo. Feche a janela **job** e volte para a janela **Runbook.**

8. Também pode ativar o seu runbook através de um webhook que pode ser criado selecionando o item do menu **Webhooks** ou numa agenda que pode ser criada selecionando o item do menu **Agendas** conforme especificado abaixo.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Crie horários para o seu runbook iniciar/parar Azure-SSIS IR

Na secção anterior, criou o seu runbook Azure Automation que pode iniciar ou parar o Azure-SSIS IR. Nesta secção, irá criar dois horários para o seu runbook. Ao configurar o primeiro horário, especifique **START** para **OPERAÇÃO**. Da mesma forma, ao configurar o segundo, especifique **STOP** for **OPERATION**. Para obter passos detalhados para criar horários, consulte Criar um artigo [de agenda.](../automation/shared-resources/schedules.md#create-a-schedule)

1. Na janela **Runbook,** selecione **Agendas** e selecione **+ Adicione um horário** na barra de ferramentas. 

   ![Azure SSIS IR - começou](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. No **painel de horários runbook,** faça as seguintes ações: 

    1. Selecione **Link um horário para o seu runbook**. 
    2. **Selecione Criar um novo horário**.
    3. No **painel New Schedule,** **insira o Início IR diariamente** para **nome**. 
    4. Para **Arranques**, insira uma hora que já passa alguns minutos da hora atual. 
    5. Para **Recorrência**, selecione **Recorrente**. 
    6. Para **repetir cada**, insira **1** e selecione **Day**. 
    7. Selecione **Criar**. 

   ![Horário para arranque do Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Mude para parâmetros e executar o **separador de definições.** Especifique os nomes do seu grupo de recursos, ADF e Azure-SSIS IR. Para **OPERAR**, insira **START** e selecione **OK**. Selecione **OK** novamente para ver a programação na página **Agendas** do seu runbook. 

   ![Screenshot que realça o campo Operação.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita os dois passos anteriores para criar um horário chamado **Stop IR diariamente**. Insira um tempo que é de pelo menos 30 minutos após o tempo especificado para iniciar o horário **diário do IR.** Para **operar,** insira **STOP** e selecione **OK**. Selecione **OK** novamente para ver a programação na página **Agendas** do seu runbook. 

5. Na janela **Runbook,** selecione **Jobs** no menu esquerdo. Deve ver os empregos criados pelos seus horários nos horários especificados e seus estatutos. Pode ver os detalhes do trabalho, como a sua saída, semelhante ao que viu depois de testar o seu runbook. 

   ![Horário para olhar para o Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Depois de fazer os testes, desative os seus horários editando-os. Selecione **Horários** no menu esquerdo, **selecione Iniciar IR diariamente/Parar IR diariamente**, e selecione **Nº** para **Ativado**. 

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte publicação de blog:
-   [Modernizar e alargar os seus fluxos de trabalho ETL/ELT com atividades SSIS em gasodutos ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Veja os artigos seguintes da documentação do SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Implementar, executar e monitorizar pacotes do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Ligar ao catálogo do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Agendar a execução de pacotes no Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)