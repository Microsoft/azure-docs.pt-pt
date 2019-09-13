---
title: Ativos de variável na automação do Azure
description: Ativos de variável são valores que estão disponíveis para todos os runbooks e configurações DSC na automação do Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas na criação de texto e gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe008d20ab43636b59861bcc5a7914ba0fca17e
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910068"
---
# <a name="variable-assets-in-azure-automation"></a>Ativos de variável na automação do Azure

Ativos de variável são valores que estão disponíveis para todos os runbooks e configurações DSC em sua conta de automação. Eles podem ser gerenciados no portal do Azure, no PowerShell, em um runbook ou em uma configuração DSC. Variáveis de automatização são úteis para os seguintes cenários:

- Compartilhe um valor entre vários runbooks ou configurações DSC.

- Compartilhe um valor entre vários trabalhos do mesmo runbook ou configuração DSC.

- Gerencie um valor do portal ou da linha de comando do PowerShell que é usada por runbooks ou configurações DSC, como um conjunto de itens de configuração comuns, como lista específica de nomes de VM, um grupo de recursos específico, um nome de domínio do AD e muito mais.  

Como as variáveis de automação são persistidas, elas estarão disponíveis mesmo se o runbook ou a configuração DSC falhar. Esse comportamento permite que um valor seja definido por um runbook que é usado por outro, ou é usado pelo mesmo runbook ou configuração DSC na próxima vez em que for executado.

Quando uma variável é criada, você pode especificar que ela seja armazenada criptografada. As variáveis criptografadas são armazenadas com segurança na automação do Azure e seu valor não pode ser recuperado do cmdlet [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) que é fornecido como parte do módulo Azure PowerShell. A única maneira de recuperar um valor criptografado é da atividade **Get-AutomationVariable** em um runbook ou configuração DSC. Se você quiser alterar uma variável criptografada para não criptografado, será possível excluir e recriar a variável como não criptografada.

>[!NOTE]
>Os ativos seguros na automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada de Key Vault e, em seguida, usada para criptografar o ativo. Esse processo é gerenciado pela automação do Azure.

## <a name="variable-types"></a>Tipos de variáveis

Ao criar uma variável com o portal do Azure, você deve especificar um tipo de dados na lista suspensa para que o portal possa exibir o controle apropriado para inserir o valor da variável. A variável não está restrita a este tipo de dados. Você deve definir a variável usando o Windows PowerShell se desejar especificar um valor de um tipo diferente. Se você especificar **não definido**, o valor da variável definirá como **$NULL**, e você deverá definir o valor com o cmdlet [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) ou a atividade **set-AutomationVariable** . Não é possível criar ou alterar o valor de um tipo de variável complexa no portal, mas você pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos são retornados como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Você pode armazenar vários valores em uma única variável criando uma matriz ou tabela de hash e salvando-a na variável.

Veja a seguir uma lista de tipos de variáveis disponíveis na automação:

* Cadeia
* Integer
* DateTime
* Booleano
* Null

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do PowerShell do AzureRM

Para AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo AzureRM. Automation](/powershell/azure/overview), que está disponível para uso em Runbooks de automação e configurações DSC.

| Cmdlets | Descrição |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Obtém o valor de uma variável existente.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Cria uma nova variável e define seu valor.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Remove uma variável existente.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Define o valor de uma variável existente.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar variáveis em um runbook e configurações DSC. A diferença entre os cmdlets Get-AzureRmAutomationVariable e Get-AutomationVariable é esclarecida acima no início deste documento.

| Atividades | Descrição |
|:---|:---|
|Get-AutomationVariable|Obtém o valor de uma variável existente.|
|Set-AutomationVariable|Define o valor de uma variável existente.|

> [!NOTE]
> Evite usar variáveis no parâmetro – Name de **Get-AutomationVariable** em um runbook ou configuração DSC, pois isso pode complicar a descoberta de dependências entre runbooks ou configuração DSC e variáveis de automação em tempo de design.

As funções na tabela a seguir são usadas para acessar e recuperar variáveis em um runbook Python2.

|Funções Python2|Descrição|
|:---|:---|
|automationassets.get_automation_variable|Obtém o valor de uma variável existente. |
|automationassets.set_automation_variable|Define o valor de uma variável existente. |

> [!NOTE]
> Você deve importar o módulo "automationassets" na parte superior do seu runbook do Python para acessar as funções de ativo.

## <a name="creating-a-new-automation-variable"></a>Criando uma nova variável de automação

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Para criar uma nova variável com o portal do Azure

1. Na sua conta de automação, clique no bloco **ativos** e, em seguida, na folha **ativos** , selecione **variáveis**.
2. No bloco **variáveis** , selecione **Adicionar uma variável**.
3. Conclua as opções na folha **nova variável** e clique em **criar** para salvar a nova variável.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Para criar uma nova variável com o Windows PowerShell

O cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cria uma nova variável e define seu valor inicial. Você pode recuperar o valor usando [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Se o valor for um tipo simples, o mesmo tipo será retornado. Se for um tipo complexo, um **PSCustomObject** será retornado.

Os comandos de exemplo a seguir mostram como criar uma variável do tipo cadeia de caracteres e retornar seu valor.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Os comandos de exemplo a seguir mostram como criar uma variável com um tipo complexo e, em seguida, retornar suas propriedades. Nesse caso, um objeto de máquina virtual de **Get-AzureRmVm** é usado.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Usando uma variável em um runbook ou configuração DSC

Use a atividade **set-AutomationVariable** para definir o valor de uma variável de automação em um runbook do PowerShell ou configuração DSC e **Get-AutomationVariable** para recuperá-la. Você não deve usar os cmdlets **set-AzureRMAutomationVariable** ou **Get-AzureRMAutomationVariable** em um runbook ou configuração DSC, pois eles são menos eficientes do que as atividades de fluxo de trabalho. Você também não pode recuperar o valor de variáveis seguras com **Get-AzureRMAutomationVariable**. A única maneira de criar uma nova variável de dentro de um runbook ou configuração DSC é usar o cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) .

### <a name="textual-runbook-samples"></a>Exemplos de runbook de texto

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Configurando e recuperando um valor simples de uma variável

Os comandos de exemplo a seguir mostram como definir e recuperar uma variável em um runbook textual. Neste exemplo, supõe-se que as variáveis do tipo inteiro denominado *NumberOfIterations* e *NumberOfRunnings* e uma variável do tipo cadeia de caracteres denominada *SampleMessage* foram criadas.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Configurando e recuperando uma variável em Python2

O código de exemplo a seguir mostra como usar uma variável, definir uma variável e manipular uma exceção para uma variável não existente em um runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Em um runbook gráfico, você adiciona o **Get-AutomationVariable** ou o **set-AutomationVariable** clicando com o botão direito do mouse na variável no painel Biblioteca do editor gráfico e selecionando a atividade desejada.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Definindo valores em uma variável

A imagem a seguir mostra as atividades de exemplo para atualizar uma variável com um valor simples em um runbook gráfico. Neste exemplo, **Get-AzureRmVM** recupera uma única máquina virtual do Azure e o nome do computador é salvo em uma variável de automação existente com um tipo de cadeia de caracteres. Não importa se o [link é um pipeline ou uma sequência](../automation-graphical-authoring-intro.md#links-and-workflow) , pois você só espera um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximos Passos

- Para saber mais sobre como conectar atividades juntas na criação gráfica, confira [links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow)
- Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
