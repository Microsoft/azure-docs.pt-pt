---
title: Gerir variáveis na Automação Azure
description: Este artigo diz como trabalhar com variáveis em livros de execução e configurações de DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cb89caa0a2fe5cde5bcea92e08c0df117215d916
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713306"
---
# <a name="manage-variables-in-azure-automation"></a>Gerir variáveis na Automação Azure

Os ativos variáveis são valores disponíveis para todos os livros de execução e configurações de DSC na sua conta De automação. Pode geri-los a partir do portal Azure, a partir do PowerShell, dentro de um livro de execução, ou numa configuração DSC.

As variáveis de automação são úteis para os seguintes cenários:

- Partilhar um valor entre vários livros de execução ou configurações de DSC.

- Partilhar um valor entre vários trabalhos a partir do mesmo livro de execução ou configuração DSC.

- Gerir um valor utilizado por livros de execução ou configurações DSC a partir do portal ou da linha de comando PowerShell. Um exemplo é um conjunto de itens de configuração comuns, tais como uma lista específica de nomes VM, um grupo de recursos específico, um nome de domínio AD, e muito mais.  

A Azure Automation persiste variáveis e as disponibiliza mesmo que uma configuração de livro ou DSC falhe. Este comportamento permite que uma configuração de um livro de execução ou DSC desempor um valor que é então usado por outro livro de execução, ou pela mesma configuração de releir ou DSC da próxima vez que funcionar.

A Azure Automation armazena cada variável encriptada de forma segura. Ao criar uma variável, pode especificar a sua encriptação e armazenamento pela Azure Automation como um ativo seguro. 

>[!NOTE]
>Os ativos seguros na Automatização Azure incluem credenciais, certificados, ligações e variáveis encriptadas. Estes ativos são encriptados e armazenados na Automatização Azure utilizando uma chave única que é gerada para cada conta De Automação. A Azure Automation armazena a chave no Cofre chave gerido pelo sistema. Antes de armazenar um ativo seguro, a Automation carrega a chave a partir do Key Vault e depois usa-a para encriptar o ativo. 

## <a name="variable-types"></a>Tipos variáveis

Quando cria uma variável com o portal Azure, deve especificar um tipo de dados da lista de dropdown para que o portal possa apresentar o controlo adequado para a introdução do valor variável. Seguem-se os tipos variáveis disponíveis na Automação Azure:

* String
* Número inteiro
* DateTime
* Booleano
* Null

A variável não se restringe ao tipo de dados especificado. Tem de definir a variável utilizando o Windows PowerShell se pretender especificar um valor de um tipo diferente. Se `Not defined` indicar, o valor da variável é definido para Nulo. Deve definir o valor com o [cmdlet Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou o `Set-AutomationVariable` cmdlet interno.

Não é possível utilizar o portal Azure para criar ou alterar o valor para um tipo variável complexo. No entanto, pode fornecer um valor de qualquer tipo utilizando o Windows PowerShell. Os tipos complexos são recuperados como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Pode armazenar vários valores numa única variável, criando uma matriz ou hashtable e economizando-as na variável.

>[!NOTE]
>Variáveis de nome VM podem ser um máximo de 80 caracteres. As variáveis do grupo de recursos podem ser um máximo de 90 caracteres. Consulte as regras e restrições de [nomeação dos recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell cmdlets para aceder a variáveis

Os cmdlets na tabela seguinte criam e gerem variáveis de Automação com powerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Obtém o valor de uma variável existente. Se o valor for simples, esse mesmo tipo é recuperado. Se for um tipo complexo, um `PSCustomObject` tipo é recuperado. <br>**Nota:**  Não pode usar este cmdlet para recuperar o valor de uma variável encriptada. A única maneira de o fazer é utilizando o cmdlet interno numa configuração de `Get-AutomationVariable` livro ou DSC. Consulte [cmdlets internos para aceder a variáveis](#internal-cmdlets-to-access-variables). |
|[Nova AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Cria uma nova variável e define o seu valor.|
|[Remover-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Remove uma variável existente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Define o valor de uma variável existente. |

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlets internos para aceder a variáveis

Os cmdlets internos na tabela seguinte são utilizados para aceder a variáveis nos seus livros de execução e configurações dSC. Estes cmdlets vêm com o módulo `Orchestrator.AssetManagement.Cmdlets` global. Para mais informações, consulte [cmdlets internos](modules.md#internal-cmdlets).

| Cmdlet Interno | Descrição |
|:---|:---|
|`Get-AutomationVariable`|Obtém o valor de uma variável existente.|
|`Set-AutomationVariable`|Define o valor de uma variável existente.|

> [!NOTE]
> Evite utilizar variáveis no parâmetro de uma configuração de `Name` `Get-AutomationVariable` releiro ou DSC. O uso das variáveis pode complicar a descoberta de dependências entre livros de corridas e variáveis de Automação no momento do projeto.

`Get-AutomationVariable`não funciona na PowerShell, mas apenas numa configuração de livro de execução ou DSC. Por exemplo, para ver o valor de uma variável encriptada, pode criar um livro de execução para obter a variável e depois escrevê-lo para o fluxo de saída:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Funções Python 2 para aceder a variáveis

As funções na tabela seguinte são usadas para aceder a variáveis num livro de execução Python 2.

|Funções Python 2|Descrição|
|:---|:---|
|`automationassets.get_automation_variable`|Obtém o valor de uma variável existente. |
|`automationassets.set_automation_variable`|Define o valor de uma variável existente. |

> [!NOTE]
> Tem de importar o `automationassets` módulo no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="create-and-get-a-variable"></a>Criar e obter uma variável

>[!NOTE]
>Se pretender remover a encriptação para uma variável, deve eliminar a variável e recriá-la como não encriptada.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Crie e obtenha uma variável usando o portal Azure

1. Na sua conta de Automação, clique no azulejo **Assets,** em seguida, na lâmina **Do Ativo,** e selecione **Variáveis**.
2. No azulejo **Variáveis,** selecione **Adicionar uma variável**.
3. Complete as opções na lâmina **New Variable** e clique em **Criar** para salvar a nova variável.

> [!NOTE]
> Uma vez guardado uma variável encriptada, não pode ser visto no portal. Só pode ser atualizado.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Criar e obter uma variável no Windows PowerShell

A configuração do seu livro de execução ou DSC utiliza o `New-AzAutomationVariable` cmdlet para criar uma nova variável e definir o seu valor inicial. Se a variável estiver encriptada, a chamada deve utilizar o `Encrypted` parâmetro. O seu script pode recuperar o valor da variável usando `Get-AzAutomationVariable` . 

>[!NOTE]
>Um script PowerShell não consegue obter um valor encriptado. A única maneira de o fazer é usar o `Get-AutomationVariable` cmdlet interno.

O exemplo seguinte mostra como criar uma variável de cadeia e, em seguida, devolver o seu valor.

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

## <a name="textual-runbook-examples"></a>Exemplos de livro textual

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>Recuperar e definir um valor simples a partir de uma variável

O exemplo seguinte mostra como definir e recuperar uma variável num livro textual. Este exemplo assume a criação de variáveis inteiros nomeadas `NumberOfIterations` e `NumberOfRunnings` uma variável de cadeia chamada `SampleMessage` .

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>Recuperar e definir uma variável num livro de corridas Python 2

A amostra seguinte mostra como obter uma variável, definir uma variável, e lidar com uma exceção para uma variável inexistente em um livro python 2.

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

## <a name="graphical-runbook-examples"></a>Exemplos de livro gráfico

Num livro gráfico, pode adicionar atividades para os cmdlets internos `Get-AutomationVariable` ou `Set-AutomationVariable` . Basta clicar em cada variável no painel da Biblioteca do editor gráfico e selecione a atividade que deseja.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

A imagem que se segue mostra atividades de exemplo para atualizar uma variável com um valor simples num livro gráfico. Neste exemplo, a atividade para `Get-AzVM` recuperar uma única máquina virtual Azure e guarda o nome do computador para uma variável de cadeia de automação existente. Não importa se a [ligação é um oleoduto ou sequência,](../automation-graphical-authoring-intro.md#use-links-for-workflow) uma vez que o código só espera um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximos passos

* [Utilizar módulos na Automatização do Azure](modules.md)
* [Execução de runbooks na Automatização do Azure](../automation-runbook-execution.md)
* [Visão geral da configuração do Estado da Automação Azure](../automation-dsc-overview.md) 
