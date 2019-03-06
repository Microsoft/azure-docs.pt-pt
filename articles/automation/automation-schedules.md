---
title: Agendas na automatização do Azure
description: As agendas da automatização são utilizadas para agendar runbooks na automatização do Azure para iniciar automaticamente. Descreve como criar e gerir uma agenda em para que pode iniciar automaticamente um runbook num determinado momento ou numa agenda periódica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6677733339babf45e7351e10d6de1c002a6ea93
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436515"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendar um runbook na Automatização do Azure

Para agendar um runbook na automatização do Azure para iniciar a uma hora especificada, ligue-o a uma ou mais agendas. Uma agenda pode ser configurada para a agenda de execução de uma vez ou de a ocorrer por hora ou diariamente para runbooks no portal do Azure. Também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> Agendas não suportam atualmente as configurações de DSC de automatização do Azure.

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela seguinte são utilizados para criar e gerir os agendamentos com o Windows PowerShell na automatização do Azure. Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Obtém uma agenda. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de uma agenda existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Obtém o runbooks agendados. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook com base numa agenda. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates um runbook a partir de uma agenda. |

## <a name="creating-a-schedule"></a>Criar uma agenda

Pode criar uma nova agenda para runbooks no portal do Azure ou o Windows PowerShell.

> [!NOTE]
> A automatização do Azure utiliza os módulos mais recentes na sua conta de automatização quando é executada uma nova tarefa agendada.  Para evitar ter impacto nos seus runbooks e os processos que automatizar, primeiro deve testar todos os runbooks que tenham agendamentos ligados com uma conta de automatização dedicada para fins de teste.  Isto valida a nossa agendada runbooks continuam a funcionar corretamente e se não, ainda pode resolver problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de automatização não obter automaticamente as novas versões de módulos, a menos que atualizou-los manualmente ao selecionar o [módulos do Azure de atualização](automation-update-azure-modules.md) opção a partir do **módulos**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure

1. No portal do Azure, da sua conta de automatização, selecione **agendas** na secção **recursos partilhados** à esquerda.
1. Clique em **adicionar uma agenda** na parte superior da página.
1. Sobre o **nova agenda** painel, escreva um **nome** e, opcionalmente, uma **Descrição** para a nova agenda.
1. Selecione se a agenda é executada uma vez, ou com base numa agenda recorrente, selecionando **uma vez** ou **periódico**. Se selecionou **uma vez** especificar um **hora de início**e, em seguida, clique em **criar**. Se selecionou **periódico**, especifique um **hora de início** e para **Repetir cada**, selecione a frequência para a frequência com que pretende que o runbook para repetir - por **hora**, **dia**, **semana**, ou pelo **mês**.
    1. Se selecionou **semana**, é fornecida uma lista dos dias da semana à sua escolha. Selecione tantos dias quantos quiser. A primeira execução da sua agenda acontecerá no primeiro dia selecionado após a hora de início.
    2. Se selecionou **mês**, tem opções diferentes. Para o **ocorrências mensais** opção, selecione **dias do mês** ou **dias da semana**. Se escolher **dias do mês** um calendário é mostrado que permite-lhe escolher como número de dias que quiser. Se escolher uma data como o 31st que não ocorre no mês atual, a agenda não será executada. Se pretender que a agenda para ser executada no último dia, escolha **Sim** sob **executar no último dia do mês**. Se escolher **dias da semana**, o **Repetir cada** opção é apresentada. Escolher **primeira**, **segundo**, **terceiro**, **quarta**, ou **última**. Por fim, escolha um dia para repetir em.
1. Quando terminar clique **criar**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova agenda com o Windows PowerShell

Utilizar o [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet para criar agendas. Especifique a hora de início para a agenda e a frequência que deve ser executado.

Comandos de exemplo seguintes mostram como criar um agendamento para o dia 15 e 30th de cada mês com um cmdlet do Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ligar uma agenda a um runbook

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um runbook tiver parâmetros, pode fornecer valores para os mesmos. Tem de fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para quaisquer parâmetros opcionais. Estes valores são utilizados sempre que o runbook é iniciado por esta agenda. Pode anexar o mesmo runbook ao agendamento outro e especifique os valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para ligar uma agenda a um runbook com o portal do Azure

1. No portal do Azure, da sua conta de automatização, selecione **Runbooks** na secção **automatização de processos** à esquerda.
2. Clique no nome do runbook a agendar.
3. Se o runbook não está atualmente associado a uma agenda, em seguida, é-lhe dada a opção para criar uma nova agenda ou ligar a uma agenda existente.
4. Se o runbook tiver parâmetros, pode selecionar a opção **modificar definições de execução (predefinição: Azure)** e o **parâmetros** painel é apresentado onde pode introduzir as informações em conformidade.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para ligar uma agenda a um runbook com o Windows PowerShell

Pode utilizar o [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet para ligar uma agenda. Pode especificar valores para parâmetros do runbook com o parâmetro de parâmetros. Para obter mais informações sobre como especificar os valores de parâmetros, consulte [a partir de um Runbook na automatização do Azure](automation-starting-a-runbook.md).
Comandos de exemplo seguintes mostram como ligar uma agenda a um runbook com um cmdlet do Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Agendar runbooks com mais frequência

O intervalo mais frequente que uma agenda na automatização do Azure pode ser configurada para é de uma hora. Se necessitar de agendas para executar com mais frequência do que isso, existem duas opções:

* Criar uma [webhook](automation-webhooks.md) para o runbook e a utilização [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) para chamar o webhook. O Azure Scheduler fornece granularidade mais refinada, ao definir uma agenda.

* Crie quatro agendas todos os começando em 15 minutos entre si em execução uma vez por hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as agendas diferentes.

## <a name="disabling-a-schedule"></a>Desativar uma agenda

Quando desativa um agendamento, qualquer runbook ligada ao mesmo já não é executado nessa agenda. Pode desactivar uma agenda ou definir um prazo de expiração para agendas com uma frequência quando criá-los manualmente. Quando for atingida a hora de expiração, a agenda está desativada.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desactivar uma agenda do portal do Azure

1. No portal do Azure, da sua conta de automatização, selecione **agendas** na secção **recursos partilhados** à esquerda.
1. Clique no nome de uma agenda para abrir o painel de detalhes.
1. Alteração **habilitado** ao **não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desactivar uma agenda com o Windows PowerShell

Pode utilizar o [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet para alterar as propriedades de uma agenda existente. Para desativar a agenda, especifique **false** para o **IsEnabled** parâmetro.

Comandos de exemplo seguintes mostram como desactivar uma agenda de um runbook com um cmdlet do Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks na automatização do Azure, veja [iniciar um Runbook na automatização do Azure](automation-starting-a-runbook.md)

