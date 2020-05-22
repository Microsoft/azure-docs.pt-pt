---
title: Criar livros modulares na Automação Azure
description: Este artigo diz como criar um livro de corridas que é chamado por outro livro de corridas.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 5c562fb43966fda203e92cc5003ef3c85945364b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742837"
---
# <a name="create-modular-runbooks"></a>Criar runbooks modulares

É uma prática recomendada na Automatização Azure escrever livros reutilizáveis e modulares com uma função discreta que é chamada por outros livros de execução. Um livro de pais frequentemente chama um ou mais livros infantis para executar a funcionalidade necessária. 

Há duas maneiras de chamar um livro de crianças, e há diferenças distintas que deve entender para ser capaz de determinar qual é o melhor para os seus cenários. A tabela que se segue resume as diferenças entre as duas formas de chamar um livro de execução de outro.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Tarefa |O runbook subordinado é executado na mesma tarefa que o principal. |É criada uma tarefa separada para o runbook subordinado. |
| Execução |O runbook principal aguarda que o runbook subordinado seja concluído antes de continuar. |O livro de corridas dos pais continua imediatamente após o início do livro de corridas de crianças *ou* o livro de corridas dos pais espera que o trabalho da criança termine. |
| Saída |O runbook principal pode obter resultados diretamente do runbook subordinado. |O livro de corridas dos pais deve recuperar a saída do trabalho do livro de corridas infantil *ou* o livro de execução dos pais pode obter diretamente a saída do livro de execução infantil. |
| Parâmetros |Os valores para o runbook subordinado são especificados em separado e podem utilizar quaisquer tipos de dados. |Os valores dos parâmetros do livro infantil têm de ser combinados num único hashtable. Este hashtable só pode incluir tipos simples, matriz estomados e objetos que usam a serialização jSON. |
| Conta de Automatização |O livro de execução dos pais só pode usar o livro de execução infantil na mesma conta de Automação. |Os livros de execução dos pais podem usar um livro de execução infantil a partir de qualquer conta Automation, a partir da mesma subscrição Azure, e até mesmo de uma subscrição diferente à qual você tem uma ligação. |
| Publicar |O runbook subordinado tem de ser publicado antes da publicação do runbook principal. |O livro de corridas para crianças é publicado a qualquer momento antes do início do livro de corridas dos pais. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Invoque um livro de crianças usando execução inline

Para invocar um livro de execução inline de outro livro de execução, use o nome do livro de execução e forneça valores para os seus parâmetros, tal como utilizaria uma atividade ou um cmdlet. Todos os livros na mesma conta Automation estão disponíveis para todos os outros para serem usados desta forma. O livro de corridas dos pais aguarda que o livro de corridas da criança esteja completo antes de passar para a linha seguinte, e qualquer saída retorna diretamente ao progenitor.

Quando invoca um runbook inline, este é executado na mesma tarefa que o runbook principal. Não há indícios no histórico de trabalho do livro infantil. Quaisquer exceções e quaisquer saídas de fluxo do livro de execução infantil estão associadas ao progenitor. Este comportamento resulta em menos empregos e torna-os mais fáceis de rastrear e de resolver problemas.

Quando um livro de execução é publicado, qualquer livro infantil que ele chama já deve ser publicado. A razão é que a Azure Automation constrói uma associação com qualquer livro infantil quando compila um livro de corridas. Se os livros infantis ainda não foram publicados, o livro de execução dos pais parece publicar corretamente, mas gera uma exceção quando é iniciado. Se isso acontecer, pode reeditar o livro de execução dos pais para fazer referência adequada aos livros de gestão de crianças. Não é necessário reeditar o livro de execução dos pais se algum livro de corridas para crianças for alterado porque a associação já foi criada.

Os parâmetros de um livro infantil chamado inline podem ser de qualquer tipo de dados, incluindo objetos complexos. Não existe [serialização JSON](start-runbooks.md#work-with-runbook-parameters), como existe quando se inicia o livro de execução utilizando o portal Azure ou com o cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Tipos de runbooks

Que tipos de livros de execução podem chamar uns aos outros?

* Um livro de [execução powerShell](automation-runbook-types.md#powershell-runbooks) e um [livro gráfico](automation-runbook-types.md#graphical-runbooks) podem chamar-se um ao outro em linha, uma vez que ambos são baseados em PowerShell.
* Um livro de execução do [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) e um livro de execução gráfico powerShell Workflow podem chamar-se um ao outro em linha, uma vez que ambos são baseados em PowerShell Workflow.
* Os tipos PowerShell e os tipos powerShell Workflow não podem ligar uns aos outros em linha, e devem usar `Start-AzAutomationRunbook` .

Quando é que a ordem de publicação importa?

A ordem de publicação de livros de execução só importa para powerShell Workflow e livros gráficos powerShell Workflow.

Quando o seu livro de recortes chama um livro de corridas para crianças gráfica ou PowerShell Workflow utilizando a execução inline, ele usa o nome do livro de execução. O nome deve começar `.\\` por especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo seguinte inicia um livro de teste para crianças que aceita um objeto complexo, um valor inteiro e um valor booleano. O resultado do runbook subordinado é atribuído a uma variável. Neste caso, o livro de corridas para crianças é um livro de execução powerShell Workflow.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Aqui está o mesmo exemplo usando um livro de execução PowerShell como a criança.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Inicie um livro de crianças usando um cmdlet

> [!IMPORTANT]
> Se o seu livro de corridas invocar um livro de crianças com o `Start-AzAutomationRunbook` cmdlet com o parâmetro e o livro de crianças produzir um resultado de `Wait` objeto, a operação pode encontrar um erro. Para contornar o erro, consulte os [livros infantis com saída](troubleshoot/runbooks.md#child-runbook-object) de objetos para aprender a implementar a lógica para fazer sondagem para os resultados utilizando o cmdlet [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Pode utilizar `Start-AzAutomationRunbook` para iniciar um livro de execução como descrito no Início de um livro de [execução com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existem dois modos de utilização para este cmdlet. Num modo, o cmdlet devolve o ID de trabalho quando o trabalho é criado para o livro de corridas infantil. No outro modo, que o seu script permite especificando o parâmetro *de espera,* o cmdlet aguarda até que o trabalho da criança termine e desconere a saída do livro de execução infantil.

O trabalho de um livro infantil começou com um cmdlet que funciona separadamente do trabalho do livro de corridas dos pais. Este comportamento resulta em mais empregos do que iniciar o livro de corridas, e torna os trabalhos mais difíceis de acompanhar. O progenitor pode começar mais do que um livro infantil sincronicamente sincronicamente sem esperar que cada um esteja completo. Para esta execução paralela, chamando os livros de execução da criança inline, o livro de execução dos pais deve usar a [palavra-chave paralela](automation-powershell-workflow.md#use-parallel-processing).

A saída do livro de crianças não regressa ao livro de corridas dos pais de forma fiável devido ao timing. Além disso, variáveis `$VerbosePreference` `$WarningPreference` como, e outras, podem não ser propagadas aos livros infantis. Para evitar estes problemas, pode iniciar os livros de execução para crianças como trabalhos separados de automação utilizando `Start-AzAutomationRunbook` com o `Wait` parâmetro. Esta técnica bloqueia o livro dos pais até que o livro de corridas da criança esteja completo.

Se não quiser que o livro de corridas dos pais seja bloqueado à espera, pode iniciar o livro de corridas para crianças `Start-AzAutomationRunbook` usando sem o `Wait` parâmetro. Neste caso, o seu livro de execução deve utilizar [o Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para aguardar a conclusão do trabalho. Também deve utilizar [get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [Get-AzAutomationJobOutputOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar os resultados.

Os parâmetros para um livro infantil iniciado com um cmdlet são fornecidos como um hashtable, como descrito nos [parâmetros Runbook](start-runbooks.md#work-with-runbook-parameters). Só podem ser utilizados tipos simples de dados. Se o runbook tiver um parâmetro com um tipo de dados complexo, tem de ser chamado inline.

O contexto de subscrição pode perder-se ao iniciar os livros de execução de crianças como trabalhos separados. Para que o livro de execução infantil execute cmdlets de módulo Az contra uma subscrição específica do Azure, a criança deve autenticar a esta subscrição independentemente do livro-mãe.

Se os trabalhos dentro da mesma conta Automation funcionarem com mais de uma subscrição, selecionar uma subscrição num só trabalho pode alterar o contexto de subscrição atualmente selecionado para outros trabalhos. Para evitar esta situação, utilize `Disable-AzContextAutosave –Scope Process` no início de cada livro de execução. Esta ação apenas salva o contexto para a execução do livro de corridas.

### <a name="example"></a>Exemplo

O exemplo seguinte inicia um livro infantil com parâmetros e, em seguida, aguarda que esteja completo utilizando o `Start-AzAutomationRunbook` cmdlet com o `Wait` parâmetro. Uma vez concluído, o exemplo recolhe a saída de cmdlet do livro de execução infantil. Para `Start-AzAutomationRunbook` utilizar, o script deve autenticar a sua assinatura Azure.

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

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>Passos seguintes

* [Starting a Runbook in Azure Automation](start-runbooks.md) (Iniciar um Runbook na Automatização do Azure)
* [Saída de livro de execução e mensagens na Automação Azure](automation-runbook-output-and-messages.md)