---
title: Resolução de problemas Azure Automation Start/Stop VMs durante problemas fora de horas
description: Este artigo diz como resolver problemas e resolver problemas que surgem durante a utilização dos VMs iniciar/parar durante o período de folga.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: 763e1321556ade73778b82ea70926af21a83f7ec
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896279"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Resolução de problemas Iniciar/Parar VMs durante problemas fora de horas

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que surgem quando implementa os VMs de Arranque/Paragem da Automação Azure durante o período de folga nos seus VMs. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: VMs de início/paragem durante o período de folga falha em implantar corretamente

### <a name="issue"></a>Problema

Quando implementar [VMs de início/paragem durante as horas de folga,](../automation-solution-vm-management.md)recebe um dos seguintes erros:

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

As implementações podem falhar devido a uma das seguintes razões:

- Já existe uma conta Automation com o mesmo nome na região selecionada.
- Uma política não permite a colocação de VMs de início/paragem durante o horário de folga.
- O tipo de recursos ou recursos `Microsoft.OperationsManagement` `Microsoft.Insights` não está `Microsoft.Automation` registado.
- O seu espaço de trabalho Log Analytics está bloqueado.
- Tem uma versão desatualizada dos módulos AzureRM ou dos VMs iniciar/parar durante o período de folga.

### <a name="resolution"></a>Resolução

Rever as seguintes correções para potenciais resoluções:

* As contas de automação têm de ser únicas dentro de uma região do Azure, mesmo que estejam em diferentes grupos de recursos. Consulte as suas contas de Automação existentes na região alvo.
* Uma política existente impede que um recurso necessário para iniciar/parar VMs durante o período de folga seja implantado. Vá às suas atribuições políticas no portal Azure e verifique se tem uma atribuição de política que não permite a implementação deste recurso. Para saber mais, consulte [o erro requestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implementar VMs de início/paragem durante o horário de folga, a sua subscrição precisa de estar registada nos seguintes espaços de nomes de recursos Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Para saber mais sobre erros quando registar fornecedores, consulte [Resolver erros para o registo do fornecedor de recursos.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Se tiver uma fechadura no seu espaço de trabalho Log Analytics, vá ao seu espaço de trabalho no portal Azure e remova quaisquer bloqueios no recurso.
* Se estas resoluções não resolverem o seu problema, siga as instruções em [atualizar a função](../automation-solution-vm-management.md#update-the-feature) para recolocar VMs iniciar/parar durante o horário de folga.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todos os VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Configurar VMs de início/paragem durante o horário de folga, mas não começa nem para todos os VMs.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

- Um horário não está configurado corretamente.
- A conta Run As pode não estar configurada corretamente.
- Um livro pode ter tido erros.
- Os VMs podem ter sido excluídos.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais resoluções:

* Verifique se configurau corretamente um horário para VMs de início/paragem durante as horas de folga. Para aprender a configurar um horário, consulte [horários.](../shared-resources/schedules.md)

* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. Procure empregos a partir de um dos seguintes livros:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se a sua [conta Run As](../manage-runas-account.md) tem permissões adequadas para os VMs que está a tentar iniciar ou parar. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de aplicação para o principal serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta Automation no portal Azure. Selecione **Executar como contas** em **Definições de Conta** e selecione a conta Run As apropriada.

* Os VMs podem não ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMs excluídos são definidos na `External_ExcludeVMNames` variável na conta Automation a que a funcionalidade é implantada. O exemplo a seguir mostra como pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Alguns dos meus VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Configurar VMs de início/paragem durante o horário de folga, mas não começa nem para alguns dos VMs configurados.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

- No cenário da sequência, uma etiqueta pode estar em falta ou incorreta.
- O VM pode ser excluído.
- A conta Run As pode não ter permissões suficientes no VM.
- O VM pode ter um problema que o impediu de começar ou parar.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais resoluções:

* Quando utilizar o cenário de [sequência](../automation-solution-vm-management.md) de VMs de início/paragem durante o horário de folga, deve certificar-se de que cada VM que pretende iniciar ou parar tem a etiqueta adequada. Certifique-se de que os VMs que pretende começar têm a `sequencestart` etiqueta e os VMs que pretende parar têm a `sequencestop` etiqueta. Ambas as tags requerem um valor inteiro positivo. Pode utilizar uma consulta semelhante ao seguinte para procurar todos os VMs com as etiquetas e os seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Os VMs podem não ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMs excluídos são definidos na `External_ExcludeVMNames` variável na conta Automation a que a funcionalidade é implantada. O exemplo a seguir mostra como pode consultar esse valor com o PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar os VMs, a conta Run As para a conta Automação deve ter permissões apropriadas para o VM. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de aplicação para o principal serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta Automation no portal Azure. Selecione **Executar como contas** em **Definições de Conta** e selecione a conta Run As apropriada.
* Se o VM estiver com problemas de partida ou de negociação, pode haver um problema no próprio VM. Exemplos são uma atualização que está a ser aplicada quando o VM está a tentar desligar, um serviço que está pendurado, e muito mais. Vá ao seu recurso VM e verifique **registos de atividade para** ver se há algum erro nos registos. Também pode tentar iniciar sessão no VM para ver se existem erros nos registos do evento. Para saber mais sobre a resolução de problemas do seu VM, consulte [máquinas virtuais Azure de resolução de problemas.](../../virtual-machines/troubleshooting/index.yml)
* Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. No portal, aceda à sua conta de Automação e selecione **Jobs** under **Process Automation**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: O meu livro personalizado não começa ou para os meus VMs

### <a name="issue"></a>Problema

Foi autor de um livro personalizado ou descarregou um da Galeria PowerShell, e não está a funcionar corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para o fracasso. Aceda à sua conta de Automação no portal Azure e selecione **Jobs** under **Process Automation**. Na página **Jobs,** procure empregos no seu runbook para ver quaisquer falhas de emprego.

### <a name="resolution"></a>Resolução

É recomendável que:

* Utilize [VMs de início/paragem durante as horas de folga](../automation-solution-vm-management.md) para iniciar e parar VMs na Azure Automation. 
* Esteja ciente de que a Microsoft não suporta livros de execução personalizados. Você pode encontrar uma resolução para o seu livro de corridas personalizado em [problemas de runbook](runbooks.md)de resolução de problemas . Verifique os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: VMs não arrancam ou param na sequência correta

### <a name="issue"></a>Problema

Os VMs que ativou para a funcionalidade não arrancam ou param na sequência correta.

### <a name="cause"></a>Causa

Esta questão é causada por uma marcação incorreta nos VMs.

### <a name="resolution"></a>Resolução

Siga estes passos para garantir que a funcionalidade está ativada corretamente:

1. Certifique-se de que todos os VMs a serem iniciados ou parados têm uma `sequencestart` ou `sequencestop` etiqueta, dependendo da sua situação. Estas etiquetas precisam de um número inteiro positivo como o valor. Os VMs são processados por ordem ascendente com base neste valor.
1. Certifique-se de que os grupos de recursos para os VMs a serem iniciados ou parados estão nas `External_Start_ResourceGroupNames` variáveis ou `External_Stop_ResourceGroupNames` variáveis, dependendo da sua situação.
1. Teste as suas alterações executando o **SequencedStartStop_Parent** livro com o `WHATIF` parâmetro definido para True para visualizar as suas alterações.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: VMs de início/paragem durante o trabalho fora de horas falha com 403 erros proibidos

### <a name="issue"></a>Problema

Encontrapresocos que falharam com um `403 forbidden` erro para Iniciar/Parar VMs durante os livros fora de horas.

### <a name="cause"></a>Causa

Este problema pode ser causado por uma conta Run As configurada ou caducada indevidamente configurada. Também pode ser devido a permissões inadequadas aos recursos VM pela conta Run As.

### <a name="resolution"></a>Resolução

Para verificar se a sua conta Run As está devidamente configurada, aceda à sua conta de Automação no portal Azure e selecione **Executar como contas** em **Definições de Conta**. Se uma conta Run As estiver configurada ou caducada incorretamente, o estado mostra a condição.

Se a sua conta Run As estiver mal configurada, elimine e reesconte a sua conta Run As. Para obter mais informações, consulte [Gerir a Gestão da Automação Azure Executar como contas.](../manage-runas-account.md)

Se o certificado tiver expirado para a sua conta Run As, siga os passos na [renovação do certificado auto-assinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

Se faltar permissões, consulte [Quickstart: Ver funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Deve fornecer o ID de aplicação para o principal serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta Automation no portal Azure. Selecione **Executar como contas** em **Definições de Conta** e selecione a conta Run As apropriada.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Cenário: O meu problema não está listado aqui

### <a name="issue"></a>Problema

Sente um problema ou resultado inesperado quando utiliza VMs de início/paragem durante o horário de folga que não está listado nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados pela utilização de uma versão antiga e desatualizada da funcionalidade.

> [!NOTE]
> Os VMs de início/paragem durante o período de folga foram testados com os módulos Azure que são importados para a sua conta Automation quando implementa a funcionalidade em VMs. A funcionalidade não funciona atualmente com versões mais recentes do módulo Azure. Esta restrição apenas afeta a conta Automation que utiliza para executar VMs iniciar/parar durante o horário de folga. Pode ainda utilizar versões mais recentes do módulo Azure nas suas outras contas de Automação, conforme descrito nos [módulos Update Azure PowerShell](../automation-update-azure-modules.md).

### <a name="resolution"></a>Resolução

Para resolver muitos erros, remova e [atualize VMs iniciar/parar durante as horas de folga](../automation-solution-vm-management.md#update-the-feature). Também pode verificar os [fluxos de trabalho](../automation-runbook-execution.md#job-statuses) para procurar quaisquer erros. 

## <a name="next-steps"></a>Próximos passos

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas da Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) , a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, suporte e especialistas.
* Arquive um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **Apoio**.
