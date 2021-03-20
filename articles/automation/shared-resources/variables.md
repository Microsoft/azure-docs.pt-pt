---
title: Gerir variáveis em Azure Automation
description: Este artigo diz como trabalhar com variáveis em runbooks e configurações DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: 6db0c82c034aab97deee1be4aa8bdc54368521bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98131530"
---
# <a name="manage-variables-in-azure-automation"></a>Gerir variáveis em Azure Automation

Os ativos variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC na sua conta Dem automação. Pode geri-los a partir do portal Azure, a partir de PowerShell, dentro de um runbook, ou numa configuração DSC.

As variáveis de automatização são úteis para os seguintes cenários:

- Partilhar um valor entre vários runbooks ou configurações de DSC.

- Partilhar um valor entre vários trabalhos a partir do mesmo runbook ou configuração DSC.

- Gerir um valor utilizado por runbooks ou configurações DSC a partir do portal ou da linha de comando PowerShell. Um exemplo é um conjunto de itens de configuração comuns, tais como uma lista específica de nomes VM, um grupo de recursos específico, um nome de domínio AD, e muito mais.  

A Azure Automation persiste em variáveis e disponibiliza-as mesmo que uma configuração de runbook ou DSC falhe. Este comportamento permite que um livro de execução ou configuração DSC desaje assim seja utilizado por outro runbook, ou pela mesma configuração de runbook ou DSC da próxima vez que for executado.

A Azure Automation armazena cada variável encriptada de forma segura. Quando cria uma variável, pode especificar a sua encriptação e armazenamento pela Azure Automation como um ativo seguro. Depois de criar a variável, não pode alterar o seu estado de encriptação sem recriar a variável. Se tiver variáveis de conta de Automação que armazenam dados sensíveis que ainda não estão encriptados, então tem de as eliminar e recriar como variáveis encriptadas. Uma recomendação do Azure Security Center é encriptar todas as variáveis Azure Automation, tal como descritas nas [variáveis de conta Demôm automação, devem ser encriptadas](../../security-center/recommendations-reference.md#recs-compute). Se tiver variáveis não encriptadas que deseja excluídas desta recomendação de segurança, consulte [isentar um recurso de recomendações e obter pontuação segura](../../security-center/exempt-resource.md) para criar uma regra de isenção.

>[!NOTE]
>Os ativos seguros na Azure Automation incluem credenciais, certificados, conexões e variáveis encriptadas. Estes ativos são encriptados e armazenados na Azure Automation utilizando uma chave única que é gerada para cada conta Dem automação. A Azure Automation armazena a chave no Key Vault gerido pelo sistema. Antes de armazenar um ativo seguro, a Automatização carrega a chave do Key Vault e utiliza-a para encriptar o ativo.

## <a name="variable-types"></a>Tipos variáveis

Quando cria uma variável com o portal Azure, deve especificar um tipo de dados da lista de abandono para que o portal possa apresentar o controlo adequado para introduzir o valor variável. Seguem-se os tipos variáveis disponíveis na Azure Automation:

* String
* Número inteiro
* DateTime
* Booleano
* Nulo

A variável não se restringe ao tipo de dados especificado. Tem de definir a variável utilizando o Windows PowerShell se pretender especificar um valor de um tipo diferente. Se `Not defined` indicar, o valor da variável é definido para Nulo. Deve definir o valor com o [cmdlet Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) ou com o `Set-AutomationVariable` cmdlet interno.

Não é possível utilizar o portal Azure para criar ou alterar o valor para um tipo variável complexo. No entanto, pode fornecer um valor de qualquer tipo utilizando o Windows PowerShell. Tipos complexos são recuperados como um [Newtonsoft.Js. Linq.JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) para um tipo de objeto complexo em vez de um PSObject do tipo [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Pode armazenar vários valores a uma única variável criando uma matriz ou hashtable e guardando-o para a variável.

>[!NOTE]
>As variáveis de nome VM podem ser no máximo 80 caracteres. As variáveis de grupo de recursos podem ser no máximo 90 caracteres. Consulte [as regras de nomeação e as restrições para os recursos da Azure.](../../azure-resource-manager/management/resource-name-rules.md)

## <a name="powershell-cmdlets-to-access-variables"></a>PowerShell cmdlets para aceder a variáveis

Os cmdlets na tabela seguinte criam e gerem variáveis de automação com PowerShell. Eles enviam como parte dos [módulos Az.](modules.md#az-modules)

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Obtém o valor de uma variável existente. Se o valor for um tipo simples, esse mesmo tipo é recuperado. Se for um tipo complexo, um `PSCustomObject` tipo é recuperado. <br>**Nota:** Não pode usar este cmdlet para recuperar o valor de uma variável encriptada. A única maneira de o fazer é utilizando o `Get-AutomationVariable` cmdlet interno num livro de bordo ou configuração DSC. Consulte [as cmdlets internas para aceder a variáveis](#internal-cmdlets-to-access-variables). |
|[Nova AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Cria uma nova variável e define o seu valor.|
|[Remover-AzAutomationVariável](/powershell/module/az.automation/remove-azautomationvariable)| Remove uma variável existente.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Define o valor de uma variável existente. |

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlets internos para aceder a variáveis

Os cmdlets internos na tabela seguinte são utilizados para aceder a variáveis nos seus runbooks e configurações DSC. Estes cmdlets vêm com o módulo `Orchestrator.AssetManagement.Cmdlets` global. Para obter mais informações, consulte [as cmdlets internas.](modules.md#internal-cmdlets)

| Cmdlet Interno | Description |
|:---|:---|
|`Get-AutomationVariable`|Obtém o valor de uma variável existente.|
|`Set-AutomationVariable`|Define o valor de uma variável existente.|

> [!NOTE]
> Evite utilizar variáveis no `Name` parâmetro de um livro de bordo ou `Get-AutomationVariable` configuração DSC. A utilização das variáveis pode complicar a descoberta de dependências entre runbooks e variáveis de automação no momento do design.

`Get-AutomationVariable` não funciona em PowerShell, mas apenas num livro de execução ou configuração DSC. Por exemplo, para ver o valor de uma variável encriptada, pode criar um livro de execução para obter a variável e, em seguida, escrevê-lo para o fluxo de saída:

```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-functions-to-access-variables"></a>Funções python para aceder a variáveis

As funções na tabela seguinte são utilizadas para aceder a variáveis num livro de bordo Python 2 e 3. Os livros de 100 python estão atualmente em pré-visualização.

|Funções Python|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Obtém o valor de uma variável existente. |
|`automationassets.set_automation_variable`|Define o valor de uma variável existente. |

> [!NOTE]
> Deve importar o `automationassets` módulo no topo do seu livro de bordo Python para aceder às funções do ativo.

## <a name="create-and-get-a-variable"></a>Criar e obter uma variável

>[!NOTE]
>Se quiser remover a encriptação para uma variável, deve eliminar a variável e recriá-la como não encriptada.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Criar e obter uma variável usando o portal Azure

1. Na sua conta de Automação, no painel esquerdo selecione Variáveis em **Recursos Partilhados.** 
2. Na página **'Variáveis',** **selecione Adicionar uma variável.**
3. Preencha as opções na página **Nova Variável** e, em seguida, selecione **Criar** para guardar a nova variável.

> [!NOTE]
> Uma vez guardada uma variável encriptada, não pode ser vista no portal. Só pode ser atualizado.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Criar e obter uma variável no Windows PowerShell

O seu runbook ou configuração DSC utiliza o `New-AzAutomationVariable` cmdlet para criar uma nova variável e definir o seu valor inicial. Se a variável estiver encriptada, a chamada deve utilizar o `Encrypted` parâmetro. O seu script pode recuperar o valor da variável utilizando `Get-AzAutomationVariable` .

>[!NOTE]
>Um script PowerShell não consegue recuperar um valor encriptado. A única maneira de fazer isto é usar o `Get-AutomationVariable` cmdlet interno.

O exemplo a seguir mostra como criar uma variável de corda e, em seguida, devolver o seu valor.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

O exemplo a seguir mostra como criar uma variável com um tipo complexo e, em seguida, recuperar as suas propriedades. Neste caso, é utilizado um objeto de máquina virtual da [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) especificando um subconjunto das suas propriedades.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable"

$vmName = $vmValue.Name
$vmExtensions = $vmValue.Extensions
```

## <a name="textual-runbook-examples"></a>Exemplos de runbook textual

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como definir e recuperar uma variável num livro de texto. Este exemplo pressupõe a criação de variáveis inteiros nomeadas `NumberOfIterations` `NumberOfRunnings` e uma variável de cordas denominada `SampleMessage` .

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

# <a name="python-2"></a>[Python 2](#tab/python2)

A amostra que se segue mostra como obter uma variável, definir uma variável, e lidar com uma exceção para uma variável inexistente num livro de bordo python 2.

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

# <a name="python-3"></a>[Python 3](#tab/python3)

A amostra que se segue mostra como obter uma variável, definir uma variável, e lidar com uma exceção para uma variável inexistente num livro de bordo Python 3 (pré-visualização).

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
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Exemplos gráficos de runbook

Num livro gráfico, pode adicionar atividades para os cmdlets internos `Get-AutomationVariable` ou `Set-AutomationVariable` . Basta clicar à direita em cada variável no painel de biblioteca do editor gráfico e selecione a atividade que deseja.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

A imagem que se segue mostra atividades de exemplo para atualizar uma variável com um valor simples num runbook gráfico. Neste exemplo, a atividade para `Get-AzVM`  recuperar uma única máquina virtual Azure e guarda o nome do computador para uma variável de cadeia de automação existente. Não importa se a [ligação é um oleoduto ou sequência, uma](../automation-graphical-authoring-intro.md#use-links-for-workflow) vez que o código apenas espera um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os cmdlets utilizados para aceder a variáveis, consulte [Gerir módulos na Azure Automation.](modules.md)
* Para obter informações gerais sobre os runbooks, consulte [a execução do Runbook na Azure Automation](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações do DSC, consulte [a visão geral da configuração do Estado da Automação Azure](../automation-dsc-overview.md).
