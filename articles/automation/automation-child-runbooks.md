---
title: Criar runbooks modulares na Azure Automation
description: Este artigo diz como criar um livro de bordo que é chamado por outro runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: f0dd5cf5209924972080af6d22429252338754de
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491253"
---
# <a name="create-modular-runbooks"></a>Criar runbooks modulares

É uma prática recomendada na Azure Automation para escrever livros reutilizáveis e modulares com uma função discreta que é chamada por outros runbooks. Um livro de bordo dos pais costuma chamar um ou mais livros infantis para executar a funcionalidade necessária. 

Existem duas maneiras de chamar um livro infantil, e há diferenças distintas que você deve entender para ser capaz de determinar qual é o melhor para os seus cenários. A tabela seguinte resume as diferenças entre as duas formas de chamar um livro de bordo de outro.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| **Tarefa** |O runbook subordinado é executado na mesma tarefa que o principal. |É criada uma tarefa separada para o runbook subordinado. |
| **Execução** |O runbook principal aguarda que o runbook subordinado seja concluído antes de continuar. |O livro de bordo dos pais continua imediatamente após o início do livro de recortes de crianças *ou* o livro de bordo dos pais espera que o trabalho da criança termine. |
| **Saída** |O runbook principal pode obter resultados diretamente do runbook subordinado. |O livro de bordo dos pais deve obter a saída do trabalho do livro de recortes de *crianças ou* do livro de bordo dos pais pode obter diretamente a saída do livro infantil. |
| **Parâmetros** |Os valores para o runbook subordinado são especificados em separado e podem utilizar quaisquer tipos de dados. |Os valores para os parâmetros do livro de crianças têm de ser combinados numa única hashtable. Este hashtable só pode incluir tipos de dados simples, array e objeto que usam a serialização JSON. |
| **Conta de Automatização** |O livro de bordo dos pais só pode utilizar o livro de crianças na mesma conta Dem automação. |Os livros de bordo dos pais podem utilizar um livro de crianças a partir de qualquer conta de Automação, a partir da mesma subscrição do Azure, e até mesmo de uma subscrição diferente à qual tem uma ligação. |
| **Publicar** |O runbook subordinado tem de ser publicado antes da publicação do runbook principal. |O livro infantil é publicado a qualquer momento antes do início do livro de bordo dos pais. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Invocar um livro infantil usando a execução inline

Para invocar um livro de bordo de outro livro de recortes, use o nome do runbook e forneça valores para os seus parâmetros, tal como utilizaria uma atividade ou um cmdlet. Todos os livros na mesma conta Automation estão disponíveis para todos os outros para serem utilizados desta forma. O livro de bordo dos pais aguarda que o livro de bebés esteja completo antes de passar para a linha seguinte, e qualquer saída retorna diretamente ao progenitor.

Quando invoca um runbook inline, este é executado na mesma tarefa que o runbook principal. Não há indicação no histórico de trabalho do livro infantil. Quaisquer exceções e quaisquer saídas de fluxo do livro de crianças estão associadas ao progenitor. Este comportamento resulta em menos empregos e torna-os mais fáceis de rastrear e resolver problemas.

Quando um livro de bordo é publicado, todos os livros infantis a que chama já devem ser publicados. A razão é que a Azure Automation constrói uma associação com quaisquer livros infantis quando compila um livro de recortes. Se os livros para crianças ainda não tiverem sido publicados, o livro de bordo dos pais parece publicar corretamente, mas gera uma exceção quando é iniciado. Se isto acontecer, pode voltar a publicar o livro de bordo dos pais para fazer referência adequada aos livros de ensaio para crianças. Não é necessário reeditar o livro de bordo dos pais se algum livro de crianças for alterado porque a associação já foi criada.

Os parâmetros de um livro infantil chamado inline podem ser de qualquer tipo de dado, incluindo objetos complexos. Não existe [uma serialização JSON](start-runbooks.md#work-with-runbook-parameters), como acontece quando inicia o livro de bordo utilizando o portal Azure ou com o [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Tipos de runbooks

Que tipos de livros podem chamar uns aos outros?

* Um [livro de execução PowerShell](automation-runbook-types.md#powershell-runbooks) e um [runbook gráfico](automation-runbook-types.md#graphical-runbooks) podem chamar-se inline, uma vez que ambos são baseados em PowerShell.
* Um [manual de workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e um manual gráfico do fluxo de trabalho powerShell podem chamar-se inline, uma vez que ambos são baseados em PowerShell Workflow.
* Os tipos PowerShell e os tipos de fluxo de trabalho PowerShell não podem ligar-se entre si e devem ser utilizados `Start-AzAutomationRunbook` .

Quando é que a ordem de publicação importa?

A ordem de publicação dos livros de execução apenas importa para o fluxo de trabalho powerShell e para os livros gráficos de fluxo de trabalho powerShell.

Quando o seu runbook chama um livro de trabalho gráfico ou powerShell workflow usando execução inline, ele usa o nome do livro de bordo. O nome deve começar `.\\` por especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um ensaio de ensaio para crianças que aceita um objeto complexo, um valor inteiro e um valor booleano. O resultado do runbook subordinado é atribuído a uma variável. Neste caso, o livro de recortes para crianças é um manual de fluxo de trabalho PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Aqui está o mesmo exemplo usando um manual PowerShell como a criança.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Inicie um livro infantil usando um cmdlet

> [!IMPORTANT]
> Se o seu livro de bordo invocar um livro de crianças com o `Start-AzAutomationRunbook` cmdlet com o parâmetro e o livro de `Wait` ensaios da criança produzir um resultado de objeto, a operação poderá encontrar um erro. Para contornar o erro, consulte [os livros de aplicação infantil com saída](troubleshoot/runbooks.md#child-runbook-object) de objeto para aprender a implementar a lógica de pesquisa para obter os resultados usando o Cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Pode utilizar `Start-AzAutomationRunbook` para iniciar um livro de execução como descrito para iniciar um livro com o Windows [PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existem dois modos de utilização para este cmdlet. Num modo, o cmdlet devolve o ID do trabalho quando o trabalho é criado para o livro de crianças. No outro modo, que o seu script permite especificando o parâmetro *Wait,* o cmdlet aguarda até que o trabalho da criança termine e devolva a saída do livro de ensaios da criança.

O trabalho de um livro infantil começou com um cmdlet executado separadamente do trabalho de runbook dos pais. Este comportamento resulta em mais empregos do que iniciar o runbook inline, e torna os trabalhos mais difíceis de rastrear. O progenitor pode iniciar mais de um livro infantil assíncroneamente sem esperar que cada um esteja completo. Para esta execução paralela, chamando os livros de ensaio para crianças em linha, o livro de bordo dos pais deve utilizar a [palavra-chave paralela](automation-powershell-workflow.md#use-parallel-processing).

A saída do livro de corridas para crianças não volta ao livro de bordo dos pais de forma fiável devido ao tempo. Além disso, variáveis `$VerbosePreference` `$WarningPreference` como, e outras, podem não ser propagadas aos livros infantis. Para evitar estes problemas, pode iniciar os livros para crianças como trabalhos de automatização separados utilizando `Start-AzAutomationRunbook` o `Wait` parâmetro. Esta técnica bloqueia o livro de bordo dos pais até que o livro de bebés esteja completo.

Se não quiser que o livro de bordo dos pais seja bloqueado à espera, pode iniciar o livro de bebés utilizando `Start-AzAutomationRunbook` sem o `Wait` parâmetro. Neste caso, o seu runbook deve usar [o Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para aguardar a conclusão do trabalho. Também deve utilizar [o Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [o Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar os resultados.

Os parâmetros para um livro infantil iniciado com um cmdlet são fornecidos como um haxixe, conforme descrito nos [parâmetros Runbook](start-runbooks.md#work-with-runbook-parameters). Apenas tipos simples de dados podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexo, tem de ser chamado inline.

O contexto de subscrição pode perder-se quando se iniciam os livros infantis como trabalhos separados. Para que o livro de aplicação da criança execute cmdlets de móduloS Az contra uma subscrição específica do Azure, a criança deve autenticar esta subscrição independentemente do livro de bordo dos pais.

Se os trabalhos dentro da mesma conta Automation funcionarem com mais de uma subscrição, selecionar uma subscrição num trabalho pode alterar o contexto de subscrição atualmente selecionado para outros trabalhos. Para evitar esta situação, `Disable-AzContextAutosave –Scope Process` utilize-o no início de cada livro de bordo. Esta ação só guarda o contexto para a execução do livro de bordo.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um livro de aplicação de crianças com parâmetros e, em seguida, espera que este complete usando o `Start-AzAutomationRunbook` cmdlet com o `Wait` parâmetro. Uma vez concluído, o exemplo recolhe a saída de cmdlet do livro de aplicação da criança. Para `Start-AzAutomationRunbook` utilizar, o script deve autenticar a sua subscrição Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>Passos seguintes

* Para executar o seu runbook, consulte [Iniciar um livro de recortes na Azure Automation](start-runbooks.md).
* Para monitorizar a operação do runbook, consulte [a saída do Runbook e as mensagens na Azure Automation](automation-runbook-output-and-messages.md).
