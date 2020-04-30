---
title: Gerir horários na Automação Azure
description: Os horários de automatização são usados para agendar livros de execução na Automatização Azure para iniciar automaticamente. Descreve como criar e gerir um horário para que possa iniciar automaticamente um livro de recortes num determinado momento ou num horário recorrente.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 17d46ddb738abc812ebfc458e25c745b84a29c2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136605"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerir horários na Automação Azure

Para agendar um livro de recortes na Automatização Azure para começar num determinado horário, ligue-o a um ou mais horários. Um horário pode ser configurado para funcionar uma vez ou em horário sem contar horário ou diário para livros de execução no portal Azure. Também pode agendar para dias semanais, mensais, específicos da semana ou dias do mês, ou um determinado dia do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> Os horários não suportam atualmente configurações de DSC de Automação Azure.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Os cmdlets na tabela seguinte são usados para criar e gerir horários com a PowerShell na Automação Azure. Eles enviam como parte do [módulo Azure PowerShell](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Recupera um horário. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Recupera os livros de execução programados. |
| [Programação New-AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Cria um novo horário. |
| [Registo-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associa um livro com um horário. |
| [Remover-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Remove um horário. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Define as propriedades para um horário existente. |
| [Livro deRunções Não Registrados-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Dissocia um livro de um horário. |

## <a name="creating-a-schedule"></a>Criar um horário

Pode criar uma nova programação para livros de execução no portal Azure ou com powerShell.

> [!NOTE]
> A Azure Automation utiliza os módulos mais recentes na sua conta Automation quando um novo trabalho programado é executado.  Para evitar afetar os seus livros de execução e os processos que automatizam, deve primeiro testar quaisquer livros de execução que tenham ligado horários a uma conta de Automação dedicada aos testes.  Isto valida que os seus livros de execução programados continuem a funcionar corretamente e, caso contrário, poderá resolver mais problemas e aplicar quaisquer alterações necessárias antes de migrar a versão atualizada do livro de reprodução para a produção.
> A sua conta Automation não obtém automaticamente quaisquer novas versões de módulos a menos que os tenha atualizado manualmente selecionando a opção [Módulos Update Azure](../automation-update-azure-modules.md) dos **Módulos**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Criar uma nova programação no portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Horários** sob a secção **Recursos Partilhados** à esquerda.
2. Clique em **Adicionar um horário** no topo da página.
3. No painel de horárionovo, digite um nome e opcionalmente uma descrição para a nova programação.
4. Selecione se o horário funciona uma vez ou num horário recorrente selecionando **Uma ou** **Recorrente**. Se selecionar **Uma vez,** especifique uma hora de início e, em seguida, clique em **Criar**. Se selecionar **Recorrente,** especifique uma hora de início. Para **recur cada um**, selecione quantas vezes quer que o livro de corridas se repita - por hora, dia, semana ou mês.
    1. Se selecionar **semana**, os dias da semana são apresentados para que possa escolher. Selecione quantos dias quiser. A primeira execução da sua agenda acontecerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher um horário de fim de semana, selecione sábado e domingo. 
    
       ![Definição de horário recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    2. Se selecionar **mês,** tem diferentes opções. Para a opção **ocorrências mensais,** selecione dias **de mês** ou **dias de semana**. Se escolher **os dias**mensais , é mostrado um calendário que lhe permite escolher os dias que quiser. Se escolher uma data como a 31ª que não ocorre no mês em curso, o horário não será executado. Se quiser que o horário seja executado no último dia, escolha **Sim** sob **Corrida no último dia do mês.** Se escolher **os dias da Semana,** o **Recur é** apresentada todas as opções. Escolha **Primeiro,** **Segundo,** **Terceiro,** **Quarto**ou **Último.** Finalmente escolha um dia para repetir.

       ![Horário mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, clique em **Criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Crie um novo horário com a PowerShell

Utilize o cmdlet [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) para criar horários. Especifica a hora de início do horário e a frequência que deve funcionar. Os exemplos que se seguem mostram como criar muitos cenários de horários diferentes.

#### <a name="create-a-one-time-schedule"></a>Criar um horário único

Os comandos de amostra seguintecriam um horário único.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um horário recorrente

O exemplo que se segue mostra como criar um horário recorrente que funciona todos os dias às 13:00 h durante um ano.

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

Os comandos de amostra seguem mostram como criar um horário semanal que decorre apenas aos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Criar um horário recorrente para os primeiros, 15 e últimos dias do mês

O exemplo que se segue mostra como criar um horário recorrente que decorre nos dias 1, 15 e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Ligando um horário a um livro de corridas

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um livro de execução tiver parâmetros, então pode fornecer-lhes valores. Deve fornecer valores para quaisquer parâmetros obrigatórios e pode fornecer valores para quaisquer parâmetros opcionais. Estes valores são usados cada vez que o livro de execução é iniciado por este horário. Pode anexar o mesmo livro de execução a outro horário e especificar diferentes valores de parâmetros.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ligue um horário a um livro de corridas com o portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Runbooks** em **Process Automation**.
2. Clique no nome do runbook a agendar.
3. Se o livro de execução não estiver atualmente ligado a um horário, então é-lhe oferecida a opção de criar um novo horário ou link para um horário existente.
4. Se o livro de execução tiver parâmetros, pode selecionar a opção Modificar as definições de **execução (Predefinido:Azure)** e o painel de parâmetros é apresentado. Pode introduzir informações de parâmetros aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ligue um horário a um livro de corridas com a PowerShell

Utilize o [Cmdlet Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) para ligar um horário. Pode especificar os valores dos parâmetros do runbook através do parâmetro Parâmetros . Para obter mais informações sobre a especificação dos valores dos parâmetros, consulte [Iniciar um Livro de Execução em Automação Azure](../automation-starting-a-runbook.md).
O exemplo seguinte mostra como ligar um horário a um livro de execução usando um cmdlet do Gestor de Recursos Azure com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Agendar livros de execução para executar com mais frequência

O intervalo mais frequente que um horário na Automatização Azure pode ser configurado é de uma hora. Se necessitar de horários para executar com mais frequência do que isso, existem duas opções:

* Crie um [webhook](../automation-webhooks.md) para o livro de execução e use [as Aplicações Lógicas Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. As Aplicações Lógicas Azure proporcionam uma granularidade mais fina ao definir um horário.

* Crie quatro horários todos a partir de 15 minutos um do outro correndo uma vez a cada hora. Este cenário permite que o livro de corridas seja executado a cada 15 minutos com os diferentes horários.

## <a name="disabling-a-schedule"></a>Desativar um horário

Quando desativa um horário, qualquer livro de execução ligado a ele já não funciona nesse horário. Pode desativar manualmente um horário ou definir um prazo de validade para horários com frequência quando os criar. Uma vez atingido o prazo de validade, o horário é desativado.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desativar um horário do portal Azure

1. Na sua conta de Automação, selecione **Horários** em **Recursos Partilhados**.
2. Clique no nome de um horário para abrir o painel de detalhes.
3. Alteração **ativada** a **Nº**.

> [!NOTE]
> Se quiser desativar um horário que tenha uma hora de início no passado, tem de alterar a data de início para uma hora no futuro antes de a guardar.

### <a name="disable-a-schedule-with-powershell"></a>Desative um horário com a PowerShell

Utilize o [cmdlet Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) para alterar as propriedades de um horário existente. Para desativar o `IsEnabled` horário, especifique falso para o parâmetro.

O exemplo seguinte mostra como desativar um horário para um livro de execução utilizando um cmdlet do Gestor de Recursos Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Remoção de um horário

Quando estiver pronto para remover os seus horários, pode utilizar `Remove-AzureRmAutomationSchedule` o portal Azure ou o cmdlet. Lembre-se de que só pode remover um horário que tenha sido desativado como descrito na secção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remova um horário usando o portal Azure

1. Na sua conta de Automação, selecione **Horários** em **Recursos Partilhados**.
2. Clique no nome de um horário para abrir o painel de detalhes.
3. Clique em **Apagar**.

### <a name="remove-a-schedule-with-powershell"></a>Remova um horário com powerShell

Pode utilizar o cmdlet [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) para eliminar um horário existente. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos seguintes

* Para começar com livros de corridas em Azure Automation, veja [Starting a Runbook in Azure Automation](../automation-starting-a-runbook.md).