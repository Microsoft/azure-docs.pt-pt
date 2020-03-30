---
title: Recursos de variáveis na Automatização do Azure
description: Os ativos variáveis são valores disponíveis para todos os livros de execução e configurações dSC na Automação Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas tanto na autoria textual como gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365831"
---
# <a name="variable-assets-in-azure-automation"></a>Recursos de variáveis na Automatização do Azure

Os ativos variáveis são valores disponíveis para todos os livros de execução e configurações de DSC na sua conta De automação. Pode geri-los a partir do portal Azure, a partir do PowerShell, dentro de um livro de execução, ou numa configuração DSC.

As variáveis de automação são úteis para os seguintes cenários:

- Partilhar um valor entre vários livros de execução ou configurações de DSC.

- Partilhar um valor entre vários trabalhos a partir do mesmo livro de execução ou configuração DSC.

- Gerir um valor utilizado por livros de execução ou configurações DSC a partir do portal ou da linha de comando PowerShell. Um exemplo é um conjunto de itens de configuração comuns, tais como uma lista específica de nomes VM, um grupo de recursos específico, um nome de domínio AD, e muito mais.  

A Azure Automation persiste variáveis e as disponibiliza mesmo que uma configuração de livro ou DSC falhe. Este comportamento permite que uma configuração de um livro de execução ou DSC desempor um valor que é então usado por outro livro de execução, ou pela mesma configuração de releir ou DSC da próxima vez que funcionar.

A Azure Automation armazena cada variável encriptada de forma segura. Ao criar uma variável, pode especificar a sua encriptação e armazenamento pela Azure Automation como um ativo seguro. Outros ativos seguros incluem credenciais, certificados e ligações. A Azure Automation encripta estes ativos e armazena-os utilizando uma chave única que é gerada para cada conta De Automação. A chave está armazenada num cofre de chaves gerido pelo sistema. Antes de armazenar um ativo seguro, a Azure Automation carrega a chave a partir do Cofre de Chaves e depois usa-a para encriptar o ativo. 

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipos variáveis

Quando cria uma variável com o portal Azure, deve especificar um tipo de dados da lista de dropdown para que o portal possa apresentar o controlo adequado para a introdução do valor variável. Seguem-se os tipos variáveis disponíveis na Automação Azure:

* Cadeia
* Número inteiro
* DateTime
* Booleano
* Null

A variável não se restringe ao tipo de dados designado. Tem de definir a variável utilizando o Windows PowerShell se pretender especificar um valor de um tipo diferente. Se indicar `Not defined`que o valor da variável está definido para Nulo, e deve definir o valor `Set-AutomationVariable` com o Cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou a atividade.

Não é possível utilizar o portal Azure para criar ou alterar o valor para um tipo variável complexo. No entanto, pode fornecer um valor de qualquer tipo utilizando o Windows PowerShell. Os tipos complexos são recuperados como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Pode armazenar vários valores numa única variável, criando uma matriz ou hashtable e economizando-as na variável.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell cmdlets que criam e gerem ativos variáveis

Para o módulo Az, os cmdlets na tabela seguinte são usados para criar e gerir ativos variáveis Automation com o Windows PowerShell. Eles enviam como parte do [módulo Az.Automation,](/powershell/azure/overview)que está disponível para uso em livros de automação e configurações DSC.

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Obtém o valor de uma variável existente. Não pode usar este cmdlet para recuperar o valor de uma variável encriptada. A única maneira de o `Get-AutomationVariable` fazer é utilizando a atividade numa configuração de livro ou DSC. |
|[Nova AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Cria uma nova variável e define o seu valor.|
|[Remover-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Remove uma variável existente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Define o valor de uma variável existente. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Atividades de acesso a variáveis em livros de execução e configurações dSC

As atividades na tabela seguinte são utilizadas para aceder a variáveis em livros de execução e configurações de DSC. Os cmdlets para estas atividades `Orchestrator.AssetManagement.Cmdlets`vêm com o módulo global.

| Atividade | Descrição |
|:---|:---|
|`Get-AutomationVariable`|Obtém o valor de uma variável existente.|
|`Set-AutomationVariable`|Define o valor de uma variável existente.|

> [!NOTE]
> Evite utilizar variáveis `Name` no `Get-AutomationVariable` parâmetro de uma configuração de releiro ou DSC. A utilização deste parâmetro pode complicar a descoberta de dependências entre os livros de execução ou as configurações de DSC e as variáveis de Automação no momento do design.

Note `Get-AutomationVariable` que não funciona no PowerShell, mas apenas numa configuração de livro de execução ou DSC. Por exemplo, para ver o valor de uma variável encriptada, pode criar um livro de execução para obter a variável e depois escrevê-lo para o fluxo de saída:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funções para aceder a variáveis em livros de execução Python 2

As funções na tabela seguinte são usadas para aceder a variáveis num livro de execução Python 2.

|Funções Python 2|Descrição|
|:---|:---|
|`automationassets.get_automation_variable`|Obtém o valor de uma variável existente. |
|`automationassets.set_automation_variable`|Define o valor de uma variável existente. |

> [!NOTE]
> Tem de `automationassets` importar o módulo no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="working-with-automation-variables"></a>Trabalhar com variáveis de Automação

>[!NOTE]
>Se pretender remover a encriptação para uma variável, deve eliminar a variável e recriá-la como não encriptada.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Criar uma nova variável usando o portal Azure

1. Na sua conta de Automação, clique no azulejo **Assets,** em seguida, na lâmina **Do Ativo,** e selecione **Variáveis**.
2. No azulejo **Variáveis,** selecione **Adicionar uma variável**.
3. Complete as opções na lâmina **New Variable** e clique em **Criar** para salvar a nova variável.

> [!NOTE]
> Uma vez guardado uma variável encriptada, não pode ser visto no portal. Só pode ser atualizado.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Criar e usar uma variável no Windows PowerShell

Um script PowerShell `New-AzAutomationVariable` utiliza o cmdlet, ou o seu equivalente de módulo AzureRM, para criar uma nova variável e definir o seu valor inicial. Se a variável estiver encriptada, `Encrypted` a chamada deve utilizar o parâmetro.

O script recupera o valor da variável usando [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Se o valor for um tipo simples, o cmdlet recupera esse mesmo tipo. Se for um tipo complexo, um `PSCustomObject` tipo é recuperado.

>[!NOTE]
>Um script PowerShell não consegue obter um valor encriptado. A única maneira de fazer `Get-AutomationVariable` isso é usar uma atividade numa configuração de livro ou DSC.

O exemplo seguinte mostra como criar uma variável de tipo String e, em seguida, devolver o seu valor.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

O exemplo seguinte mostra como criar uma variável com um tipo complexo e depois recuperar as suas propriedades. Neste caso, é utilizado um objeto de máquina virtual da [Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Criar e usar uma variável numa configuração de livro de execução ou DSC

A única maneira de criar uma nova variável dentro de um `New-AzAutomationVariable` livro de execução ou de uma configuração DSC é usar o cmdlet, ou o seu módulo AzureRM equivalente. O script usa este cmdlet para definir o valor inicial da variável. O script pode então `Get-AzAutomationVariable`recuperar o valor usando . Se o valor for simples, esse mesmo tipo é recuperado. Se é um tipo complexo, `PSCustomObject` então um tipo é recuperado.

>[!NOTE]
>A única maneira de recuperar um valor `Get-AutomationVariable` encriptado é utilizando a atividade na configuração do livro de execução ou DSC. 

### <a name="textual-runbook-samples"></a>Amostras de livro textual

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Definir e recuperar um valor simples a partir de uma variável

Os comandos de amostra seguem mostram como definir e recuperar uma variável num livro textual. Esta amostra assume a criação de `NumberOfIterations` variáveis inteiros nomeadas e `NumberOfRunnings` uma variável de cadeia chamada `SampleMessage`.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Definir e recuperar uma variável em um livro de corridas Python 2

A amostra seguinte mostra como usar uma variável, definir uma variável, e lidar com uma exceção para uma variável inexistente num livro python 2.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Amostras gráficas de livro

Num livro gráfico, pode adicionar `Get-AutomationVariable` a `Set-AutomationVariable` ou atividade. Basta clicar na variável no painel da Biblioteca do editor gráfico e selecionar a atividade que deseja.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Definir valores numa variável

A imagem seguinte mostra atividades de amostra para atualizar uma variável com um valor simples num livro gráfico. Nesta amostra, `Get-AzVM` recupera uma única máquina virtual Azure e guarda o nome do computador para uma variável de cadeia de automação existente. Não importa se a [ligação é um oleoduto ou sequência,](../automation-graphical-authoring-intro.md#links-and-workflow) uma vez que o código só espera um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação de atividades em autoria gráfica, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](../automation-first-runbook-graphical.md)
