---
title: Resolução de problemas dos VMs de arranque/paragem durante a solução de horas de folga
description: Este artigo fornece informações sobre a resolução de problemas da solução Start/Stop VM.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679152"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Resolução de problemas dos VMs de arranque/paragem durante a solução de horas de folga

Este artigo fornece informações sobre problemas de resolução de problemas que surgem enquanto trabalham com VMs Start/Stop durante a solução de horas de folga.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: Os VMs de início/paragem durante o horário de folga não conseguem ser corretamente implantados

### <a name="issue"></a>Problema

Ao implementar os [VMs start/stop durante a solução off hours,](../automation-solution-vm-management.md)recebe um dos seguintes erros:

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

1. Já existe uma conta de Automação com o mesmo nome na região selecionada.
2. Uma política está a permitir a implantação dos VMs de início/paragem durante a solução de horas de folga.
3. O `Microsoft.OperationsManagement` `Microsoft.Insights`tipo `Microsoft.Automation` de recurso não está registado.
4. O seu espaço de trabalho Log Analytics está bloqueado.
5. Tem uma versão desatualizada dos módulos AzureRM ou dos VMs Start/Stop durante a solução de horas de folga.

### <a name="resolution"></a>Resolução

Reveja as seguintes correções para potenciais soluções para o seu problema:

* As contas de automação têm de ser únicas dentro de uma região do Azure, mesmo que estejam em diferentes grupos de recursos. Verifique as suas contas de Automação existentes na região alvo.
* Uma política existente impede que seja necessário um recurso necessário para que os VMs de início/paragem durante o horário de folga sejam implantados. Vá às suas atribuições políticas no portal Azure e verifique se tem uma atribuição de política que não permita a implantação deste recurso. Para saber mais sobre isso, consulte [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Para implementar a solução Start/Stop VMs, a sua subscrição tem de ser registada nos seguintes espaços de nome de recursos Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Consulte [a Resolução de erros para](../../azure-resource-manager/templates/error-register-resource-provider.md) o registo do fornecedor de recursos para saber mais sobre erros ao registar fornecedores.
* Se tiver um bloqueio no seu espaço de trabalho Log Analytics, vá ao seu espaço de trabalho no portal Azure e remova quaisquer fechaduras no recurso.
* Se as resoluções acima não resolverem o seu problema, siga as instruções em ['Atualizar a Solução'](../automation-solution-vm-management.md#update-the-solution) para reutilizar a solução Iniciar/Parar.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todos os VMs não conseguem iniciar ou parar

### <a name="issue"></a>Problema

Configurou os VMs de início/paragem durante a solução de horas de folga, mas não inicia nem para todos os VMs.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

1. Um horário não está configurado corretamente.
2. A conta Executar Como pode não ser configurada corretamente.
3. Um livro de corridas pode ter tido erros.
4. Os VMs podem ter sido excluídos.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para soluções potenciais para o seu problema:

* Verifique se configura bem um horário para os VMs iniciar/parar durante a solução de horas de folga. Para aprender a configurar um horário, consulte o artigo [Agendas.](../automation-schedules.md)

* Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. Procure empregos num dos seguintes livros:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Verifique se a sua [conta Run As](../manage-runas-account.md) tem permissões adequadas para os VMs que está a tentar iniciar ou parar. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta De automação no portal Azure, selecionando **Executar como contas** em Definições de **Conta,** e clicando na conta De executar adequada.

* Os VMs não podem ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMexcluídos são `External_ExcludeVMNames` definidos na variável na conta Automation para a qual a solução é implementada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Alguns dos meus VMs não conseguem começar ou parar

### <a name="issue"></a>Problema

Configurou os VMs de início/paragem durante a solução de horas de folga, mas não inicia nem para alguns dos VMs configurados.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

1. No cenário de sequência, uma etiqueta pode estar em falta ou incorreta.
2. O VM pode ser excluído.
3. A conta Run As pode não ter permissões suficientes no VM.
4. O VM pode ter um problema que o impediu de começar ou parar.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais soluções para o seu problema ou locais para procurar:

* Ao utilizar o cenário de [sequência](../automation-solution-vm-management.md) dos VMs iniciar/parar durante a solução de horas de folga, deve certificar-se de que cada VM que pretende iniciar ou parar tem a etiqueta correta. Certifique-se de que os VMs `sequencestart` que deseja começar têm a `sequencestop` etiqueta e os VMs que pretende parar têm a etiqueta. Ambas as etiquetas requerem um valor inteiro positivo. Pode utilizar uma consulta semelhante ao seguinte exemplo para procurar todos os VMs com as etiquetas e os seus valores.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Os VMs podem não ser iniciados ou parados se estiverem a ser explicitamente excluídos. Os VMexcluídos são `External_ExcludeVMNames` definidos na variável na conta Automation para a qual a solução é implementada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar vMs, a conta Executar Como conta para a conta Automation deve ter permissões adequadas para o VM. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta De automação no portal Azure, selecionando **Executar como contas** em **Definições** de Conta e clicando na conta de Executar adequada.

* Se o VM está a ter problemas em começar ou em negociar, pode haver um problema sobre o próprio VM. Por exemplo, está a ser aplicada uma atualização quando o VM está a tentar desligar, um serviço fica pendurado, e muito mais. Navegue para o seu recurso VM e verifique os **Registos de Atividade** para ver se existem erros nos registos. Também pode tentar entrar no VM para ver se existem erros nos registos do evento. Para saber mais sobre a resolução de problemas do seu VM, consulte [máquinas virtuais Desacato Azure](../../virtual-machines/troubleshooting/index.yml)

* Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. No portal, vá à sua conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: O meu livro de corridas personalizado não consegue iniciar ou parar os meus VMs

### <a name="issue"></a>Problema

Você é autor de um livro personalizado ou descarregou um da PowerShell Gallery e não está funcionando corretamente.

### <a name="cause"></a>Causa

Pode haver muitas causas para o fracasso. Vá à sua conta de Automação no portal Azure e selecione **Jobs** no âmbito **da Automação**de Processos. Na página Jobs, procure empregos no seu livro de corridas para ver quaisquer falhas de emprego.

### <a name="resolution"></a>Resolução

É recomendado para:

* Utilize os [VMs de arranque/paragem durante](../automation-solution-vm-management.md) o horário de folga para iniciar e parar VMs na Automação Azure. Esta solução é da autoria da Microsoft. 

* Esteja ciente de que a Microsoft não suporta livros personalizados. Você pode encontrar uma solução para o seu livro de corridas personalizado de resolução de problemas de livro de [corridas](runbooks.md). Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

Os VMs que configuraste na solução não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Esta questão é causada por uma marcação incorreta nos VMs.

### <a name="resolution"></a>Resolução

Tome os seguintes passos para garantir que a solução está corretamente configurada.

1. Certifique-se de que todos os `sequencestart` VMs devem ser iniciados ou parados têm uma ou `sequencestop` etiqueta, dependendo da sua situação. Estas etiquetas precisam de um inteiro positivo como o valor. Os VMs são processados por ordem ascendente com base neste valor.
2. Certifique-se de que os grupos de recursos para `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` os VMs serem iniciados ou parados estão nas ou variáveis, dependendo da sua situação.
3. Teste as suas alterações executando o `SequencedStartStop_Parent` livro de execução com o `WHATIF` parâmetro definido para True para pré-visualizar as suas alterações.
4. Para mais informações sobre a utilização da solução para iniciar e parar os VMs em sequência, consulte [Os VMs iniciar/parar em sequência](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Cenário: Start/Stop VMs durante horas de folga falha com 403 erros proibidos

### <a name="issue"></a>Problema

Encontra empregos que falharam com um `403 forbidden` erro para Os VMs Start/Stop durante os livros de solução off hours.

### <a name="cause"></a>Causa

Este problema pode ser causado por uma conta de Execução configurada ou expirada indevidamente. Pode também ser devido a permissões inadequadas aos recursos vm pela conta Run As.

### <a name="resolution"></a>Resolução

Para verificar se a sua conta Executar Como conta está corretamente configurada, vá à sua conta de Automação no portal Azure e selecione **Executar como contas** em **Definições de Conta**. Se uma conta Run As estiver configurada ou expirada, o estado mostra a condição.

Se a sua conta Executar As estiver mal configurada, deve eliminar e recriar a sua conta 'Executar As'. Ver Gerir a Execução de [Automação Azure Como contas](../manage-runas-account.md).

Se o certificado estiver caducada para a sua conta Run As, consulte as etapas na [renovação do certificado auto-assinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

Se faltarem permissões, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Deve fornecer o ID de inscrição para o principal de serviço utilizado pela conta Run As. Pode recuperar este valor indo para a sua conta De automação no portal Azure, selecionando **Executar como contas** em Definições de **Conta,** e clicando na conta De executar adequada.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Cenário: O meu problema não está listado acima

### <a name="issue"></a>Problema

Experimente um problema ou resultado inesperado ao utilizar os VMs Start/Stop durante a solução de horas de folga que não está listada nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados através da utilização de uma versão antiga e desatualizada da solução.

> [!NOTE]
> Os VMs Start/Stop durante o horário de folga foram testados com os módulos Azure que são importados para a sua conta Automation quando implementa a solução. A tualmente, a solução não funciona com versões mais recentes do módulo Azure. Isto só afeta a conta Automation que utiliza para executar os VMs Start/Stop durante a solução de horas de folga. Ainda pode utilizar versões mais recentes do módulo Azure nas suas outras contas de Automação, conforme descrito em [Como atualizar módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md)

### <a name="resolution"></a>Resolução

Para resolver muitos erros, é aconselhável remover e [atualizar os VMs Iniciar/Parar durante a solução de horas de folga](../automation-solution-vm-management.md#update-the-solution). Além disso, pode verificar os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. 

## <a name="next-steps"></a>Passos seguintes

Se não vir o seu problema acima ou não conseguir resolver o seu problema, experimente um dos seguintes canais para obter apoio adicional:

* Obtenha respostas de especialistas do Azure através dos [Fóruns Azure.](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport)a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Arquiva um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.