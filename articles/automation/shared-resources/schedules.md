---
title: Agendas na automação do Azure
description: Os agendamentos de automação são usados para agendar runbooks na automação do Azure para serem iniciados automaticamente. Descreve como criar e gerenciar uma agenda no para que você possa iniciar automaticamente um runbook em um horário específico ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8daa87eca74570f5b1fdf1537b83dae60d292128
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849467"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendar um runbook na Automatização do Azure

Para agendar um runbook na automação do Azure para iniciar em um horário especificado, vincule-o a um ou mais agendamentos. Uma agenda pode ser configurada para ser executada uma vez ou em um agendamento recorrente por hora ou diário para runbooks no portal do Azure. Você também pode agendá-los para dias semanais, mensais, específicos da semana ou dias do mês ou um dia específico do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> No momento, os agendamentos não dão suporte a configurações de DSC de Automação do Azure.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar agendas com o PowerShell na automação do Azure. Eles são fornecidos como parte do [módulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Obtém uma agenda. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de um agendamento existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Recupera runbooks agendados. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook a uma agenda. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocia um runbook de um agendamento. |

## <a name="creating-a-schedule"></a>Criar uma agenda

Você pode criar um novo agendamento para runbooks no portal do Azure ou com o PowerShell.

> [!NOTE]
> A automação do Azure usa os módulos mais recentes em sua conta de automação quando um novo trabalho agendado é executado.  Para evitar afetar seus runbooks e os processos que eles automatizam, primeiro você deve testar todos os runbooks que têm agendas vinculadas com uma conta de automação dedicada para teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso contrário, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de automação não obtém automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar módulos do Azure](../automation-update-azure-modules.md) nos **módulos**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **agendas** na seção **recursos compartilhados** à esquerda.
2. Clique em **Adicionar um agendamento** na parte superior da página.
3. No painel **novo agendamento** , digite um **nome** e, opcionalmente, uma **Descrição** para o novo agendamento.
4. Selecione se a agenda é executada uma vez ou em um agendamento recorrente selecionando **uma vez** ou **repetindo**. Se você selecionar **uma vez** , especifique uma **hora de início**e, em seguida, clique em **criar**. Se você selecionar **recorrência**, especifique uma **hora de início** e, para **repetir a cada**, selecione a frequência com que deseja que o runbook seja repetido-por **hora**, **dia**, **semana**ou **mês**.
    1. Se você selecionar **semana**, será fornecida uma lista dos dias da semana para sua escolha. Selecione quantos dias desejar. A primeira execução da sua agenda ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher uma agenda de fim de semana, escolha **sábado** e **domingo**.

       ![Definindo agendamento recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se você selecionar **mês**, terá opções diferentes. Para a opção **ocorrências mensais** , selecione **dias do mês** ou **dias da semana**. Se você escolher **dias do mês**, será mostrado um calendário que permite que você escolha quantos dias desejar. Se você escolher uma data como o dia 31 que não ocorre no mês atual, a agenda não será executada. Se você quiser que a agenda seja executada no último dia, escolha **Sim** em **executar no último dia do mês**. Se você escolher **dias da semana**, a opção **repetir a cada** será apresentada. Escolha **primeiro**, **segundo**, **terceiro**, **quarto**ou **último**. Por fim, escolha um dia para repetir.

       ![Agendamento mensal no primeiro, décimo-quinto e no último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **criar**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Para criar uma nova agenda com o PowerShell

Use o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) para criar agendas. Você especifica a hora de início para o agendamento e a frequência que ele deve executar. Os exemplos a seguir mostram como criar vários cenários de agendamento diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar um agendamento de uma vez

Os comandos de exemplo a seguir criam um agendamento de uma vez.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um agendamento recorrente

Os comandos de exemplo a seguir mostram como criar uma agenda recorrente que é executada todos os dias em 1:13h por um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar uma agenda recorrente semanal

Os comandos de exemplo a seguir mostram como criar um agendamento semanal que é executado somente em dias da semana.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um agendamento recorrente semanal para fins de semana

Os comandos de exemplo a seguir mostram como criar um agendamento semanal que é executado somente em fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Criar um agendamento recorrente para o primeiro, o 15º e o último dia do mês

Os comandos de exemplo a seguir mostram como criar uma agenda recorrente que é executada no 1º, no 15º e no último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um runbook tiver parâmetros, você poderá fornecer valores para eles. Você deve fornecer valores para quaisquer parâmetros obrigatórios e pode fornecer valores para quaisquer parâmetros opcionais. Esses valores são usados cada vez que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular um agendamento a um runbook com o portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **Runbooks** na seção **automação do processo** à esquerda.
2. Clique no nome do runbook a agendar.
3. Se o runbook não estiver atualmente vinculado a um agendamento, você terá a opção de criar uma nova agenda ou vincular a uma agenda existente.
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (padrão: Azure)** e o painel de **parâmetros** será apresentado onde você pode inserir as informações.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Para vincular um agendamento a um runbook com o PowerShell

Você pode usar o cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) para vincular uma agenda. Pode especificar os valores dos parâmetros do runbook através do parâmetro Parâmetros. Para obter mais informações sobre como especificar valores de parâmetro, consulte [iniciando um runbook na automação do Azure](../automation-starting-a-runbook.md).
Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook usando um cmdlet Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Agendando runbooks com mais frequência

O intervalo mais frequente que uma agenda na automação do Azure pode ser configurado por é de uma hora. Se você precisar que os agendamentos sejam executados com mais frequência do que isso, há duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use o [Agendador do Azure](../../scheduler/scheduler-get-started-portal.md) para chamar o webhook. O Agendador do Azure fornece granularidade mais refinada ao definir uma agenda.

* Crie quatro agendas, começando dentro de 15 minutos, em execução uma vez a cada hora. Esse cenário permite que o runbook seja executado a cada 15 minutos com os diferentes agendamentos.

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculado a ela não é mais executado nesse agendamento. Você pode desabilitar manualmente um agendamento ou definir um tempo de expiração para agendamentos com uma frequência ao criá-los. Depois que o tempo de expiração for atingido, o agendamento será desabilitado.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desabilitar uma agenda do portal do Azure

1. Na portal do Azure, na sua conta de automação, selecione **agendas** na seção **recursos compartilhados** à esquerda.
2. Clique no nome de um agendamento para abrir o painel de detalhes.
3. Altere **habilitado** para **não**.

> [!NOTE]
> Se desejar desabilitar uma agenda que tenha uma hora de início no passado, você deverá alterar a data de início para uma hora no futuro antes de salvá-la.

### <a name="to-disable-a-schedule-with-powershell"></a>Para desabilitar uma agenda com o PowerShell

Você pode usar o cmdlet [set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para alterar as propriedades de uma agenda existente. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled** .

Os comandos de exemplo a seguir mostram como desabilitar uma agenda para um runbook usando um cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos seguintes

* Para começar a usar runbooks na automação do Azure, consulte [Iniciar um runbook na automação do Azure](../automation-starting-a-runbook.md)

