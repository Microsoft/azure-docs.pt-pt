---
title: Problemas de automação Azure Start/Stop VMs durante problemas de implementação fora de horas
description: Este artigo diz como resolver problemas e resolver problemas que surgem durante a implementação dos VMs de início/paragem durante a funcionalidade off-hours.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bd537fd943e9a13a59c2fa630235130ce9ccfe2d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680845"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-deployment-issues"></a>Problemas Start/Stop VMs durante problemas de implantação fora de horas

Este artigo fornece informações sobre problemas de resolução e resolução de problemas que surgem quando implementa os VMs De Arranque/Paragem de Automação Azure durante a funcionalidade off-hours nos seus VMs. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: Start/Stop VMs durante o horário de folga não é corretamente implantado

### <a name="issue"></a>Problema

Quando implementa [VMs start/stop durante as horas de folga,](../automation-solution-vm-management.md)recebe um dos seguintes erros:

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

- Já existe uma conta de Automação com o mesmo nome na região selecionada.
- Uma política proíbe a implantação de VMs Start/Stop durante o horário de folga.
- O `Microsoft.OperationsManagement` `Microsoft.Insights` tipo de recurso não `Microsoft.Automation` está registado.
- O seu espaço de trabalho Log Analytics está bloqueado.
- Tem uma versão desatualizada dos módulos AzureRM ou dos VMs Start/Stop durante a funcionalidade off-hours.

### <a name="resolution"></a>Resolução

Reveja as seguintes correções para potenciais resoluções:

* As contas de automação têm de ser únicas dentro de uma região do Azure, mesmo que estejam em diferentes grupos de recursos. Verifique as suas contas de Automação existentes na região alvo.
* Uma política existente impede que um recurso necessário para os VMs de arranque/paragem durante o horário de folga seja implantado. Vá às suas atribuições políticas no portal Azure e verifique se tem uma atribuição de política que não permita a implantação deste recurso. Para saber mais, consulte [RequestDisallowedByPolicy error](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implementar VMs Start/Stop durante o horário de folga, a sua subscrição tem de ser registada nos seguintes espaços de nome de recursos Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Para saber mais sobre erros quando regista fornecedores, consulte Resolver erros para o registo do fornecedor de [recursos.](../../azure-resource-manager/templates/error-register-resource-provider.md)
* Se tiver um bloqueio no seu espaço de trabalho Log Analytics, vá ao seu espaço de trabalho no portal Azure e remova quaisquer fechaduras no recurso.
* Se estas resoluções não resolverem o seu problema, siga as instruções em ['Actualizar' a funcionalidade](../automation-solution-vm-management.md#update-the-feature) para reimplantar Os VMs Start/Stop durante o horário de folga.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todos os VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Configuraste Os VMs de Início/Paragem durante o horário de folga, mas não começa nem para todos os VMs.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

- Um horário não está configurado corretamente.
- A conta Executar Como pode não ser configurada corretamente.
- Um livro de corridas pode ter tido erros.
- Os VMs podem ter sido excluídos.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais resoluções:

* Verifique se configura bem um horário para VMs de início/paragem durante o horário de folga. Para aprender a configurar um horário, consulte [Horários.](../automation-schedules.md)

* Verifique os [fluxos](../automation-runbook-execution.md#job-statuses) de trabalho para procurar erros. Procure empregos num dos seguintes livros:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se a sua [conta Run As](../manage-runas-account.md) tem permissões adequadas para os VMs que está a tentar iniciar ou parar. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta de Automação no portal Azure. Selecione **Executar como contas** em **Definições**de Conta , e selecione o executar apropriado Como conta.

* Os VMs podem não ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMexcluídos são definidos na `External_ExcludeVMNames` variável na conta Automation para a qual a funcionalidade é implantada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Alguns dos meus VMs não conseguem começar ou parar

### <a name="issue"></a>Problema

Configurou Os VMs de Início/Paragem durante as horas de folga, mas não inicia nem para alguns dos VMs configurados.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

- No cenário de sequência, uma etiqueta pode estar em falta ou incorreta.
- O VM pode ser excluído.
- A conta Run As pode não ter permissões suficientes no VM.
- O VM pode ter um problema que o impediu de começar ou parar.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais resoluções:

* Quando utilizar o cenário de [sequência](../automation-solution-vm-management.md) de VMs de arranque/paragem durante o horário de folga, deve certificar-se de que cada VM que pretende iniciar ou parar tem a etiqueta adequada. Certifique-se de que os VMs que deseja começar têm a `sequencestart` etiqueta e os VMs que pretende parar têm a `sequencestop` etiqueta. Ambas as etiquetas requerem um valor inteiro positivo. Pode utilizar uma consulta semelhante ao seguinte exemplo para procurar todos os VMs com as etiquetas e os seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Os VMs podem não ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMexcluídos são definidos na `External_ExcludeVMNames` variável na conta Automation para a qual a funcionalidade é implantada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar vMs, a conta Executar Como conta para a conta Automation deve ter permissões adequadas para o VM. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta de Automação no portal Azure. Selecione **Executar como contas** em **Definições** de Conta e selecione o executar apropriado Como conta.
* Se o VM está a ter problemas em começar ou em negociar, pode haver um problema sobre o próprio VM. Exemplos são uma atualização que está a ser aplicada quando o VM está a tentar desligar, um serviço que está pendurado, e muito mais. Vá ao seu recurso VM e verifique **registos** de atividade para ver se existem erros nos registos. Também pode tentar iniciar sessão no VM para ver se existem erros nos registos do evento. Para saber mais sobre a resolução de problemas do seu VM, consulte [máquinas virtuais Desacato Azure](../../virtual-machines/troubleshooting/index.yml).
* Verifique os [fluxos](../automation-runbook-execution.md#job-statuses) de trabalho para procurar erros. No portal, vá à sua conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: O meu livro de corridas personalizado não consegue iniciar ou parar os meus VMs

### <a name="issue"></a>Problema

Você é autor de um livro personalizado ou descarregado um da PowerShell Gallery, e não está funcionando corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para o fracasso. Vá à sua conta de Automação no portal Azure e selecione **Jobs** under **Process Automation**. Na página **Jobs,** procure empregos no seu livro de corridas para ver quaisquer falhas de emprego.

### <a name="resolution"></a>Resolução

É recomendável que:

* Utilize [VMs start/stop durante](../automation-solution-vm-management.md) o horário de folga para iniciar e parar VMs em Automação Azure. 
* Esteja ciente de que a Microsoft não suporta livros personalizados. Você pode encontrar uma resolução para o seu livro de corridas personalizado em problemas de livro de [corridas de Troubleshoot](runbooks.md). Verifique os [fluxos](../automation-runbook-execution.md#job-statuses) de trabalho para procurar erros. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

Os VMs que ativou para a funcionalidade não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Esta questão é causada por uma marcação incorreta nos VMs.

### <a name="resolution"></a>Resolução

Siga estes passos para garantir que a funcionalidade está ativada corretamente:

1. Certifique-se de que todos os VMs a serem iniciados ou parados têm uma `sequencestart` ou `sequencestop` etiqueta, dependendo da sua situação. Estas etiquetas precisam de um inteiro positivo como o valor. Os VMs são processados por ordem ascendente com base neste valor.
1. Certifique-se de que os grupos de recursos para os VMs a serem iniciados ou parados estão nas `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` ou variáveis, dependendo da sua situação.
1. Teste as suas alterações executando o **livro de SequencedStartStop_Parent** com o parâmetro definido para True para `WHATIF` pré-visualizar as suas alterações.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: Start/Stop VMs durante trabalho fora de horas falha com 403 erros proibidos

### <a name="issue"></a>Problema

Encontra empregos que falharam com um `403 forbidden` erro para Os VMs start/stop durante os livros de corridas fora de horas.

### <a name="cause"></a>Causa

Este problema pode ser causado por uma conta de Execução configurada ou expirada indevidamente. Pode também ser devido a permissões inadequadas aos recursos vm pela conta Run As.

### <a name="resolution"></a>Resolução

Para verificar se a sua conta Executar Como conta está corretamente configurada, vá à sua conta de Automação no portal Azure e selecione **Executar como contas** em **Definições de Conta**. Se uma conta Run As estiver configurada ou expirada, o estado mostra a condição.

Se a sua conta Executar As estiver mal configurada, elimine e recrie a sua conta Run As. Para mais informações, consulte [Manage Azure Automation Run As contas](../manage-runas-account.md).

Se o certificado estiver caducada para a sua conta Run As, siga os passos na [renovação do certificado auto-assinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

Se faltarem permissões, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Deve fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta de Automação no portal Azure. Selecione **Executar como contas** em **Definições**de Conta , e selecione o executar apropriado Como conta.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Cenário: O meu problema não está listado aqui

### <a name="issue"></a>Problema

Experimente um problema ou resultado inesperado quando utiliza VMs De Início/Paragem durante as horas de folga que não estão listadas nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados através da utilização de uma versão antiga e desatualizada da funcionalidade.

> [!NOTE]
> Os VMs Start/Stop durante o off-hours foram testados com os módulos Azure que são importados para a sua conta Automation quando implementa a funcionalidade em VMs. A funcionalidade atualmente não funciona com versões mais recentes do módulo Azure. Esta restrição apenas afeta a conta Automation que utiliza para executar VMs Start/Stop durante o horário de folga. Ainda pode utilizar versões mais recentes do módulo Azure nas suas outras contas De automação, conforme descrito nos [módulos Update Azure PowerShell](../automation-update-azure-modules.md).

### <a name="resolution"></a>Resolução

Para resolver muitos erros, remova e [atualize os VMs start/stop durante as horas de folga](../automation-solution-vm-management.md#update-the-feature). Também pode verificar os [fluxos](../automation-runbook-execution.md#job-statuses) de trabalho para procurar erros. 

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema aqui ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Azure Support liga a comunidade Azure a respostas, apoios e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter Suporte**.