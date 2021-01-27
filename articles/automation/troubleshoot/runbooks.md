---
title: Problemas na resolução de problemas do runbook da Azure Automation
description: Este artigo diz como resolver problemas e resolver problemas com os runbooks da Azure Automation.
services: automation
ms.subservice: ''
ms.date: 11/03/2020
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: 116677354009c538eece02184d801cfda11574a0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896313"
---
# <a name="troubleshoot-runbook-issues"></a>Resolver problemas de runbooks

 Este artigo descreve problemas de runbook que podem ocorrer e como resolvê-los. Para obter informações gerais, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnosticar problemas de runbook

Quando recebe erros durante a execução do livro de bordo na Azure Automation, pode utilizar os seguintes passos para ajudar a diagnosticar os problemas:

1. Certifique-se de que o seu script de runbook executa com sucesso na sua máquina local.

    Para módulos de referência linguística e aprendizagem, consulte os [PowerShell Docs](/powershell/scripting/overview) ou [Python Docs](https://docs.python.org/3/). Executar o seu script localmente pode descobrir e resolver erros comuns, tais como:

      * Módulos em falta
      * Erros de sintaxe
      * Erros lógicos

1. Investigue fluxos de [erros de runbook](../automation-runbook-output-and-messages.md#runbook-output).

    Olhe para estes streams para mensagens específicas e compare-as com os erros documentados neste artigo.

1. Certifique-se de que os seus nós e espaço de trabalho de automação têm os módulos necessários.

    Se o seu livro de bordo importar quaisquer módulos, verifique se estão disponíveis na sua conta de Automação utilizando os passos nos [módulos De Importação.](../shared-resources/modules.md#import-modules) Atualize os seus módulos PowerShell para a versão mais recente seguindo as instruções nos [módulos Update Azure PowerShell em Azure Automation](../automation-update-azure-modules.md). Para obter mais informações sobre resolução de [problemas, consulte os módulos de resolução de problemas](shared-resources.md#modules).

1. Se o seu livro de bordo estiver suspenso ou falhar inesperadamente:

    * [Renove o certificado](../manage-runas-account.md#cert-renewal) se a conta Run As tiver expirado.
    * [Renove o webhook](../automation-webhooks.md#renew-a-webhook) se estiver a tentar usar um webhook expirado para iniciar o livro de aplicação.
    * [Verifique o estado do trabalho](../automation-runbook-execution.md#job-statuses) para determinar o estado atual do livro e algumas possíveis causas da emissão.
    * [Adicione uma saída adicional](../automation-runbook-output-and-messages.md#working-with-message-streams) ao livro de execução para identificar o que acontece antes de o livro de execução ser suspenso.
    * [Lide com quaisquer exceções](../automation-runbook-execution.md#exceptions) que sejam jogadas pelo seu trabalho.

1. Faça este passo se o trabalho de runbook ou o ambiente no Hybrid Runbook Worker não responder.

    Se estiver a executar os seus livros de corridas num Trabalhador De Runbook Híbrido em vez de na Azure Automation, poderá ter de [resolver problemas com o próprio trabalhador híbrido](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Cenário: Runbook falha com um erro proibido ou proibido 403

### <a name="issue"></a>Problema

O seu runbook falha com uma permissão proibida ou erro Proibido 403, ou equivalente.

### <a name="cause"></a>Causa

Executar Como as contas podem não ter as mesmas permissões contra os recursos da Azure que a sua conta de Automação corrente. 

### <a name="resolution"></a>Resolução

Certifique-se de que a sua conta Run As tem [permissões para aceder a quaisquer recursos utilizados](../../role-based-access-control/role-assignments-portal.md) no seu script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: Sedún na conta do Azure falhou

### <a name="issue"></a>Problema

Recebe um dos seguintes erros quando trabalha com o `Connect-AzAccount` cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Estes erros ocorrem se o nome do ativo credencial não for válido. Também podem ocorrer se o nome de utilizador e a palavra-passe que usou para configurar o ativo credencial Automation não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga estes passos:

1. Certifique-se de que não tem nenhum personagem especial. Estes caracteres incluem `\@` o personagem no nome do ativo credencial Automation que está a usar para ligar ao Azure.
1. Verifique se pode utilizar o nome de utilizador e a palavra-passe que estão armazenados na credencial Azure Automation no seu editor local powerShell ISE. Executar os seguintes cmdlets no PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Se a sua autenticação falhar localmente, não configura corretamente as suas credenciais Azure Ative Directory (Azure AD). Para obter a conta AZure AD configurada corretamente, consulte o artigo [Authenticate to Azure utilizando o Azure Ative Directory](../automation-use-azure-ad.md).

1. Se o erro parecer ser transitório, tente adicionar lógica de repetição à sua rotina de autenticação para tornar a autenticação mais robusta.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: Executar Login-AzureRMAccount para iniciar sessão

### <a name="issue"></a>Problema

Recebe o seguinte erro quando executa um livro de bordo:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer quando não estiver a utilizar uma conta Run As ou a conta Run As expirou. Para obter mais informações, consulte [Gerir a Gestão da Automação Azure Executar como contas.](../manage-runas-account.md)

Este erro tem duas causas primárias:

* Existem diferentes versões do módulo AzureRM ou Az.
* Está a tentar aceder aos recursos numa subscrição separada.

### <a name="resolution"></a>Resolução

Se receber este erro depois de atualizar um módulo AzureRM ou Az, atualize todos os seus módulos para a mesma versão.

Se estiver a tentar aceder aos recursos noutra subscrição, siga estes passos para configurar permissões:

1. Vá à Gestão de Automação Como conta, e copie o ID de **aplicação** e **impressão digital .**

    ![ID de aplicação de cópia e impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)

1. Aceda ao controlo de **Acesso** da subscrição onde a conta Automation *não* está hospedada e adicione uma nova atribuição de funções.

    ![Controlo de acesso](../media/troubleshoot-runbooks/access-control.png)

1. Adicione o **ID de aplicação** recolhido anteriormente. Selecione permissões **de Contribuinte.**

    ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copie o nome da assinatura.

1. Pode agora utilizar o seguinte código de livro para testar as permissões da sua conta Automation para a outra subscrição. `"\<CertificateThumbprint\>"`Substitua-o pelo valor copiado no passo 1. `"\<SubscriptionName\>"`Substitua-o pelo valor copiado no passo 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Cenário: Incapaz de encontrar a assinatura do Azure

### <a name="issue"></a>Problema

Recebe o seguinte erro quando trabalha com o `Select-AzureSubscription` `Select-AzureRMSubscription` , ou `Select-AzSubscription` cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Este erro pode ocorrer se:

* O nome da assinatura não é válido.
* O utilizador AZURE AD que está a tentar obter os detalhes da subscrição não está configurado como administrador da subscrição.
* O cmdlet não está disponível.

### <a name="resolution"></a>Resolução

Siga estes passos para determinar se autenticou o Azure e tem acesso à subscrição que está a tentar selecionar:

1. Para se certificar de que o seu script funciona autónomo, teste-o fora da Azure Automation.
1. Certifique-se de que o seu script executa o [cmdlet Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) antes de executar o `Select-*` cmdlet.
1. Adicione `Disable-AzContextAutosave –Scope Process` ao início do seu runbook. Este cmdlet garante que quaisquer credenciais se aplicam apenas à execução do livro de aplicação atual.
1. Se ainda vir a mensagem de erro, modifique o seu código adicionando o `AzContext` parâmetro para , e execute o `Connect-AzAccount` código.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: Runbooks falham ao lidar com várias subscrições

### <a name="issue"></a>Problema

Ao executar livros de execução, o livro de recortes não consegue gerir os recursos do Azure.

### <a name="cause"></a>Causa

O livro não está a usar o contexto correto quando está a correr.

### <a name="resolution"></a>Resolução

O contexto de subscrição pode perder-se quando um livro de bordo invoca vários runbooks. Para garantir que o contexto de subscrição é passado para os runbooks, certifique-se de que o livro de bordo do cliente passe o contexto para o `Start-AzureRmAutomationRunbook` cmdlet no `AzureRmContext` parâmetro. Utilize o `Disable-AzureRmContextAutosave` cmdlet com o `Scope` parâmetro definido para garantir que as `Process` credenciais especificadas são utilizadas apenas para o livro de bordo atual. Para mais informações, consulte [Subscrições.](../automation-runbook-execution.md#subscriptions)

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: Autenticação para Azure falha porque a autenticação multifactor está ativada

### <a name="issue"></a>Problema

Recebe o seguinte erro ao autenticar a Azure com o seu nome de utilizador Escaure e senha:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se tiver autenticação multifactor na sua conta Azure, não pode utilizar um utilizador do Azure Ative Directory para autenticar a Azure. Em vez disso, é necessário utilizar um certificado ou um principal de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para utilizar uma corrida clássica Como conta com cmdlets de modelo clássico de implementação Azure, consulte [Criar uma conta Classic Run As para gerir os serviços Azure](../automation-create-standalone-account.md#create-a-classic-run-as-account). Para utilizar um principal de serviço com cmdlets Azure Resource Manager, consulte [criar o principal do serviço utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticar um diretor de serviço com o Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Cenário: Runbook falha com mensagem de erro "Uma tarefa foi cancelada"

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Este erro pode ser causado através da utilização de módulos Azure desatualizados.

### <a name="resolution"></a>Resolução

Pode resolver este erro atualizando os seus módulos Azure para a versão mais recente:

1. Na sua conta Dem automação, selecione **Módulos** e, em seguida, selecione **módulos Update Azure**.
1. A atualização demora cerca de 15 minutos. Depois de terminado, re-executar o runbook que falhou.

Para saber mais sobre a atualização dos seus módulos, consulte [os módulos Update Azure na Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: Termo não reconhecido como o nome de um cmdlet, função ou script

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Este erro pode acontecer pelas seguintes razões:

* O módulo que contém o cmdlet não é importado para a conta Automation.
* O módulo que contém o cmdlet é importado mas está desatualizado.

### <a name="resolution"></a>Resolução

Faça uma das seguintes tarefas para resolver este erro:

* Para obter um módulo Azure, consulte [Como atualizar os módulos Azure PowerShell na Azure Automation](../automation-update-azure-modules.md) para aprender a atualizar os seus módulos na sua conta Demôm automação.
* Para um módulo não-Azure, certifique-se de que o módulo é importado para a sua conta de Automação.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: Cmdlet falha no runbook PnP PowerShell na Azure Automation

### <a name="issue"></a>Problema

Quando um livro de execução escreve um objeto gerado por PnP PowerShell para a saída da Automatização Azure diretamente, a saída de cmdlet não pode ser transmitida de volta para a Automação.

### <a name="cause"></a>Causa

Esta questão ocorre mais frequentemente quando a Azure Automation processa os runbooks que invocam os cmdlets PnP PowerShell, por exemplo, `add-pnplistitem` sem capturar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite os seus scripts para atribuir quaisquer valores de retorno a variáveis para que os cmdlets não tentem escrever objetos inteiros para a saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, como mostrado aqui.

```azurecli
  $null = add-pnplistitem
```

Se o seu script analisar a saída do cmdlet, o script deve armazenar a saída numa variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecido ao executar um livro de bordo

### <a name="issue"></a>Problema

O seu trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Este erro é causado quando o motor PowerShell não consegue encontrar o cmdlet que está a usar no seu manual. É possível que o módulo que contém o cmdlet esteja em falta na conta, há um conflito de nomes com um nome de livro de correr, ou o cmdlet também existe em outro módulo e a Automação não consegue resolver o nome.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir o problema:

* Certifique-se de que introduziu corretamente o nome do cmdlet.
* Certifique-se de que o cmdlet existe na sua conta de Automação e que não existem conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e procure o cmdlet que pretende encontrar na biblioteca, ou corra `Get-Command <CommandName>` . Depois de validar que o cmdlet está disponível para a conta, e que não há nenhum nome em conflito com outros cmdlets ou runbooks, adicione o cmdlet à tela. Certifique-se de que está a utilizar um parâmetro válido definido no seu runbook.
* Se tiver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema utilizando o nome totalmente qualificado para o cmdlet. Por exemplo, pode utilizar `ModuleName\CmdletName`.
* Se estiver a executar o runbook no local de um grupo de trabalhadores híbridos, certifique-se de que o módulo e o cmdlet estão instalados na máquina que acolhe o trabalhador híbrido.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Cenário: Referência incorreta do objeto na chamada para Add-AzAccount

### <a name="issue"></a>Problema

Recebe este erro quando `Add-AzAccount` trabalha, que é um pseudónimo para o `Connect-AzAccount` cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Este erro pode ocorrer se o livro de recortes não fizer os passos adequados antes de ligar `Add-AzAccount` para adicionar a conta Automation. Um exemplo de um dos passos necessários é iniciar sessão com uma conta Run As. Para que as operações corretas utilizem no seu runbook, consulte a [execução do Runbook na Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: Referência de objeto não definida para uma instância de um objeto

### <a name="issue"></a>Problema

Recebe o seguinte erro quando invoca um livro de crianças com o `Wait` parâmetro e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Se o fluxo contiver objetos, `Start-AzAutomationRunbook` não manuseia corretamente o fluxo de saída.

### <a name="resolution"></a>Resolução

Implemente uma lógica de sondagem e use o [cmdlet Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar a saída. Uma amostra desta lógica é definida aqui:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Cenário: Runbook falha por causa de objeto desercializado

### <a name="issue"></a>Problema

O seu livro de execução falha com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu runbook for um Fluxo de Trabalho PowerShell, armazena objetos complexos num formato deseralizado para persistir o seu estado de runbook se o fluxo de trabalho for suspenso.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir este problema:

* Se estiver a canalizar objetos complexos de um cmdlet para outro, enrole estes cmdlets numa `InlineScript` atividade.
* Passe o nome ou valor que você precisa do objeto complexo em vez de passar todo o objeto.
* Utilize um livro de execução PowerShell em vez de um manual de fluxo de trabalho PowerShell.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: 400 mau pedido ao chamar um webhook

### <a name="issue"></a>Problema

Quando tenta invocar um webhook para um livro de formulários Azure Automation, recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que está a tentar ligar ou está desativado ou expirado. 

### <a name="resolution"></a>Resolução

Se o webhook estiver desativado, pode reativá-lo através do portal Azure. Se o webhook tiver expirado, deve apagar e, em seguida, re-criá-lo. Só pode [renovar um webhook](../automation-webhooks.md#renew-a-webhook) se ainda não tiver expirado. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: A taxa de pedido é atualmente demasiado grande

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro ao executar o `Get-AzAutomationJobOutput` cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Este erro pode ocorrer ao recuperar a saída de trabalho de um runbook que tem [muitos fluxos verbosos](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Resolução

Faça um dos seguintes para resolver este erro:

* Editar o runbook e reduzir o número de fluxos de trabalho que emite.
* Reduza o número de correntes a recuperar ao executar o cmdlet. Para isso, pode definir o valor do `Stream` parâmetro para o [cmdlet Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar apenas os fluxos de saída. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Cenário: Trabalho em runbook falha porque quota atribuída foi ultrapassada

### <a name="issue"></a>Problema

O seu trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Este erro ocorre quando a execução do trabalho excede a quota gratuita de 500 minutos para a sua conta. Esta quota aplica-se a todos os tipos de tarefas de execução de empregos. Algumas destas tarefas estão a testar um trabalho, a iniciar um trabalho a partir do portal, a executar um trabalho utilizando webhooks, ou a agendar um trabalho para executar usando o portal Azure ou o seu datacenter. Para saber mais sobre preços para Automação, consulte [os preços da Automação.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Resolução

Se pretender utilizar mais de 500 minutos de processamento por mês, altere a sua subscrição do nível Free para o nível Básico:

1. Inscreva-se na sua assinatura Azure.
1. Selecione a conta Automation para atualizar.
1. Selecione **Definições** e, em seguida, **selecione Preços**.
1. Selecione **Ative** na página inferior para atualizar a sua conta para o nível Básico.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Cenário: Fluxo de saída de runbook superior a 1 MB

### <a name="issue"></a>Problema

O seu runbook em funcionamento na caixa de areia Azure falha com o seguinte erro:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Causa

Este erro ocorre porque o seu livro de execução tentou escrever demasiados dados de exceção para o fluxo de saída.

### <a name="resolution"></a>Resolução

Há um limite de 1 MB no fluxo de saída de trabalho. Certifique-se de que o seu livro de execuções encerra chamadas para um executável ou subprocessamento utilizando `try` e `catch` bloqueia. Se as operações lançarem uma exceção, mande o código escrever a mensagem da exceção para uma variável De Automação. Esta técnica impede que a mensagem seja escrita no fluxo de saída do trabalho. Para os trabalhos do Trabalhador de Runbook Híbrido executados, o fluxo de saída truncado para 1 MB é apresentado sem mensagem de erro.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: Runbook job start tentado três vezes, mas não começa cada vez

### <a name="issue"></a>Problema

O seu livro de execução falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Este erro ocorre devido a uma das seguintes questões:

* **Limite de memória.** Um trabalho pode falhar se estiver a usar mais de 400 MB de memória. Os limites documentados na memória atribuída a uma caixa de areia encontram-se nos [limites de serviço da Automação.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* **Tomadas de rede.** As caixas de areia Azure estão limitadas a 1.000 tomadas de rede simultâneas. Para mais informações, consulte [os limites do serviço de Automação.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)

* **Módulo incompatível.** As dependências dos módulos podem não estar corretas. Neste caso, o seu livro de recortes normalmente devolve uma `Command not found` ou `Cannot bind parameter` mensagem.

* **Não há autenticação com Diretório Ativo para caixa de areia.** O seu runbook tentou chamar um executável ou subprocesso que funciona numa caixa de areia Azure. Configurar os runbooks para autenticar com Azure AD utilizando a Biblioteca de Autenticação de Diretório Ativo Azure (ADAL) não é suportado.

### <a name="resolution"></a>Resolução

* **Limite de memória, tomadas de rede.** As formas sugeridas de trabalhar dentro dos limites de memória são dividir a carga de trabalho entre vários runbooks, processar menos dados na memória, evitar escrever saídas desnecessárias dos seus livros de execução e considerar quantos pontos de verificação estão escritos nos seus livros de fluxo de trabalho PowerShell. Utilize o método claro, `$myVar.clear` como, para limpar variáveis e usar `[GC]::Collect` para executar a recolha de lixo imediatamente. Estas ações reduzem a pegada de memória do seu livro de bordo durante o tempo de execução.

* **Módulo incompatível.** Atualize os seus módulos Azure seguindo os passos de [Como atualizar os módulos Azure PowerShell na Azure Automation](../automation-update-azure-modules.md).

* **Não há autenticação com Diretório Ativo para caixa de areia.** Quando autenticar a AZure AD com um livro de aplicação, certifique-se de que o módulo AD AZure está disponível na sua conta Automation. Certifique-se de conceder à conta Run As as permissões necessárias para executar as tarefas que o livro de bordo automatiza.

  Se o seu runbook não puder chamar um executável ou subprocesso em execução numa caixa de areia Azure, utilize o livro de execução num [Trabalhador de Runbook Híbrido](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não são limitados pelos limites de memória e rede que as caixas de areia Azure têm.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Cenário: Trabalho da PowerShell falha com mensagem de erro "Não pode invocar método"

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro quando inicia um trabalho PowerShell num livro de bordo que funciona em Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Este erro pode indicar que os livros de bordo que funcionam numa caixa de areia Azure não podem ser executados no [modo Linguagem Completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas formas de resolver este erro:

* Em vez de utilizar [o Start-Job,](/powershell/module/microsoft.powershell.core/start-job)utilize [o Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar o livro de execução.
* Tente executar o livro de corridas num trabalhador híbrido.

Para saber mais sobre este comportamento e outros comportamentos dos runbooks da Azure Automation, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: Um livro de corridas de longa duração não está concluído

### <a name="issue"></a>Problema

O seu livro de corridas mostra-se num estado parado depois de correr durante três horas. Pode também receber este erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Este comportamento é por design nas caixas de areia Azure devido à monitorização [justa](../automation-runbook-execution.md#fair-share) dos processos dentro da Azure Automation. Se um processo executar mais de três horas, a parte justa para automaticamente um livro de recortes. O estado de um runbook que ultrapassa o prazo de partilha justo difere por tipo de runbook. Os livros powerShell e Python estão definidos para um estado stop. Os livros de fluxo de trabalho PowerShell estão definidos para Falhados.

### <a name="cause"></a>Causa

O livro de corridas ultrapassou o limite de três horas permitido por uma parte justa numa caixa de areia Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook num [Trabalhador De Runbook Híbrido.](../automation-hrw-run-runbooks.md) Os trabalhadores híbridos não estão limitados pelo limite de três horas de pagamento de ações justas que as caixas de areia Azure têm. Os runbooks que funcionam em Trabalhadores De Runbook Híbridos devem ser desenvolvidos para apoiar comportamentos de reinício se houver problemas inesperados de infraestruturas locais.

Outra solução é otimizar o runbook criando [livros infantis.](../automation-child-runbooks.md) Se o seu runbook circular pela mesma função em vários recursos, por exemplo, numa operação de base de dados em várias bases de dados, pode mover a função para um livro de aplicação infantil. Cada livro de crianças executa em paralelo num processo separado. Este comportamento diminui a quantidade total de tempo para que o livro de bordo dos pais esteja concluído.

Os cmdlets PowerShell que permitem o cenário do livro infantil são:

* [Livro Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Este cmdlet permite-lhe iniciar um runbook e transmitir parâmetros para o runbook.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Se houver operações que precisem de ser realizadas após a conclusão do livro de aplicação da criança, este cmdlet permite-lhe verificar o estado do trabalho de cada criança.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: Erro nos fluxos de emprego sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Vê o seguinte erro nos fluxos de trabalho para um livro de bordo:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Causa

Este erro é provavelmente causado pela utilização de uma migração incompleta dos módulos AzureRM para Az no seu livro de bordo. Esta situação pode fazer com que a Azure Automation inicie um trabalho de runbook utilizando apenas módulos AzureRM e, em seguida, iniciar outro trabalho usando apenas módulos Az, o que leva a uma queda de caixa de areia.

### <a name="resolution"></a>Resolução

Não recomendamos a utilização de cmdlets Az e AzureRM no mesmo livro. Para saber mais sobre a utilização correta dos módulos, consulte [os módulos Migrar para Az.](../shared-resources/modules.md#migrate-to-az-modules)

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Cenário: Acesso negado ao utilizar a caixa de areia Azure para runbook ou aplicação

### <a name="issue"></a>Problema

Quando o seu runbook ou aplicação tenta funcionar numa caixa de areia Azure, o ambiente nega o acesso.

### <a name="cause"></a>Causa

Este problema pode ocorrer porque as caixas de areia Azure impedem o acesso a todos os servidores COM fora do processo. Por exemplo, uma aplicação ou um livro de bordo com caixa de areia não pode ligar para a Instrumentação de Gestão do Windows (WMI) ou para o serviço de Instalador do Windows (msiserver.exe).

### <a name="resolution"></a>Resolução

Para obter detalhes sobre a utilização de caixas de areia Azure, consulte [o ambiente de execução do Runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Cenário: Código de estado proibido inválido ao usar o Cofre de Chaves dentro de um livro de bordo

### <a name="issue"></a>Problema

Quando tenta aceder ao Cofre de Chaves Azure através de um manual da Azure Automation, obtém-se o seguinte erro:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Causa

As possíveis causas para esta questão são:

* Não usar uma conta Run As.
* Permissões insuficientes.

### <a name="resolution"></a>Resolução

#### <a name="not-using-a-run-as-account"></a>Não usar uma conta Run As

Siga [o Passo 5 - Adicione a autenticação para gerir os recursos do Azure](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) para garantir que está a utilizar uma conta Run As para aceder ao Key Vault.

#### <a name="insufficient-permissions"></a>Permissões insuficientes

[Adicione permissões ao Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) para garantir que a sua conta Run As tem permissões suficientes para aceder ao Key Vault.

## <a name="recommended-documents"></a>Documentos recomendados

* [Execução de runbook na Azure Automation](../automation-runbook-execution.md)
* [Iniciar um runbook na Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Próximos passos

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga-o à comunidade Azure para respostas, apoio e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
