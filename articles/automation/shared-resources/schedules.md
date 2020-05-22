---
title: Gerir horários na Automação Azure
description: Este artigo diz como criar e trabalhar com um horário na Automação Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 557dc3ad48f8f21d8898e2beb5d940d66058e90c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744985"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerir horários na Automação Azure

Para agendar um livro de recortes na Automatização Azure para começar num determinado horário, ligue-o a um ou mais horários. Um horário pode ser configurado para funcionar uma vez ou em horário sinuoso ou diário para livros de execução no portal Azure. Também pode agendar para dias semanais, mensais, específicos da semana ou dias do mês, ou um determinado dia do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> A Azure Automation suporta o Horário de verão e programa-o adequadamente para operações de automação.

> [!NOTE]
> Os horários atualmente não estão ativados para configurações de DSC de Automação Azure.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlets PowerShell usados para aceder a horários

Os cmdlets na tabela seguinte criam e gerem os horários de Automação com a PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules) 

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera um horário. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera os livros de execução programados. |
| [Programação New-AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Cria um novo horário. |
| [Registo-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa um livro com um horário. |
| [Remover-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Remove um horário. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Define as propriedades para um horário existente. |
| [Livro deRunções Não Registrados-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissocia um livro de um horário. |

## <a name="create-a-schedule"></a>Criar um horário

Pode criar uma nova programação para os seus livros de execução no portal Azure ou com powerShell. Para evitar afetar os seus livros de execução e os processos que automatizam, deve primeiro testar quaisquer livros de execução que tenham ligado horários a uma conta de Automação dedicada aos testes. Um teste valida que os seus livros de execução programados continuam a funcionar corretamente. Se vir um problema, pode resolver problemas e aplicar quaisquer alterações necessárias antes de migrar a versão atualizada do livro de corridas para a produção.

> [!NOTE]
> A sua conta Automation não obtém automaticamente quaisquer novas versões de módulos a menos que os tenha atualizado manualmente selecionando a opção de [módulos Update Azure](../automation-update-azure-modules.md) a partir de **Módulos**. A Azure Automation utiliza os módulos mais recentes na sua conta Automation quando um novo trabalho programado é executado. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Criar uma nova programação no portal Azure

1. No portal Azure, a partir da sua conta Automation, selecione **Horários** sob a secção **Recursos Partilhados** à esquerda.
1. Selecione **Adicionar um horário** no topo da página.
1. No **painel de horários novo,** insira um nome e insira opcionalmente uma descrição para a nova programação.
1. Selecione se o horário funciona uma vez ou num horário recorrente selecionando **Uma ou** **Recorrente**. Se selecionar **Uma vez,** especifique uma hora de início e, em seguida, selecione **Criar**. Se selecionar **Recorrente,** especifique uma hora de início. Para **recurar cada um**, selecione quantas vezes pretende que o livro de corridas se repita. Selecione por hora, dia, semana ou mês.
    1. Se selecionar **a Week**, os dias da semana são apresentados para que possa escolher. Selecione quantos dias quiser. A primeira execução da sua agenda acontecerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher um horário de fim de semana, selecione sábado e domingo.
    
       ![Definição de horário recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se selecionar **o Mês,** tem diferentes opções. Para a opção **ocorrências mensais,** selecione dias **de mês** ou **dias de semana**. Se selecionar **os dias do Mês**, aparece um calendário para que possa escolher os dias que quiser. Se escolher uma data como a 31ª que não ocorre no mês em curso, o horário não será executado. Se quiser que o horário seja executado no último dia, selecione **Yes** under **Run no último dia do mês**. Se selecionar **os dias da Semana,** aparece todas **as** opções. Escolha **Primeiro,** **Segundo,** **Terceiro,** **Quarto**ou **Último.** Finalmente, escolha um dia para repetir.

       ![Horário mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

1. Quando terminar, selecione **Criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Crie um novo horário com a PowerShell

Utilize o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) para criar horários. Especifica a hora de início do horário e a frequência que deve funcionar. Os exemplos que se seguem mostram como criar muitos cenários de horários diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar um horário único

O exemplo seguinte cria um horário único.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um horário recorrente

O exemplo que se segue mostra como criar um horário recorrente que funciona todos os dias às 13:00 horas durante um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar um horário semanal recorrente

O exemplo que se segue mostra como criar um horário semanal que funciona apenas nos dias úteis.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um horário semanal recorrente para fins de semana

O exemplo que se segue mostra como criar um horário semanal que funciona apenas aos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Criar um horário recorrente para o primeiro, décimo quinto e último dia do mês

O exemplo que se segue mostra como criar um horário recorrente que decorre no primeiro, décimo quinto e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Ligue um horário a um livro de corridas

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um livro tiver parâmetros, pode fornecer-lhes valores. Deve fornecer valores para quaisquer parâmetros obrigatórios, e também pode fornecer valores para quaisquer parâmetros opcionais. Estes valores são usados cada vez que o livro de execução é iniciado por este horário. Pode anexar o mesmo livro de execução a outro horário e especificar diferentes valores de parâmetros.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ligue um horário a um livro de corridas com o portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Runbooks** em **Process Automation**.
1. Selecione o nome do livro de execução para agendar.
1. Se o livro de execução não estiver atualmente ligado a um horário, é-lhe oferecida a opção de criar um novo horário ou link para um horário existente.
1. Se o livro de execução tiver parâmetros, pode selecionar a opção Modificar as definições de **execução (Padrão:Azure)** e aparecer o painel de **parâmetros.** Pode introduzir informações de parâmetros aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ligue um horário a um livro de corridas com a PowerShell

Utilize o [Cmdlet Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) para ligar um horário. Pode especificar os valores dos parâmetros do runbook através do parâmetro Parâmetros . Para obter mais informações sobre como especificar os valores dos parâmetros, consulte [Iniciar um Livro de Execução em Automação Azure](../automation-starting-a-runbook.md).
O exemplo seguinte mostra como ligar um horário a um livro de execução utilizando um cmdlet do Gestor de Recursos Azure com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Agendar livros de execução para executar com mais frequência

O intervalo mais frequente para o qual um horário na Automatização Azure pode ser configurado é de uma hora. Se necessitar que os horários sejam executados com mais frequência do que isso, existem duas opções:

* Crie um [webhook](../automation-webhooks.md) para o livro de execução e use [as Aplicações Lógicas Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. As Aplicações Lógicas Azure proporcionam uma granularidade mais fina para definir um horário.

* Crie quatro horários que começam dentro de 15 minutos um do outro e que corram uma vez a cada hora. Este cenário permite que o livro de corridas seja executado a cada 15 minutos com os diferentes horários.

## <a name="disable-a-schedule"></a>Desativar um horário

Quando desativa um horário, qualquer livro de execução ligado a ele já não funciona nesse horário. Pode desativar manualmente um horário ou definir um prazo de validade para horários com frequência quando os criar. Quando o prazo de validade é atingido, o horário é desativado.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desativar um horário do portal Azure

1. Na sua conta de Automação, selecione **Horários** em **Recursos Partilhados**.
1. Selecione o nome de um horário para abrir o painel de detalhes.
1. Alteração **ativada** a **Nº**.

> [!NOTE]
> Se quiser desativar um horário que tenha uma hora de início no passado, tem de alterar a data de início para uma hora no futuro antes de a guardar.

### <a name="disable-a-schedule-with-powershell"></a>Desative um horário com a PowerShell

Utilize o [cmdlet Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) para alterar as propriedades de um horário existente. Para desativar o horário, especifique falso para o `IsEnabled` parâmetro.

O exemplo seguinte mostra como desativar um horário para um livro de execução utilizando um cmdlet do Gestor de Recursos Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Remover um horário

Quando estiver pronto para remover os seus horários, pode utilizar o portal Azure ou o PowerShell. Lembre-se de que só pode remover um horário que tenha sido desativado como descrito na secção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remova um horário usando o portal Azure

1. Na sua conta de Automação, selecione **Horários** em **Recursos Partilhados**.
2. Clique no nome de um horário para abrir o painel de detalhes.
3. Clique em **Apagar**.

### <a name="remove-a-schedule-with-powershell"></a>Remova um horário com powerShell

Pode utilizar o `Remove-AzAutomationSchedule` cmdlet como mostrado abaixo para eliminar um horário existente. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets usados para aceder aos horários, consulte [Gerir módulos em Automação Azure](modules.md).
* Para obter informações gerais sobre livros de execução, consulte a execução do Livro de [Corridas na Automação Azure.](../automation-runbook-execution.md)