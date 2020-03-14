---
title: Horários na Automação Azure
description: Os horários de automatização são usados para agendar livros de execução na Automatização Azure para iniciar automaticamente. Descreve como criar e gerir um horário para que possa iniciar automaticamente um livro de recortes num determinado momento ou num horário recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252665"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendar um runbook na Automatização do Azure

Para agendar um livro de recortes na Automatização Azure para começar num determinado horário, ligue-o a um ou mais horários. Um horário pode ser configurado para funcionar uma vez ou em horário sem contar horário ou diário para livros de execução no portal Azure. Também pode agendar para dias semanais, mensais, específicos da semana ou dias do mês, ou um determinado dia do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> Os horários não suportam atualmente configurações de DSC de Automação Azure.

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela seguinte são usados para criar e gerir horários com a PowerShell na Automação Azure. Eles enviam como parte do [módulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Obtém uma agenda. |
| [Programa de Automação New-AzureRm](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria um novo horário. |
| [Remover-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove um horário. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades para um horário existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Recupera os livros de execução programados. |
| [Registo-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um livro com um horário. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocia um livro de um horário. |

## <a name="creating-a-schedule"></a>Criar uma agenda

Pode criar uma nova programação para livros de execução no portal Azure ou com powerShell.

> [!NOTE]
> A Azure Automation utiliza os módulos mais recentes na sua conta Automation quando um novo trabalho programado é executado.  Para evitar afetar os seus livros de execução e os processos que automatizam, deve primeiro testar quaisquer livros de execução que tenham ligado horários a uma conta de Automação dedicada aos testes.  Isto valida que os seus livros de execução programados continuem a funcionar corretamente e, caso contrário, poderá resolver mais problemas e aplicar quaisquer alterações necessárias antes de migrar a versão atualizada do livro de reprodução para a produção.
> A sua conta Automation não obtém automaticamente quaisquer novas versões de módulos a menos que os tenha atualizado manualmente selecionando a opção [Módulos Update Azure](../automation-update-azure-modules.md) dos **Módulos**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova programação no portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Horários** sob a secção **Recursos Partilhados** à esquerda.
2. Clique em **Adicionar um horário** no topo da página.
3. No painel de **horárionovo,** digite um **nome** e opcionalmente uma **Descrição** para a nova programação.
4. Selecione se o horário funciona uma vez ou num horário recorrente selecionando **Uma ou** **Recorrente**. Se selecionar **Uma vez** especificado um tempo de **início,** e, em seguida, clique em **Criar**. Se selecionar **Recorrentes**, especificar um **tempo** de início e **para recur cada**um , selecione a frequência para a frequência com que pretende que o livro de corridas se repita - por **hora,** **dia,** **semana,** ou por **mês**.
    1. Se selecionar **semana,** é-lhe fornecida uma lista dos dias da semana para escolher. Selecione quantos dias quiser. A primeira execução da sua agenda acontecerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher um horário de fim de semana, escolha **sábado** e **domingo.**

       ![Definição de horário recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se selecionar **mês,** tem diferentes opções. Para a opção **ocorrências mensais,** selecione dias **de mês** ou **dias de semana**. Se escolher **os dias**mensais , é mostrado um calendário que lhe permite escolher os dias que quiser. Se escolher uma data como a 31ª que não ocorre no mês em curso, o horário não será executado. Se quiser que o horário seja executado no último dia, escolha **Sim** sob **Corrida no último dia do mês.** Se escolher **os dias da Semana,** o **Recur é** apresentada todas as opções. Escolha **Primeiro,** **Segundo,** **Terceiro,** **Quarto**ou **Último.** Finalmente escolha um dia para repetir.

       ![Horário mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **Criar**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Para criar uma nova programação com a PowerShell

Utiliza o [cmdlet New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) para criar horários. Especifica a hora de início do horário e a frequência que deve funcionar. Os exemplos que se seguem mostram como criar muitos cenários de horários diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar um horário de uma vez

Os comandos de amostra seguintecriam um horário único.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um horário recorrente

Os seguintes comandos de amostra mostram como criar um horário recorrente que funciona todos os dias às 13:00 pm durante um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar um horário semanal recorrente

Os comandos de amostra seguem mostram como criar um horário semanal que funciona apenas nos dias úteis.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um horário semanal recorrente para fins de semana

Os comandos de amostra seguem mostram como criar um horário semanal que decorre apenas aos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Criar um horário recorrente para os primeiros, 15 e últimos dias do mês

Os seguintes comandos de amostra mostram como criar um horário recorrente que decorre no dia 1, 15 e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ligando um horário a um livro de corridas

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um livro de execução tiver parâmetros, então pode fornecer-lhes valores. Deve fornecer valores para quaisquer parâmetros obrigatórios e pode fornecer valores para quaisquer parâmetros opcionais. Estes valores são usados cada vez que o livro de execução é iniciado por este horário. Pode anexar o mesmo livro de execução a outro horário e especificar diferentes valores de parâmetros.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para ligar um horário a um livro de corridas com o portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Runbooks** sob a secção **Automation** process o lado esquerdo.
2. Clique no nome do livro de execução para agendar.
3. Se o livro de execução não estiver atualmente ligado a um horário, então é-lhe oferecida a opção de criar um novo horário ou link para um horário existente.
4. Se o livro de execução tiver parâmetros, pode selecionar a opção Modificar as definições de **execução (Predefinido:Azure)** e o painel **de parâmetros** é apresentado onde pode introduzir a informação.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Para ligar um horário a um livro de corridas com a PowerShell

Pode utilizar o [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet para ligar um horário. Pode especificar os valores dos parâmetros do runbook através do parâmetro Parâmetros. Para obter mais informações sobre a especificação dos valores dos parâmetros, consulte [Iniciar um Livro de Execução em Automação Azure](../automation-starting-a-runbook.md).
Os comandos de amostra seguem mostrar como ligar um horário a um livro de execução utilizando um cmdlet do Gestor de Recursos Azure com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Agendar livros com mais frequência

O intervalo mais frequente que um horário na Automatização Azure pode ser configurado é de uma hora. Se necessitar de horários para executar com mais frequência do que isso, existem duas opções:

* Crie um [webhook](../automation-webhooks.md) para o livro de execução e use [as Aplicações Lógicas Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. As Aplicações Lógicas Azure proporcionam uma granularidade mais fina ao definir um horário.

* Crie quatro horários todos a partir de 15 minutos um do outro correndo uma vez a cada hora. Este cenário permite que o livro de corridas seja executado a cada 15 minutos com os diferentes horários.

## <a name="disabling-a-schedule"></a>Desativar um horário

Quando desativa um horário, qualquer livro de execução ligado a ele já não funciona nesse horário. Pode desativar manualmente um horário ou definir um prazo de validade para horários com frequência quando os criar. Uma vez atingido o prazo de validade, o horário é desativado.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desativar um horário do portal Azure

1. No portal Azure, a partir da sua conta Automation, selecione **Horários** sob a secção **Recursos Partilhados** à esquerda.
2. Clique no nome de um horário para abrir o painel de detalhes.
3. Alteração **ativada** a **Nº**.

> [!NOTE]
> Se quiser desativar um horário que tenha uma hora de início no passado, tem de alterar a data de início para uma hora no futuro antes de a guardar.

### <a name="to-disable-a-schedule-with-powershell"></a>Para desativar um horário com a PowerShell

Pode utilizar o [cmdlet Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para alterar as propriedades de um horário existente. Para desativar o horário, especifique **falso** para o parâmetro **IsEnabled.**

Os comandos de amostra seguem mostrar como desativar um horário para um livro de execução utilizando um cmdlet do Gestor de Recursos Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos Seguintes

* Para começar com livros de corridas na Azure Automation, veja [Starting a Runbook in Azure Automation](../automation-starting-a-runbook.md)

