---
title: Gerir livros de execução na Azure Automation
description: Este artigo diz como gerir os livros de execução na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: af767ab37e8e77195b7d13b24ea78f4fb88485fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122141"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerir livros de execução na Azure Automation

Pode adicionar um livro de execução à Azure Automation, criando um novo ou importando um existente a partir de um ficheiro ou da [Galeria Runbook](automation-runbook-gallery.md). Este artigo fornece informações para a gestão de um livro de recortes importado de um ficheiro. Pode encontrar todos os detalhes de aceder a runbooks e módulos comunitários em [Runbook e galerias de módulos para a Azure Automation.](automation-runbook-gallery.md)

## <a name="create-a-runbook"></a>Criar um runbook

Crie um novo runbook na Azure Automation utilizando o portal Azure ou Windows PowerShell. Uma vez criado o livro de bordo, pode editá-lo utilizando informações em:

* [Editar o livro de texto na Azure Automation](automation-edit-textual-runbook.md)
* [Aprenda os principais conceitos de fluxo de trabalho do Windows PowerShell para os runbooks de automação](automation-powershell-workflow.md)
* [Gerir pacotes Python 2 em Azure Automation](python-packages.md)
* [Gerir pacotes Python 3 (pré-visualização) em Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um livro de corridas no portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do hub, selecione **Runbooks** em **Process Automation** para abrir a lista de runbooks.
3. Clique **em Criar um livro de execução.**
4. Introduza um nome para o livro de bordo e selecione o seu [tipo](automation-runbook-types.md). O nome do livro deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o livro de execução e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um livro de bordo com PowerShell

Utilize o [cmdlet New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) para criar um livro de bordo vazio. Utilize o `Type` parâmetro para especificar um dos tipos de livro definidos para `New-AzAutomationRunbook` .

O exemplo a seguir mostra como criar um novo livro de bordo vazio.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um livro de corridas

Você pode importar um powerShell ou powerShell Workflow (**.ps1**), um runbook gráfico **(.graphrunbook),** ou um script Python 2 ou Python 3 **(.py**) para fazer o seu próprio livro de execução. Deve especificar o tipo de livro de [execução](automation-runbook-types.md) que é criado durante a importação, tendo em conta as seguintes considerações.

* Pode importar um ficheiro **.ps1** que não contenha um fluxo de trabalho num [livro de execução PowerShell](automation-runbook-types.md#powershell-runbooks) ou num [manual de fluxo de trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o importar num livro de trabalho powerShell, é convertido para um fluxo de trabalho. Neste caso, os comentários estão incluídos no livro de bordo para descrever as alterações feitas.

* Pode importar apenas um ficheiro **.ps1** contendo um fluxo de trabalho PowerShell num [manual de fluxo de trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o ficheiro contiver vários fluxos de trabalho powerShell, a importação falha. Deve guardar cada fluxo de trabalho para o seu próprio ficheiro e importar cada um separadamente.

* Não importe um ficheiro **.ps1** contendo um fluxo de trabalho PowerShell num [manual powerShell](automation-runbook-types.md#powershell-runbooks), uma vez que o motor de script PowerShell não o reconhece.

* Apenas importe um ficheiro **.graphrunbook** num novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importe um runbook do portal Azure

Pode utilizar o seguinte procedimento para importar um ficheiro de script para a Azure Automation.

> [!NOTE]
> Só é possível importar um ficheiro **.ps1** num manual de fluxo de trabalho PowerShell utilizando o portal.

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique **em Importar um livro de recortes.**
4. Clique **no ficheiro Runbook** e selecione o ficheiro para importar.
5. Se o campo **Nome** estiver ativado, tem a opção de alterar o nome do livro de verificação. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas pode alterar o tipo depois de ter em conta as restrições aplicáveis.
7. Clique em **Criar**. O novo runbook aparece na lista de runbooks para a conta Automation.
8. Tem de [publicar o livro de recortes](#publish-a-runbook) antes de o poder executar.

> [!NOTE]
> Depois de importar um livro gráfico, pode convertê-lo para outro tipo. No entanto, não é possível converter um livro gráfico num livro de texto.

### <a name="import-a-runbook-with-windows-powershell"></a>Importar um livro com Windows PowerShell

Utilize o [cmdlet Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) para importar um ficheiro de script como um rascunho de livro de recortes. Se o livro já existir, a importação falha a menos que utilize o `Force` parâmetro com o cmdlet.

O exemplo a seguir mostra como importar um ficheiro de script num livro de aplicação.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Gerir recursos

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

Pode recuperar detalhes do runbook, como a pessoa ou conta que iniciou um livro de contas, a partir do [registo de Atividade](automation-runbook-execution.md#activity-logging) para a conta Automation. O exemplo powerShell a seguir fornece o último utilizador a executar o runbook especificado.

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

É uma boa prática autoriar os seus livros de corridas para serem modulares na natureza, com lógica que pode ser reutilizada e reiniciada facilmente. O acompanhamento do progresso num livro de bordo garante que a lógica do livro executa corretamente se houver problemas.

Pode acompanhar o progresso de um livro de execução utilizando uma fonte externa, como uma conta de armazenamento, uma base de dados ou ficheiros partilhados. Crie lógica no seu livro de bordo para verificar primeiro o estado das últimas medidas tomadas. Em seguida, com base nos resultados da verificação, a lógica pode saltar ou continuar tarefas específicas no livro de aplicação.

## <a name="prevent-concurrent-jobs"></a>Prevenir empregos simultâneos

Alguns livros comportam-se de forma estranha se encontrarem vários empregos ao mesmo tempo. Neste caso, é importante que um livro de aplicação da lógica implemente a lógica para determinar se já existe um trabalho em execução. Aqui está um exemplo básico.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

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

Os seus livros de execução devem ser robustos e capazes de lidar com [erros,](automation-runbook-execution.md#errors)incluindo erros transitórios que podem fazê-los reiniciar ou falhar. Se um livro falhar, a Azure Automation retira-o.

Se o seu livro de execução normalmente funcionar dentro de uma restrição de tempo, tenha a lógica de implementação do script para verificar o tempo de execução. Esta verificação garante o funcionamento de operações como arranque, paragem ou escala apenas em horários específicos.

> [!NOTE]
> A hora local no processo de caixa de areia Azure está definida para UTC. Os cálculos para a data e hora nos seus livros devem ter este facto em consideração.

## <a name="work-with-multiple-subscriptions"></a>Trabalhar com várias subscrições

O seu livro de execução deve poder trabalhar com [subscrições.](automation-runbook-execution.md#subscriptions) Por exemplo, para lidar com várias subscrições, o runbook utiliza o cmdlet [Disable-AzContextAutosave.](/powershell/module/Az.Accounts/Disable-AzContextAutosave) Este cmdlet garante que o contexto de autenticação não é recuperado de outro runbook que funciona na mesma caixa de areia. O livro também utiliza o `Get-AzContext` cmdlet para recuperar o contexto da sessão atual, e atribuí-lo à variável `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>Trabalhe com um script personalizado

> [!NOTE]
> Normalmente não é possível executar scripts e livros personalizados no anfitrião com um agente Log Analytics instalado.

Para usar um script personalizado:

1. Criar uma conta De Automação e obter uma [função contribuinte.](automation-role-based-access-control.md)
2. [Ligue a conta ao espaço de trabalho Azure.](../security-center/security-center-enable-data-collection.md)
3. Ativar [o Trabalhador de Runbook Híbrido,](automation-hybrid-runbook-worker.md) [a Gestão de Atualização](./update-management/overview.md)ou outra funcionalidade de Automação. 
4. Se numa máquina Linux, precisa de permissões elevadas. Faça login para [desativar as verificações de assinaturas.](automation-linux-hrw-install.md#turn-off-signature-validation)

## <a name="test-a-runbook"></a>Testar um runbook

Quando se testa um livro de execução, a [versão Draft](#publish-a-runbook) é executada e todas as ações que executa são concluídas. Não foi criado nenhum histórico de trabalho, mas os fluxos [de saída](automation-runbook-output-and-messages.md#use-the-output-stream) e de aviso [e erro](automation-runbook-output-and-messages.md#working-with-message-streams) são apresentados no painel de saída do Teste. As mensagens para o [fluxo verboso](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) só são apresentadas no painel de saída se a variável [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) estiver definida para `Continue` .

Mesmo que a versão do projeto esteja a ser executada, o livro de corridas ainda executa normalmente e executa quaisquer ações contra recursos no ambiente. Por esta razão, só deve testar livros de recortes em recursos não produtivos.

O procedimento para testar cada [tipo de livro é](automation-runbook-types.md) o mesmo. Não há diferença nos testes entre o editor textual e o editor gráfico no portal Azure.

1. Abra a versão Draft do livro de recortes quer no [editor textual](automation-edit-textual-runbook.md) quer no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique em **Teste** para abrir a página de Teste.
1. Se o livro de bordo tiver parâmetros, estão listados no painel esquerdo, onde pode fornecer valores a serem utilizados para o teste.
1. Se pretender executar o teste num [Trabalhador de Runbook Híbrido,](automation-hybrid-runbook-worker.md)altere **as Definições de Execução** para **Trabalhador Híbrido** e selecione o nome do grupo alvo.  Caso contrário, mantenha o **Azure** predefinido para executar o teste na nuvem.
1. Clique **em Iniciar** o teste.
1. Pode utilizar os botões debaixo do painel de saída para parar ou suspender um [fluxo de trabalho powerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou um livro [de recortes gráficos](automation-runbook-types.md#graphical-runbooks) enquanto está a ser testado. Quando suspende o runbook, este conclui a atividade que estava em curso antes de ser suspenso. Depois de o runbook ter sido suspenso, pode pará-lo ou reiniciá-lo.
1. Inspecione a saída do livro de recortes no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um livro de corridas

Quando cria ou importa um novo livro de bordo, deve publicá-lo antes de o poder executar. Cada runbook na Azure Automation tem uma versão Draft e uma versão publicada. Apenas a versão Publicada está disponível para ser executada e só a versão de Rascunho pode ser editada. A versão Publicada não é afetada por quaisquer alterações feitas à versão de Rascunho. Quando a versão Draft deve ser disponibilizada, publica-a, sobreescrevendo a versão atual publicada com a versão Draft.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publique um livro de bordo no portal Azure

1. Abra o livro de correr no portal Azure.
2. Clique em **Editar**.
3. Clique em **Publicar** e, em seguida, **Sim** em resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publique um livro de execução usando o PowerShell

Utilize o [cmdlet Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) para publicar o seu runbook. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Agendar um runbook no portal do Azure

Quando o seu livro de bordo tiver sido publicado, pode agendar para operação:

1. Abra o livro de correr no portal Azure.
2. Selecione **Horários** em **Recursos**.
3. **Selecione Adicione um horário**.
4. No painel 'Schedule Runbook', **selecione Link um horário para o seu runbook**.
5. Escolha **Criar um novo horário** no painel agendamento.
6. Introduza um nome, descrição e outros parâmetros no painel de horários novo.
7. Uma vez criado o horário, realce-o e clique **em OK**. Deve agora estar ligado ao seu livro de bordo.
8. Procure um e-mail na sua caixa de correio para notificá-lo do estado do livro de aplicação.

## <a name="obtain-job-statuses"></a>Obter estatutos de emprego

### <a name="view-statuses-in-the-azure-portal"></a>Ver statuses no portal Azure

Os detalhes do tratamento de emprego na Azure Automation são fornecidos em [Jobs](automation-runbook-execution.md#jobs). Quando estiver pronto para ver os seus trabalhos de runbook, use o portal Azure e aceda à sua conta Automation. À direita, pode ver-se um resumo de todos os trabalhos de runbook em **Estatísticas de Emprego.**

![Azulejo de Estatísticas de Emprego](./media/manage-runbooks/automation-account-job-status-summary.png)

O resumo apresenta uma contagem e representação gráfica do estado de trabalho de cada trabalho executado.

Clicar no azulejo apresenta a página Jobs, que inclui uma lista resumida de todos os trabalhos executados. Esta página mostra o estado, nome do livro, hora de início e tempo de conclusão para cada trabalho.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Screenshot da página Jobs.":::

Pode filtrar a lista de empregos selecionando **trabalhos de filtragem**. Filtrar num livro de bordo específico, estado de trabalho ou uma escolha da lista de abandono e fornecer o intervalo de tempo para a pesquisa.

![Estado do trabalho do filtro](./media/manage-runbooks/automation-account-jobs-filter.png)

Em alternativa, pode visualizar os dados do resumo do trabalho para um livro de contas específico selecionando o livro de recortes da página Runbooks na sua conta Demômes automática e, em seguida, selecionando **Jobs**. Esta ação apresenta a página Jobs. A partir daqui, pode clicar num registo de trabalho para ver os seus detalhes e saída.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Screenshot da página Jobs com o botão Errors realçado.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperar o estatuto de trabalho usando o PowerShell

Utilize o [cmdlet Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) para recuperar os postos de trabalho criados para um livro de bordo e os detalhes de um determinado trabalho. Se iniciar um livro de bordo `Start-AzAutomationRunbook` utilizando, devolve o trabalho resultante. Use [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar a saída do trabalho.

O exemplo a seguir obtém o último trabalho para um livro de amostras e apresenta o seu estado, os valores fornecidos para os parâmetros do livro de bordo e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída para um trabalho específico e devolve cada registo. Se houver uma [exceção](automation-runbook-execution.md#exceptions) para um dos registos, o guião escreve a exceção em vez do valor. Este comportamento é útil uma vez que as exceções podem fornecer informações adicionais que podem não ser registadas normalmente durante a saída.

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

* Para obter detalhes sobre a gestão de [runbook, consulte a execução do Runbook na Azure Automation](automation-runbook-execution.md).
* Para preparar um livro de bordo PowerShell, consulte [editar livros de texto em Azure Automation](automation-edit-textual-runbook.md).
* Para resolver problemas com a execução do livro de [recortes, consulte os problemas do livro de resolução de problemas](troubleshoot/runbooks.md).