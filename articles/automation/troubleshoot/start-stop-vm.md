---
title: Resolução de problemas Starting and Stop VMs - Azure Automation
description: Este artigo fornece informações sobre a resolução de problemas Starting and Stop VMs in Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851393"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Resolução de problemas dos VMs de arranque/paragem durante a solução de horas de folga

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Cenário: A solução Start/Stop VM não funciona corretamente

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

As implantações podem falhar devido a uma das seguintes razões:

1. Já existe uma Conta de Automação com o mesmo nome na região selecionada.
2. Está em vigor uma política que proíbe a implantação da solução Start/Stop VMs.
3. Os `Microsoft.OperationsManagement` `Microsoft.Insights`tipos `Microsoft.Automation` de recursos não estão registados.
4. O seu espaço de trabalho Log Analytics tem um bloqueio.
5. Tem uma versão desatualizada dos módulos AzureRM ou a solução Start/Stop.

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais soluções para o seu problema ou locais para procurar:

1. As contas de automação têm de ser únicas dentro de uma região do Azure, mesmo que estejam em diferentes grupos de recursos. Verifique as suas Contas de Automação existentes na região alvo.
2. Uma política existente impede que seja implementado um recurso necessário para a solução Start/Stop VM ser implementada. Vá às suas atribuições políticas no portal Azure e verifique se tem uma atribuição de política que não permita a implantação deste recurso. Para saber mais sobre isso, consulte [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Para implementar a solução Start/Stop VM, a sua subscrição tem de ser registada nos seguintes espaços de nome de recursos Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Consulte, [Resolva erros no registo do fornecedor](../../azure-resource-manager/templates/error-register-resource-provider.md) de recursos para saber mais sobre erros ao registar fornecedores.
4. Se tiver um bloqueio no seu espaço de trabalho Log Analytics, vá ao seu espaço de trabalho no portal Azure e remova quaisquer fechaduras no recurso.
5. Se as resoluções acima não resolverem o seu problema, siga as instruções em ['Atualizar a Solução'](../automation-solution-vm-management.md#update-the-solution) para reutilizar a solução Iniciar/Parar.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Cenário: Todos os VMs não conseguem iniciar/parar

### <a name="issue"></a>Problema

Configurou a solução Start/Stop VM, mas não inicia nem para todos os VMs configurados.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

1. Um horário não está configurado corretamente
2. A conta RunAs pode não ser configurada corretamente
3. Um livro de corridas pode ter tido erros
4. Os VMs podem ter sido excluídos

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais soluções para o seu problema ou locais para procurar:

* Verifique se configura bem um horário para a solução Start/Stop VM. Para aprender a configurar um horário, consulte o artigo [Agendas.](../automation-schedules.md)

* Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. No portal, vá à sua Conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.** A partir da página **Jobs** procure empregos de um dos seguintes livros:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifique se a sua [Conta RunAs](../manage-runas-account.md) tem permissões adequadas para os VMs que está a tentar iniciar ou parar. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela Conta Run As. Pode recuperar este valor indo para a sua Conta de Automação no portal Azure, selecionando **Executar como contas** em **Definições** de Conta e clicando na conta de Execução adequada.

* Os VMs não podem ser iniciados ou parados se estiverem a ser explicitamente excluídos. VMs excluídos no conjunto da **variável External_ExcludeVMNames** na Conta de Automação para a solução para a a que a solução está implantada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Cenário: Alguns dos meus VMs não conseguem começar ou parar

### <a name="issue"></a>Problema

Configurou a solução Start/Stop VM, mas não inicia nem detém alguns dos VMs configurados.

### <a name="cause"></a>Causa

Este erro pode ser causado por uma das seguintes razões:

1. Se utilizar o cenário de sequência, uma etiqueta pode estar em falta ou incorreta
2. O VM pode ser excluído
3. A conta RunAs pode não ter permissões suficientes no VM
4. O VM pode ter algo que o impediu de começar ou parar

### <a name="resolution"></a>Resolução

Reveja a seguinte lista para potenciais soluções para o seu problema ou locais para procurar:

* Ao utilizar o cenário de [sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) do VM Iniciar/Parar durante o horário de folga, tem de se certificar de que cada VM que pretende iniciar ou parar tem a etiqueta correta. Certifique-se de que os VMs `sequencestart` que deseja começar têm a `sequencestop` etiqueta e os VMs que pretende parar têm a etiqueta. Ambas as etiquetas requerem um valor inteiro positivo. Pode utilizar uma consulta semelhante ao seguinte exemplo para procurar todos os VMs com as etiquetas e os seus valores.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Os VMs não podem ser iniciados ou parados se estiverem a ser explicitamente excluídos. VMs excluídos no conjunto da **variável External_ExcludeVMNames** na Conta de Automação para a solução para a a que a solução está implantada. O exemplo que se segue mostra como pode consultar esse valor com a PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar e parar os VMs, a conta RunAs para a conta Automation deve ter permissões adequadas para o VM. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela Conta Run As. Pode recuperar este valor indo para a sua Conta de Automação no portal Azure, selecionando **Executar como contas** em **Definições** de Conta e clicando na conta de Execução adequada.

* Se o VM está com problemas de partida ou de locação, este comportamento pode ser causado por um problema no próprio VM. Alguns exemplos ou potenciais problemas são, uma atualização está sendo aplicada ao tentar desligar, um serviço fica pendurado, e muito mais). Navegue para o seu recurso VM e verifique os **Registos de Atividade** para ver se existem erros nos registos. Também pode tentar iniciar sessão no VM para ver se existem erros nos registos do Evento. Para saber mais sobre a resolução de problemas do seu VM, consulte [máquinas virtuais Desacato Azure](../../virtual-machines/troubleshooting/index.yml)

* Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. No portal, vá à sua Conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Cenário: O meu livro de corridas personalizado não consegue iniciar ou parar os meus VMs

### <a name="issue"></a>Problema

Você é autor de um livro personalizado ou descarregou um da PowerShell Gallery e não está funcionando corretamente.

### <a name="cause"></a>Causa

A causa do fracasso pode ser uma de muitas coisas. Vá à sua Conta de Automação no portal Azure e selecione **Jobs** no âmbito **da Automação de Processos.** Na página **Jobs,** procure empregos no seu livro de corridas para ver quaisquer falhas de emprego.

### <a name="resolution"></a>Resolução

É aconselhável utilizar os [VMs start/stop durante](../automation-solution-vm-management.md) a solução de horas de folga para iniciar e parar VMs em Automação Azure. Esta solução é da autoria da Microsoft. Os livros de execução personalizados não são suportados pela Microsoft. Você pode encontrar uma solução para o seu livro de corridas personalizado, visitando o artigo de resolução de [problemas](runbooks.md) do livro de corridas. Este artigo fornece orientação geral e resolução de problemas para livros de todos os tipos. Verifique os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. No portal, vá à sua Conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.**

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Cenário: VMs não iniciam ou param na sequência correta

### <a name="issue"></a>Problema

Os VMs que configuraste na solução não iniciam ou param na sequência correta.

### <a name="cause"></a>Causa

Isto é causado por uma marcação incorreta nos VMs.

### <a name="resolution"></a>Resolução

Tome os seguintes passos para garantir que a solução está corretamente configurada.

1. Certifique-se de que todos os `sequencestart` VMs devem ser iniciados ou parados têm uma ou `sequencestop` etiqueta, dependendo da sua situação. Estas etiquetas precisam de um inteiro positivo como o valor. Os VMs são processados por ordem ascendente com base neste valor.
2. Certifique-se de que os grupos de recursos para `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` os VMs serem iniciados ou parados estão nas ou variáveis, dependendo da sua situação.
3. Teste as suas alterações executando o `SequencedStartStop_Parent` livro de execução com o parâmetro WHATIF definido para True para pré-visualizar as suas alterações.

Para obter instruções mais detalhadas e adicionais sobre como utilizar a solução para iniciar e parar vMs em sequência, consulte [Os VMs iniciar/parar em sequência](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Cenário: Trabalho de Início/Stop VM falha com 403 estatuto proibido

### <a name="issue"></a>Problema

Encontra empregos que falharam com um `403 forbidden` erro para os VMs de arranque/paragem durante os livros de solução off hours.

### <a name="cause"></a>Causa

Este problema pode ser causado por uma conta de Execução indevidamente configurada ou expirada. Pode também ser devido a permissões inadequadas aos recursos VM pela Conta de Contas de Automação.

### <a name="resolution"></a>Resolução

Para verificar a sua conta Executar Como a conta está devidamente configurada, vá à sua Conta de Automação no portal Azure e selecione **Executar como contas** em **Definições de Conta**. Aqui você verá o estado da sua execução como contas, se uma Conta Run As estiver configurada ou expirada, o estado mostrará isso.

Se a sua conta Executar As estiver mal configurada, deve eliminar e recriar a sua conta 'Executar As'. Ver Gerir a Execução de [Automação Azure Como contas](../manage-runas-account.md).

Se o certificado estiver caducada para a sua Conta Run As, siga os passos listados na [renovação do certificado auto-assinado](../manage-runas-account.md#cert-renewal) para renovar o certificado.

O problema pode ser causado por permissões em falta. Para saber como verificar as permissões num recurso, consulte [Quickstart: Ver as funções atribuídas a um utilizador utilizando o portal Azure](../../role-based-access-control/check-access.md). Terá de fornecer o ID de inscrição para o principal de serviço utilizado pela Conta Run As. Pode recuperar este valor indo para a sua Conta de Automação no portal Azure, selecionando **Executar como contas** em **Definições** de Conta e clicando na conta de Execução adequada.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Cenário: O meu problema não está listado acima

### <a name="issue"></a>Problema

Experimente um problema ou resultado inesperado ao utilizar os VMs Start/Stop durante a solução off-hours que não está listada nesta página.

### <a name="cause"></a>Causa

Muitas vezes os erros podem ser causados através da utilização de uma versão antiga e desatualizada da solução.

> [!NOTE]
> Os VMs Start/Stop durante a solução off-hours foram testados com os módulos Azure que são importados para a sua Conta de Automação quando implementa a solução. A tualmente, a solução não funciona com versões mais recentes do módulo Azure. Isto só afeta a Conta de Automação que utiliza para executar os VMs start/stop durante a solução off-hours. Ainda pode utilizar versões mais recentes do módulo Azure nas suas outras Contas de Automação, conforme descrito em [Como atualizar módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md)

### <a name="resolution"></a>Resolução

Para resolver muitos erros, é aconselhável remover e atualizar a solução. Para aprender a atualizar a solução, consulte [Atualizar os VMs iniciar/parar durante a solução fora](../automation-solution-vm-management.md#update-the-solution)de horas . Além disso, pode verificar os [fluxos](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) de trabalho para procurar erros. No portal, vá à sua Conta de Automação e selecione **Jobs** no âmbito **da Automatização de Processos.**

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
