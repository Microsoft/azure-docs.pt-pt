---
title: Gerir horários na Azure Automation
description: Este artigo diz como criar e trabalhar com um horário na Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: a829cd946f36fb5996405ba00945e9f9cb65d162
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544235"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerir horários na Azure Automation

Para agendar um livro de execução na Azure Automation para começar numa hora especificada, ligue-o a um ou mais horários. Um horário pode ser configurado para ser executado uma vez ou em uma agenda recorrente de hora ou de dia para os runbooks no portal Azure. Também pode agendar para dias semanais, mensais, específicos da semana ou dias do mês, ou um dia particular do mês. Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados.

> [!NOTE]
> A Azure Automation suporta o horário de verão e programa-o adequadamente para operações de automação.

> [!NOTE]
> Atualmente, os horários não estão ativados para configurações DSC de Automação Azure.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlets PowerShell usados para aceder a horários

Os cmdlets na tabela seguinte criam e gerem horários de Automação com o PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

| Cmdlets | Description |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Recupera um horário. |
| [Get-AzAutomationScheduldRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Recupera livros de corridas programados. |
| [Nova AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Cria um novo horário. |
| [Registro-AzAutomationScheduldRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Associa um livro com um horário. |
| [Remover-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Remove um horário. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Define as propriedades para um horário existente. |
| [Não registro-azautomationScheduldRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Dissocia um livro de bordo de um horário. |

## <a name="create-a-schedule"></a>Criar um horário

Pode criar um novo horário para os seus runbooks a partir do portal Azure, com o PowerShell, ou utilizando um modelo de Gestor de Recursos Azure (ARM). Para evitar afetar os seus runbooks e os processos que automatizam, deve primeiro testar quaisquer livros que tenham agendados ligados a uma conta Automation dedicada a testes. Um teste valida que os seus livros de execução programados continuam a funcionar corretamente. Se vir um problema, pode resolver problemas e aplicar quaisquer alterações necessárias antes de migrar a versão atualizada do runbook para a produção.

> [!NOTE]
> A sua conta de Automação não obtém automaticamente novas versões de módulos a menos que os tenha atualizado manualmente selecionando a opção [de módulos Azure de Atualização](../automation-update-azure-modules.md) a partir de **Módulos**. A Azure Automation utiliza os módulos mais recentes da sua conta de Automação quando é executado um novo trabalho programado. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Criar uma nova programação no portal Azure

1. A partir da sua conta Demôm automação, no painel esquerdo selecione Agendas em **Recursos** **Partilhados.**
2. Na página **Agendas,** **selecione Adicionar um horário**.
3. Na página de **nova programação,** insira um nome e introduza opcionalmente uma descrição para o novo horário.

    >[!NOTE]
    >Atualmente, os horários de automatização não suportam a utilização de caracteres especiais no nome do horário.
    >

4. Selecione se o horário é executado uma vez ou num horário recorrente selecionando **Uma vez** ou **recorrente**. Se selecionar **Uma vez**, especifique uma hora de início e, em seguida, selecione **Criar**. Se selecionar **'Recorrente',** especifique uma hora de início. Para **repetir cada**, selecione quantas vezes pretende que o livro de execução repita. Selecione por hora, dia, semana ou mês.

    * Se selecionar **Week,** os dias da semana são apresentados para que possa escolher. Selecione quantos dias quiser. A primeira execução da sua programação acontecerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher um horário de fim de semana, selecione sábado e domingo.

    ![Definição de horário recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    * Se selecionar **mês,** é-lhe dadas diferentes opções. Para a opção **de ocorrências mensais,** selecione **os dias mensais** ou **semanas**. Se selecionar **dias de mês,** aparece um calendário para que possa escolher quantos dias quiser. Se escolher uma data como a 31ª que não ocorre no mês em curso, o horário não será executado. Se quiser que o horário seja executado no último dia, selecione **Sim** em **Execução no último dia do mês**. Se selecionar **os dias semanais,** aparece todas as opções **de Recur.** Escolha **primeiro,** **segundo,** **terceiro,** **quarto** ou **último**. Finalmente, escolha um dia para repetir.

    ![Horário mensal no primeiro, décimo quinto e último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Quando terminar, **selecione Criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Criar um novo horário com a PowerShell

Utilize o [cmdlet New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) para criar horários. Especifica a hora de início do horário e a frequência que deve ser executada. Os exemplos a seguir mostram como criar muitos cenários de agenda diferentes.

>[!NOTE]
>Atualmente, os horários de automatização não suportam a utilização de caracteres especiais no nome do horário.
>

#### <a name="create-a-one-time-schedule"></a>Criar um horário único

O exemplo a seguir cria um horário único.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um horário recorrente

O exemplo a seguir mostra como criar um horário recorrente que funciona todos os dias às 13:00 durante um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar um horário semanal recorrente

O exemplo a seguir mostra como criar um horário semanal que funciona apenas nos dias úteis.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um horário semanal recorrente para fins de semana

O exemplo a seguir mostra como criar um horário semanal que funciona apenas aos fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Crie um horário recorrente para o primeiro, décimo quinto e últimos dias do mês

O exemplo a seguir mostra como criar um horário recorrente que funciona no primeiro, décimo quinto e último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="create-a-schedule-with-a-resource-manager-template"></a>Crie um horário com um modelo de Gestor de Recursos

Neste exemplo, usamos um modelo de Gestor de Recursos de Automação (ARM) que cria um novo horário de trabalho. Para obter informações gerais sobre este modelo para gerir os horários de trabalho da Automação, consulte [a referência do modelo microsoft.Automation automation](/azure/templates/microsoft.automation/2015-10-31/automationaccounts/jobschedules#quickstart-templates)

Copie este ficheiro de modelo num editor de texto:

```json
{
  "name": "5d5f3a05-111d-4892-8dcc-9064fa591b96",
  "type": "Microsoft.Automation/automationAccounts/jobSchedules",
  "apiVersion": "2015-10-31",
  "properties": {
    "schedule": {
      "name": "scheduleName"
    },
    "runbook": {
      "name": "runbookName"
    },
    "runOn": "hybridWorkerGroup",
    "parameters": {}
  }
}
```

Edite os seguintes valores de parâmetro e guarde o modelo como um ficheiro JSON:

* Nome do objeto da agenda de trabalho: Um GUID (Globalmente Unique Identifier) é usado como o nome do objeto do agendamento de trabalho.

   >[!IMPORTANT]
   > Para cada programa de trabalho implantado com um modelo ARM, o GUID deve ser único. Mesmo que esteja a reagendar um horário existente, terá de alterar o GUID. Isto aplica-se mesmo que tenha apagado previamente um horário de trabalho existente que foi criado com o mesmo modelo. Reutilizar o mesmo GUID resulta numa implantação falhada.</br></br>
   > Existem serviços online que podem gerar um novo GUID para si, como este [Gerador GUIADO Online Gratuito.](https://guidgenerator.com/)

* Nome da agenda: Representa o nome do horário de trabalho da Automatização que estará ligado ao livro de contas especificado.
* Nome do runbook: Representa o nome do livro de trabalho da Automação com o qual o horário de trabalho deve ser associado.

Uma vez guardado o ficheiro, pode criar o horário de trabalho do runbook com o seguinte comando PowerShell. O comando utiliza o `TemplateFile` parâmetro para especificar o caminho e o nome de arquivo do modelo.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "<path>\RunbookJobSchedule.json"
```

## <a name="link-a-schedule-to-a-runbook"></a>Ligue um horário a um livro de corridas

Um runbook pode ser ligado a várias agendas e uma agenda pode ter vários runbooks a si ligados. Se um livro de bordo tiver parâmetros, pode fornecer-lhes valores. Deve fornecer valores para quaisquer parâmetros obrigatórios, e também pode fornecer valores para quaisquer parâmetros opcionais. Estes valores são utilizados sempre que o livro de recortes é iniciado por este programa. Pode anexar o mesmo livro de execução a outro horário e especificar diferentes valores de parâmetros.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Ligue um horário a um livro com o portal Azure

1. No portal Azure, a partir da sua conta de automação, selecione **Runbooks** em **Processo Demôm automação**.
1. Selecione o nome do livro de corridas para agendar.
1. Se o livro de formulários não estiver atualmente ligado a um horário, é-lhe oferecida a opção de criar um novo horário ou ligação a um horário existente.
1. Se o livro de execução tiver parâmetros, pode selecionar as definições de **execução de modificação (Predefinição:Azure)** e aparecer o painel **de parâmetros.** Pode introduzir informações sobre parâmetros aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Ligue um horário a um livro com o PowerShell

Utilize o [cmdlet Register-AzAutomationScheduldRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) para ligar um horário. Pode especificar os valores dos parâmetros do runbook através do parâmetro Parâmetros . Para obter mais informações sobre como especificar valores de parâmetros, consulte [Iniciar um Runbook na Azure Automation](../start-runbooks.md).
O exemplo a seguir mostra como ligar um horário a um runbook utilizando um cmdlet Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Agendar livros para executar com mais frequência

O intervalo mais frequente para o qual um horário na Azure Automation pode ser configurado é de uma hora. Se necessitar de horários mais frequentes do que isso, existem duas opções:

* Crie um [webhook](../automation-webhooks.md) para o runbook e use [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) para ligar para o webhook. A Azure Logic Apps fornece uma granularidade mais fina para definir um horário.

* Crie quatro horários que começam dentro de 15 minutos um do outro e funcionam uma vez a cada hora. Este cenário permite que o livro de corridas seja executado a cada 15 minutos com os diferentes horários.

## <a name="disable-a-schedule"></a>Desativar um horário

Quando desativa um horário, qualquer livro de recortes ligado a ele já não funciona nesse horário. Pode desativar manualmente um horário ou definir um prazo de validade para horários com frequência quando os criar. Quando o prazo de validade é atingido, o horário é desativado.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desativar um horário do portal Azure

1. Na sua conta Demôm automação, no painel esquerdo selecione **Agendas** em **Recursos Partilhados**.
1. Selecione o nome de um horário para abrir o painel de detalhes.
1. Alteração **Ativada** para **Não**.

> [!NOTE]
> Se pretende desativar um horário que tenha uma hora de início no passado, tem de alterar a data de início para uma hora no futuro antes de a guardar.

### <a name="disable-a-schedule-with-powershell"></a>Desative um horário com o PowerShell

Utilize o [cmdlet Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) para alterar as propriedades de um horário existente. Para desativar o horário, especifique Falso para o `IsEnabled` parâmetro.

O exemplo a seguir mostra como desativar um horário de um livro de corridas utilizando um cmdlet Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Remover um horário

Quando estiver pronto para remover os seus horários, pode utilizar o portal Azure ou o PowerShell. Lembre-se de que só pode remover um horário que tenha sido desativado como descrito na secção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remova um horário usando o portal Azure

1. Na sua conta Demôm automação, no painel esquerdo selecione **Agendas** em **Recursos Partilhados**.
2. Selecione o nome de um horário para abrir o painel de detalhes.
3. Clique em **Eliminar**.

### <a name="remove-a-schedule-with-powershell"></a>Remova um horário com PowerShell

Pode utilizar o `Remove-AzAutomationSchedule` cmdlet como mostrado abaixo para eliminar um horário existente.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets utilizados para aceder a horários, consulte [Gerir módulos na Azure Automation.](modules.md)
* Para obter informações gerais sobre os runbooks, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).