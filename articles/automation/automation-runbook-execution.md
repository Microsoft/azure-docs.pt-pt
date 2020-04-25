---
title: Execução de runbooks na Automatização do Azure
description: Descreve os detalhes de como um livro de execução na Automatização Azure é processado.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 09122581a3ade4e741a29996b7202ce0f96d074b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145535"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbooks na Automatização do Azure

A automatização de processos na Automatização Azure permite-lhe criar e gerir powerShell, PowerShell Workflow e livros gráficos. Para mais detalhes, consulte os livros de [execução da Automação Azure.](automation-runbook-types.md) 

A automatização executa os seus livros com base na lógica definida dentro deles. Se um livro de execução for interrompido, reinicia no início. Este comportamento requer que escreva livros que suportam ser reiniciados se ocorrerem problemas transitórios.

Iniciar um livro de execução na Azure Automation cria um trabalho, que é uma única instância de execução do livro de execução. Cada trabalho acede aos recursos do Azure fazendo uma ligação à sua subscrição Azure. O trabalho só pode aceder a recursos no seu centro de dados se esses recursos estiverem acessíveis a partir da nuvem pública.

A Azure Automation atribui a um trabalhador que dirija cada trabalho durante a execução do livro de corridas. Enquanto os trabalhadores são partilhados por muitas contas Azure, os empregos de diferentes contas da Automação estão isolados uns dos outros. Não pode controlar que trabalhadores presta o seu trabalho.

Quando se vê a lista de livros de execução no portal Azure, mostra o estado de cada trabalho iniciado para cada livro de execução. A Azure Automation armazena registos de trabalho por um período máximo de 30 dias.

O diagrama seguinte mostra o ciclo de vida de um trabalho de livro de corridas para os livros de [execução powerShell,](automation-runbook-types.md#powershell-runbooks)livros de fluxo de [trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)e [livros gráficos](automation-runbook-types.md#graphical-runbooks).

![Estatuto de Emprego - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Onde executar os seus livros de execução

Os livros de execução em Azure Automation podem funcionar numa caixa de areia Azure ou num Trabalhador híbrido do livro de [corridas.](automation-hybrid-runbook-worker.md) Quando os livros são projetados para autenticar e correr contra recursos em Azure, eles funcionam numa caixa de areia Azure, que é um ambiente partilhado que vários trabalhos podem usar. Os trabalhos que utilizam a mesma caixa de areia estão ligados às limitações de recursos da caixa de areia.

>[!NOTE]
>O ambiente de caixa de areia Azure não suporta operações interativas. Também requer o uso de ficheiros MOF locais para livros de execução que fazem chamadas Win32.

Você pode usar um Trabalhador de Runbook Híbrido para executar livros de execução diretamente no computador que acolhe o papel e contra os recursos locais no ambiente. A Azure Automation armazena e gere os livros de execução e depois entrega-os a um ou mais computadores atribuídos.

A tabela seguinte lista algumas tarefas de execução do livro de execução com o ambiente de execução recomendado listado para cada um.

|Tarefa|Recomendação|Notas|
|---|---|---|
|Integrar com recursos Azure|Caixa de areia azure|Hospedada em Azure, a autenticação é mais simples. Se estiver a usar um Trabalhador De Raqui híbrido num VM Azure, pode utilizar [identidades geridas para recursos Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Obtenha um desempenho ideal para gerir os recursos do Azure|Caixa de areia azure|O guião é executado no mesmo ambiente, que tem menos latência.|
|Minimizar os custos operacionais|Caixa de areia azure|Não há despesas de cálculo nem necessidade de um VM.|
|Executar script de longa duração|Função de Trabalho de Runbook Híbrida|As caixas de areia azure têm limites de [recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagir com os serviços locais|Função de Trabalho de Runbook Híbrida|Pode aceder diretamente à máquina hospedeira, ou recursos em outros ambientes de nuvem, ou no seu ambiente no local. |
|Exigir software e executáveis de terceiros|Função de Trabalho de Runbook Híbrida|Gere o sistema operativo e pode instalar software.|
|Monitorize um ficheiro ou pasta com um livro de execução|Função de Trabalho de Runbook Híbrida|Use uma [tarefa observadora](automation-watchers-tutorial.md) num trabalhador híbrido do livro de corridas.|
|Executar um roteiro intensivo de recursos|Função de Trabalho de Runbook Híbrida| As caixas de areia azure têm limites de [recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Utilizar módulos com requisitos específicos| Função de Trabalho de Runbook Híbrida|Alguns exemplos incluem:</br> WinSCP - dependência de winscp.exe </br> Administração IIS - dependência de permitir ou gerir o IIS.|
|Instale um módulo com um instalador|Função de Trabalho de Runbook Híbrida|Os módulos para caixa de areia devem suportar a cópia.|
|Utilize livros ou módulos que exijam versão .NET Framework diferente de 4.7.2|Função de Trabalho de Runbook Híbrida|As caixas de areia de automação suportam .NET Framework 4.7.2, e a atualização para uma versão diferente não é suportada.|
|Executar scripts que requerem elevação|Função de Trabalho de Runbook Híbrida|As caixas de areia não permitem a elevação. Com um Trabalhador híbrido do livro, pode desligar o UAC e utilizar o [Comando Invocado](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) ao executar o comando que requer elevação.|
|Executar scripts que requerem acesso a Instrumentação de Gestão do Windows (WMI)|Função de Trabalho de Runbook Híbrida|Os empregos em caixas de areia na nuvem não podem aceder ao fornecedor de WMI. |

## <a name="using-modules-in-your-runbooks"></a>Usando módulos nos seus livros de execução

A Azure Automation suporta uma série de módulos predefinidos, incluindo os módulos AzureRM (AzureRM.Automation) e um módulo contendo vários cmdlets internos. Também são suportados módulos instalados, incluindo os módulos Az (Az.Automation), atualmente a ser utilizados em preferência aos módulos AzureRM. Para mais detalhes sobre os módulos disponíveis para os seus livros de execução e configurações DSC, consulte [Gerir módulos em Automação Azure](shared-resources/modules.md).

## <a name="creating-resources"></a>Criação de recursos

Se o seu livro de execução criar um recurso, o script deve verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

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

## <a name="supporting-time-dependent-scripts"></a>Apoiar scripts dependentes do tempo

Os seus livros devem ser robustos e capazes de lidar com erros transitórios que possam fazê-los reiniciar ou falhar. Se um livro de execução falhar, a Azure Automation tenta-o novamente.

Se o seu livro de execução normalmente estiver dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Este controlo garante o funcionamento de operações como arranque, paragem ou escala apenas em tempos específicos.

> [!NOTE]
> A hora local no processo de caixa de areia Azure está definida para UTC. Os cálculos relativos à data e hora nos seus livros de execução devem ter em conta este facto.

## <a name="tracking-progress"></a>Acompanhar o progresso

É uma boa prática autorizar os seus livros de corridas para serem modulares na natureza, com lógica que pode ser reutilizada e reiniciada facilmente. Acompanhar o progresso num livro de corridas é uma boa forma de garantir que a lógica do livro executa corretamente se houver problemas. É possível acompanhar o progresso de um livro de execução usando uma fonte externa, como uma conta de armazenamento, uma base de dados ou ficheiros partilhados. Pode criar lógica no seu livro de execução para verificar primeiro o estado da última ação tomada. Em seguida, com base no resultado da verificação, a lógica pode saltar ou continuar tarefas específicas no livro de execução.

## <a name="preventing-concurrent-jobs"></a>Prevenção de empregos simultâneos

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

## <a name="working-with-multiple-subscriptions"></a>Trabalhar com várias subscrições

Para lidar com várias subscrições, o seu livro de execução deve utilizar o cmdlet [Desactivação-AzContextAutosave.](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) Este cmdlet garante que o contexto de autenticação não é recuperado de outro livro de corridas que funciona na mesma caixa de areia. O livro de`AzContext` execução também utiliza o parâmetro no módulo Az cmdlets e passa-lhe o contexto adequado.

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

## <a name="handling-exceptions"></a>Processamento de exceções

Esta secção descreve algumas formas de lidar com exceções ou problemas intermitentes nos seus livros de execução. Um exemplo é uma exceção WebSocket. O manuseamento correto da exceção impede que falhas de rede transitórias causem a falha dos seus livros de execução. 

### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro não terminando. Os erros de terminação terminam `ErrorActionPreference`sempre e não são afetados por .

Quando o livro `ErrorActionPreference`de execução utiliza , `PathNotFound` um erro normalmente não terminante, como o da cmdlet [Get-ChildItem,](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) impede que o livro de execução esteja concluído. O exemplo que se `ErrorActionPreference`segue mostra a utilização de . O comando final [de Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) nunca executa, à medida que o guião para.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Tente apanhar finalmente

[Experimente o Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é utilizado em scripts PowerShell para lidar com erros de terminação. O script pode usar este mecanismo para capturar exceções específicas ou exceções gerais. A `catch` declaração deve ser utilizada para rastrear ou tentar lidar com erros. O exemplo que se segue tenta descarregar um ficheiro que não existe. Apanha a `System.Net.WebException` exceção e devolve o último valor para qualquer outra exceção.

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

### <a name="throw"></a>Arremesso

[O lançamento](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de terminação. Este mecanismo pode ser útil ao definir a sua própria lógica num livro de corridas. Se o guião cumprir um critério que deve `throw` detê-lo, pode usar a declaração para parar. O exemplo seguinte utiliza esta declaração para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="using-executables-or-calling-processes"></a>Utilização de executáveis ou processos de chamada

Os livros de execução que funcionam em caixas de areia Azure não suportam processos de chamada, tais como executáveis (ficheiros **.exe)** ou subprocessos. A razão para isso é que uma caixa de areia Azure é um processo partilhado executado num recipiente que pode não ser capaz de aceder a todas as APIs subjacentes. Para cenários que exijam software de terceiros ou chamadas para subprocessos, deve executar um livro de execução num [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md).

## <a name="accessing-device-and-application-characteristics"></a>Aceder às características do dispositivo e da aplicação

Os trabalhos de livro de corridas que funcionam em caixas de areia Azure não podem aceder a qualquer dispositivo ou características de aplicação. A API mais comum usada para consultar métricas de desempenho no Windows é o WMI, com algumas das métricas comuns a serem a memória e o uso do CPU. No entanto, não importa o que a API é usada, uma vez que os empregos que correm na nuvem não podem aceder à implementação da Microsoft de Web-Based Enterprise Management (WBEM). Esta plataforma baseia-se no Modelo de Informação Comum (CIM), fornecendo as normas da indústria para definir as características do dispositivo e da aplicação.

## <a name="handling-errors"></a>Processar erros

Os seus livros devem ser capazes de lidar com erros. A PowerShell tem dois tipos de erros, terminando e não terminando. Os erros de terminação impedem a execução do livro quando ocorrem. O livro para com um estatuto de trabalho de Falhado.

Erros não terminadores permitem que um script continue mesmo depois de ocorrerem. Um exemplo de um erro não terminante é aquele que `Get-ChildItem` ocorre quando um livro de execução usa o cmdlet com um caminho que não existe. PowerShell vê que o caminho não existe, lança um erro e continua para a próxima pasta. O erro neste caso não define o estatuto de trabalho do livro de execução para falhado, e o trabalho pode até estar concluído. Para forçar um livro de ruma para parar `ErrorAction Stop` num erro não terminante, pode utilizar o cmdlet.

## <a name="handling-jobs"></a>Manuseamento de postos de trabalho

Pode reutilizar o ambiente de execução para trabalhos na mesma conta de Automação. Um único livro de corridas pode ter muitos empregos a funcionar de uma só vez. Quanto mais empregos se gere ao mesmo tempo, mais frequentemente podem ser enviados para a mesma caixa de areia.

Os empregos a funcionar no mesmo processo de caixa de areia podem afetar-se mutuamente. Um exemplo é executar o cmdlet [Disconnect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) A execução deste cmdlet desliga cada trabalho de livro de execução no processo de caixa de areia partilhada.

Os trabalhos da PowerShell começaram a partir de um livro de corridas que funciona numa caixa de areia Azure pode não funcionar em todo o [modo de idioma PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Para obter mais informações sobre a interação com os empregos na Azure Automation, consulte [a Recuperação do estatuto de emprego com](#retrieving-job-status-using-powershell)a PowerShell .

### <a name="job-statuses"></a>Estatuto laboral

A tabela seguinte descreve os estatutos que são possíveis para um trabalho.

| Estado | Descrição |
|:--- |:--- |
| Concluído |A tarefa foi concluída com êxito. |
| Falhou |Um livro de execução de fluxo de trabalho gráfico ou PowerShell não compilou. Um livro de scripts PowerShell não começou ou o trabalho teve uma exceção. Consulte os tipos de [livro de execução da Automação Azure](automation-runbook-types.md).|
| Falhou, à espera de recursos |O trabalho falhou porque atingiu o limite [de ações justas](#fair-share) três vezes e partiu do mesmo posto de controlo ou desde o início do livro de corridas de cada vez. |
| Em fila |O trabalho está à espera que os recursos de um trabalhador da Automação fiquem disponíveis para que possa ser iniciado. |
| A iniciar |O trabalho foi atribuído a um trabalhador, e o sistema está a iniciá-lo. |
| A retomar |O sistema está a retomar o trabalho depois de ter sido suspenso. |
| A executar |A tarefa está a ser executada. |
| Correndo, esperando por recursos |O trabalho foi descarregado porque atingiu o limite de ações justas. Será retomado em breve do seu último posto de controlo. |
| Parada |A tarefa foi parada pelo utilizador antes de ser concluída. |
| A parar |O sistema está a parar o trabalho. |
| Suspenso |Aplica-se apenas aos [livros de execução gráfico e powerShell Workflow.](automation-runbook-types.md) A tarefa foi suspensa pelo utilizador, pelo sistema ou por um comando no runbook. Se um livro não tem um posto de controlo, começa desde o início. Se tiver um posto de controlo, pode recomeçar e retomar a partir do seu último posto de controlo. O sistema só suspende o livro de execução quando ocorre uma exceção. Por predefinição, a `ErrorActionPreference` variável está definida para Continuar, indicando que o trabalho continua a funcionar com um erro. Se a variável de preferência for definida para parar, o trabalho suspende-se por um erro.  |
| A suspender |Aplica-se apenas aos [livros de execução gráfico e powerShell Workflow.](automation-runbook-types.md) O sistema está a tentar suspender o trabalho a pedido do utilizador. O runbook tem de atingir o próximo ponto de verificação antes de poder ser suspenso. Se já passou o seu último posto de controlo, completa antes de poder ser suspenso. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Visualização do estado do emprego no portal Azure

Pode ver um resumo de estado para todos os trabalhos de livro ou perfurar detalhes de um trabalho específico de livro de corridas no portal Azure. Também pode configurar a integração com o seu espaço de trabalho Log Analytics para reencaminhar o estado do trabalho do livro de corridas e os fluxos de emprego. Para obter mais informações sobre a integração com os registos do Monitor Azure, consulte o [estado de trabalho avançado e os fluxos de trabalho da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md).

À direita da sua conta de Automação selecionada, pode ver um resumo de todos os trabalhos de livro de corridas no âmbito do azulejo de Estatísticas do **Emprego.**

![Azulejo sintetizar estatísticas de emprego](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Este azulejo apresenta uma contagem e representação gráfica do estatuto de trabalho para cada trabalho executado.

Clicar no azulejo apresenta a página Jobs, que inclui uma lista resumida de todos os empregos executados. Esta página mostra o estado, o nome do livro de execução, o tempo de início e o tempo de conclusão para cada trabalho.

![Página de trabalhos da conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Pode filtrar a lista de trabalhos selecionando **trabalhos**de filtro . Filtre num livro específico, no estado do trabalho ou numa escolha da lista de abandono escolar e forneça o intervalo de tempo para a pesquisa.

![Estatuto de trabalho de filtragem](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Em alternativa, pode ver detalhes de resumo de trabalho para um livro de execução específico, selecionando esse livro de execução a partir da página Runbooks na sua conta Automation e, em seguida, selecionando o azulejo **Jobs.** Esta ação apresenta a página Jobs. A partir daqui, pode clicar no registo de trabalho para ver os seus detalhes e saídas.

![Página de trabalhos da conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Visualizando o resumo do trabalho

O resumo de trabalho acima descrito permite-lhe olhar para uma lista de todos os empregos criados para um determinado livro de execução e seus estatutos mais recentes. Para ver informações detalhadas e saída para um trabalho, clique no seu nome na lista. A visão detalhada do trabalho inclui os valores dos parâmetros do livro de execução que foram fornecidos a esse trabalho.

Pode utilizar os passos seguintes para ver as tarefas de um runbook.

1. No portal Azure, selecione **Automation** e, em seguida, selecione o nome de uma conta Automation.
2. A partir do centro, selecione **Runbooks** em **Process Automation**.
3. Na página Runbooks, selecione um livro de execução da lista.
3. Na página do livro de execução selecionado, clique no azulejo **Jobs.**
4. Clique num dos trabalhos da lista e veja os seus detalhes e saídas na página de detalhes do trabalho do livro de corridas.

### <a name="retrieving-job-status-using-powershell"></a>Recuperação do estado do trabalho usando powerShell

Utilize o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) para recuperar os postos de trabalho criados para um livro de corridas e os detalhes de um determinado trabalho. Se iniciar um livro de `Start-AzAutomationRunbook`execução com a utilização da PowerShell, devolve o trabalho resultante. Utilize [o Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) para recuperar a saída de emprego.

O exemplo seguinte obtém o último trabalho para um livro de amostras e mostra o seu estado, os valores previstos para os parâmetros do livro de execução e a saída de trabalho.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo seguinte recupera a saída para um trabalho específico e devolve cada registo. Se houver uma exceção para um dos registos, o guião escreve a exceção em vez do valor. Este comportamento é útil, uma vez que as exceções podem fornecer informações adicionais que podem não ser registadas normalmente durante a saída.

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

## <a name="getting-details-from-the-activity-log"></a>Obtenção de detalhes do registo de Atividades

Pode recuperar detalhes do livro de execução, como a pessoa ou conta que iniciou o livro de execução, a partir do registo de Atividade sonorizador a conta Automation. O exemplo powerShell seguinte fornece o último utilizador a executar o livro de execução especificado.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Partilhar recursos entre livros de execução

Para partilhar recursos entre todos os livros na nuvem, a Azure Automation descarrega ou para temporariamente qualquer trabalho que tenha funcionado por mais de três horas. Os postos de trabalho para os livros de [execução da PowerShell](automation-runbook-types.md#powershell-runbooks) e [os livros de execução python](automation-runbook-types.md#python-runbooks) são parados e não reiniciados, e o estado de trabalho torna-se parado.

Para tarefas de longa duração, é aconselhável utilizar um Trabalhador híbrido do livro de corridas. Os trabalhadores híbridos não são limitados por ações justas, e não têm uma limitação de quanto tempo um livro de corridas pode executar. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de trabalho aplicam-se tanto às caixas de areia Azure como aos Trabalhadores híbridos do livro de corridas. Embora os Trabalhadores híbridos não estejam limitados pelo limite de 3 horas de ações justas, você deve desenvolver livros de corridas para executar sobre os trabalhadores que apoiam reinícios de problemas inesperados de infraestruturas locais.

Outra opção é otimizar um livro de corridas utilizando livros infantis. Por exemplo, o seu livro de execução pode passar pela mesma função em vários recursos, como uma operação de base de dados em várias bases de dados. Pode mover esta função para um livro de [execução para crianças](automation-child-runbooks.md) e pedir ao seu livro de execução que o chame usando [o Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Os livros de execução de crianças executam em paralelo em processos separados.

A utilização de livros infantis diminui o tempo total para que o livro de execução dos pais esteja concluído. O seu livro de execução pode utilizar o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) para verificar o estado de trabalho de um livro para crianças se ainda tiver mais operações após a conclusão da criança.

## <a name="next-steps"></a>Passos seguintes

* Para saber como trabalhar com um livro de corridas, consulte [Gerir livros de execução em Automação Azure](manage-runbooks.md).
* Para saber mais sobre os métodos que podem ser usados para iniciar um livro de corridas em Automação Azure, consulte [Iniciar um livro de corridas em Automação Azure.](automation-starting-a-runbook.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
