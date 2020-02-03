---
title: Execução de runbook na automação do Azure
description: Descreve os detalhes de como um runbook na automação do Azure é processado.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c97e10d2785b7dc1a438c95dca9be94fcef82f94
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714842"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na automação do Azure

Quando você inicia um runbook na automação do Azure, um trabalho é criado. Uma tarefa é uma instância de execução individual de um runbook. Um trabalhador de automação do Azure é atribuído para executar cada trabalho. Embora os trabalhadores sejam compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação são isolados uns dos outros. Você não tem controle sobre qual trabalhador serviços de solicitação para seu trabalho. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. O ambiente de execução para trabalhos da mesma conta de automação pode ser reutilizado. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser expedidos para a mesma área restrita. Os postos de trabalho que correm no mesmo processo de caixa de areia podem afetar-se mutuamente, um exemplo é executar o `Disconnect-AzureRMAccount` cmdlet. A execução desse cmdlet desconectaria cada trabalho de runbook no processo de área restrita compartilhada. Quando você exibe a lista de runbooks no portal do Azure, ele lista o status de todos os trabalhos que foram iniciados para cada runbook. Você pode exibir a lista de trabalhos para cada runbook para acompanhar o status de cada um. Os logs de trabalho são armazenados por um máximo de 30 dias. Para uma descrição dos diferentes estatutos de emprego Estatuto de [Trabalho](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

O diagrama seguinte mostra o ciclo de vida de um trabalho de livro de corridas para livros de [execução PowerShell,](automation-runbook-types.md#powershell-runbooks) [livros gráficos](automation-runbook-types.md#graphical-runbooks) e livros de [workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status dos trabalhos-fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

Seus trabalhos têm acesso aos recursos do Azure fazendo uma conexão com sua assinatura do Azure. Eles só terão acesso aos recursos em seu data center se esses recursos estiverem acessíveis da nuvem pública.

## <a name="where-to-run-your-runbooks"></a>Onde executar seus runbooks

Os livros de execução em Azure Automation podem funcionar numa caixa de areia em Azure ou num Trabalhador híbrido do livro de [corridas.](automation-hybrid-runbook-worker.md) Uma área restrita é um ambiente compartilhado no Azure que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são vinculados pelas limitações de recurso da área restrita. Hybrid runbook workers podem executar runbooks diretamente no computador que está hospedando a função e em relação aos recursos no ambiente para gerenciar esses recursos locais. Os Runbooks são armazenados e gerenciados na automação do Azure e entregues a um ou mais computadores atribuídos. A maioria dos runbooks pode ser facilmente executada nas áreas restritas do Azure. Há cenários específicos em que a escolha de um runbook híbrido em uma área restrita do Azure para executar seu runbook pode ser recomendada. Consulte a tabela a seguir para obter uma lista de alguns cenários de exemplo:

|Tarefa|Melhor opção|Notas|
|---|---|---|
|Integre com recursos do Azure|Área restrita do Azure|Hospedado no Azure, a autenticação é mais simples. Se estiver a usar um Trabalhador De Raqui híbrido num VM Azure, pode utilizar [identidades geridas para recursos Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Desempenho ideal para gerenciar recursos do Azure|Área restrita do Azure|O script é executado no mesmo ambiente, que, por sua vez, tem menos latência|
|Minimizar os custos operacionais|Área restrita do Azure|Não há nenhuma sobrecarga de computação, não há necessidade de uma VM|
|Script de longa execução|Função de Trabalho de Runbook Híbrida|Caixas de areia azure têm [limitação de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagir com serviços locais|Função de Trabalho de Runbook Híbrida|Pode ter acesso diretamente ao computador host|
|Exigir software de terceiros e executáveis|Função de Trabalho de Runbook Híbrida|Você gerencia o sistema operacional e pode instalar o software|
|Monitorar um arquivo ou uma pasta com um runbook|Função de Trabalho de Runbook Híbrida|Use uma [tarefa observadora](automation-watchers-tutorial.md) num trabalhador híbrido do Livro de Corridas|
|Script com uso intensivo de recursos|Função de Trabalho de Runbook Híbrida| Caixas de areia azure têm [limitação de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Usando módulos com requisitos específicos| Função de Trabalho de Runbook Híbrida|Alguns exemplos incluem:</br> **WinSCP** - dependência de winscp.exe </br> **IISAdministration** - Precisa de IIS para ser ativado|
|Instalar o módulo que requer o instalador|Função de Trabalho de Runbook Híbrida|Os módulos para área restrita devem ser copiable|
|Usando runbooks ou módulos que exigem .NET Framework diferentes de 4.7.2|Função de Trabalho de Runbook Híbrida|As áreas restritas de automação têm .NET Framework 4.7.2, e não há nenhuma maneira de atualizá-la|
|Scripts que exigem elevação|Função de Trabalho de Runbook Híbrida|As áreas restritas não permitem elevação. Para resolver isto, utilize um Trabalhador híbrido do livro de corridas e pode desligar o UAC e utilizar `Invoke-Command` ao executar o comando que requer elevação|
|Scripts que exigem acesso ao WMI|Função de Trabalho de Runbook Híbrida|Empregos em funcionamento em caixas de areia na nuvem [não têm acesso ao WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Comportamento do runbook

Os Runbooks são executados com base na lógica definida dentro deles. Se um runbook for interrompido, o runbook será reiniciado no início. Esse comportamento requer que os runbooks sejam gravados de uma maneira onde eles dão suporte à reinicialização, caso haja problemas transitórios.

Os trabalhos do PowerShell iniciados a partir de um runbook executado em uma área restrita do Azure podem não ser executados no modo de linguagem completa. Para saber mais sobre os modos de idioma PowerShell, consulte [os modos de idioma PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Para mais detalhes sobre como interagir com empregos na Azure Automation, consulte recuperar o estatuto de emprego com a [PowerShell](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Criando recursos

Se o script criar recursos, você deverá verificar se o recurso já existe antes de tentar criá-lo novamente. Um exemplo básico é mostrado no exemplo a seguir:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependent-scripts"></a>Scripts dependentes do tempo

Uma consideração cuidadosa deve ser feita durante a criação de runbooks. Como mencionado anteriormente, os runbooks precisam ser criados de forma que eles sejam robustos e possam lidar com erros transitórios que podem fazer com que o runbook reinicie ou falhe. Se um runbook falhar, ele será repetido. Se um runbook normalmente é executado dentro de uma restrição de tempo, a lógica para verificar o tempo de execução deve ser implementada no runbook para garantir que as operações como inicialização, desligamento ou expansão sejam executadas somente durante horários específicos.

> [!NOTE]
> A hora local no processo de área restrita do Azure é definida como hora UTC. Os cálculos para data e hora em seus runbooks precisam levar isso em consideração.

### <a name="tracking-progress"></a>Progresso do rastreamento

É uma boa prática criar runbooks para serem modulares por natureza. Isso significa estruturar a lógica no runbook de forma que ela possa ser reutilizada e reiniciada facilmente. Acompanhar o progresso em um runbook é uma boa maneira de garantir que a lógica em um runbook seja executada corretamente se houver problemas. Algumas maneiras possíveis de acompanhar o progresso do runbook é usando uma fonte externa, como contas de armazenamento, um banco de dados ou arquivos compartilhados. Acompanhando o estado externamente, você pode criar lógica em seu runbook para verificar primeiro o estado da última ação realizada pelo runbook. Em seguida, com base nos resultados, pule ou continue as tarefas específicas no runbook.

### <a name="prevent-concurrent-jobs"></a>Impedir trabalhos simultâneos

Alguns runbooks podem se comportar de forma invariada se estiverem em execução em vários trabalhos ao mesmo tempo. Nesse caso, é importante implementar a lógica para verificar se um runbook já tem um trabalho em execução. Um exemplo básico de como você pode fazer esse comportamento é mostrado no exemplo a seguir:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Trabalhar com várias subscrições

Ao autorizar livros de execução que lidam com várias subscrições, o seu livro de execução precisa de utilizar o cmdlet [Desactivação-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) para garantir que o seu contexto de autenticação não seja recuperado de outro livro de execução que possa estar a funcionar na mesma caixa de areia. Em seguida, tem de utilizar o parâmetro `-AzureRmContext` na sua `AzureRM` cmdlets e passar-lhe o seu contexto adequado.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Processamento de exceções

Ao criar scripts, é importante poder lidar com exceções e possíveis falhas intermitentes. A seguir estão algumas maneiras diferentes de lidar com exceções ou problemas intermitentes com seus runbooks:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

A variável [de preferência $ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como a PowerShell reage a um erro não terminando. Os erros de terminação não são afetados por `$ErrorActionPreference`, terminam sempre. Ao utilizar `$ErrorActionPreference`, um erro normalmente não terminante como `PathNotFound` do `Get-ChildItem` cmdlet impedirá que o livro de execução esteja concluído. O exemplo que se segue mostra a utilização `$ErrorActionPreference`. A linha final `Write-Output` nunca será executada como o guião vai parar.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Tentar capturar finalmente

[O Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é utilizado nos scripts PowerShell para ajudá-lo a lidar com erros de terminação. Usando try catch, você pode capturar exceções específicas ou exceções gerais. A instrução Catch deve ser usada para rastrear erros ou usada para tentar lidar com o erro. O exemplo a seguir tenta baixar um arquivo que não existe. Apanha a `System.Net.WebException` exceção, se houver outra exceção, o último valor é devolvido.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Arremesso

[O lançamento](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de terminação. Isso pode ser útil ao definir sua própria lógica em um runbook. Se for em dia determinados critérios que devam parar o script, pode usar `throw` para parar o script. O exemplo seguinte mostra à máquina um parâmetro de função necessário através da utilização `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Usando executáveis ou processos de chamada

Os Runbooks executados em áreas restritas do Azure não dão suporte a processos de chamada (como um. exe ou subprocesso. Call). Isso ocorre porque as áreas restritas do Azure são processos compartilhados executados em contêineres, que podem não ter acesso a todas as APIs subjacentes. Para cenários em que necessita de software de terceiros ou de chamadas de subprocessos, recomenda-se que execute o livro de corridas num Trabalhador híbrido do livro de [corridas.](automation-hybrid-runbook-worker.md)

### <a name="device-and-application-characteristics"></a>Características do dispositivo e do aplicativo

Os trabalhos de runbook executados em áreas restritas do Azure não têm acesso a nenhuma característica de dispositivo ou aplicativo. A API mais comum usada para consultar métricas de desempenho no Windows é o WMI. Algumas dessas métricas comuns são o uso de memória e CPU. No entanto, não importa qual API é usada. Os trabalhos em execução na nuvem não têm acesso à implementação da Microsoft do WBEM (Web Based Enterprise Management), que se baseia no modelo CIM (CIM), que são os padrões do setor para definir características de dispositivo e aplicativo.

## <a name="job-statuses"></a>Status do trabalho

A tabela seguinte descreve os diferentes estados possíveis das tarefas. O PowerShell tem dois tipos de erros, encerrando e não encerrando erros. Erros de terminação definir amedrado o estado do livro de execução para **Failed** se ocorrerem. Os erros de não finalização permitem que o script continue mesmo depois que eles ocorrerem. Um exemplo de um erro não terminando é usar o `Get-ChildItem` cmdlet com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua para a próxima pasta. Este erro não definiria o estado do livro de execução para **Failed** e poderia ser marcado como **Concluído**. Para forçar um livro de ruma para parar num erro não terminante, pode utilizar `-ErrorAction Stop` no cmdlet.

| Estado | Descrição |
|:--- |:--- |
| Concluída |A tarefa foi concluída com êxito. |
| Falhou |Para os livros de execução de fluxo de [trabalho gráfico e powershell,](automation-runbook-types.md)o livro de execução não compilou. Para os livros de [scripts PowerShell,](automation-runbook-types.md)o livro de execução não começou ou o trabalho teve uma exceção. |
| Falha, aguardando recursos |O trabalho falhou porque atingiu o limite [de ações justas](#fair-share) três vezes e partiu do mesmo posto de controlo ou desde o início do livro de corridas de cada vez. |
| Em fila |A tarefa está a aguardar a disponibilização de recursos num trabalho de Automatização para que possa ser iniciada. |
| A iniciar |O trabalho foi atribuído a um trabalho e o sistema o está iniciando. |
| A retomar |O sistema está retomando o trabalho depois de ser suspenso. |
| A executar |A tarefa está a ser executada. |
| Em execução, aguardando recursos |O trabalho foi descarregado porque atingiu o limite [de ações justas.](#fair-share) Ele é retomado em breve do último ponto de verificação. |
| Parado |A tarefa foi parada pelo utilizador antes de ser concluída. |
| A parar |O sistema está parando o trabalho. |
| Suspenso |A tarefa foi suspensa pelo utilizador, pelo sistema ou por um comando no runbook. Se um runbook não tiver um ponto de verificação, ele será iniciado a partir do início do runbook. Se ele tiver um ponto de verificação, ele poderá ser iniciado novamente e retomado do último ponto de verificação. O runbook só é suspenso pelo sistema quando ocorre uma exceção. Por predefinição, errorActionPreference está definido para **Continuar**, o que significa que o trabalho continua a funcionar com um erro. Se esta variável de preferência for definida para **parar,** então o trabalho suspende-se por um erro. Aplica-se apenas aos livros de execução de fluxo de [trabalho gráfico e powerShell.](automation-runbook-types.md) |
| A suspender |O sistema está tentando suspender o trabalho na solicitação do usuário. O livro de execução deve chegar ao seu próximo posto de controlo antes de poder ser suspenso. Se já passou seu último ponto de verificação, ele é concluído antes que possa ser suspenso. Aplica-se apenas aos livros de execução de fluxo de [trabalho gráfico e powerShell.](automation-runbook-types.md) |

## <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure

Você pode exibir um status resumido de todos os trabalhos de runbook ou analisar detalhes de um trabalho de runbook específico no portal do Azure. Você também pode configurar a integração com seu espaço de trabalho Log Analytics para encaminhar os fluxos de trabalho e o status do trabalho de runbook. Para obter mais informações sobre a integração com os registos do Monitor Azure, consulte o [estado de trabalho avançado e os fluxos de trabalho da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Resumo de trabalhos de runbook de automação

À direita da sua conta de Automação selecionada, pode ver um resumo de todos os trabalhos de livro de trabalho no azulejo de Estatísticas de **Emprego.**

![Bloco de estatísticas de trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Esse bloco exibe uma contagem e representação gráfica do status do trabalho para todos os trabalhos executados.

Clicar no azulejo apresenta a página **Jobs,** que inclui uma lista resumida de todos os empregos executados. Esta página mostra o status, os horários de início e os horários de conclusão.

![Página de trabalhos da conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Pode filtrar a lista de trabalhos selecionando **trabalhos de filtro** e filtrar num livro específico, estado de trabalho ou a partir da lista de abandono, e o intervalo de tempo para pesquisar no seu interior.

![Filtrar status do trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Em alternativa, pode ver detalhes de resumo de trabalho para um livro de execução específico, selecionando esse livro de execução a partir da página **Runbooks** na sua conta Automation e, em seguida, selecionar o azulejo **Jobs.** Esta ação apresenta a página **Jobs,** e a partir daí pode clicar no registo de trabalho para ver os seus detalhes e saídas.

![Página de trabalhos da conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Resumo da Tarefa

Você pode exibir uma lista de todos os trabalhos que foram criados para um runbook específico e seu status mais recente. Pode filtrar esta lista pelo estado do trabalho e o intervalo de datas para a última alteração ao trabalho. Para exibir suas informações e saída detalhadas, clique no nome de um trabalho. A vista detalhada da tarefa inclui os valores dos parâmetros do runbook fornecidos para essa tarefa.

Pode utilizar os passos seguintes para ver as tarefas de um runbook.

1. No portal Azure, selecione **Automation** e, em seguida, selecione o nome de uma conta Automation.
2. A partir do centro, selecione **Runbooks** e, em seguida, na página **Runbooks** selecione um livro de execução da lista.
3. Na página do livro de execução selecionado, clique no azulejo **Jobs.**
4. Clique em um dos trabalhos na lista e, na página de detalhes do trabalho de runbook, você pode exibir seus detalhes e saída.

## <a name="retrieving-job-status-using-powershell"></a>Recuperando o status do trabalho usando o PowerShell

Pode utilizar o [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) para recuperar os postos de trabalho criados para um livro de corridas e os detalhes de um determinado trabalho. Se iniciar um livro de execução com a PowerShell utilizando o [Start-AzureRmAutomationRunbook,](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)então devolve o trabalho resultante. Utilize [o Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para obter a saída de um emprego.

Os comandos de exemplo a seguir recuperam o último trabalho para um exemplo de runbook e exibem seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. No caso de houvesse uma exceção para um dos registros, a exceção é gravada em vez do valor. Esse comportamento é útil, pois as exceções podem fornecer informações adicionais, que podem não ser registradas normalmente durante a saída.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="get-details-from-activity-log"></a>Obter detalhes do log de atividades

Outros detalhes, como a pessoa ou a conta que iniciou o runbook, podem ser recuperados do log de atividades da conta de automação. O exemplo do PowerShell a seguir fornece o último usuário para executar o runbook em questão:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Compartilhamento justo

Para compartilhar recursos entre todos os runbooks na nuvem, a automação do Azure descarrega temporariamente ou interrompe qualquer trabalho que tenha sido executado por mais de três horas. Os empregos para [os livros de execução baseados na PowerShell](automation-runbook-types.md#powershell-runbooks) e [os livros de execução python](automation-runbook-types.md#python-runbooks) são parados e não reiniciados, e o estado de trabalho mostra Parado.

Para tarefas de longa duração, é aconselhável utilizar um Trabalhador híbrido do livro de [corridas.](automation-hrw-run-runbooks.md#job-behavior) Hybrid runbook Workers não são limitados por Fair share e não têm uma limitação de quanto tempo um runbook pode executar. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de trabalho aplicam-se tanto às caixas de areia Azure como aos Trabalhadores híbridos do livro de corridas. Embora Hybrid runbook Workers não sejam limitados pelo limite de compartilhamento justo de 3 horas, os runbooks executados neles devem ser desenvolvidos para dar suporte a comportamentos de reinicialização de problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook usando runbooks filho. Se o seu livro de recortes passar pela mesma função em vários recursos, como uma operação de base de dados em várias bases de dados, pode mover essa função para um [livro](automation-child-runbooks.md) infantil e chamá-lo com o cmdlet [Start-AzureRMAutomationRunbook.](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Cada um desses runbooks filho é executado em paralelo em processos separados. Esse comportamento diminui a quantidade total de tempo para o runbook pai ser concluído. Pode utilizar o cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) no seu livro de execução para verificar o estado de trabalho de cada criança se houver operações que realizem após o fim do livro infantil.

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um livro de corridas em Automação Azure, consulte [Iniciar um livro de corridas em Automação Azure](automation-starting-a-runbook.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
