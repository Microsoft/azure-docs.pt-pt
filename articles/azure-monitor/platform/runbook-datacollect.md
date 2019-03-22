---
title: Recolha de dados do Log Analytics com um runbook na automatização do Azure | Documentos da Microsoft
description: Tutoriais passo a passo que conduz pela criação de um runbook na automatização do Azure para recolher dados para o repositório para análise pelo Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 67378a5911e5bd83888342aa3773f7f5ed4ccf29
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102589"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Recolher dados no Log Analytics com um runbook da automatização do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode recolher uma quantidade significativa de dados no Log Analytics a partir de várias origens, incluindo [origens de dados](../../azure-monitor/platform/agent-data-sources.md) nos agentes e também [dados recolhidos a partir do Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md). Há um cenários, embora em que precisa recolher dados que não acessível através destas origens padrão. Nestes casos, pode utilizar o [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md) para escrever dados para o Log Analytics a partir de qualquer cliente de REST API. Um método comum para efetuar a recolha de dados está a utilizar um runbook na automatização do Azure.

Este tutorial explica o processo para criar e agendar um runbook na automatização do Azure para escrever dados para o Log Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Este cenário requer os seguintes recursos configurados na sua subscrição do Azure. Ambos podem ser uma conta gratuita.

- [Área de trabalho de análise de registo](../../azure-monitor/learn/quick-create-workspace.md).
- [Conta de automatização do Azure](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Descrição geral do cenário
Para este tutorial, irá escrever um runbook que recolhe informações sobre tarefas de automatização. Os Runbooks na automatização do Azure são implementados com o PowerShell, para que começa pela escrita e teste de um script no editor de automatização do Azure. Depois de verificar que está a recolher as informações necessárias, gravá-los para o Log Analytics e verificar o tipo de dados personalizados. Por fim, irá criar uma agenda para iniciar o runbook em intervalos regulares.

> [!NOTE]
> Pode configurar a automatização do Azure para enviar informações da tarefa para o Log Analytics sem este runbook. Este cenário é utilizado principalmente para suportar o tutorial e recomenda-se que enviar os dados para uma área de trabalho de teste.

## <a name="1-install-data-collector-api-module"></a>1. Instalar o módulo de API do Recoletor de dados
Cada [pedido a partir da API HTTP Data Collector](../../azure-monitor/platform/data-collector-api.md#create-a-request) têm de ser formatados adequadamente e incluir um cabeçalho de autorização. Pode fazê-lo no seu runbook, mas pode reduzir a quantidade de código necessário, através de um módulo que simplifica este processo. É um módulo que pode utilizar [OMSIngestionAPI módulo](https://www.powershellgallery.com/packages/OMSIngestionAPI) na galeria do PowerShell.

Para utilizar um [módulo](../../automation/automation-integration-modules.md) num runbook, tem de ser instalado na sua conta de automatização.  Qualquer runbook na mesma conta, em seguida, pode utilizar as funções no módulo. Pode instalar um novo módulo selecionando **ativos** > **módulos** > **adicionar um módulo** na conta de automatização.

A galeria do PowerShell entanto dá-lhe uma opção rápida para implementar um módulo diretamente à sua conta de automatização, pelo que pode utilizar essa opção para este tutorial.

![Módulo de OMSIngestionAPI](media/runbook-datacollect/OMSIngestionAPI.png)

1. Aceda a [galeria do PowerShell](https://www.powershellgallery.com/).
2. Procure **OMSIngestionAPI**.
3. Clique nas **implementar a automatização do Azure** botão.
4. Selecione a sua conta de automatização e clique em **OK** para instalar o módulo.

## <a name="2-create-automation-variables"></a>2. Criar variáveis de automatização
[Variáveis de automatização](../../automation/automation-variables.md) conter valores que podem ser utilizados por todos os runbooks na conta de automatização. Eles tornam a runbooks mais flexível, permitindo-lhe alterar estes valores sem editar o runbook real. Todas as solicitações da API de Recoletor de dados HTTP requer o ID e a chave da área de trabalho do Log Analytics e recursos de variável são ideais armazenar estas informações.

![Variáveis](media/runbook-datacollect/variables.png)

1. No portal do Azure, navegue para a sua conta de automatização.
2. Selecione **variáveis** sob **recursos partilhados**.
2. Clique em **adicionar uma variável** e criar as duas variáveis na tabela seguinte.

| Propriedade | Valor de ID de área de trabalho | Valor de chave da área de trabalho |
|:--|:--|:--|
| Name | WorkspaceId | WorkspaceKey |
| Type | String | String |
| Value | Cole o ID de área de trabalho de sua área de trabalho do Log Analytics. | Colar com a primária ou uma chave secundária da sua área de trabalho do Log Analytics. |
| Encriptados | Não | Sim |

## <a name="3-create-runbook"></a>3. Criar runbook

A automatização do Azure tem um editor no portal do qual pode editar e testar o runbook. Tem a opção para utilizar o editor de scripts para trabalhar com [PowerShell diretamente](../../automation/automation-edit-textual-runbook.md) ou [criar um runbook gráfico](../../automation/automation-graphical-authoring-intro.md). Para este tutorial, irá trabalhar com um script do PowerShell.

![Editar o runbook](media/runbook-datacollect/edit-runbook.png)

1. Navegue até à sua conta de automatização.
2. Clique em **Runbooks** > **adicionar um runbook** > **criar um runbook novo**.
3. Para o nome do runbook, escreva **tarefas de automatização de recolher**. Para o tipo de runbook, selecione **PowerShell**.
4. Clique em **criar** para criar o runbook e iniciar o editor.
5. Copie e cole o seguinte código no runbook. Veja os comentários no script para explicação do código.
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Runbook de teste
A automatização do Azure inclui um ambiente para [testar o runbook](../../automation/automation-testing-runbook.md) antes de o publicar. Pode inspecionar os dados recolhidos pelo runbook e certifique-se de que escreve para o Log Analytics como esperado antes de publicá-la para produção.

![Runbook de teste](media/runbook-datacollect/test-runbook.png)

1. Clique em **guardar** para guardar o runbook.
1. Clique em **painel de teste** para abrir o runbook no ambiente de teste.
1. Uma vez que o runbook tiver parâmetros, lhe for pedido para introduzir valores para os mesmos. Introduza o nome do grupo de recursos e a automação da conta que irá recolher informações da tarefa de.
1. Clique em **iniciar** no início do runbook.
1. O runbook será iniciado com o estado **em fila** antes de entrar para **em execução**.
1. O runbook deve apresentar uma saída verbosa ideal com os trabalhos recolhidos no formato json. Se não existirem tarefas forem apresentadas, em seguida, não podem ter havido nenhuma tarefa criada na conta de automatização na última hora. Tente iniciar qualquer runbook na conta de automatização e, em seguida, executar o teste novamente.
1. Certifique-se de que a saída não mostra os eventuais erros no comando post para o Log Analytics. Deve ter uma mensagem semelhante ao seguinte.

    ![Saída de postagem](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Certifique-se de registos no Log Analytics
Depois do runbook foi concluída em teste, e verificado que a saída foi recebida com êxito, pode verificar os registos foram criados com uma [pesquisa de registos no Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

![Saída de registo](media/runbook-datacollect/log-output.png)

1. No portal do Azure, selecione a área de trabalho do Log Analytics.
2. Clique em **pesquisa de registos**.
3. Escreva o seguinte comando `Type=AutomationJob_CL` e clique no botão de pesquisa. Observe que o tipo de registo inclui _CL que não seja especificado no script. Esse sufixo é acrescentado automaticamente para o tipo de registo para indicar que é um tipo de registo personalizado.
4. Deverá ver um ou mais registos devolvidos que indica se o runbook está a funcionar conforme esperado.

## <a name="6-publish-the-runbook"></a>6. Publicar o runbook
Depois de verificar se o runbook está a funcionar corretamente, tem de publicá-lo para que pode executá-lo em produção. Pode continuar a editar e testar o runbook sem modificar a versão publicada.

![Publicar o runbook](media/runbook-datacollect/publish-runbook.png)

1. Voltar à sua conta de automatização.
2. Clique em **Runbooks** e selecione **trabalhos da automatização de recolher**.
3. Clique em **edite** e, em seguida **publicar**.
4. Clique em **Sim** quando lhe for pedido para confirmar que pretende substituir a versão publicada anteriormente.

## <a name="7-set-logging-options"></a>7. Definir opções de registo
Para teste, podia ver [uma saída verbosa ideal](../../automation/automation-runbook-output-and-messages.md#message-streams) porque definiu a variável $VerbosePreference no script. Para a produção, terá de definir as propriedades de registo para o runbook para ver uma saída verbosa ideal. Para o runbook utilizado neste tutorial, esta ação apresenta os dados de json que está a ser enviados para o Log Analytics.

![Registo e rastreio](media/runbook-datacollect/logging.png)

1. Nas propriedades de runbook, selecione **registo e rastreio** sob **definições do Runbook**.
2. Alterar a definição para **registos verbosos** ao **no**.
3. Clique em **Guardar**.

## <a name="8-schedule-runbook"></a>8. Agendar runbook
A forma mais comum para iniciar um runbook que recolhe dados de monitorização é agendá-la para ser executado automaticamente. Fazer isso criando uma [agenda na automatização do Azure](../../automation/automation-schedules.md) e anexá-lo ao runbook.

![Agendar runbook](media/runbook-datacollect/schedule-runbook.png)

1. Nas propriedades de runbook, selecione **agendas** sob **recursos**.
2. Clique em **adicionar um agendamento** > **ligar uma agenda ao runbook** > **criar uma nova agenda**.
5. Escreva os seguintes valores para a agenda e clique em **criar**.

| Propriedade | Value |
|:--|:--|
| Name | AutomationJobs-Hourly |
| Inicia | Selecione a que qualquer altura, pelo menos, 5 minutos posterior à hora atual. |
| Recorrência | Periódico |
| Repetir a cada | 1 hora |
| Definir expiração | Não |

Depois de criar a agenda, terá de definir os valores de parâmetro que serão utilizados sempre que esta agenda inicia o runbook.

1. Clique em **configurar parâmetros e definições de execução**.
1. Preencha os valores para o seu **ResourceGroupName** e **AutomationAccountName**.
1. Clique em **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Verifique se o runbook inicia numa agenda
Sempre que um runbook é iniciado, [é criada uma tarefa](../../automation/automation-runbook-execution.md) e qualquer resultado com sessão iniciada. Na verdade, essas são as mesmas tarefas que o runbook está a recolher. Pode verificar que o runbook é iniciado conforme esperado, verificando as tarefas do runbook após a hora de início para a agenda ter passado.

![Tarefas](media/runbook-datacollect/jobs.png)

1. Nas propriedades de runbook, selecione **trabalhos** sob **recursos**.
2. Deverá ver uma lista de tarefas para cada vez que o runbook foi iniciado.
3. Clique das tarefas para ver os detalhes.
4. Clique em **todos os registos** para ver os registos e de saída do runbook.
5. Desloque-se para baixo para localizar uma entrada semelhante à imagem abaixo.<br>![Verboso](media/runbook-datacollect/verbose.png)
6. Clique nesta entrada para ver os dados json detalhado, que lhe foi enviados para o Log Analytics.

## <a name="next-steps"></a>Passos Seguintes
- Uso [estruturador de vistas](../../azure-monitor/platform/view-designer.md) para criar uma vista de exibir os dados já recolhidos para o repositório do Log Analytics.
- Empacotar o runbook numa [solução de gestão](../../azure-monitor/insights/solutions-creating.md) para distribuição a clientes.
- Saiba mais sobre [do Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Saiba mais sobre [automatização do Azure](https://docs.microsoft.com/azure/automation/).
- Saiba mais sobre o [HTTP Data Collector API](../../azure-monitor/platform/data-collector-api.md).
