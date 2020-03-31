---
title: Como agendar o Tempo de Funcionação da Integração Azure-SSIS
description: Este artigo descreve como agendar o início e a paragem do Tempo de Integração Azure-SSIS utilizando a Azure Data Factory.
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
manager: anandsub
ms.openlocfilehash: 5263af2708ee30566e90cdf59ef69f52f76a9d32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440326"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Como iniciar e parar o Azure-SSIS Integration Runtime numa agenda
Este artigo descreve como agendar o início e paragem do Tempo de Funcionamento de Integração Azure-SSIS (IR) utilizando a Azure Data Factory (ADF). O Azure-SSIS IR é um recurso de computação ADF dedicado à execução de pacotes sQL Server Integration Services (SSIS). Executar o Ir Azure-SSIS tem um custo associado a ele. Por isso, normalmente só pretende executar o seu IR quando precisa de executar pacotes SSIS em Azure e parar o seu IR quando já não precisa. Pode utilizar a Interface de Utilizador ADF (UI)/app ou o Azure PowerShell para [iniciar ou parar manualmente o seu IR](manage-azure-ssis-integration-runtime.md)).

Em alternativa, pode criar atividades Web em pipelines ADF para iniciar/parar o seu IR no horário, por exemplo, iniciá-lo de manhã antes de executar as suas cargas diárias de trabalho ETL e detê-lo à tarde depois de terminarem.  Também pode acorrentar uma atividade do Pacote Execute SSIS entre duas atividades Web que iniciam e param o seu IR, para que o seu IR inicie/pare a pedido, mesmo antes/após a execução do seu pacote. Para obter mais informações sobre a atividade do Pacote SSIS execute, consulte [Executar um pacote SSIS utilizando a atividade do Pacote Execute SSIS no](how-to-invoke-ssis-package-ssis-activity.md) artigo do pipeline ADF.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver aprovisionado o seu IR Azure-SSIS, forneça-o seguindo instruções no [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Criar e programar oleodutos ADF que iniciam e ou param o IR Azure-SSIS
Esta secção mostra-lhe como utilizar as atividades web em pipelines ADF para iniciar/parar o seu IR Azure-SSIS no horário previsto ou começar & pará-lo a pedido. Vamos guiá-lo para criar três oleodutos: 

1. O primeiro pipeline contém uma atividade Web que inicia o seu IR Azure-SSIS. 
2. O segundo pipeline contém uma atividade Web que para o seu IR Azure-SSIS.
3. O terceiro pipeline contém uma atividade do Pacote Execute SSIS acorrentada entre duas atividades Web que iniciam/param o seu IR Azure-SSIS. 

Depois de criar e testar esses oleodutos, pode criar um gatilho de horário e associá-lo a qualquer pipeline. O gatilho de horário define um horário para executar o oleoduto associado. 

Por exemplo, pode criar dois gatilhos, o primeiro está programado para funcionar diariamente às 6 da manhã e associado ao primeiro oleoduto, enquanto o segundo está programado para funcionar diariamente às 18:00 e associado ao segundo oleoduto.  Desta forma, tem um período entre as 6:00 e as 18:00 todos os dias quando o seu IR está em execução, pronto para executar as suas cargas diárias de trabalho ETL.  

Se criar um terceiro gatilho que está programado para funcionar diariamente à meia-noite e associado ao terceiro oleoduto, esse gasoduto funcionará à meia-noite todos os dias, começando o seu IR pouco antes da execução do pacote, executando posteriormente o seu pacote, e imediatamente parar o seu IR logo após a execução do pacote, para que o seu IR não esteja a funcionar de braços cruzados.

### <a name="create-your-adf"></a>Crie o seu ADF

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
2. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na página da nova fábrica de **dados,** **introduza myAzureSsisDataFactory** para **nome**. 
      
   ![Página Nova fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   O nome da sua ADF deve ser globalmente único. Se receber o seguinte erro, altere o nome da sua ADF (por exemplo, o seu nomeMyAzureSsisDataFactory) e tente criá-lo novamente. Ver [Data Factory - Naming Rules](naming-rules.md) artigo para aprender sobre regras de nomeação para artefactos ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecione a sua **Subscrição** Azure sob a qual pretende criar o seu ADF. 
5. Para **Grupo de Recursos**, siga um destes passos:
     
   - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
   - Selecione **Criar novos**e insira o nome do seu novo grupo de recursos.   
         
   Para conhecer grupos de recursos, consulte [A utilização de grupos de recursos para gerir o artigo de recursos do Azure.](../azure-resource-manager/management/overview.md)
   
6. Para **versão,** selecione **V2** .
7. Para **localização**, selecione um dos locais suportados para a criação de ADF a partir da lista de drop-down.
8. Selecione **Afixar ao dashboard**.     
9. Clique em **Criar**.
10. No painel azure, verá o seguinte azulejo com estatuto: **Implantação da Fábrica**de Dados . 

    ![Mosaico “implementar a fábrica de dados”](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Depois da criação estar completa, pode ver a sua página ADF como mostrado abaixo.
   
    ![Home page da fábrica de dados](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Clique em **Author & Monitor** para lançar ADF UI/app num separador.

### <a name="create-your-pipelines"></a>Crie os seus oleodutos

1. Na página **Let's start,** **selecione Create pipeline**. 

   ![Página Introdução](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Na caixa de **ferramentas De Atividades,** expanda o menu **geral** e arraste & deixar cair uma atividade **Web** na superfície do designer do pipeline. Em **geral,** a janela de propriedades da atividade, altere o nome da atividade para **iniciar myir**. Mude para o separador **Definições** e faça as seguintes ações.

    1. Para **URL**, introduza o seguinte URL para REST API que `{subscriptionId}` `{resourceGroupName}`inicie o Ir Azure-SSIS, substituindo, , `{factoryName}`e `{integrationRuntimeName}` com os valores reais para o seu IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternativamente, também pode copiar & colar o ID de recursos do seu IR a partir da sua página de monitorização na ADF UI/app para substituir a seguinte parte do URL acima:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![Id de recurso ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Para **método,** selecione **POST**. 
    3. Para **Corpo,** entre . `{"message":"Start my IR"}` 
    4. Para **autenticação**, selecione **MSI** para utilizar a identidade gerida para o seu ADF, consulte [a identidade gerida para](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artigo da Data Factory para obter mais informações.
    5. Para **Recurso,** insira `https://management.azure.com/`.
    
       ![Calendário de atividade web da ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Clone o primeiro oleoduto para criar um segundo, alterando o nome da atividade para **parar myir** e substituindo as seguintes propriedades.

    1. Para **URL,** introduza o seguinte URL para REST API que pare `{subscriptionId}` `{resourceGroupName}`o `{factoryName}`IR `{integrationRuntimeName}` Azure-SSIS, substituindo, , , e com os valores reais para o seu IR:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Para **Corpo,** entre . `{"message":"Stop my IR"}` 

4. Crie um terceiro oleoduto, arraste & deixe cair uma atividade do **Pacote Execute SSIS** a partir da caixa de ferramentas **De atividades** para a superfície do designer de gasodutos, e configure-o seguindo as instruções em [Invocar um pacote SSIS utilizando a atividade do Pacote Execute SSIS no artigo da ADF.](how-to-invoke-ssis-package-ssis-activity.md)  Em alternativa, pode utilizar uma atividade do **Procedimento Armazenado** e configurá-la seguindo as instruções em Invocar um pacote SSIS utilizando a atividade do Procedimento Armazenado no artigo [da ADF.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Em seguida, acorrente a atividade execute o Pacote SSIS/Procedimento Armazenado entre duas atividades Web que iniciam/param o seu IR, semelhante supor as atividades web nos primeiros/segundos oleodutos.

   ![Atividade web da ADF a pedido ssis IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Atribuir a identidade gerida para o seu Papel De **Contribuinte** para si mesmo, para que as atividades web nos seus oleodutos possam chamar a REST API para iniciar/parar iRs Azure-SSIS aprovisionados nele.  Na sua página ADF no portal Azure, clique no controlo de **acesso (IAM)**, clique + Adicionar a atribuição de **funções,** e depois em Adicionar lâmina de atribuição de **papéis,** faça as seguintes ações.

    1. Para **função,** selecione **Contributor**. 
    2. Para **atribuir acesso a**, selecione utilizador, grupo ou diretor de serviço **Azure AD**. 
    3. Para **Selecionar,** procure o seu nome ADF e selecione-o. 
    4. Clique em **Guardar**.
    
   ![Atribuição de Função de Identidade Gerida a Df](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valide o seu ADF e todas as definições do gasoduto clicando em **Validar tudo/Validar** na barra de ferramentas de fábrica/pipeline. Fechar a **saída de validação de fábrica/gasoduto** clicando **>>** no botão.  

   ![Validar o pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Teste executar os seus oleodutos

1. Selecione **Teste Executar** na barra de ferramentas para cada gasoduto e consulte a janela **de saída** no painel inferior. 

   ![Teste](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Para testar o terceiro oleoduto, lance o Estúdio de Gestão de Servidores SQL (SSMS). Na janela **Connect to Server,** faça as seguintes ações. 

    1. Para **o nome do Servidor,** introduza ** &lt;&gt;o nome do servidor de base de dados Azure SQL .database.windows.net**.
    2. Selecione **Opções >>**.
    3. Para **ligar à base de dados,** selecione **SSISDB**.
    4. Selecione **Ligar**. 
    5. Expandir **catálogos** -> de serviços de integração**SSISDB** -> Os seus **projetos** de > de pastas -> os **pacotes**de > de projeto SSIS . 
    6. Clique à direita no pacote SSIS especificado para executar e selecionar **Relatórios** -> **Standard Reportes** -> **Todas as Execuções**. 
    7. Verifique se correu. 

   ![Verifique a execução do pacote SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Agende os seus oleodutos

Agora que os seus oleodutos funcionam como esperava, pode criar gatilhos para executá-los em cadências especificadas. Para mais detalhes sobre a associação de gatilhos com oleodutos, consulte O gatilho do oleoduto num artigo de [agenda.](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)

1. Na barra de ferramentas do gasoduto, selecione **Trigger** e selecione **New/Edit**. 

   ![Gatilho -> Novo/Edição](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. No painel **adicionar gatilhos,** selecione **+ Novo**.

   ![Adicionar Gatilhos - Novo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. No **painel New Trigger,** faça as seguintes ações: 

    1. Para **Nome,** introduza um nome para o gatilho. No exemplo seguinte, **Correr diariamente** é o nome do gatilho. 
    2. Para **Escrever,** selecione **Agendar**. 
    3. Para a data de **início (UTC)**, insira uma data e hora de início na UTC. 
    4. Para **recorrência,** insira uma cadência para o gatilho. No exemplo seguinte, é **o Daily** uma vez. 
    5. Para **terminar,** selecione **No End** ou introduza uma data e hora finais depois de selecionar a data de **entrada**. 
    6. Selecione **Ativado** para ativar o gatilho imediatamente após publicar toda a definição da ADF. 
    7. Selecione **Next**.

   ![Gatilho -> Novo/Edição](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na página **Desativação** de Parâmetros de execução, reveja qualquer aviso e selecione **Terminar**. 
5. Publique todas as definições aDF selecionando **Publicar Tudo** na barra de ferramentas de fábrica. 

   ![Publicar Tudo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorize os seus oleodutos e gatilhos no portal Azure

1. Para monitorizar as correções do gatilho e o gasoduto corre, utilize o separador **Monitor** à esquerda da UI/app ADF. Para obter passos detalhados, consulte monitorize o artigo [do oleoduto.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Execuções de pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Para ver as corridas de atividade associadas a uma execução de gasoduto, selecione o primeiro link **(Ver Activity Runs**) na coluna **Ações.** Para o terceiro pipeline, você verá três corridas de atividade, uma para cada atividade acorrentada no pipeline (atividade web para iniciar a sua atividade de IR, Procedimento Armazenado para executar o seu pacote, e atividade web para parar o seu IR). Para ver o gasoduto correr novamente, selecione a ligação **pipelines** na parte superior.

   ![Execuções de atividade](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Para visualizar as correções do gatilho, selecione **Trigger Runs** a partir da lista de lançamentos em **Pipeline Runs** na parte superior. 

   ![Execuções de acionador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorize os seus oleodutos e gatilhos com a PowerShell

Utilize scripts como os seguintes exemplos para monitorizar os seus oleodutos e gatilhos.

1. Obter o estado de um oleoduto.

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

Nesta secção, aprenderá a criar o livro de execução Da Automatização Azure que executa o script PowerShell, iniciando/parando o seu Ir Azure-SSIS numa programação.  Isto é útil quando pretende executar scripts adicionais antes/depois de iniciar/parar o seu IR para pré/processamento pós-processamento.

### <a name="create-your-azure-automation-account"></a>Crie a sua conta De Automação Azure

Se ainda não tiver uma conta De Automação Azure, crie uma seguindo as instruções neste passo. Para etapas detalhadas, consulte Criar um artigo de [conta Azure Automation.](../automation/automation-quickstart-create-account.md) Como parte deste passo, cria-se uma conta **Azure Run As** (um diretor de serviço no seu Diretório Ativo Azure) e atribui-lhe um papel **de Colaborador** na sua subscrição Azure. Certifique-se de que é a mesma subscrição que contém o seu ADF com o Azure SSIS IR. A Azure Automation utilizará esta conta para autenticar o Gestor de Recursos Azure e operar os seus recursos. 

1. Abra o browser **Microsoft Edge** ou **Google Chrome**. Atualmente, a ADF UI/app só é suportada nos navegadores web Microsoft Edge e Google Chrome.
2. Inicie sessão no [portal do Azure](https://portal.azure.com/).    
3. Selecione **Novo** no menu esquerdo, selecione **Monitoring + Management,** e selecione **Automation**. 

   ![Nova Monitorização -> + Gestão -automação >](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. No painel 'Adicionar Conta **De Automatização',** faça as seguintes ações.

    1. Para **Nome**, insira um nome para a sua conta Azure Automation. 
    2. Para **Subscrição**, selecione a subscrição que tem o seu ADF com O IR Azure-SSIS. 
    3. Para **o grupo Recursos,** selecione **Criar novos** para criar um novo grupo de recursos ou utilizar **existente** para selecionar um existente. 
    4. Para **localização**, selecione um local para a sua conta De automação Azure. 
    5. Confirme **Create Azure Run Como conta** como **sim**. Será criado um diretor de serviço no seu Diretório Ativo Azure e atribuído um papel **de Colaborador** na sua subscrição Azure.
    6. Selecione **Pin o painel para** exibi-lo permanentemente no painel azure. 
    7. Selecione **Criar**. 

   ![Nova Monitorização -> + Gestão -automação >](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Verá o estado de implantação da sua conta Azure Automation no painel de instrumentos e notificações do Azure. 
    
   ![Automatização de implantação](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Verá a página inicial da sua conta Azure Automation depois de criada com sucesso. 

   ![Página inicial da automação](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Módulos ADF de importação

1. Selecione **Módulos** na secção **RECURSOS PARTILHADOS** no menu esquerdo e verifique se tem **Az.DataFactory** + **Az.Profile** na lista de módulos.

   ![Verifique os módulos necessários](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Se não tiver **Az.DataFactory**, vá à PowerShell Gallery para [o módulo Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), selecione Implementar para **automação azure,** selecione a sua conta De automação Azure e, em seguida, selecione **OK**. Volte a ver **Módulos** na secção **RECURSOS PARTILHADOS** no menu esquerdo e aguarde até ver o **STATUS** do módulo **Az.DataFactory** alterado para **Disponível**.

    ![Verifique o módulo data factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Se não tiver **Az.Profile**, vá à PowerShell Gallery para [o módulo Az.Profile](https://www.powershellgallery.com/packages/Az.profile/), selecione Implementar para **Automação Azure,** selecione a sua conta De automação Azure e, em seguida, selecione **OK**. Volte a ver **Módulos** na secção **RECURSOS PARTILHADOS** no menu esquerdo e aguarde até ver o **STATUS** do módulo **Az.Profile** alterado para **Disponível**.

    ![Verifique o módulo Perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Crie o seu livro de execução PowerShell

A secção seguinte fornece passos para a criação de um livro de execução PowerShell. O script associado ao seu livro de execução ou inicia/para o Azure-SSIS IR com base no comando que especifica para o parâmetro **OPERAÇÃO.** Esta secção não fornece todos os detalhes para a criação de um livro de execução. Para mais informações, consulte Criar um artigo [sobre o livro de corridas.](../automation/automation-quickstart-create-runbook.md)

1. Mude para o separador **'Livros'** e selecione + Adicione um livro de **execução** da barra de ferramentas. 

   ![Adicione um botão de livro de corridas](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecione Criar um novo livro de **execução** e fazer as seguintes ações: 

    1. Para **nome**, **introduza StartStopAzureSsisRuntime**.
    2. Para **o tipo de Livro de Execução,** selecione **PowerShell**.
    3. Selecione **Criar**.
    
   ![Adicione um botão de livro de corridas](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Copie & colar o seguinte script PowerShell na janela do script do seu livro de execução. Guarde e publique o seu livro de execução utilizando botões **Save** and **Publish** na barra de ferramentas. 

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

   ![Editar o livro de execução PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Teste o seu livro de execução selecionando o botão **Iniciar** na barra de ferramentas. 

   ![Iniciar botão de livro de corridas](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. No painel **Start Runbook,** faça as seguintes ações: 

    1. Para NOME DE **GRUPO DE RECURSOS,** insira o nome do grupo de recursos que tem o seu ADF com o Azure-SSIS IR. 
    2. Para **DATA FACTORY NAME,** introduza o nome do seu ADF com o Azure-SSIS IR. 
    3. Para **AZURESSISNAME,** insira o nome de Azure-SSIS IR. 
    4. Para **funcionamento,** insira **START**. 
    5. Selecione **OK**.  

   ![Iniciar janela de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Na janela de trabalho, selecione azulejo de **saída.** Na janela de saída, espere pela mensagem **##### Completed #####** after you see **#### Starting #####**. Iniciar o IR Azure-SSIS demora aproximadamente 20 minutos. Feche a janela **do Job** e volte para a janela **runbook.**

   ![Azure SSIS IR - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Repita os dois passos anteriores utilizando o **STOP** como valor para **funcionaMENTO**. Volte a ligar o seu livro de reparações selecionando o botão **Iniciar** na barra de ferramentas. Insira os nomes do seu grupo de recursos, ADF e Azure-SSIS IR. Para **funcionamento,** introduza **STOP**. Na janela de saída, espere pela mensagem **##### Completed #####** after you see **#### Stop #####**. Parar o IR Azure-SSIS não demora tanto tempo a iniciá-lo. Feche a janela **do Job** e volte para a janela **runbook.**

8. Também pode acionar o seu livro de execução através de um webhook que pode ser criado selecionando o item do menu **Webhooks** ou numa programação que pode ser criada selecionando o item do menu **'Agendas'** conforme especificado abaixo.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Crie horários para o seu livro de corridas iniciar/parar O IR Azure-SSIS

Na secção anterior, criou o seu livro de execução Azure Automation que pode iniciar ou parar o IR Azure-SSIS. Nesta secção, irá criar dois horários para o seu livro de corridas. Ao configurar o primeiro horário, especifice **START** para **OPERAÇÃO**. Da mesma forma, ao configurar o segundo, especifice **STOP** para **OPERAÇÃO**. Para dar passos detalhados para criar horários, consulte Criar um artigo de [agenda.](../automation/shared-resources/schedules.md#creating-a-schedule)

1. Na janela **'Livro de Execução',** selecione **Horários**e selecione **+ Adicione um horário** na barra de ferramentas. 

   ![Azure SSIS IR - iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. No painel **'Agenda',** faça as seguintes ações: 

    1. Selecione Link um horário para o seu livro de **execução**. 
    2. Selecione **Criar um novo horário**.
    3. No **painel New Schedule,** insira **Iniciar ir diariamente** para **nome**. 
    4. Para **Iniciars,** insira um tempo que já passa alguns minutos. 
    5. Para **recorrência, selecione** **Recorrente**. 
    6. Para **repetir cada ,** insira **1** e selecione **Dia**. 
    7. Selecione **Criar**. 

   ![Horário para início do IR Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Mude para **Parâmetros e execute** o separador de definições. Especifique os nomes do seu grupo de recursos, ADF e Azure-SSIS IR. Para **funcionamento,** introduza **START** e selecione **OK**. Selecione **OK** novamente para ver o horário na página de **Horários** do seu livro de execução. 

   ![Horário para olhar para o Ir Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Repita os dois passos anteriores para criar um horário chamado **Stop IR diariamente**. Introduza uma hora que seja de pelo menos 30 minutos após o tempo especificado para o horário **diário do Início do IR.** Para **funcionar,** introduza **STOP** e selecione **OK**. Selecione **OK** novamente para ver o horário na página de **Horários** do seu livro de execução. 

5. Na janela **Runbook,** selecione **Jobs** no menu esquerdo. Deve ver os postos de trabalho criados pelos seus horários nos horários especificados e nos seus estatutos. Pode ver os detalhes do trabalho, como a sua saída, semelhante ao que viu depois de testar o seu livro de execução. 

   ![Horário para olhar para o Ir Azure SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Depois de terminar os testes, desative os seus horários editando-os. Selecione **Horários** no menu esquerdo, selecione **Iniciar o IR diariamente/Parar o IR diariamente**, e selecione **Não** for **Ativado**. 

## <a name="next-steps"></a>Passos seguintes
Consulte a seguinte publicação no blog:
-   [Modernizar e alargar os seus fluxos de trabalho ETL/ELT com atividades SSIS em pipelines ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Veja os artigos seguintes da documentação do SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Implementar, executar e monitorizar pacotes do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Ligar ao catálogo do SSIS no Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Agendar a execução de pacotes no Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Ligar a origens de dados no local com a autenticação do Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
