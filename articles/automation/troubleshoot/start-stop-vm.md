---
title: Solução de problemas ao iniciar e parar VMs com a automação do Azure
description: Este artigo fornece informações sobre como iniciar e parar as VMs na automação do Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 860a47386b31403b6a3d41fc2473b1e1040889a7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162028"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Solucionar problemas da solução iniciar/parar VMs fora do horário comercial

## <a name="deployment-failure"></a>Cenário: a solução iniciar/parar VM não é implantada corretamente

### <a name="issue"></a>Problema

Ao implantar a [solução iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md), você receberá um dos seguintes erros:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Causa

As implantações podem falhar devido a um dos seguintes motivos:

1. Já existe uma conta de automação com o mesmo nome na região selecionada.
2. Uma política está em vigor que não permite a implantação da solução iniciar/parar VMs.
3. Os tipos de recurso `Microsoft.OperationsManagement`, `Microsoft.Insights`ou `Microsoft.Automation` não estão registrados.
4. Seu espaço de trabalho do Log Analytics tem um bloqueio.
5. Você tem uma versão desatualizada dos módulos do AzureRM ou a solução iniciar/parar.

### <a name="resolution"></a>Resolução

Examine a lista a seguir para obter as possíveis soluções para o problema ou os locais a serem examinados:

1. As contas de automação precisam ser exclusivas em uma região do Azure, mesmo se estiverem em grupos de recursos diferentes. Verifique suas contas de automação existentes na região de destino.
2. Uma política existente impede que um recurso necessário para a solução iniciar/parar VM seja implantado. Vá para as atribuições de política no portal do Azure e verifique se você tem uma atribuição de política que não permite a implantação desse recurso. Para saber mais sobre isso, consulte [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Para implantar a solução iniciar/parar VM, sua assinatura precisa ser registrada nos seguintes namespaces de recursos do Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Consulte [resolver erros de registro do provedor de recursos](../../azure-resource-manager/resource-manager-register-provider-errors.md) para saber mais sobre erros ao registrar provedores.
4. Se você tiver um bloqueio em seu espaço de trabalho Log Analytics, vá para seu espaço de trabalho no portal do Azure e remova os bloqueios no recurso.
5. Se as resoluções acima não resolverem o problema, siga as instruções em [atualizar a solução](../automation-solution-vm-management.md#update-the-solution) para reimplantar a solução de início/parada.

## <a name="all-vms-fail-to-startstop"></a>Cenário: falha ao iniciar/parar todas as VMs

### <a name="issue"></a>Problema

Você configurou a solução iniciar/parar VM, mas ela não inicia ou interrompe todas as VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. Uma agenda não está configurada corretamente
2. A conta Executar como pode não estar configurada corretamente
3. Um runbook pode ter se executado em erros
4. As VMs podem ter sido excluídas

### <a name="resolution"></a>Resolução

Examine a lista a seguir para obter as possíveis soluções para o problema ou os locais a serem examinados:

* Verifique se você configurou corretamente uma agenda para a solução iniciar/parar VM. Para saber como configurar uma agenda, consulte o artigo [agendas](../automation-schedules.md) .

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse sua conta de automação e selecione **trabalhos** em **automação de processo**. Na página **trabalhos** procure trabalhos de um dos seguintes runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifique se sua [conta runas](../manage-runas-account.md) tem as permissões adequadas às VMs que você está tentando iniciar ou parar. Para saber como verificar as permissões em um recurso, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure, selecionando **contas Executar como** em **configurações de conta** e clicando na conta Executar como apropriada.

* As VMs podem não ser iniciadas ou interrompidas se estiverem sendo explicitamente excluídas. VMs excluídas em conjunto na variável **External_ExcludeVMNames** na conta de automação na qual a solução é implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Cenário: algumas das minhas VMs falham ao iniciar ou parar

### <a name="issue"></a>Problema

Você configurou a solução iniciar/parar VM, mas ela não inicia ou interrompe algumas das VMs configuradas.

### <a name="cause"></a>Causa

Esse erro pode ser causado por um dos seguintes motivos:

1. Se estiver usando o cenário de sequência, uma marca poderá estar ausente ou incorreta
2. A VM pode ser excluída
3. A conta Executar como pode não ter permissões suficientes na VM
4. A VM pode ter algo que a impediu de iniciar ou parar

### <a name="resolution"></a>Resolução

Examine a lista a seguir para obter as possíveis soluções para o problema ou os locais a serem examinados:

* Ao usar o [cenário de sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) da solução iniciar/parar VM durante o expediente, você deve verificar se cada VM que deseja iniciar ou parar tem a marca apropriada. Verifique se as VMs que você deseja iniciar têm a marca de `sequencestart` e as VMs que você deseja interromper têm a marca de `sequencestop`. Ambas as marcas exigem um valor inteiro positivo. Você pode usar uma consulta semelhante ao exemplo a seguir para procurar todas as VMs com as marcas e seus valores.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* As VMs podem não ser iniciadas ou interrompidas se estiverem sendo explicitamente excluídas. VMs excluídas em conjunto na variável **External_ExcludeVMNames** na conta de automação na qual a solução é implantada. O exemplo a seguir mostra como você pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar VMs, a conta Executar como para a conta de automação deve ter as permissões apropriadas para a VM. Para saber como verificar as permissões em um recurso, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure, selecionando **contas Executar como** em **configurações de conta** e clicando na conta Executar como apropriada.

* Se a VM estiver tendo um problema ao iniciar ou desalocar, esse comportamento poderá ser causado por um problema na própria VM. Alguns exemplos ou problemas potenciais são, uma atualização está sendo aplicada ao tentar desligar, um serviço é interrompido e muito mais). Navegue até o recurso da VM e verifique os **logs de atividade** para ver se há erros nos logs. Você também pode tentar fazer logon na VM para ver se há erros nos logs de eventos. Para saber mais sobre como solucionar problemas de sua VM, confira [solução de problemas de máquinas virtuais do Azure](../../virtual-machines/troubleshooting/index.md)

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse sua conta de automação e selecione **trabalhos** em **automação de processo**.

## <a name="custom-runbook"></a>Cenário: meu runbook personalizado falha ao iniciar ou parar minhas VMs

### <a name="issue"></a>Problema

Você criou um runbook personalizado ou baixou um do Galeria do PowerShell e ele não está funcionando corretamente.

### <a name="cause"></a>Causa

A causa da falha pode ser uma das muitas coisas. Acesse sua conta de automação no portal do Azure e selecione **trabalhos** em **automação de processo**. Na página **trabalhos** , procure trabalhos do seu runbook para exibir quaisquer falhas de trabalho.

### <a name="resolution"></a>Resolução

É recomendável usar a [solução iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md) para iniciar e parar VMs na automação do Azure. Essa solução é criada pela Microsoft. Os runbooks personalizados não são suportados pela Microsoft. Você pode encontrar uma solução para seu runbook personalizado visitando o artigo de [solução de problemas de runbook](runbooks.md) . Este artigo fornece diretrizes gerais e solução de problemas para runbooks de todos os tipos. Verifique os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse sua conta de automação e selecione **trabalhos** em **automação de processo**.

## <a name="dont-start-stop-in-sequence"></a>Cenário: as VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

As VMs que você configurou na solução não são iniciadas ou param na sequência correta.

### <a name="cause"></a>Causa

Isso é causado por marcação incorreta nas VMs.

### <a name="resolution"></a>Resolução

Execute as etapas a seguir para garantir que a solução esteja configurada corretamente.

1. Certifique-se de que todas as VMs sejam iniciadas ou interrompidas tenham uma marca `sequencestart` ou `sequencestop`, dependendo de sua situação. Essas marcas precisam de um inteiro positivo como o valor. As VMs são processadas em ordem crescente com base nesse valor.
2. Verifique se os grupos de recursos das VMs a serem iniciadas ou interrompidas estão nas variáveis `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames`, dependendo de sua situação.
3. Teste as alterações executando o runbook `SequencedStartStop_Parent` com o parâmetro WHATIF definido como true para visualizar as alterações.

Para obter instruções mais detalhadas e adicionais sobre como usar a solução para iniciar e parar VMs em sequência, consulte [iniciar/parar VMs em sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Cenário: falha no trabalho de iniciar/parar VM com o status de 403 Proibido

### <a name="issue"></a>Problema

Você encontra trabalhos que falharam com um erro de `403 forbidden` para os runbooks de solução iniciar/parar VMs durante os horários inativos.

### <a name="cause"></a>Causa

Esse problema pode ser causado por uma conta Executar como configurada incorretamente ou expirada. Também pode ser devido a permissões inadequadas para os recursos da VM pela conta de automação contas Executar como.

### <a name="resolution"></a>Resolução

Para verificar se sua conta Executar como está configurada corretamente, acesse sua conta de automação no portal do Azure e selecione **contas Executar como** em **configurações da conta**. Aqui você verá o status de suas contas Executar como, se uma conta Executar como estiver incorretamente configurada ou expirar, o status mostrará isso.

Se sua conta Executar como estiver [configurada incorretamente](../manage-runas-account.md#misconfiguration), você deverá excluir e recriar sua conta Executar como.

Se o certificado tiver expirado para sua conta Executar como, siga as etapas listadas em [renovação de certificado autoassinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

O problema pode ser causado por falta de permissões. Para saber como verificar as permissões em um recurso, consulte [início rápido: exibir funções atribuídas a um usuário usando o portal do Azure](../../role-based-access-control/check-access.md). Você precisará fornecer a ID do aplicativo para a entidade de serviço usada pela conta Executar como. Você pode recuperar esse valor acessando sua conta de automação no portal do Azure, selecionando **contas Executar como** em **configurações de conta** e clicando na conta Executar como apropriada.

## <a name="other"></a>Cenário: meu problema não está listado acima

### <a name="issue"></a>Problema

Você enfrenta um problema ou resultado inesperado ao usar a solução Iniciar/Parar VMs fora do horário comercial que não está listada nesta página.

### <a name="cause"></a>Causa

Muitas vezes, erros podem ser causados pelo uso de uma versão antiga e desatualizada da solução.

> [!NOTE]
> A solução Iniciar/Parar VMs fora do horário comercial foi testada com os módulos do Azure que são importados para sua conta de automação quando você implanta a solução. Atualmente, a solução não funciona com versões mais recentes do módulo do Azure. Isso afeta apenas a conta de automação que você usa para executar a solução de Iniciar/Parar VMs fora do horário comercial. Você ainda pode usar versões mais recentes do módulo do Azure em suas outras contas de automação, conforme descrito em [como atualizar os módulos de Azure PowerShell na automação do Azure](../automation-update-azure-modules.md)

### <a name="resolution"></a>Resolução

Para resolver muitos erros, é recomendável remover e atualizar a solução. Para saber como atualizar a solução, consulte [atualizar a solução iniciar/parar VMs fora do horário comercial](../automation-solution-vm-management.md#update-the-solution). Além disso, você pode verificar os [fluxos de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para procurar quaisquer erros. No portal, acesse sua conta de automação e selecione **trabalhos** em **automação de processo**.

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
