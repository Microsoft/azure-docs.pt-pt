---
title: Problemas de resolução de erros do livro de execução da Automação Azure
description: Aprenda a resolver problemas e a resolver problemas que poderá encontrar com os livros de execução da Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 08325c8163073c083e927f84fecbde9a9d104572
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652786"
---
# <a name="troubleshoot-runbook-errors"></a>Erros do livro de corridas de resolução de problemas

 Este artigo descreve vários erros de livro que podem ocorrer e como resolvê-los.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Azure Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="diagnose-runbook-issues"></a>Diagnosticar problemas de livro

Quando receber erros durante a execução do livro de corridas na Automatização Azure, pode utilizar os seguintes passos para ajudar a diagnosticar os problemas:

1. Certifique-se de que o seu script de rés-do-livro executa com sucesso na sua máquina local.

    Para módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](/powershell/scripting/overview) ou [Python Docs](https://docs.python.org/3/). Executar o seu script localmente pode descobrir e resolver erros comuns, tais como:

      * Módulos em falta
      * Erros de sintaxe
      * Erros lógicos

1. Investigue os fluxos de erros do livro de [corridas.](../automation-runbook-output-and-messages.md#runbook-output)

    Veja estes fluxos para mensagens específicas e compare-as com os erros documentados neste artigo.

1. Certifique-se de que os seus nódosos e espaço de trabalho automationol dispõem dos módulos necessários.

    Se o seu livro de recortes importar quaisquer módulos, verifique se estão disponíveis na sua conta Automation utilizando os passos nos [módulos de Importação](../shared-resources/modules.md#import-modules). Atualize os seus módulos PowerShell para a versão mais recente seguindo as instruções nos [módulos Update Azure PowerShell em Automação Azure](../automation-update-azure-modules.md). Para obter mais informações sobre resolução de [problemas, consulte os módulos Troubleshoot](shared-resources.md#modules).

1. Se o seu livro de execução for suspenso ou falhar inesperadamente:

    * [Renovar o certificado](../manage-runas-account.md#cert-renewal) se a conta Executar Como conta tiver expirado.
    * [Renove o webhook](../automation-webhooks.md#renew-a-webhook) se estiver a tentar utilizar um webhook expirado para iniciar o livro de execução.
    * [Verifique o estado](../automation-runbook-execution.md#job-statuses) do trabalho para determinar os estados atuais do livro de corridas e algumas possíveis causas do problema.
    * [Adicione uma saída adicional](../automation-runbook-output-and-messages.md#message-streams) ao livro de execução para identificar o que acontece antes da suspensão do livro de execução.
    * [Lide com quaisquer exceções](../automation-runbook-execution.md#handling-exceptions) que sejam lançadas pelo seu trabalho.

1. Faça este passo se o trabalho do livro ou o ambiente no Trabalhador do Livro Híbrido não responder.

    Se estiver a executar os seus livros de corridas num Trabalhador híbrido em vez de na Azure Automation, talvez precise de [resolver o próprio trabalhador híbrido.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Cenário: Runbook falha com uma não permissão ou erro proibido 403

### <a name="issue"></a>Problema

O seu livro de execução falha com uma não permissão ou erro proibido 403, ou equivalente.

### <a name="cause"></a>Causa

Executar Como contas podem não ter as mesmas permissões contra os recursos Azure que a sua conta de Automação atual. 

### <a name="resolution"></a>Resolução

Certifique-se de que a sua conta Run As tem [permissões para aceder a quaisquer recursos utilizados](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) no seu script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: Inscrição na conta Azure falhou

### <a name="issue"></a>Problema

Recebe um dos seguintes erros quando `Connect-AzAccount` trabalha com o cmdlet:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Estes erros ocorrem se o nome do ativo credencial não for válido. Podem também ocorrer se o nome de utilizador e a palavra-passe que usou para configurar o ativo credencial automation não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga estes passos:

1. Certifique-se de que não tem personagens especiais. Estes caracteres `\@` incluem o personagem no nome de ativo credencial automation que está a usar para ligar ao Azure.
1. Verifique se pode utilizar o nome de utilizador e a palavra-passe que estão armazenados na credencial Azure Automation no seu editor local powerShell ISE. Executar os seguintes cmdlets no PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Se a sua autenticação falhar localmente, não configura risadamente as suas credenciais de Diretório Ativo Azure (Azure AD). Para configurar corretamente a conta Azure AD, consulte o post de blog [Authenticating to Azure utilizando o Azure Ative Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/).

1. Se o erro parecer transitório, tente adicionar lógica de novo tentativa à sua rotina de autenticação para tornar a autenticação mais robusta.

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

Recebe o seguinte erro quando executa um livro de execução:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer quando não está a utilizar uma conta 'Executar Como' ou a conta Run As a conta expirou. Para mais informações, consulte [Manage Azure Automation Run As contas](https://docs.microsoft.com/azure/automation/manage-runas-account).

Este erro tem duas causas primárias:

* Existem diferentes versões do módulo AzureRM ou Az.
* Estás a tentar aceder a recursos numa subscrição separada.

### <a name="resolution"></a>Resolução

Se receber este erro depois de atualizar um módulo AzureRM ou Az, atualize todos os seus módulos para a mesma versão.

Se estiver a tentar aceder a recursos noutra subscrição, siga estes passos para configurar permissões:

1. Vá à execução de automação Como conta, e copie o ID de **aplicação** e **a impressão digital**.

    ![Id de aplicação de cópia e impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)

1. Aceda ao controlo de **acesso** da subscrição onde a conta Automation *não* esteja hospedada e adicione uma nova atribuição de funções.

    ![Controlo de acesso](../media/troubleshoot-runbooks/access-control.png)

1. Adicione o ID de **aplicação** recolhido anteriormente. Selecione permissões **do Contribuinte.**

    ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copie o nome da subscrição.

1. Agora pode utilizar o seguinte código de livro para testar as permissões da sua conta Automation para a outra subscrição. Substitua `"\<CertificateThumbprint\>"` pelo valor copiado no passo 1. Substitua `"\<SubscriptionName\>"` pelo valor copiado no passo 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Cenário: Incapaz de encontrar a subscrição do Azure

### <a name="issue"></a>Problema

Recebe o seguinte erro quando `Select-AzureSubscription`trabalha `Select-AzureRMSubscription`com `Select-AzSubscription` o , ou cmdlet:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Este erro pode ocorrer se:

* O nome da subscrição não é válido.
* O utilizador da AD Azure que está a tentar obter os detalhes da subscrição não está configurado como administrador da subscrição.
* O cmdlet não está disponível.

### <a name="resolution"></a>Resolução

Siga estes passos para determinar se autenticou ao Azure e tem acesso à subscrição que está a tentar selecionar:

1. Para garantir que o seu guião funciona sozinho, teste-o fora da Automação Azure.
1. Certifique-se de que o seu script executa o `Select-*` cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) antes de executar o cmdlet.
1. Adicione `Disable-AzContextAutosave –Scope Process` ao início do seu livro de corridas. Este cmdlet garante que quaisquer credenciais se aplicam apenas à execução do atual livro de execução.
1. Se ainda vir a mensagem de erro, `AzContext` modifique `Connect-AzAccount`o seu código adicionando o parâmetro para , e, em seguida, execute o código.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: Os livros de corridas falham ao lidar com várias subscrições

### <a name="issue"></a>Problema

Ao executar livros de execução, o livro de execução não consegue gerir os recursos do Azure.

### <a name="cause"></a>Causa

O livro de execução não está a usar o contexto correto quando corre.

### <a name="resolution"></a>Resolução

O contexto de subscrição pode perder-se quando um livro de execução invoca vários livros de execução. Para garantir que o contexto de subscrição é passado para os `Start-AzureRmAutomationRunbook` livros de execução, o livro de execução do cliente passe o contexto para o cmdlet no `AzureRmContext` parâmetro. Utilize `Disable-AzureRmContextAutosave` o cmdlet `Scope` com o `Process` parâmetro definido para garantir que as credenciais especificadas só são utilizadas para o livro de execução atual. Para mais informações, consulte [Trabalhar com várias subscrições.](../automation-runbook-execution.md#working-with-multiple-subscriptions)

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
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: A autenticação para o Azure falha porque a autenticação multifactor está ativada

### <a name="issue"></a>Problema

Recebe o seguinte erro ao autenticar o Azure com o seu nome de utilizador EA E a palavra-passe:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se tiver a autenticação multifactor na sua conta Azure, não pode utilizar um utilizador do Diretório Ativo Azure para autenticar o Azure. Em vez disso, precisa de usar um certificado ou um diretor de serviço para autenticar.

### <a name="resolution"></a>Resolução

Para utilizar um certificado com cmdlets modelo de implantação clássico azure, consulte [Criar e adicionar um certificado para gerir os serviços Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Para utilizar um diretor de serviço com o Azure Resource Manager cmdlets, consulte [Criar o principal de serviço utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e autenticar um diretor de serviço com o Gestor de Recursos [Azure.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Cenário: Runbook falha com mensagem de erro "Uma tarefa foi cancelada"

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Este erro pode ser causado pela utilização de módulos Azure desatualizados.

### <a name="resolution"></a>Resolução

Pode resolver este erro atualizando os seus módulos Azure para a versão mais recente:

1. Na sua conta De automação, selecione **Módulos**, e, em seguida, selecione **módulos Update Azure**.
1. A atualização demora cerca de 15 minutos. Depois de terminado, recorra o livro que falhou.

Para saber mais sobre a atualização dos seus módulos, consulte [módulos Update Azure em Automação Azure](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: Termo não reconhecido como o nome de um cmdlet, função ou script

### <a name="issue"></a>Problema

O seu livro de execução falha com um erro semelhante ao seguinte exemplo:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Este erro pode acontecer pelas seguintes razões:

* O módulo que contém o cmdlet não é importado para a conta de Automação.
* O módulo que contém o cmdlet é importado mas está desatualizado.

### <a name="resolution"></a>Resolução

Faça uma das seguintes tarefas para resolver este erro:

* Para um módulo Azure, consulte [como atualizar os módulos Azure PowerShell na Azure Automation](../automation-update-azure-modules.md) para aprender a atualizar os seus módulos na sua conta Automation.
* Para um módulo não-Azure, certifique-se de que o módulo é importado para a sua conta Deautomação.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: Cmdlet falha no livro de execução PnP PowerShell sobre automação Azure

### <a name="issue"></a>Problema

Quando um livro de execução escreve um objeto gerado por PnP PowerShell diretamente para a saída da Automação Azure, a saída de cmdlet não pode voltar para a Automação.

### <a name="cause"></a>Causa

Esta questão ocorre mais frequentemente quando a Azure Automation processa livros de execução `add-pnplistitem`que invocam cmdlets PnP PowerShell, por exemplo, sem pegar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite os seus scripts para atribuir quaisquer valores de retorno a variáveis para que os cmdlets não tentem escrever objetos inteiros para a saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, como mostrado aqui.

```azurecli
  $null = add-pnplistitem
```

Se o seu script analisar a saída cmdlet, o script deve armazenar a saída numa variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecido na execução de um livro de corridas

### <a name="issue"></a>Problema

O seu trabalho no livro de recortes falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Este erro é causado quando o motor PowerShell não encontra o cmdlet que está a usar no seu livro de execução. É possível que o módulo que contém o cmdlet esteja ausente da conta, há um conflito de nomes com um nome de livro de corridas, ou o cmdlet também existe em outro módulo e a Automação não consegue resolver o nome.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir o problema:

* Certifique-se de que introduziu corretamente o nome cmdlet.
* Certifique-se de que o cmdlet existe na sua conta De automação e que não existem conflitos. Para verificar se o cmdlet está presente, abra um livro de execução no modo de `Get-Command <CommandName>`edição e procure o cmdlet que pretende encontrar na biblioteca, ou corra . Depois de ter validado que o cmdlet está disponível para a conta, e que não existem conflitos de nome com outros cmdlets ou livros de execução, adicione o cmdlet à tela. Certifique-se de que está a utilizar um parâmetro válido definido no seu livro de execução.
* Se tiver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema utilizando o nome totalmente qualificado para o cmdlet. Por exemplo, pode utilizar `ModuleName\CmdletName`.
* Se estiver a executar o livro de corridas no local num grupo de trabalhadores híbridos, certifique-se de que o módulo e o cmdlet estão instalados na máquina que acolhe o trabalhador híbrido.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Cenário: Referência incorreta do objeto na chamada para Add-AzAccount

### <a name="issue"></a>Problema

Recebe este erro quando `Add-AzAccount`trabalha com , que `Connect-AzAccount` é um pseudónimo para o cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Este erro pode ocorrer se o livro de execução não fizer os passos adequados antes de ligar `Add-AzAccount` para adicionar a conta Deautomação. Um exemplo de um dos passos necessários é assinar com uma conta Run As. Para as operações corretas a utilizar no seu livro de execução, consulte a execução do Livro de [Execução em Automação Azure](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: Referência do objeto não definida para uma instância de um objeto

### <a name="issue"></a>Problema

Recebe o seguinte erro quando invoca um `Wait` livro de execução infantil com o parâmetro e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Se o fluxo `Start-AzAutomationRunbook` contiver objetos, não manuseie corretamente o fluxo de saída.

### <a name="resolution"></a>Resolução

Implementar uma lógica de sondagem e utilizar o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar a saída. Uma amostra desta lógica é definida aqui:

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
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Cenário: Runbook falha por causa de objeto desserializado

### <a name="issue"></a>Problema

O seu livro de execução falha com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu livro de execução for um PowerShell Workflow, armazena objetos complexos num formato desserializado para persistir o seu estado de releiro se o fluxo de trabalho for suspenso.

### <a name="resolution"></a>Resolução

Utilize qualquer uma das seguintes soluções para corrigir este problema:

* Se estiver a canalizar objetos complexos de um cmdlet para `InlineScript` outro, enrole estes cmdlets numa atividade.
* Passe o nome ou valor que precisa do objeto complexo em vez de passar todo o objeto.
* Utilize um livro de execução PowerShell em vez de um livro de execução powerShell Workflow.



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: 400 Mau Pedido ao chamar um webhook

### <a name="issue"></a>Problema

Quando tenta invocar um webhook para um livro de execução da Automação Azure, recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O gancho que está a tentar ligar está desativado ou expirado. 

### <a name="resolution"></a>Resolução

Se o webhook estiver desativado, pode reecê-lo através do portal Azure. Se o webhook tiver expirado, tem de apagar e recriá-lo. Só pode [renovar um webhook](../automation-webhooks.md#renew-a-webhook) se ainda não tiver expirado. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: A taxa de pedido é atualmente demasiado grande

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de `Get-AzAutomationJobOutput` erro ao executar o cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Este erro pode ocorrer ao recuperar a saída de trabalho de um livro de execução que tem muitos [fluxos verbosos](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Faça um dos seguintes para resolver este erro:

* Editar o livro de execução e reduzir o número de fluxos de trabalho que emite.
* Reduza o número de correntes a recuperar ao executar o cmdlet. Para tal, pode definir o `Stream` valor do parâmetro para o cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) para recuperar apenas os fluxos de saída. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Cenário: Trabalho de releiro falha porque quota atribuída foi ultrapassada

### <a name="issue"></a>Problema

O seu trabalho no livro de recortes falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Este erro ocorre quando a execução do trabalho excede a quota gratuita de 500 minutos para a sua conta. Esta quota aplica-se a todos os tipos de tarefas de execução de postos de trabalho. Algumas destas tarefas estão a testar um trabalho, a iniciar um trabalho a partir do portal, a executar um trabalho utilizando webhooks, ou a agendar um trabalho para executar usando o portal Azure ou o seu datacenter. Para saber mais sobre os preços da Automação, consulte [os preços da Automação.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Resolução

Se pretender utilizar mais de 500 minutos de processamento por mês, altere a sua subscrição do nível Livre para o nível Básico:

1. Inscreva-se na sua assinatura Azure.
1. Selecione a conta Automation para atualizar.
1. Selecione **Definições,** e, em seguida, **selecione Preços**.
1. Selecione **Ativar** na página inferior para atualizar a sua conta para o nível Básico.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: Trabalho de runbook tenta três vezes, mas não começa de cada vez

### <a name="issue"></a>Problema

O seu livro de execução falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Este erro ocorre devido a uma das seguintes questões:

* **Limite de memória.** Um trabalho pode falhar se estiver a usar mais de 400 MB de memória. Os limites documentados da memória atribuída a uma caixa de areia encontram-se nos [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)do serviço Automation . 
* **Tomadas de rede.** As caixas de areia azure estão limitadas a 1.000 tomadas de rede simultâneas. Para mais informações, consulte [os limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)do serviço automation .
* **Módulo incompatível.** As dependências dos módulos podem não estar corretas. Neste caso, o seu livro `Command not found` de `Cannot bind parameter` execução normalmente devolve uma ou mensagem.
* **Sem autenticação com Diretório Ativo para caixa de areia.** O seu livro de corridas tentou chamar um executável ou subprocessque que funciona numa caixa de areia Azure. Configurar livros de execução para autenticar com a AD Azure utilizando a Biblioteca de Autenticação de Diretórios Ativos Azure (ADAL) não é suportado.
* **Muitos dados de exceção.** O seu livro de execução tentou escrever demasiados dados de exceção ao fluxo de saída.

### <a name="resolution"></a>Resolução

* **Limite de memória, tomadas de rede.** As formas sugeridas de trabalhar dentro dos limites de memória são dividir a carga de trabalho entre vários livros de execução, processar menos dados na memória, evitar escrever saídas desnecessárias dos seus livros de execução, e considerar quantos postos de controlo estão escritos nos seus livros de fluxo de trabalho PowerShell. Utilize o método claro, como, por exemplo, `$myVar.clear`para limpar variáveis e usar `[GC]::Collect` para executar a recolha de lixo imediatamente. Estas ações reduzem a pegada de memória do seu livro de execução durante o tempo de execução.
* **Módulo incompatível.** Atualize os seus módulos Azure seguindo os passos em Como atualizar os [módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).
* **Sem autenticação com Diretório Ativo para caixa de areia.** Quando autenticar a Azure AD com um livro de execução, certifique-se de que o módulo Azure AD está disponível na sua conta Automation. Certifique-se de conceder a Executar Como conta as permissões necessárias para executar as tarefas que o livro de execução automatiza.

  Se o seu livro de execução não puder chamar um executável ou subprocesso a funcionar numa caixa de areia Azure, utilize o livro de execução num Trabalhador híbrido do livro de [corridas](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não se limitam pelos limites de memória e rede que as caixas de areia Azure têm.

* **Muitos dados de exceção.** Há um limite de 1 MB no fluxo de saída de emprego. Certifique-se de que o seu livro de execução `try` `catch` encerra chamadas para um executável ou subprocesso utilizando e bloqueia. Se as operações lançarem uma exceção, faça com que o código escreva a mensagem da exceção para uma variável de Automação. Esta técnica impede que a mensagem seja escrita no fluxo de saída de trabalho.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Cenário: Trabalho da PowerShell falha com mensagem de erro "Não pode invocar método"

### <a name="issue"></a>Problema

Recebe a seguinte mensagem de erro quando inicia um trabalho powerShell num livro que corre em Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Este erro pode indicar que os livros de execução que funcionam numa caixa de areia Azure não podem ser executados no [modo Full Language](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas formas de resolver este erro:

* Em vez de utilizar o [Start-Job,](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)utilize o [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) para iniciar o livro de execução.
* Tente executar o livro de corridas num trabalhador híbrido.

Para saber mais sobre este comportamento e outros comportamentos dos livros de execução da Automação Azure, consulte a execução do Livro de [Corridas na Automação Azure.](../automation-runbook-execution.md)

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: Um livro de corridas de longa duração não está concluído

### <a name="issue"></a>Problema

O teu livro mostra-se num estado parado depois de correr especado durante três horas. Também pode receber este erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Este comportamento é por design em caixas de areia Azure devido à monitorização [justa](../automation-runbook-execution.md#fair-share) dos processos no âmbito da Automação Azure. Se um processo executar mais de três horas, a parte justa para automaticamente um livro de execução. O estatuto de um livro de execução que ultrapassa o prazo de partilha justa difere por tipo de livro de execução. Os livros powerShell e Python estão definidos para um estado de paragem. Os livros de fluxo de trabalho PowerShell estão definidos para falhar.

### <a name="cause"></a>Causa

O livro de corridas ultrapassou o limite de três horas permitido por uma participação justa numa caixa de areia Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o livro de corridas num Trabalhador híbrido do livro de [corridas.](../automation-hrw-run-runbooks.md) Os trabalhadores híbridos não estão limitados pelo limite de três horas de fair book que as caixas de areia Azure têm. Os livros de corridas que funcionam em Trabalhadores híbridos de runbook devem ser desenvolvidos para apoiar comportamentos de reinício se houver problemas inesperados de infraestrutura local.

Outra solução é otimizar o livro de corridas criando [livros infantis.](../automation-child-runbooks.md) Se o seu livro de recortes passar pela mesma função em vários recursos, por exemplo, numa operação de base de dados em várias bases de dados, pode mover a função para um livro de rés-do-ar para crianças. Cada livro de corridas de crianças executa em paralelo num processo separado. Este comportamento diminui a quantidade total de tempo para o livro de execução dos pais completar.

Os cmdlets PowerShell que permitem o cenário do livro de crianças são:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Este cmdlet permite-lhe iniciar um runbook e transmitir parâmetros para o runbook.
* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Se houver operações que devam ser realizadas após o fim do livro de recorridas para crianças, este cmdlet permite-lhe verificar o estado de trabalho de cada criança.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: Erro nos fluxos de emprego sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Você vê o seguinte erro nos seus fluxos de trabalho para um livro de corridas:

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

Este erro é provavelmente causado pela utilização de uma migração incompleta de módulos AzureRM para Az no seu livro de execução. Esta situação pode fazer com que a Azure Automation inicie um trabalho de livro de corridas utilizando apenas módulos AzureRM e, em seguida, inicie outro trabalho usando apenas módulos Az, o que leva a uma queda de caixa de areia.

### <a name="resolution"></a>Resolução

Não recomendamos a utilização de cmdlets Az e AzureRM no mesmo livro de corridas. Para saber mais sobre a correta utilização destes módulos, consulte [os módulos Migrating para Az](../shared-resources/modules.md#migrating-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Cenário: Acesso negado ao utilizar caixa de areia Azure para livro de execução ou aplicação

### <a name="issue"></a>Problema

Quando o seu livro de execução ou aplicação tenta funcionar numa caixa de areia Azure, o ambiente nega o acesso.

### <a name="cause"></a>Causa

Este problema pode ocorrer porque as caixas de areia Azure impedem o acesso a todos os servidores COM fora do processo. Por exemplo, uma aplicação ou um livro de execução sandbox não pode entrar na Instrumentação de Gestão do Windows (WMI) ou no serviço de Instalação do Windows (msiserver.exe). 

### <a name="resolution"></a>Resolução

Para mais detalhes sobre a utilização de caixas de areia Azure, consulte a execução do Livro de [Corridas na Automação Azure.](../automation-runbook-execution.md#where-to-run-your-runbooks)

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Cenário: Código de estado proibido inválido ao utilizar o Cofre chave dentro de um livro de execução

### <a name="issue"></a>Problema

Ao tentar aceder ao Cofre chave Azure através de um livro de execução da Automação Azure, obtém-se o seguinte erro:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Causa

As possíveis causas para esta questão são:

* Não usar uma conta Run As.
* Permissões insuficientes.

### <a name="resolution"></a>Resolução

#### <a name="not-using-a-run-as-account"></a>Não usar uma conta Run As

Siga [o Passo 5 - Adicione a autenticação para gerir os recursos do Azure](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) para garantir que está a utilizar uma conta Run As para aceder ao Cofre chave.

#### <a name="insufficient-permissions"></a>Permissões insuficientes

[Adicione permissões ao Cofre chave](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) para garantir que a sua conta Run As tenha permissões suficientes para aceder ao Cofre chave.

## <a name="recommended-documents"></a>Documentos recomendados

* [Execução de runbooks na Automatização do Azure](../automation-runbook-execution.md)
* [Iniciar um livro de corridas na Automação Azure](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema aqui ou não consegue resolver o seu problema, experimente um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga-o à comunidade Azure para obter respostas, apoioe e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter Suporte**.
