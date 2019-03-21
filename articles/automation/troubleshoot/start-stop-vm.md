---
title: Resolução de problemas de iniciar e parar VMs com a automatização do Azure
description: Este artigo fornece informações sobre resolução de problemas de iniciar e parar VMs na automatização do Azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1894fc1823772c27f37829b9b27ec515dcb841b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861065"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Iniciar/parar VMs durante a solução de horas de resolução de problemas

## <a name="deployment-failure"></a>Cenário: A solução iniciar/parar VM não consegue implementar corretamente

### <a name="issue"></a>Problema

Ao implementar o [iniciar/parar VMs durante a solução de horas](../automation-solution-vm-management.md), receberá um dos seguintes erros:

```
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Causa

As implementações podem falhar devido a uma das seguintes razões:

1. Já existe uma conta de automatização com o mesmo nome na região selecionada.
2. É uma política que não permite a implementação da solução iniciar/parar VMs.
3. O `Microsoft.OperationsManagement`, `Microsoft.Insights`, ou `Microsoft.Automation` tipos de recursos não estão registados.
4. A área de trabalho do Log Analytics tem um bloqueio.

### <a name="resolution"></a>Resolução

Reveja a seguir uma lista de possíveis soluções para o seu problema ou locais para procurar:

1. Contas de automatização tem de ser exclusivo dentro de uma região do Azure, mesmo que se encontrem em grupos de recursos diferentes. Verifique as suas contas de automatização existentes na região de destino.
2. Uma política existente impede que um recurso que é necessário para a solução iniciar/parar VM devem ser implantados. Vá para as atribuições de política no portal do Azure e verifique se tem uma atribuição de política que não permite a implementação deste recurso. Para obter mais informações, veja [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Para poder implementar a solução iniciar/parar VM, a subscrição tem de estar registada para os espaços de nomes de recursos do Azure seguintes:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Ver, [resolver erros de registo do fornecedor de recursos](../../azure-resource-manager/resource-manager-register-provider-errors.md) para obter mais informações sobre erros quando registar fornecedores.
4. Se tiver um bloqueio na sua área de trabalho do Log Analytics, vá para a área de trabalho no portal do Azure e remover nenhum bloqueio no recurso.

## <a name="all-vms-fail-to-startstop"></a>Cenário: Todas as VMs não conseguem iniciar/parar

### <a name="issue"></a>Problema

Configurar a solução iniciar/parar VM, mas não iniciar ou parar todas as VMs configuradas.

### <a name="cause"></a>Causa

Este erro pode ser causado por um dos seguintes motivos:

1. Uma agenda não está configurada corretamente
2. A conta RunAs pode não estar configurada corretamente
3. Um runbook pode ter se deparar com erros
4. As VMs podem foram excluídas

### <a name="resolution"></a>Resolução

Reveja a seguir uma lista de possíveis soluções para o seu problema ou locais para procurar:

* Verifique se configurou corretamente uma agenda para a solução iniciar/parar VM. Para saber como configurar uma agenda, veja a [agendas](../automation-schedules.md) artigo.

* Verifique os fluxos de trabalho para os runbooks procurar erros. No portal, aceda à sua conta de automatização e selecione **trabalhos** sob **automatização de processos**. Do **tarefas** aparência da página para as tarefas a partir de um dos runbooks do seguintes:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifique se sua [conta RunAs](../manage-runas-account.md) possui permissões adequadas para as VMs que está tentando iniciar ou parar. Para saber como verificar as permissões num recurso, veja [início rápido: Ver as funções atribuídas a um utilizador com o portal do Azure](../../role-based-access-control/check-access.md). Terá de fornecer o Id da aplicação para o principal de serviço utilizado pela conta Run As. Pode obter este valor ao aceder à sua conta de automatização no portal do Azure, selecionando **contas Run as** sob **definições de conta** e clicando em apropriado a conta Run as.

* VMs não podem ser iniciadas ou paradas se estiver a ser explicitamente excluídos. Excluídos VMs no conjunto do **External_ExcludeVMNames** variável na conta de automatização a solução é implementada. O exemplo seguinte, mostra como pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Cenário: Algumas das minhas VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Configurar a solução iniciar/parar VM, mas ele não iniciar ou parar a algumas das VMs configuradas.

### <a name="cause"></a>Causa

Este erro pode ser causado por um dos seguintes motivos:

1. Se utilizar o cenário de sequência, uma marca poderia ser em falta ou incorretas
2. A VM pode ser excluída
3. A conta RunAs pode não ter permissões suficientes na VM
4. A VM pode ter algo que o impediu de início ou paragem

### <a name="resolution"></a>Resolução

Reveja a seguir uma lista de possíveis soluções para o seu problema ou locais para procurar:

* Ao utilizar o [cenário sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) da VM iniciar/parar, durante a solução de horas, deve certificar-se de que cada VM que pretende iniciar ou parar tem a etiqueta adequada. Certificar-se de que as VMs que pretende começar a ter o `sequencestart` têm de etiqueta e as VMs que pretende parar a `sequencestop` marca. Ambas as etiquetas requerem um valor de número inteiro positivo. Pode usar uma consulta semelhante ao seguinte exemplo para procurar todas as VMs com as etiquetas e os respetivos valores.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VMs não podem ser iniciadas ou paradas se estiver a ser explicitamente excluídos. Excluídos VMs no conjunto do **External_ExcludeVMNames** variável na conta de automatização a solução é implementada. O exemplo seguinte, mostra como pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar VMs, a conta RunAs para a conta de automatização tem de ter permissões adequadas para a VM. Para saber como verificar as permissões num recurso, veja [início rápido: Ver as funções atribuídas a um utilizador com o portal do Azure](../../role-based-access-control/check-access.md). Terá de fornecer o Id da aplicação para o principal de serviço utilizado pela conta Run As. Pode obter este valor ao aceder à sua conta de automatização no portal do Azure, selecionando **contas Run as** sob **definições de conta** e clicando em apropriado a conta Run as.

* Se a VM está a ter um problema ao iniciar ou a desalocar, esse comportamento pode ser causado por um problema na VM em si. Alguns exemplos ou problemas potenciais são, uma atualização está a ser aplicada ao tentar desligar, um serviço paradas e muito mais). Navegue para o recurso da VM e verifique o **registos de atividades** para ver se existem erros nos registos. Também pode tentar iniciar sessão na VM para ver se existem erros nos logs de eventos. Para saber mais sobre a VM de resolução de problemas, consulte [máquinas virtuais de resolução de problemas do Azure](../../virtual-machines/troubleshooting/index.md)

## <a name="custom-runbook"></a>Cenário: Meu runbook personalizado não consegue iniciar ou parar as minhas VMs

### <a name="issue"></a>Problema

É autor de um runbook personalizado ou transferido a partir da galeria do PowerShell e não está funcionando corretamente.

### <a name="cause"></a>Causa

A causa da falha pode ser uma das muitas coisas. Aceda à sua conta de automatização no portal do Azure e selecione **trabalhos** sob **automatização de processos**. Partir do **tarefas** página, procure tarefas do runbook para ver quaisquer falhas de tarefas.

### <a name="resolution"></a>Resolução

É recomendado que utilize o [iniciar/parar VMs durante a solução de horas](../automation-solution-vm-management.md) para iniciar e parar VMs na automatização do Azure. Esta solução é criada pela Microsoft. Runbooks personalizados não são suportados pela Microsoft. Poderá encontrar uma solução para o runbook personalizado ao visitar a [resolução de problemas de runbook](runbooks.md) artigo. Este artigo fornece orientações gerais e resolução de problemas para runbooks de todos os tipos.

## <a name="dont-start-stop-in-sequence"></a>Cenário: VMs não iniciar ou parar na sequência correta

### <a name="issue"></a>Problema

As VMs que configurou na solução não iniciar ou parar na sequência correta.

### <a name="cause"></a>Causa

Isto é provocado por marcação incorretos nas VMs.

### <a name="resolution"></a>Resolução

Siga os passos seguintes para se certificar de que a solução está configurada corretamente.

1. Certifique-se de que todas as VMs para ser iniciada ou interrompida tem um `sequencestart` ou `sequencestop` marca, dependendo da sua situação. Estas etiquetas precisam de um número inteiro positivo como o valor. As VMs são processadas por baseada neste valor de ordem ascendente.
2. Certifique-se os grupos de recursos para as VMs ser iniciado ou parado estão no `External_Start_ResourceGroupNames` ou `External_Stop_ResourceGroupNames` variáveis, dependendo da sua situação.
3. Teste as alterações ao executar o `SequencedStartStop_Parent` runbook com o parâmetro WHATIF definido como True para pré-visualizar as alterações.

Para obter instruções mais detalhadas e adicionais sobre como utilizar a solução para iniciar e parar VMs na sequência, consulte [iniciar/parar VMs na sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Cenário: Falha de tarefa iniciar/parar VM com o estado da 403 Proibido 

### <a name="issue"></a>Problema

Encontrar tarefas que falharam com um `403 forbidden` erro para o iniciar/parar VMs durante a desativar os runbooks de soluções de horas.

### <a name="cause"></a>Causa

Este problema pode ser causado por uma incorretamente configurado ou expirou conta Run As. Também pode ser devido a permissões inadequadas para os recursos de VM pela contas executar como conta de automatização.

### <a name="resolution"></a>Resolução

Para verificar a sua conta Run As está configurada corretamente, aceda à sua conta de automatização no portal do Azure e selecione **contas Run as** sob **definições de conta**. Aqui verá o estado de sua execução contas Run as, se uma conta Run As é configurada incorretamente ou expirada o estado será apresentado isso.

Se for a conta Run As [mal configurados](../manage-runas-account.md#misconfiguration), deve eliminar e recriar a sua conta Run As.

Se o certificado expirou para a sua conta Run As, siga os passos listados em [auto-assinado renovação do certificado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

O problema pode ser causado por permissões em falta. Para saber como verificar as permissões num recurso, veja [início rápido: Ver as funções atribuídas a um utilizador com o portal do Azure](../../role-based-access-control/check-access.md). Terá de fornecer o Id da aplicação para o principal de serviço utilizado pela conta Run As. Pode obter este valor ao aceder à sua conta de automatização no portal do Azure, selecionando **contas Run as** sob **definições de conta** e clicando em apropriado a conta Run as.

## <a name="other"></a>Cenário: Meu problema não estiver listado acima

### <a name="issue"></a>Problema

Pode ter um problema ou um resultado inesperado ao utilizar a iniciar/parar VMs durante a solução de horário comercial que não está listada nesta página.

### <a name="cause"></a>Causa

Erros de muitas vezes podem ser causados por utilizar uma versão antiga e desatualizada da solução.

### <a name="resolution"></a>Resolução

Para resolver muitos erros, é recomendado para remover e atualizar a solução. Para saber como atualizar a solução, veja [atualizar o iniciar/parar VMs durante a solução de horas](../automation-solution-vm-management.md#update-the-solution).

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
