---
title: Gerir livros de corridas na Automação Azure
description: Este artigo diz como gerir livros de corridas na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 28ddd2a2d75ab8a57dfc3176eefd703f6c43d0b6
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745058"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerir livros de corridas na Automação Azure

Pode adicionar um livro de execução à Azure Automation, criando um novo ou importando um existente a partir de um ficheiro ou da [Galeria Runbook.](automation-runbook-gallery.md) Este artigo fornece informações para a gestão de um livro de execução importado de um ficheiro. Você pode encontrar todos os detalhes de acesso a livros e módulos comunitários em [Runbook e galerias de módulos para Automação Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Criar um runbook

Crie um novo livro de execução na Azure Automation utilizando o portal Azure ou windows PowerShell. Uma vez criado o livro de execução, pode editá-lo usando informações em:

* [Editar livro de texto na Automação Azure](automation-edit-textual-runbook.md) 
* [Conheça os conceitos chave Windows PowerShell Workflow para livros de execução automation](automation-powershell-workflow.md)
* [Autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md)
* [Gerir pacotes Python 2 na Automação Azure](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Crie um livro de corridas no portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do centro, selecione **Runbooks** sob Automatização de **Processos** para abrir a lista de livros de execução.
3. Clique **em Criar um livro de execução**.
4. Introduza um nome para o livro de execução e selecione o seu [tipo](automation-runbook-types.md). O nome do livro de corridas deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** o livro de execução e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Crie um livro de corridas com a PowerShell

Utilize o cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) para criar um livro de execução vazio. Utilize o `Type` parâmetro para especificar um dos tipos de livro definidopara `New-AzAutomationRunbook` .

O exemplo que se segue mostra como criar um novo livro vazio.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um livro de corridas

Pode importar um script PowerShell ou PowerShell Workflow **(.ps1),** um livro de execução gráfico **(.graphrunbook)** ou um script Python 2 **(.py**) para fazer o seu próprio livro de execução.  Deve especificar o tipo de livro de [execução](automation-runbook-types.md) que é criado durante a importação, tendo em conta as seguintes considerações.

* Pode importar um ficheiro **.ps1** que não contenha um fluxo de trabalho num livro de [execução da PowerShell](automation-runbook-types.md#powershell-runbooks) ou num livro de [execução powerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se o importar num livro de fluxos de trabalho powerShell, é convertido num fluxo de trabalho. Neste caso, os comentários estão incluídos no livro de execução para descrever as alterações feitas.

* Só pode importar um ficheiro **.ps1** contendo um PowerShell Workflow num [livro de execução powerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se o ficheiro contiver múltiplos fluxos de trabalho powerShell, a importação falha. Deve guardar cada fluxo de trabalho para o seu próprio ficheiro e importar cada um separadamente.

* Não importe um ficheiro **.ps1** contendo um PowerShell Workflow num livro de [execução powerShell](automation-runbook-types.md#powershell-runbooks), uma vez que o motor de script PowerShell não o reconhece.

* Apenas importe um ficheiro **.graphrunbook** num novo [livro de execução gráfico](automation-runbook-types.md#graphical-runbooks). 

### <a name="import-a-runbook-from-the-azure-portal"></a>Importar um livro de escoamentos do portal Azure

Pode utilizar o seguinte procedimento para importar um ficheiro de script para a Automação Azure.

> [!NOTE]
> Só é possível importar um ficheiro **.ps1** num livro de fluxo sinuoso powerShell utilizando o portal.

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.
3. Clique **em importar um livro de execução**.
4. Clique no **ficheiro Runbook** e selecione o ficheiro para importar.
5. Se o campo **Nome** estiver ativado, tem a opção de alterar o nome do livro de execução. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O tipo de livro de [execução](automation-runbook-types.md) é automaticamente selecionado, mas pode alterar o tipo depois de ter em conta as restrições aplicáveis.
7. Clique em **Criar**. O novo livro de execução aparece na lista de livros de execução para a conta Automation.
8. Tem de publicar o livro de [execução](#publish-a-runbook) antes de o poder executar.

> [!NOTE]
> Depois de importar um livro gráfico, pode convertê-lo em outro tipo. No entanto, não é possível converter um livro de execução gráfico num livro textual.

### <a name="import-a-runbook-with-windows-powershell"></a>Importar um livro de corridas com o Windows PowerShell

Utilize o cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) para importar um ficheiro script como um rascunho de livro de execução. Se o livro de execução já existir, a importação falha a menos que utilize o `Force` parâmetro com o cmdlet.

O exemplo que se segue mostra como importar um ficheiro de script num livro de corridas.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Lidar com recursos

Se o seu livro de execução criar um [recurso,](automation-runbook-execution.md#resources)o script deve verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Recuperar detalhes do registo de atividades

Pode recuperar detalhes do livro de execução, como a pessoa ou conta que iniciou um livro de execução, a partir do [registo de Atividade](automation-runbook-execution.md#activity-logging) sonorizador a conta Automation. O exemplo powerShell seguinte fornece o último utilizador a executar o livro de execução especificado.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Controlar o progresso

É uma boa prática autorizar os seus livros de corridas para serem modulares na natureza, com lógica que pode ser reutilizada e reiniciada facilmente. O acompanhamento do progresso num livro de execução garante que a lógica do livro de recortes executa corretamente se houver problemas. 

Pode acompanhar o progresso de um livro de execução utilizando uma fonte externa, como uma conta de armazenamento, uma base de dados ou ficheiros partilhados. Crie lógica no seu livro de execução para primeiro verificar o estado da última ação tomada. Em seguida, com base nos resultados da verificação, a lógica pode saltar ou continuar tarefas específicas no livro de execução.

## <a name="prevent-concurrent-jobs"></a>Prevenir empregos simultâneos

Alguns livros comportam-se de forma estranha se encontrarem vários empregos ao mesmo tempo. Neste caso, é importante que um livro implemente a lógica para determinar se já existe um trabalho em execução. Aqui está um exemplo básico.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Lidar com erros transitórios num script dependente do tempo

Os seus livros de execução devem ser robustos e capazes de lidar com [erros](automation-runbook-execution.md#errors), incluindo erros transitórios que podem fazê-los reiniciar ou falhar. Se um livro de execução falhar, a Azure Automation tenta-o novamente.

Se o seu livro de execução normalmente estiver dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Este controlo garante o funcionamento de operações como arranque, paragem ou escala apenas em tempos específicos.

> [!NOTE]
> A hora local no processo de caixa de areia Azure está definida para UTC. Os cálculos relativos à data e hora nos seus livros de execução devem ter em conta este facto.

## <a name="work-with-multiple-subscriptions"></a>Trabalhar com várias subscrições

O seu livro de execução deve poder trabalhar com [subscrições](automation-runbook-execution.md#subscriptions). Por exemplo, para lidar com várias subscrições, o livro de execução utiliza o cmdlet [Desactivação-AzContextAutosave.](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Este cmdlet garante que o contexto de autenticação não é recuperado de outro livro de corridas que funciona na mesma caixa de areia. O livro de execução também utiliza o `AzContext` parâmetro no módulo Az cmdlets e passa-lhe o contexto adequado.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>Trabalhe com um roteiro personalizado

> [!NOTE]
> Normalmente não é possível executar scripts e livros personalizados no anfitrião com um agente Log Analytics instalado. 

Para usar um script personalizado:

1. Criar uma conta de Automação e obter uma [função de Contribuinte.](automation-role-based-access-control.md)
2. Ligue a conta ao espaço de [trabalho Azure.](../security-center/security-center-enable-data-collection.md)
3. Ativar [o Trabalhador do Livro híbrido,](automation-hybrid-runbook-worker.md) [gestão de atualizações,](automation-update-management.md)ou outra funcionalidade de Automação. 
4. Se numa máquina linux, precisas de altas permissões. Faça login para [desativar as verificações de assinatura](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testar um runbook

Quando testa um livro de execução, a [versão Draft](#publish-a-runbook) é executada e quaisquer ações que executa são concluídas. Não é criado histórico de trabalho, mas os fluxos de [saída](automation-runbook-output-and-messages.md#use-the-output-stream) e [de aviso e erro](automation-runbook-output-and-messages.md#monitor-message-streams) são apresentados no painel de saída do Teste. As mensagens para o [fluxo verbose](automation-runbook-output-and-messages.md#monitor-message-streams) só são apresentadas no painel de saída se a variável [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) estiver definida para `Continue` .

Mesmo que o projeto de versão esteja a ser executado, o livro de execução ainda executa normalmente e executa quaisquer ações contra recursos no ambiente. Por esta razão, só deve testar livros de execução sobre recursos não produtivos.

O procedimento para testar cada tipo de livro de [execução](automation-runbook-types.md) é o mesmo. Não há diferença nos testes entre o editor textual e o editor gráfico no portal Azure.

1. Abra a versão Draft do livro de execução no [editor textual](automation-edit-textual-runbook.md) ou no [editor gráfico.](automation-graphical-authoring-intro.md)
1. Clique em **Testar** para abrir a página de Teste.
1. Se o livro tiver parâmetros, estão listados no painel esquerdo, onde pode fornecer valores a utilizar para o teste.
1. Se quiser eexecutar o teste num Trabalhador híbrido do livro de [corridas,](automation-hybrid-runbook-worker.md)altere as **Definições** de Execução para **O Trabalhador Híbrido** e selecione o nome do grupo alvo.  Caso contrário, mantenha o **Azure** padrão para executar o teste na nuvem.
1. Clique **em Começar** para iniciar o teste.
1. Pode utilizar os botões sob o painel de saída para parar ou suspender um [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou um livro [gráfico](automation-runbook-types.md#graphical-runbooks) enquanto está a ser testado. Quando suspende o runbook, este conclui a atividade que estava em curso antes de ser suspenso. Depois de o runbook ter sido suspenso, pode pará-lo ou reiniciá-lo.
1. Inspecione a saída do livro de execução no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um livro de corridas

Quando criar ou importar um novo livro de execução, deve publicá-lo antes de o poder executar. Cada livro de execução em Azure Automation tem uma versão Draft e uma versão Publicada. Apenas a versão Publicada está disponível para ser executada e só a versão de Rascunho pode ser editada. A versão Publicada não é afetada por quaisquer alterações feitas à versão de Rascunho. Quando a versão Do Projeto deve ser disponibilizada, publica-a, sobressando a versão atual publicada com a versão Draft.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publique um livro de corridas no portal Azure

1. Abra o livro de corridas no portal Azure.
2. Clique em **Editar**.
3. Clique em **Publicar** e, em **seguida, sim** em resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publique um livro de corridas usando powerShell

Utilize o cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) para publicar o seu livro de execução. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Agende um livro de corridas no portal Azure

Quando o seu livro de execução tiver sido publicado, pode programar para o funcionamento:

1. Abra o livro de corridas no portal Azure.
2. Selecione **Horários** em **Recursos**.
3. Selecione **Adicionar um horário**.
4. No painel 'AgendaR Runbook', selecione Link um horário para o seu livro de **execução**.
5. Escolha **Criar um novo horário** no painel de horários.
6. Introduza um nome, descrição e outros parâmetros no painel de horários novo. 
7. Assim que a programação for criada, realce-a e clique em **OK**. Deve agora estar ligado ao seu livro de corridas.
8. Procure um e-mail na sua caixa de correio para notificá-lo do estado do livro de recortes.

## <a name="obtain-job-statuses"></a>Obter estatuto de emprego

### <a name="view-statuses-in-the-azure-portal"></a>Ver estados no portal Azure

Os detalhes do manuseamento de postos de trabalho na Automação Azure são fornecidos em [Jobs](automation-runbook-execution.md#jobs). Quando estiver pronto para ver os seus trabalhos de livro, use o portal Azure e aceda à sua conta De automação. À direita, pode ver-se um resumo de todos os trabalhos de livro em **Estatísticas do Emprego.** 

![Azulejo sintetizar estatísticas de emprego](./media/manage-runbooks/automation-account-job-status-summary.png)

O resumo apresenta uma contagem e representação gráfica do estatuto de emprego para cada trabalho executado.

Clicar no azulejo apresenta a página Jobs, que inclui uma lista resumida de todos os empregos executados. Esta página mostra o estado, o nome do livro de execução, o tempo de início e o tempo de conclusão para cada trabalho.

![Página de trabalhos da conta de automação](./media/manage-runbooks/automation-account-jobs-status-blade.png)

Pode filtrar a lista de trabalhos selecionando **trabalhos**de filtro . Filtre num livro específico, no estado do trabalho ou numa escolha da lista de abandono escolar e forneça o intervalo de tempo para a pesquisa.

![Estatuto de trabalho de filtragem](./media/manage-runbooks/automation-account-jobs-filter.png)

Em alternativa, pode ver detalhes de resumo de trabalho para um livro de execução específico, selecionando esse livro de execução a partir da página Runbooks na sua conta Automation e, em seguida, selecionando **Jobs**. Esta ação apresenta a página Jobs. A partir daqui, pode clicar num registo de trabalho para ver os seus detalhes e saídas.

![Página de trabalhos da conta de automação](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperar os estatutos de trabalho usando o PowerShell

Utilize o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) para recuperar os postos de trabalho criados para um livro de corridas e os detalhes de um determinado trabalho. Se iniciar um livro de corridas `Start-AzAutomationRunbook` utilizando, devolve o trabalho resultante. Utilize [o Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) para recuperar a saída de emprego.

O exemplo seguinte obtém o último trabalho para um livro de amostras e mostra o seu estado, os valores previstos para os parâmetros do livro de execução e a saída de trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo seguinte recupera a saída para um trabalho específico e devolve cada registo. Se houver uma [exceção](automation-runbook-execution.md#exceptions) para um dos registos, o guião escreve a exceção em vez do valor. Este comportamento é útil, uma vez que as exceções podem fornecer informações adicionais que podem não ser registadas normalmente durante a saída.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Execução de runbooks na Automatização do Azure](automation-runbook-execution.md)
* [Editar livros de texto na Automação Azure](automation-edit-textual-runbook.md)
* [Livros gráficos de autor na Automação Azure](automation-graphical-authoring-intro.md)
