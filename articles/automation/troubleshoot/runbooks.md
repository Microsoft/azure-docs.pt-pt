---
title: Resolver problemas de erros com Runbooks de automatização do Azure
description: Saiba como resolver problemas com os runbooks de automatização do Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 77f6a48402aef5b493b115d2137ccb6a507050b7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760210"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Resolver problemas de erros com runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erros de autenticação ao trabalhar com runbooks de automatização do Azure

### <a name="sign-in-failed"></a>Cenário: Inicie sessão na conta do Azure falhou

#### <a name="issue"></a>Problema

Receber o erro seguinte ao trabalhar com o `Add-AzureAccount` ou `Connect-AzureRmAccount` cmdlets.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Causa

Este erro ocorre se o nome de recurso de credencial não é válido. Este erro também pode ocorrer se o nome de utilizador e palavra-passe que utilizou para configurar o recurso de credencial de automatização não são válidas.

#### <a name="resolution"></a>Resolução

Para determinar qual é o problema, siga os passos seguintes:  

1. Certifique-se de que não tem carateres especiais. Estes carateres incluem o **\@** caráter no nome do elemento de credencial de automatização que está a utilizar para ligar ao Azure.  
2. Verifique se pode utilizar o nome de utilizador e palavra-passe que são armazenados na credencial da automatização do Azure no seu editor do ISE do PowerShell local. Pode fazê-Verifique o nome de utilizador e palavra-passe estão corretos, executando os seguintes cmdlets no ISE do PowerShell:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a autenticação falhar localmente, significa que não configurou as suas credenciais do Azure Active Directory corretamente. Consulte a [a autenticação no Azure com o Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) mensagem de blogue para obter a conta do Azure Active Directory configurada corretamente.  

4. Se ele se parece com um erro transitório, tente adicionar a lógica de repetição para sua rotina de autenticação para fazer a autenticação mais robusto.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Cenário: Não é possível encontrar a subscrição do Azure

#### <a name="issue"></a>Problema

Receber o erro seguinte ao trabalhar com o `Select-AzureSubscription` ou `Select-AzureRmSubscription` cmdlets:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Erro

Este erro pode ocorrer se:

* O nome da subscrição não é válido

* O utilizador do Azure Active Directory que está a tentar obter os detalhes da subscrição não está configurado como um administrador da subscrição.

#### <a name="resolution"></a>Resolução

Siga os passos seguintes para determinar se tive autenticado para o Azure e ter acesso à subscrição que está a tentar selecionar:  

1. Para garantir que funciona autónomo, teste o seu script fora da automatização do Azure.
2. Certifique-se de que executa o `Add-AzureAccount` cmdlet antes de executar o `Select-AzureSubscription` cmdlet. 
3. Adicionar `Disable-AzureRmContextAutosave –Scope Process` no início do runbook. Este cmdlet garante que quaisquer credenciais aplica-se apenas para a execução do runbook atual.
4. Se continuar a ver esta mensagem de erro, modifique o código, adicionando a **AzureRmContext** seguinte parâmetro o `Add-AzureAccount` cmdlet e, em seguida, executar o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Cenário: Falha na autenticação para o Azure, porque a autenticação multifator está ativada

#### <a name="issue"></a>Problema

Receber o erro seguinte durante a autenticação do Azure com o seu nome de utilizador do Azure e a palavra-passe:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Causa

Se tiver de autenticação multifator na sua conta do Azure, não é possível utilizar um utilizador do Azure Active Directory para autenticar para o Azure. Em vez disso, tem de utilizar um certificado ou um principal de serviço para autenticar para o Azure.

#### <a name="resolution"></a>Resolução

Para utilizar um certificado com os cmdlets do modelo de implementação clássica do Azure, veja [criação e adição de um certificado para gerir serviços do Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para utilizar um principal de serviço com os cmdlets do Azure Resource Manager, veja [criar através do portal do Azure principal de serviço](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticar um principal de serviço com o Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Erros comuns ao trabalhar com runbooks

###<a name="child-runbook-object"></a>Runbook subordinado devolve um erro quando o fluxo de saída contém objetos em vez de tipos de dados simples

#### <a name="issue"></a>Problema

Receber o seguinte erro ao invocar uma childrunbook com o `-Wait` comutador e o fluxo de saída contém e de objeto:

```
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Causa

Existe um problema conhecido em que o [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) não manipula o fluxo de saída corretamente se contiver objetos.

#### <a name="resolution"></a>Resolução

Para resolver esta situação, é recomendável que, em vez disso, implementa uma lógica de consulta e utilize o [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet para obter o resultado. Um exemplo essa lógica é definido no exemplo a seguir.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Cenário: Verá um erro em seus fluxos de trabalho sobre o método de get_SerializationSettings

#### <a name="issue"></a>Problema

Consulte o erro em seus fluxos de trabalho para um runbook com a seguinte mensagem:

```
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Causa

Este erro é causado utilizando os cmdlets do AzureRM e Az num runbook. Isso ocorre quando importa `Az` antes de importar `AzureRM`.

#### <a name="resolution"></a>Resolução

Cmdlets de AZ e AzureRM não pode ser importados e utilizado no mesmo runbook, para saber mais sobre o suporte de Az na automatização do Azure, veja [suporte de módulo de Az na automatização do Azure](../az-modules.md).

### <a name="task-was-cancelled"></a>Cenário: O runbook falhar com o erro: Uma tarefa foi cancelada

#### <a name="issue"></a>Problema

O runbook falhar com um erro semelhante ao seguinte exemplo:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Causa

Este erro pode dever-se através da utilização de módulos do Azure desatualizados.

#### <a name="resolution"></a>Resolução

Este erro pode ser resolvido através da atualização de seus módulos do Azure para a versão mais recente.

Na sua conta de automatização, clique em **módulos**e clique em **módulos do Azure de atualização**. Leva a atualização aproximadamente 15 minutos, depois de concluído volte a executar o runbook que era a falhar. Para saber mais sobre a atualização de seus módulos, consulte [módulos do Azure de atualização na automatização do Azure](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Cenário: Runbooks falhar ao lidar com várias subscrições

#### <a name="issue"></a>Problema

Durante a execução de runbooks com `Start-AzureRmAutomationRunbook`, o runbook falhar gerir recursos do Azure.

#### <a name="cause"></a>Causa

O runbook não está a utilizar o contexto correto quando em execução.

#### <a name="resolution"></a>Resolução

Ao trabalhar com várias subscrições, o contexto da subscrição poderão perder-se ao invocar runbooks. Para garantir que o contexto da subscrição é transferido para os runbooks, adicione o `AzureRmContext` parâmetro para o cmdlet e passe o contexto para o mesmo. Também é recomendado que utilize o `Disable-AzureRmContextAutosave` cmdlet com o **processo** âmbito para garantir que as credenciais que utiliza apenas são utilizadas para o runbook atual.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Cenário: O runbook falhar devido a um cmdlet em falta

#### <a name="issue"></a>Problema

O runbook falhar com um erro semelhante ao seguinte exemplo:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Causa

Este erro pode ocorrer com base num pelas seguintes razões:

1. O módulo que contém o cmdlet não é importado para a conta de automatização
2. O módulo que contém o cmdlet é importado, mas está desatualizado

#### <a name="resolution"></a>Resolução

Este erro pode ser resolvido, efetuando uma das seguintes tarefas:

Se o módulo é um módulo do Azure, veja [como atualizar módulos do Azure PowerShell na automatização do Azure](../automation-update-azure-modules.md) para saber como atualizar os módulos na conta de automatização.

Se for um módulo separado, certifique-se o módulo no importou na sua conta de automatização.

### <a name="job-attempted-3-times"></a>Cenário: O início de tarefa de runbook foi tentado a três vezes, mas ocorreu uma falha para cada hora de início

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Causa

Este erro ocorre devido a um dos seguintes problemas:

1. Limite de memória. Os limites indicados na quantidade de memória atribuída a uma área de segurança foi encontrado na [limites de serviço de automatização](../../azure-subscription-service-limits.md#automation-limits). Uma tarefa pode falhar, se estiver a utilizar mais de 400 MB de memória.

2. Soquetes de rede. Áreas de segurança do Azure estão limitadas a soquetes de rede em simultâneo de 1000, conforme descrito em [limites de serviço de automatização](../../azure-subscription-service-limits.md#automation-limits).

3. Módulo incompatível. Este erro pode ocorrer se as dependências do módulo não estão corretas e se não forem, o runbook devolve normalmente um "comando não encontrado" ou "Não é possível vincular o parâmetro" mensagem.

4. O runbook tentou chamar um um executável ou subprocess num runbook que é executado numa área de segurança do Azure. Este cenário não é suportado em áreas de segurança do Azure.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolver o problema:

* Os métodos sugeridos para funcionar dentro do limite de memória são para dividir a carga de trabalho entre vários runbooks, não processar a quantidade de dados na memória, não para escrever a saída desnecessária de seus runbooks ou considere quantos pontos de verificação escrever em seu fluxo de trabalho do PowerShell runbooks. Pode utilizar o método clear, tal como `$myVar.clear()` para limpar a variável e utilize `[GC]::Collect()` executar imediatamente a coleta de lixo. Estas ações reduzem a superfície de memória do seu runbook durante o tempo de execução.

* Atualizar os módulos do Azure ao seguir os passos [como atualizar módulos do Azure PowerShell na automatização do Azure](../automation-update-azure-modules.md).  

* Outra solução é executar o runbook num [Runbook Worker híbrido](../automation-hrw-run-runbooks.md). Funções de trabalho híbridas não estão limitadas pelos limites de memória e rede que são áreas de segurança do Azure.

* Se tiver de chamar um processo (por exemplo, .exe ou subprocess.call) num runbook, terá de executar o runbook [Runbook Worker híbrido](../automation-hrw-run-runbooks.md).

### <a name="fails-deserialized-object"></a>Cenário: Runbook falhar devido ao objeto de serialização anulado

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Causa

Se o runbook for um fluxo de trabalho do PowerShell, ele armazena objetos complexos num formato de serialização anulado para manter o estado do runbook se o fluxo de trabalho é suspenso.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes três soluções corrigir este problema:

1. Se está encaminhando objetos complexos, de um cmdlet para outro, encapsule esses cmdlets num InlineScript.
2. Passe o nome ou o valor que precisa do objeto complexo em vez de transmitir o objeto inteiro.
3. Utilize um runbook do PowerShell em vez de um runbook de fluxo de trabalho do PowerShell.

### <a name="runbook-fails"></a>Cenário: Meu Runbook falhar, mas funciona quando executado localmente

#### <a name="issue"></a>Problema

O script falha quando são executadas como um runbook, mas funciona quando executado localmente.

#### <a name="cause"></a>Causa

O script pode falhar durante a execução como um runbook para um dos seguintes motivos:

1. Problemas de autenticação
2. Módulos necessários não são importados ou desatualizada.
3. O script pode ser solicitar a interação do utilizador.
4. Alguns módulos fazer suposições sobre bibliotecas que estão presentes em computadores Windows. Essas bibliotecas podem não estar presentes numa área de segurança.
5. Alguns módulos contam com uma versão do .NET que é diferente da disponível na área de segurança.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções pode corrigir este problema:

1. Verifique se estão corretamente [a autenticação no Azure](../manage-runas-account.md).
2. Certifique-se sua [módulos do Azure são importados e atualizados](../automation-update-azure-modules.md).
3. Certifique-se de que nenhum dos seus cmdlets são pedir informações. Este comportamento não é suportado em runbooks.
4. Verifique se tudo o que faz parte do seu módulo tem uma dependência em algo que não está incluído no módulo.
5. Áreas de segurança do Azure utilizam 4.7.2, do .NET Framework, se um módulo utiliza uma versão superior, que ele não funcionará. Neste caso, deve usar um [Runbook Worker híbrido](../automation-hybrid-runbook-worker.md)

Se nenhuma dessas soluções resolver o seu problemReview a [registos da tarefa](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para obter detalhes específicos por que motivo o runbook pode ter falhado.

### <a name="quota-exceeded"></a>Cenário: Tarefa de Runbook falhou porque excedeu a quota alocada

#### <a name="issue"></a>Problema

A tarefa de runbook falha com o erro:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Causa

Este erro ocorre quando a execução da tarefa excede a quota gratuita de 500 minutos para a sua conta. Esta quota aplica-se a todos os tipos de tarefas de execução de tarefa. Algumas destas tarefas podem testar uma tarefa, a partir de uma tarefa no portal, executar uma tarefa ao utilizar webhooks, ou agendar uma tarefa para executar com o portal do Azure ou no seu datacenter. Para saber mais sobre os preços para a automatização, veja [os preços de automatização](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Resolução

Se pretender utilizar mais de 500 minutos de processamento por mês, terá de alterar a sua subscrição do gratuito camada para o escalão básico. Pode atualizar para o escalão básico, efetuando os seguintes passos:  

1. Inicie sessão na sua subscrição do Azure  
2. Selecione a conta de Automatização que quer atualizar  
3. Clique em **configurações** > **preços**.
4. Clique em **habilitar** na parte inferior da página para atualizar a conta para o **básica** escalão.

### <a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecido ao executar um runbook

#### <a name="issue"></a>Problema

A tarefa de runbook falha com o erro:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Causa

Este erro é causado quando o motor do PowerShell não é possível encontrar o cmdlet que está a utilizar no runbook. Este erro pode ser porque o módulo que contém o cmdlet está em falta da conta, existe um conflito de nome com um nome de runbook, ou o cmdlet também existe outro módulo e automatização não é possível resolver o nome.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções resolver o problema:  

* Verifique se introduziu o nome do cmdlet corretamente.  
* Certifique-se de que o cmdlet existe na conta de automatização e que não existam conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e de pesquisa para o cmdlet que pretende localizar na biblioteca ou executar `Get-Command <CommandName>`. Depois de ter confirmado que o cmdlet está disponível para a conta, e que não há nenhum nome entra em conflito com outros cmdlets ou runbooks, adicioná-lo para a tela e certifique-se de que está a utilizar um parâmetro válido definido no runbook.  
* Se tiver um conflito de nomes e o cmdlet está disponível em dois módulos diferentes, pode resolver este problema ao utilizar o nome completamente qualificado para o cmdlet. Por exemplo, pode usar **ModuleName\CmdletName**.  
* Se estiver a executar o runbook no local num grupo de trabalho híbrida, em seguida, certifique-se de que o módulo e o cmdlet está instalado no computador que aloja a função de trabalho híbrida.

### <a name="long-running-runbook"></a>Cenário: Um runbook de longa execução não consegue concluir

#### <a name="issue"></a>Problema

O runbook mostra num **parado** Estado após a execução durante 3 horas. Também poderá receber o erro:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Este comportamento é propositado em áreas de segurança do Azure devido a "Justa" monitorização de processos dentro da automatização do Azure. Se for executado durante mais de três horas, o compartilhamento justo para automaticamente um runbook. O estado de um runbook que vai passado o limite de tempo de partilha de fair difere por tipo de runbook. Runbooks do PowerShell e Python estão definidas para uma **parado** estado. Runbooks de fluxo de trabalho do PowerShell estão definidas como **falhada**.

#### <a name="cause"></a>Causa

O runbook foi executado sobre o limite de duração de 3 horas permitido pelo justa numa área de segurança do Azure.

#### <a name="resolution"></a>Resolução

Um recomendado a solução é executar o runbook num [Runbook Worker híbrido](../automation-hrw-run-runbooks.md).

Funções de trabalho híbridas não estão limitadas pela [justa](../automation-runbook-execution.md#fair-share) limite de runbook de duração de 3 horas que são áreas de segurança do Azure. Runbooks foi executado nos Runbook Workers híbridos deve ser desenvolvidos para oferecer suporte a comportamentos de reinício se existirem problemas de infraestrutura local inesperado.

Outra opção é otimizar o runbook criando [runbooks subordinados](../automation-child-runbooks.md). Se o seu runbook percorre a mesma função em vários recursos, como uma operação de base de dados em várias bases de dados, pode mover essa função para um runbook subordinado. Cada um destes runbooks subordinados é executado em paralelo em processos separados. Este comportamento diminui a quantidade total de tempo para o runbook de principal concluir.

Os cmdlets do PowerShell que permitem o cenário de runbook subordinado são:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) -este cmdlet permite-lhe iniciar um runbook e passar parâmetros para o runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) -se existem operações que precisam ser executadas após a conclusão do runbook subordinado, este cmdlet permite-lhe verificar o estado da tarefa para cada filho.

### <a name="expired webhook"></a>Cenário: Estado: 400 pedido inválido ao chamar um webhook

#### <a name="issue"></a>Problema

Quando tentar invocar um webhook para um runbook da automatização do Azure, recebe o erro seguinte:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Causa

O webhook que está a tentar chamar está desabilitado ou expirou.

#### <a name="resolution"></a>Resolução

Se o webhook está desabilitado, pode reativar o webhook através do portal do Azure. Quando um webhook tiver expirado, o webhook tem de ser eliminado e recriado. Só é possível [renovar um webhook](../automation-webhooks.md#renew-webhook) se ainda não já expirou.

### <a name="429"></a>Cenário: 429: A taxa de pedidos está atualmente demasiado grande. Tente novamente

#### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro ao executar o `Get-AzureRmAutomationJobOutput` cmdlet:

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Causa

Este erro pode ocorrer ao obter o resultado da tarefa a partir de um runbook que tem muitas [fluxos verbosos](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Resolução

Existem duas formas de resolver este erro:

* Editar o runbook e reduzir o número de fluxos de trabalho que emite.
* Reduza o número de fluxos para ser recuperado quando executar o cmdlet. Para seguir este comportamento, pode especificar a `-Stream Output` parâmetro para o `Get-AzureRmAutomationJobOutput` fluxos de saída do cmdlet para obter apenas. 

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.