---
title: Runbooks filho na automação do Azure
description: Descreve os diferentes métodos para iniciar um runbook na automação do Azure de outro runbook e compartilhar informações entre eles.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a35ee69e6a167f4907294c88710d0484353d4cb2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367014"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks filho na automação do Azure

É uma prática recomendada na automação do Azure escrever runbooks reutilizáveis e modulares com uma função discreta que é por outros runbooks. Um runbook pai geralmente chama um ou mais runbooks filho para executar a funcionalidade necessária. Há duas maneiras de chamar um runbook filho, e cada uma tem diferenças distintas que você deve entender para que você possa determinar qual é a melhor para seus diferentes cenários.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocando um runbook filho usando a execução embutida

Para invocar um runbook inline a partir de outro runbook, utilize o nome do runbook e forneça valores para seus parâmetros, exatamente da mesma forma que usaria uma atividade ou um cmdlet.  Todos os runbooks na mesma conta de automação estão disponíveis para todas as outras pessoas a serem usadas dessa maneira. O runbook principal aguarda o runbook subordinado seja concluído antes de passar para a próxima linha e qualquer resultado é devolvido diretamente ao principal.

Quando invoca um runbook inline, ele é executado na mesma tarefa que o runbook principal. Não há nenhuma indicação no histórico de trabalhos do runbook filho que ele executou. Todas as exceções e qualquer saída de fluxo do runbook filho é associada ao pai. Esse comportamento resulta em menos trabalhos e torna mais fácil controlar e solucionar problemas, já que as exceções geradas pelo runbook filho e qualquer uma de suas saídas de fluxo são associadas ao trabalho pai.

Quando um runbook é publicado, todos os runbooks filho que ele chama já devem estar publicados. Isso ocorre porque a automação do Azure cria uma associação com quaisquer runbooks filho quando um runbook é compilado. Se não forem, o runbook pai parecerá publicar corretamente, mas gerará uma exceção quando ela for iniciada. Se isso acontecer, você poderá republicar o runbook pai para referenciar corretamente os runbooks filho. Você não precisará republicar o runbook pai se qualquer um dos runbooks filho for alterado porque a associação já foi criada.

Os parâmetros de um runbook filho chamado Inline podem ser qualquer tipo de dados, incluindo objetos complexos. Não há nenhuma [serialização JSON](start-runbooks.md#runbook-parameters) como há quando você inicia o runbook usando o portal do Azure ou com o cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos podem chamar uns aos outros:

* Um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) podem chamar uns aos outros embutidos (ambos são baseados no PowerShell).
* Um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks gráficos de fluxo de trabalho do PowerShell podem chamar uns aos outros (ambos são baseados no fluxo de trabalho do PowerShell
* Os tipos do PowerShell e os tipos de fluxo de trabalho do PowerShell não podem chamar uns aos outros embutidos e devem usar Start-AzureRmAutomationRunbook.

Quando a ordem de publicação é importante:

* A ordem de publicação de runbooks só é importante para o fluxo de trabalho do PowerShell e runbooks gráficos de fluxo de trabalho do PowerShell.

Ao chamar um runbook filho de fluxo de trabalho gráfico ou do PowerShell usando a execução embutida, você usa o nome do runbook.  Ao chamar um runbook filho do PowerShell, você deve iniciar seu nome com *.\\* para especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho de teste que aceita três parâmetros, um objeto complexo, um inteiro e um booliano. A saída do runbook subordinado é atribuída a uma variável.  Nesse caso, o runbook filho é um runbook de fluxo de trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

A seguir está o mesmo exemplo usando um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciando um runbook filho usando o cmdlet

> [!IMPORTANT]
> Se você estiver invocando um runbook filho com o cmdlet `Start-AzureRmAutomationRunbook` com a opção `-Wait` e os resultados do runbook filho for um objeto, você poderá encontrar erros. Para contornar o erro, consulte [Runbooks filho com a saída do objeto](troubleshoot/runbooks.md#child-runbook-object) para saber como implementar a lógica para sondar os resultados e usar [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Você pode usar o cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) para iniciar um runbook, conforme descrito em [para iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Há dois modos de uso para esse cmdlet.  Em um modo, o cmdlet retorna a ID do trabalho quando o trabalho filho é criado para o runbook filho.  No outro modo, que você habilita especificando o parâmetro **-Wait** , o cmdlet aguarda até que o trabalho filho seja concluído e retorna a saída do runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet é executado em um trabalho separado do runbook pai. Esse comportamento resulta em mais trabalhos do que iniciar o runbook embutido e torna-os mais difíceis de rastrear. O pai pode iniciar mais de um runbook filho de forma assíncrona sem aguardar a conclusão de cada um. Para esse mesmo tipo de execução paralela de invocar os runbooks subordinados inline, o runbook principal seria necessário usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

A saída de runbooks filho não é retornada ao runbook pai de forma confiável devido ao tempo. Também determinadas variáveis como $VerbosePreference, $WarningPreference e outras não podem ser propagadas para os runbooks filho. Para evitar esses problemas, você pode iniciar os runbooks filho como trabalhos de automação separados usando o cmdlet `Start-AzureRmAutomationRunbook` com a opção `-Wait`. Isso bloqueará o runbook pai até que o runbook filho seja concluído.

Se não quiser que o runbook pai seja bloqueado na espera, você poderá iniciar o runbook filho usando `Start-AzureRmAutomationRunbook` cmdlet sem a opção `-Wait`. Em seguida, você precisaria usar `Get-AzureRmAutomationJob` para aguardar a conclusão do trabalho e `Get-AzureRmAutomationJobOutput` e `Get-AzureRmAutomationJobOutputRecord` recuperar os resultados.

Parâmetros para um runbook subordinado iniciado com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [parâmetros do Runbook](start-runbooks.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tem um parâmetro com um tipo de dados complexos, em seguida, tem de ser chamado inline.

O contexto de assinatura pode ser perdido ao iniciar runbooks filho como trabalhos separados. Para que o runbook filho execute os cmdlets do Azure RM em uma assinatura específica do Azure, o runbook filho deve se autenticar para essa assinatura independentemente do runbook pai.

Se os trabalhos dentro da mesma conta de automação funcionarem com mais de uma assinatura, selecionar uma assinatura em um trabalho poderá alterar o contexto de assinatura selecionado no momento para outros trabalhos. Para evitar esse problema, use `Disable-AzureRmContextAutosave –Scope Processsave` no início de cada runbook. Essa ação salva apenas o contexto para a execução do runbook.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho com parâmetros e, em seguida, aguarda que ele seja concluído usando o parâmetro Start-AzureRmAutomationRunbook-Wait. Depois de concluído, sua saída é coletada do runbook filho. Para usar `Start-AzureRmAutomationRunbook`, você deve se autenticar em sua assinatura do Azure.

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

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook filho

A tabela seguinte resume as diferenças entre os dois métodos para chamar um runbook a partir de outro runbook.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| Tarefa |Runbook subordinado é executado na mesma tarefa como pai. |Uma tarefa separada é criada para o runbook subordinado. |
| Execução |O runbook principal aguarda que o runbook subordinado seja concluído antes de continuar. |O runbook pai continua imediatamente após o runbook filho ser iniciado *ou* o runbook pai aguarda a conclusão do trabalho filho. |
| Saída |O runbook principal pode obter resultados diretamente do runbook subordinado. |O runbook pai deve recuperar a saída do trabalho de runbook filho *ou* o runbook pai pode obter a saída diretamente do runbook filho. |
| Parâmetros |Valores para parâmetros do runbook subordinado são especificados em separado e podem utilizar qualquer tipo de dados. |Os valores para os parâmetros de runbook filho precisam ser combinados em uma única tabela de hash. Essa tabela de hash só pode incluir tipos de dados simples, de matriz e de objeto que usam a serialização JSON. |
| Conta de Automatização |O runbook pai só pode usar o runbook filho na mesma conta de automação. |Os runbooks pai podem usar um runbook filho de qualquer conta de automação da mesma assinatura do Azure e até mesmo uma assinatura diferente à qual você tem uma conexão. |
| Publicação |Runbook subordinado tem de ser publicado antes de o runbook principal. |O runbook filho deve ser publicado a qualquer momento antes do runbook pai ser iniciado. |

## <a name="next-steps"></a>Passos seguintes

* [Iniciando um runbook na automação do Azure](start-runbooks.md)
* [Saída e mensagens do runbook na automação do Azure](automation-runbook-output-and-messages.md)

