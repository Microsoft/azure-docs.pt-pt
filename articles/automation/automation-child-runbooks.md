---
title: Runbooks subordinados na automatização do Azure
description: Descreve os diferentes métodos para iniciar um runbook na automatização do Azure a partir de outro runbook e partilhar informações entre eles.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84f17b76f03c01d0b1441a50b9bcbddc1dfe2ef3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57851318"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks subordinados na automatização do Azure

É uma prática recomendada na automatização do Azure para escrever runbooks modulares, reutilizáveis, com uma função discreta que está por outros runbooks. Um runbook principal chamará, muitas vezes, um ou mais runbooks subordinados para executar a funcionalidade necessária. Existem duas formas de chamar um runbook subordinado e cada uma delas apresenta diferenças distintas que deverá compreender para que pode determinar qual é a melhor para os seus cenários diferentes.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook subordinado com a execução inline

Para invocar um runbook inline a partir de outro runbook, utilize o nome do runbook e forneça valores para seus parâmetros, exatamente da mesma forma que usaria uma atividade ou um cmdlet.  Todos os runbooks na mesma conta de automatização estão disponíveis para todos os outros a ser utilizada desta forma. O runbook principal aguarda o runbook subordinado seja concluído antes de passar para a próxima linha e qualquer resultado é devolvido diretamente ao principal.

Quando invoca um runbook inline, ele é executado na mesma tarefa que o runbook principal. Não existe nenhuma indicação no histórico da tarefa do runbook subordinado que foi executado. Quaisquer exceções e qualquer fluxo de saída do runbook subordinado é associadas ao principal. Este comportamento resulta em menos tarefas e os torna mais fácil de controlar e resolver problemas desde eventuais exceções geradas pelo runbook subordinado e qualquer uma das respetivas saídas de fluxo são associadas a tarefa principal.

Quando um runbook é publicado, todos os runbooks subordinados que aquele invoque já tem de ser publicados. Isto acontece porque a automatização do Azure cria uma associação com todos os runbooks subordinados quando um runbook é compilado. Se não forem, o runbook principal parece ter sido publicado corretamente, mas gerará uma exceção quando é iniciada. Se isto acontecer, pode voltar a publicar o runbook de principal para referenciar adequadamente os runbooks subordinados. Não é necessário voltar a publicar o runbook principal se qualquer um dos runbooks subordinados forem alterados, porque a associação já foi criada.

Os parâmetros de um runbook subordinado invocado inline podem ser qualquer tipo de dados, incluindo objetos complexos. Não existe nenhuma [serialização JSON](start-runbooks.md#runbook-parameters) porque não existe quando iniciar o runbook no portal do Azure ou com o cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipos de runbooks

Quais são os tipos podem chamar entre si:

* R [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) pode chamar cada outros embutidos (ambos são PowerShell com base).
* R [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks de fluxo de trabalho de PowerShell gráfico pode chamar cada outros embutidos (ambos são o fluxo de trabalho do PowerShell com base)
* Os tipos de PowerShell e os tipos de fluxo de trabalho do PowerShell não é possível chamar entre si inline e tem de utilizar Start-AzureRmAutomationRunbook.

Quando publicar a questão de ordem:

* A ordem de publicação de runbooks só é importante para os runbooks de fluxo de trabalho do PowerShell e o fluxo de trabalho de PowerShell gráfico.

Quando chama um runbook subordinado gráfico ou fluxo de trabalho do PowerShell com a execução inline, utilize o nome do runbook.  Quando chama um runbook subordinado do PowerShell, tem de começar o seu nome com *.\\*  para especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo seguinte inicia um runbook subordinado de teste que aceita três parâmetros, um objeto complexo, um número inteiro e um booleano. A saída do runbook subordinado é atribuída a uma variável.  Neste caso, o runbook subordinado é um runbook de fluxo de trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Segue-se o mesmo exemplo com um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>A partir de um runbook subordinado com o cmdlet

> [!IMPORTANT]
> Se está a invocar um runbook subordinado com o `Start-AzureRmAutomationRunbook` cmdlet com o `-Wait` comutador e os resultados do runbook subordinado é um objeto, poderá encontrar erros. Para contornar o erro, consulte [runbooks subordinados com a saída do objeto](troubleshoot/runbooks.md#child-runbook-object) para saber como implementar a lógica para consultar os resultados e usar o [Get AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Pode utilizar o [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) cmdlet para iniciar um runbook, conforme descrito na [para iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existem dois modos de utilização para este cmdlet.  Num modo, o cmdlet devolve o id da tarefa quando a tarefa subordinada é criada para o runbook subordinado.  No outro modo, que permitem ao especificar que o **-aguardar** parâmetro, o cmdlet espera até que a tarefa filho é concluído e devolve o resultado do runbook subordinado.

A tarefa a partir de um runbook subordinado iniciado com um cmdlet é executado num trabalho separado do runbook principal. Este comportamento resulta em mais tarefas do que a iniciar o runbook inline e torna mais difícil de controlar. O principal pode iniciar mais do que um runbook de subordinado forma assíncrona, sem aguardar a conclusão de cada. Para esse mesmo tipo de execução paralela de invocar os runbooks subordinados inline, o runbook principal seria necessário usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

A saída de runbooks subordinados não são devolvidos para o runbook principal fiável devido a tempo. Também o determinadas variáveis, como a $VerbosePreference, $WarningPreference, e outras pessoas não podem ser propagadas para os runbooks subordinados. Para evitar estes problemas, pode iniciar runbooks subordinados como separadas das tarefas de automatização com o `Start-AzureRmAutomationRunbook` cmdlet com o `-Wait` mudar. Isto impede o runbook principal até que o runbook subordinado seja concluído.

Se não pretender que o runbook principal até ser bloqueado em espera, pode iniciar o runbook subordinado com `Start-AzureRmAutomationRunbook` cmdlet sem o `-Wait` mudar. Em seguida, seria necessário usar `Get-AzureRmAutomationJob` a aguardar pela conclusão da tarefa, e `Get-AzureRmAutomationJobOutput` e `Get-AzureRmAutomationJobOutputRecord` para recuperar os resultados.

Parâmetros para um runbook subordinado iniciado com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [parâmetros do Runbook](start-runbooks.md#runbook-parameters). Apenas os tipos de dados simples podem ser utilizados. Se o runbook tem um parâmetro com um tipo de dados complexos, em seguida, tem de ser chamado inline.

O contexto da subscrição poderão perder-se ao iniciar runbooks subordinados como tarefas separadas. Por ordem para o runbook subordinado executar os cmdlets do Azure RM em relação a uma subscrição do Azure específica, o runbook subordinado tem de ser autenticado para esta subscrição, independentemente do runbook principal.

Se as tarefas dentro da mesma conta de automatização funcionam com mais de uma assinatura, selecionando uma assinatura num trabalho pode alterar o contexto de subscrição selecionada atualmente para outros trabalhos. Para evitar este problema, utilize `Disable-AzureRmContextAutosave –Scope Processsave` no início de cada runbook. Esta ação guarda apenas o contexto para essa execução de runbooks.

### <a name="example"></a>Exemplo

O exemplo seguinte inicia um runbook subordinado com parâmetros e, em seguida, aguarda que este seja concluído com o Start-AzureRmAutomationRunbook-parâmetro de espera. Depois de concluído, o resultado é recolhido do runbook subordinado. Para utilizar `Start-AzureRmAutomationRunbook`, tem de autenticar a sua subscrição do Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook subordinado

A tabela seguinte resume as diferenças entre os dois métodos para chamar um runbook a partir de outro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Tarefa |Runbook subordinado é executado na mesma tarefa como pai. |Uma tarefa separada é criada para o runbook subordinado. |
| Execução |O runbook principal aguarda que o runbook subordinado seja concluído antes de continuar. |O runbook principal continua imediatamente após o início do runbook subordinado *ou* runbook principal aguarda o conclusão do trabalho filho. |
| Saída |O runbook principal pode obter resultados diretamente do runbook subordinado. |O runbook principal tem de obter o resultado da tarefa do runbook subordinado *ou* runbook principal pode obter resultados diretamente do runbook subordinado. |
| Parâmetros |Valores para parâmetros do runbook subordinado são especificados em separado e podem utilizar qualquer tipo de dados. |Valores dos parâmetros do runbook subordinado tem de ser combinados numa única tabela hash. Esta tabela de hash só pode incluir simple, matriz e os tipos de dados que usar a serialização do JSON de objeto. |
| Conta de Automatização |O runbook principal só pode utilizar o runbook subordinado na mesma conta de automatização. |Runbooks principais pode utilizar um runbook subordinado a partir de qualquer conta de automatização da mesma subscrição do Azure e até mesmo uma subscrição diferente que têm uma ligação. |
| Publicação |Runbook subordinado tem de ser publicado antes de o runbook principal. |Runbook subordinado tem de ser publicado em qualquer altura antes de principal runbook é iniciado. |

## <a name="next-steps"></a>Passos Seguintes

* [Iniciar um runbook na automatização do Azure](start-runbooks.md)
* [Resultado do Runbook e mensagens na automatização do Azure](automation-runbook-output-and-messages.md)

