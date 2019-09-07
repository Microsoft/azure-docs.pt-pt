---
title: Solucionar erros com Runbooks de automação do Azure
description: Saiba como solucionar problemas com runbooks de automação do Azure
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 326a2dbf644cd5792a45dbf40d9574982053aae1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743903"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Solucionar erros com runbooks

Quando houver erros ao executar runbooks na automação do Azure, você poderá usar as etapas a seguir para ajudar a diagnosticar o problema.

1. **Verifique se o script de runbook é executado com êxito no computador local:**  Consulte a documentação do [PowerShell](/powershell/scripting/overview) ou os [documentos do Python](https://docs.python.org/3/) para referência de linguagem e módulos de aprendizado.

   Executar o script localmente pode descobrir e resolver erros comuns, como:

   - **Módulos ausentes**
   - **Erros de sintaxe**
   - **Erros lógicos**

2. **Investigar runbook** [fluxos de erro](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) para mensagens específicas e os compara com os erros abaixo.

3. **Verifique se os nós e o espaço de trabalho de automação têm os módulos necessários:** Se o runbook importar módulos, verifique se eles estão disponíveis na sua conta de automação usando as etapas listadas em [Importar módulos](../shared-resources/modules.md#import-modules). Atualize seus módulos para a versão mais recente seguindo as instruções em [Atualizar módulos do Azure na automação do Azure](..//automation-update-azure-modules.md). Para obter mais informações sobre solução de problemas, consulte [solucionar problemas de módulos](shared-resources.md#modules).

Se o runbook for suspenso ou falhar inesperadamente:

* [Verifique](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) se os status do trabalho definem os status do runbook e algumas causas possíveis.
* [Adicione uma saída adicional](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) ao runbook para identificar o que acontece antes de o runbook ser suspenso.
* [Manipule quaisquer exceções](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) geradas pelo seu trabalho.

## <a name="login-azurerm"></a>Cenário Execute login-AzureRMAccount para fazer logon

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Este erro pode ocorrer se não estiver a utilizar a conta RunAs ou se esta tiver expirado. Consulte [gerenciar contas Executar como da automação do Azure](https://docs.microsoft.com/azure/automation/manage-runas-account).

Esse erro tem duas causas principais:

* Versões diferentes dos módulos do AzureRM.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM, deverá atualizar todos os seus módulos AzureRM para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, poderá seguir as etapas abaixo para configurar permissões.

1. Vá para a conta Executar como da conta de automação e copie a ID do aplicativo e a impressão digital.
  ![Copiar a ID do aplicativo e a impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)
1. Vá para o controle de acesso da assinatura em que a conta de automação não está hospedada e adicione uma nova atribuição de função.
  ![Controlo de acesso](../media/troubleshoot-runbooks/access-control.png)
1. Adicione a ID do aplicativo coletada na etapa anterior. Selecione permissões de colaborador.
   ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copie o nome da assinatura para a próxima etapa.
1. Agora você pode usar o seguinte código de runbook para testar as permissões de sua conta de automação para a outra assinatura.

    Substitua "\<CertificateThumbprint\>" pelo valor que você copiou na etapa #1\>e o valor "\<subscriptionname" copiado na etapa #4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>Cenário Não é possível localizar a assinatura do Azure

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com os `Select-AzureSubscription` cmdlets ou `Select-AzureRmSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Esse erro pode ocorrer se:

* O nome da assinatura não é válido

* O usuário Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.

### <a name="resolution"></a>Resolução

Execute as seguintes etapas para determinar se você autenticou para o Azure e tem acesso à assinatura que está tentando selecionar:

1. Para garantir que ele funcione de forma autônoma, teste seu script fora da automação do Azure.
2. Certifique-se de executar o `Add-AzureAccount` cmdlet antes de executar `Select-AzureSubscription` o cmdlet.
3. Adicione `Disable-AzureRmContextAutosave –Scope Process` ao início do seu runbook. Esse cmdlet garante que todas as credenciais se apliquem somente à execução do runbook atual.
4. Se você ainda vir essa mensagem de erro, modifique seu código adicionando o parâmetro **AzureRmContext** após o `Add-AzureAccount` cmdlet e, em seguida, execute o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Cenário Falha na autenticação do Azure porque a autenticação multifator está habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com seu nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, não poderá usar um Azure Active Directory usuário para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para autenticar no Azure.

### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com cmdlets Azure Resource Manager, consulte [criando a entidade de serviço usando portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [Autenticando uma entidade de serviço com Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="get-serializationsettings"></a>Cenário Você verá um erro em seus fluxos de trabalho sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Você vê em seu erro em seus fluxos de trabalho para um runbook com a seguinte mensagem:

```error
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

### <a name="cause"></a>Causa

Esse erro é causado pelo uso de cmdlets AzureRM e AZ em um runbook. Ele ocorre quando você importa `Az` antes da `AzureRM`importação.

### <a name="resolution"></a>Resolução

Os cmdlets AZ e AzureRM não podem ser importados e usados no mesmo runbook, para saber mais sobre o suporte AZ na automação do Azure, consulte [AZ module support in Azure Automation](../az-modules.md).

## <a name="task-was-cancelled"></a>Cenário O runbook falha com o erro: Uma tarefa foi cancelada

### <a name="issue"></a>Problema

O runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado pelo uso de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido atualizando seus módulos do Azure para a versão mais recente.

Em sua conta de automação, clique em **módulos**e clique em **Atualizar módulos do Azure**. A atualização leva aproximadamente 15 minutos, uma vez concluída, execute novamente o runbook que estava falhando. Para saber mais sobre como atualizar seus módulos, confira [Atualizar módulos do Azure na automação do Azure](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Cenário Os Runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha ao gerenciar recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto ao executar.

### <a name="resolution"></a>Resolução

Ao trabalhar com várias assinaturas, o contexto da assinatura pode ser perdido ao invocar runbooks. Para garantir que o contexto de assinatura seja passado para os runbooks, adicione `AzureRmContext` o parâmetro ao cmdlet e passe o contexto para ele. Também é recomendável usar o `Disable-AzureRmContextAutosave` cmdlet com o escopo do **processo** para garantir que as credenciais usadas sejam usadas somente para o runbook atual.

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

Para obter mais informações, consulte [trabalhando com várias assinaturas](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Cenário Termo não reconhecido como o nome de um cmdlet, função, script

### <a name="issue"></a>Problema

O runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer com base em um dos seguintes motivos:

* O módulo que contém o cmdlet não é importado para a conta de automação
* O módulo que contém o cmdlet é importado, mas está desatualizado

### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido com a conclusão de uma das seguintes tarefas:

Se o módulo for um módulo do Azure, consulte [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md) para saber como atualizar seus módulos em sua conta de automação.

Se for um módulo separado, certifique-se de que o módulo em importado na sua conta de automação.

## <a name="job-attempted-3-times"></a>Cenário Houve uma tentativa de início do trabalho de runbook três vezes, mas ele não foi iniciado a cada vez

### <a name="issue"></a>Problema

O runbook falha com o erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* Limite de memória. Os limites documentados sobre a quantidade de memória alocada para uma área restrita são encontrados nos [limites do serviço de automação](../../azure-subscription-service-limits.md#automation-limits). Um trabalho poderá falhar se ele estiver usando mais de 400 MB de memória.

* Soquetes de rede. As áreas restritas do Azure são limitadas a 1000 soquetes de rede simultâneos, conforme descrito em [limites do serviço de automação](../../azure-subscription-service-limits.md#automation-limits).

* Módulo incompatível. Esse erro pode ocorrer se as dependências de módulo não estiverem corretas e, se não forem, o runbook normalmente retorna uma mensagem "comando não encontrado" ou "não é possível associar o parâmetro".

* Seu runbook tentou chamar um executável ou subprocesso em um runbook executado em uma área restrita do Azure. Não há suporte para esse cenário em áreas restritas do Azure.

* O runbook tentou gravar muitos dados de exceção no fluxo de saída.

### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Os métodos sugeridos para trabalhar dentro do limite de memória são dividir a carga de trabalho entre vários runbooks, não processar tantos dados na memória, não gravar a saída desnecessária de seus runbooks ou considerar quantos pontos de verificação você grava em seu fluxo de trabalho do PowerShell runbook. Você pode usar o método Clear, `$myVar.clear()` como para limpar a variável e usar `[GC]::Collect()` para executar a coleta de lixo imediatamente. Essas ações reduzem a superfície de memória do runbook durante o tempo de execução.

* Atualize seus módulos do Azure seguindo as etapas [como atualizar os módulos Azure PowerShell na automação do Azure](../automation-update-azure-modules.md).

* Outra solução é executar o runbook em um [Hybrid runbook Worker](../automation-hrw-run-runbooks.md). Os Hybrid Workers não são limitados pelos limites de memória e de rede que as áreas restritas do Azure são.

* Se precisar chamar um processo (como. exe ou subprocesso. Call) em um runbook, você precisará executar o runbook em um [Hybrid runbook Worker](../automation-hrw-run-runbooks.md).

* Há um limite de 1MB no fluxo de saída do trabalho. Certifique-se de colocar chamadas para um executável ou subprocesso em um bloco try/catch. Se eles lançarem uma exceção, grave a mensagem dessa exceção em uma variável de automação. Isso impedirá que ele seja gravado no fluxo de saída do trabalho.

## <a name="sign-in-failed"></a>Cenário Falha ao entrar na conta do Azure

### <a name="issue"></a>Problema

Você recebe um dos seguintes erros ao trabalhar com os `Add-AzureAccount` cmdlets ou: `Connect-AzureRmAccount`

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esse erro ocorrerá se o nome do ativo de credencial não for válido. Esse erro também pode ocorrer se o nome de usuário e a senha que você usou para configurar o ativo de credencial de automação não forem válidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, execute as seguintes etapas:

1. Certifique-se de que você não tem nenhum caractere especial. Esses caracteres incluem o **\@** caractere no nome do ativo de credencial de automação que você está usando para se conectar ao Azure.
2. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial de automação do Azure em seu editor de ISE do PowerShell local. Você pode verificar se o nome de usuário e a senha estão corretos executando os seguintes cmdlets no ISE do PowerShell:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a autenticação falhar localmente, isso significa que você não configurou suas credenciais de Azure Active Directory corretamente. Consulte [Autenticando no Azure usando](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) a postagem de blog Azure Active Directory para obter a conta de Azure Active Directory configurada corretamente.

4. Se parecer um erro transitório, tente adicionar lógica de repetição à sua rotina de autenticação para tornar a autenticação mais robusta.

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
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>Referência de objeto não definida para uma instância do objeto

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao invocar um runbook filho com a `-Wait` opção e o fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Há um problema conhecido em que o [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) não trata o fluxo de saída corretamente se ele contiver objetos.

### <a name="resolution"></a>Resolução

Para resolver isso, é recomendável que você implemente uma lógica de sondagem e use o cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para recuperar a saída. Um exemplo dessa lógica é definido no exemplo a seguir.

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

## <a name="fails-deserialized-object"></a>Cenário Falha no runbook devido ao objeto desserializado

### <a name="issue"></a>Problema

O runbook falha com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o runbook for um fluxo de trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para persistir o estado do runbook se o fluxo de trabalho for suspenso.

### <a name="resolution"></a>Resolução

Qualquer uma das três soluções a seguir corrige esse problema:

* Se você estiver canalizando objetos complexos de um cmdlet para outro, empacote esses cmdlets em um InlineScript.
* Passe o nome ou o valor que você precisa do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de fluxo de trabalho do PowerShell.

## <a name="quota-exceeded"></a>Cenário Falha no trabalho de runbook porque a cota alocada foi excedida

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota livre de 500 minutos para sua conta. Essa cota se aplica a todos os tipos de tarefas de execução de trabalho. Algumas dessas tarefas podem estar testando um trabalho, iniciando um trabalho no portal, executando um trabalho usando WebHooks ou agendando um trabalho para ser executado usando o portal do Azure ou em seu datacenter. Para saber mais sobre os preços da automação, confira [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, você precisará alterar sua assinatura da camada gratuita para a camada básica. Você pode atualizar para a camada básica executando as seguintes etapas:

1. Inicie sessão na sua subscrição do Azure
2. Selecione a conta de Automatização que quer atualizar
3. Clique em **configurações** > **preços**.
4. Clique em **habilitar** na parte inferior da página para atualizar sua conta para a camada **básica** .

## <a name="cmdlet-not-recognized"></a>Cenário Cmdlet não reconhecido ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue localizar o cmdlet que você está usando em seu runbook. Esse erro pode ocorrer porque o módulo que contém o cmdlet está ausente na conta, há um conflito de nome com um nome de runbook ou o cmdlet também existe em outro módulo e a automação não pode resolver o nome.

### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Verifique se você inseriu o nome do cmdlet corretamente.
* Verifique se o cmdlet existe em sua conta de automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e procure o cmdlet que você deseja encontrar na biblioteca ou execute `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há nenhum conflito de nome com outros cmdlets ou runbooks, adicione-o à tela e verifique se você está usando um conjunto de parâmetros válido em seu runbook.
* Se você tiver um conflito de nome e o cmdlet estiver disponível em dois módulos diferentes, você poderá resolver esse problema usando o nome totalmente qualificado para o cmdlet. Por exemplo, você pode usar **ModuleName\CmdletName**.
* Se você estiver executando o runbook local em um grupo de trabalho híbrido, verifique se o módulo e o cmdlet estão instalados no computador que hospeda o Hybrid Worker.

## <a name="long-running-runbook"></a>Cenário Falha na conclusão de um runbook de execução longa

### <a name="issue"></a>Problema

O runbook é exibido em um estado **parado** após a execução por 3 horas. Você também pode receber o erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Esse comportamento é por design em áreas restritas do Azure devido ao monitoramento "justa share" de processos na automação do Azure. Se ele for executado por mais de três horas, Fair share interromperá automaticamente um runbook. O status de um runbook que passa pelo limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos como um status **parado** . Os runbooks de fluxo de trabalho do PowerShell estão definidos como **com falha**.

### <a name="cause"></a>Causa

O runbook foi executado no limite de 3 horas permitido pelo Fair share em uma área restrita do Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid runbook Worker](../automation-hrw-run-runbooks.md).

Os Hybrid Workers não são limitados pelo limite de runbook [Fair share](../automation-runbook-execution.md#fair-share) 3 horas que as áreas restritas do Azure são. Os Runbooks executados em Hybrid runbook Workers devem ser desenvolvidos para dar suporte a comportamentos de reinicialização se houver problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook criando [runbooks filho](../automation-child-runbooks.md). Se o seu runbook faz o loop pela mesma função em vários recursos, como uma operação de banco de dados em vários bancos, você pode mover essa função para um runbook filho. Cada um desses runbooks filho é executado em paralelo em processos separados. Esse comportamento diminui a quantidade total de tempo para o runbook pai ser concluído.

Os cmdlets do PowerShell que habilitam o cenário de runbook filho são:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) -este cmdlet permite que você inicie um runbook e passe parâmetros para o runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – se houver operações que precisam ser executadas após a conclusão do runbook filho, esse cmdlet permitirá que você verifique o status do trabalho para cada filho.

## <a name="expired webhook"></a>Cenário Estado 400 solicitação inadequada ao chamar um webhook

### <a name="issue"></a>Problema

Ao tentar invocar um webhook para um runbook de automação do Azure, você receberá o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou.

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você poderá reabilitar o webhook por meio do portal do Azure. Quando um webhook expira, o webhook precisa ser excluído e recriado. Você só poderá [renovar um webhook](../automation-webhooks.md#renew-webhook) se ele ainda não tiver expirado.

## <a name="429"></a>Cenário 429: The request rate is currently too large. Tente novamente

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao `Get-AzureRmAutomationJobOutput` executar o cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tem muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Resolução

Há duas maneiras de resolver esse erro:

* Edite o runbook e reduza o número de fluxos de trabalho emitidos por ele.
* Reduza o número de fluxos a serem recuperados ao executar o cmdlet. Para seguir esse comportamento, você pode especificar o `-Stream Output` parâmetro para o `Get-AzureRmAutomationJobOutput` cmdlet para recuperar somente os fluxos de saída. 

## <a name="cannot-invoke-method"></a>Cenário O trabalho do PowerShell falha com o erro: Não é possível invocar o método

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho do PowerShell em um runbook em execução no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você inicia um trabalho do PowerShell em um runbook executado no Azure. Esse comportamento pode ocorrer porque os runbooks executados em uma área restrita do Azure podem não ser executados no [modo de linguagem completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há duas maneiras de resolver esse erro:

* Em vez de `Start-Job`usar, `Start-AzureRmAutomationRunbook` use para iniciar um runbook
* Se o runbook tiver essa mensagem de erro, execute-a em um Hybrid Runbook Worker

Para saber mais sobre esse comportamento e outros comportamentos dos Runbooks de automação do Azure, consulte [comportamento do runbook](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Meu problema não está listado acima

As seções a seguir listam outros erros comuns, além da documentação de suporte para ajudá-lo a resolver o problema.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A função de trabalho de runbook híbrida não executa trabalhos ou não está a responder

Se você estiver executando trabalhos usando um Hybrid Worker em vez de na automação do Azure, talvez seja necessário [solucionar o problema do próprio trabalhador híbrido](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>O runbook falha com "Sem permissão" ou uma variante desta mensagem

As contas RunAs poderão não ter as mesmas permissões para os recursos do Azure que a sua conta atual. Confirme que a conta RunAs [tem as permissões para aceder aos recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilizados no seu script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam a funcionar, mas pararam repentinamente

* Se os runbooks foram executados anteriormente, mas forem interrompidos, [Verifique se a conta Executar como não expirou](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se estiver a utilizar webhooks para iniciar os runbooks, [confirme que o webhook não expirou](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Problemas ao passar parâmetros para WebHooks

Para obter ajuda para passar parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Problemas usando módulos AZ

A utilização de módulos do Azure e do AzureRM na mesma Conta de Automatização não é suportada. Consulte [AZ modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules) para obter mais detalhes.

## <a name="inconsistent-behavior-in-runbooks"></a>Comportamento inconsistente nos runbooks

Siga as orientações em [Runbook Execution](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) (Execução do Runbook), para evitar problemas em trabalhos simultâneos, para impedir a criação de recursos várias vezes ou para impedir outra lógica sensível ao tempo nos runbooks.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>O runbook falha com os erros: Nenhuma permissão, proibido, 403 ou alguma variação

As contas RunAs poderão não ter as mesmas permissões para os recursos do Azure que a sua conta atual. Verifique se sua conta RunAs tem [permissões para acessar os recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) usados em seu script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Os runbooks estavam a funcionar, mas pararam repentinamente

* Se os runbooks foram executados anteriormente, mas forem interrompidos, verifique se a conta Executar como [não expirou](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Se você estiver usando WebHooks para iniciar runbooks, verifique se o webhook [não expirou](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="passing-parameters-into-webhooks"></a>Transmitir parâmetros para os webhooks

Para obter ajuda para passar parâmetros em WebHooks, consulte [Iniciar um runbook de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Utilizar os módulos do Az

A utilização de módulos do Azure e do AzureRM na mesma Conta de Automatização não é suportada. Consulte [AZ modules in runbooks](https://docs.microsoft.com/azure/automation/az-modules) para obter mais detalhes.

## <a name="using-self-signed-certificates"></a>Utilizar Certificados Autoassinados

Para usar certificados autoassinados, você deve seguir o guia em [criando um novo certificado](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Documentos Recomendados

* [Starting a Runbook in Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook) (Iniciar um Runbook na Automatização do Azure)
* [Runbook Execution in Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution) (Execução de Runbooks na Automatização do Azure)

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
