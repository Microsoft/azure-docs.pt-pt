---
title: Ativos variáveis na Automação Azure
description: Os ativos variáveis são valores disponíveis para todos os livros de execução e configurações dSC na Automação Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas tanto na autoria textual como gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ce56b64502904308f45c74a5471447d93419452
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303057"
---
# <a name="variable-assets-in-azure-automation"></a>Ativos variáveis na Automação Azure

Os ativos variáveis são valores disponíveis para todos os livros de execução e configurações de DSC na sua conta De automação. Pode geri-los a partir do portal Azure, a partir do PowerShell, dentro de um livro de execução, ou numa configuração DSC.

Variáveis de automatização são úteis para os seguintes cenários:

- Partilhar um valor entre vários livros de execução ou configurações de DSC.

- Partilhar um valor entre vários trabalhos a partir do mesmo livro de execução ou configuração DSC.

- Gerir um valor utilizado por livros de execução ou configurações DSC a partir do portal ou da linha de comando PowerShell. Um exemplo é um conjunto de itens de configuração comuns, tais como uma lista específica de nomes VM, um grupo de recursos específico, um nome de domínio AD, e muito mais.  

Uma vez que as variáveis de Automação são persistidas, estão disponíveis mesmo que a configuração do livro de execução ou dSC falhe. Este comportamento permite que uma configuração de um livro de execução ou DSC desempor um valor que é então usado por outro livro de execução, ou pela mesma configuração de releir ou DSC da próxima vez que funcionar.

Ao criar uma variável, pode especificar a sua encriptação e armazenamento pela Azure Automation como um ativo seguro. Outros ativos seguros incluem credenciais, certificados e ligações. A Azure Automation encripta estes ativos e armazena-os utilizando uma chave única que é gerada para cada conta De Automação. A chave está armazenada num cofre de chaves gerido pelo sistema. Antes de armazenar um ativo seguro, a Azure Automation carrega a chave a partir do Cofre de Chaves e depois usa-a para encriptar o ativo. 

A Azure Automation armazena cada variável encriptada de forma segura. O seu valor não pode ser recuperado utilizando o cmdlet [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) que envia como parte do módulo Azure PowerShell. A única maneira de recuperar um valor encriptado é utilizando a atividade **Get-AutomationVariable** numa configuração de livro de execução ou DSC.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Tipos variáveis

Quando cria uma variável com o portal Azure, deve especificar um tipo de dados da lista de dropdown para que o portal possa apresentar o controlo adequado para a introdução do valor variável. Seguem-se os tipos variáveis disponíveis na Automação Azure:

* String
* Número inteiro
* DateTime
* Booleano
* Null

A variável não se restringe ao tipo de dados designado. Tem de definir a variável utilizando o Windows PowerShell se pretender especificar um valor de um tipo diferente. Se indicar **não definido**, o valor da variável está definido como **nulo,** e deve definir o valor com o cmdlet [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) ou a atividade **Set-AutomationVariable.**

Não é possível usar o portal para criar ou alterar o valor para um tipo variável complexo. No entanto, pode fornecer um valor de qualquer tipo utilizando o Windows PowerShell. Os tipos complexos são recuperados como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Pode armazenar vários valores numa única variável, criando uma matriz ou hashtable e guardando-os para uma variável.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>PowerShell cmdlets que criam e gerem ativos variáveis

Para o módulo Az, os cmdlets na tabela seguinte são usados para criar e gerir ativos variáveis Automation com o Windows PowerShell. Eles enviam como parte do [módulo Az.Automation,](/powershell/azure/overview)que está disponível para uso em livros de automação e configurações DSC.

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Obtém o valor de uma variável existente.|
|[Nova AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Cria uma nova variável e define o seu valor.|
|[Remover-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Remove uma variável existente.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Define o valor de uma variável existente.|

## <a name="activities-to-access-variables"></a>Atividades de acesso a variáveis

As atividades na tabela seguinte são utilizadas para aceder a variáveis em livros de execução e configurações de DSC. A diferença entre **Get-AzAutomationVariable** e **Get-AutomationVariable** é explicada para variáveis encriptadas no início deste artigo.

| Atividade | Descrição |
|:---|:---|
|**Get-AutomationVariable**|Obtém o valor de uma variável existente.|
|**Variável de automatização de conjunto**|Define o valor de uma variável existente.|

> [!NOTE]
> Evite utilizar variáveis no parâmetro *de nome* de **Get-AutomationVariable** numa configuração de livro ou DSC. A utilização deste parâmetro pode complicar a descoberta de dependências entre os livros de execução ou as configurações de DSC e as variáveis de Automação no momento do design.

As funções na tabela seguinte são usadas para aceder e recuperar variáveis num livro python2.

|Funções Python2|Descrição|
|:---|:---|
|automationassets.get_automation_variable|Obtém o valor de uma variável existente. |
|automationassets.set_automation_variable|Define o valor de uma variável existente. |

> [!NOTE]
> Tem de importar o módulo de **automação** no topo do seu livro de execução Python para aceder às funções de ativo.

## <a name="creating-a-new-automation-variable"></a>Criação de uma nova variável de Automação

### <a name="create-a-new-variable-using-the-azure-portal"></a>Criar uma nova variável usando o portal Azure

1. Na sua conta De automação, clique no azulejo **Do Ativo** e, em seguida, na lâmina **do Ativo,** selecione **Variáveis**.
2. No azulejo **Variáveis,** selecione **Adicionar uma variável**.
3. Complete as opções na lâmina **New Variable** e clique em **Criar** para salvar a nova variável.

>[!NOTE]
>Se pretender remover a encriptação para uma variável, deve eliminar a variável e recriá-la como não encriptada.

### <a name="create-a-new-variable-with-windows-powershell"></a>Criar uma nova variável com o Windows PowerShell

O script utiliza o cmdlet **New-AzAutomationVariable** para criar uma nova variável e definir o seu valor inicial. Pode então recuperar o valor utilizando **get-AzAutomationVariable**. Se o valor for um tipo simples, então esse mesmo tipo é recuperado. Se for um tipo complexo, então um tipo **PSCustomObject** é recuperado.

O exemplo seguinte mostra como criar uma variável de tipo String e, em seguida, devolver o seu valor.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

O exemplo seguinte mostra como criar uma variável com um tipo complexo e depois recupera as suas propriedades. Neste caso, é utilizado um objeto de máquina virtual da [Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Usando uma variável numa configuração de livro de execução ou DSC

Utilize a atividade **Set-AutomationVariable** para definir o valor de uma variável de Automação numa configuração powerShell ou DSC e a **Get-AutomationVariable** para recuperá-la. Não deve utilizar os cmdlets **Set-AzAutomationVariable** e **Get-AzAutomationVariable** ou os seus equivalentes de módulo AzureRM numa configuração de livro de execução ou DSC, uma vez que são menos eficientes do que as atividades de fluxo de trabalho. 

Note que não é possível recuperar o valor de uma variável segura com **get-AzAutomationVariable** ou o seu módulo AzureRM equivalente. 

A única maneira de criar uma nova variável dentro de um livro de execução ou configuração DSC é usar o cmdlet **New-AzAutomationVariable.**

### <a name="textual-runbook-samples"></a>Amostras de livro textual

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Definir e recuperar um valor simples a partir de uma variável

Os comandos de amostra seguem mostram como definir e recuperar uma variável num livro textual. Esta amostra assume a criação de variáveis inteiros chamadas *NumberOfItas* e *NumberOfRunnings* e uma variável de cadeia chamada *SampleMessage*.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Definir e recuperar uma variável em um livro python2

A amostra seguinte mostra como usar uma variável, definir uma variável, e lidar com uma exceção para uma variável inexistente num livro python2.

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

Num livro gráfico, pode adicionar a atividade **Get-AutomationVariable** ou **Set-AutomationVariable.** Basta clicar na variável no painel da Biblioteca do editor gráfico e selecionar a atividade que deseja.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Definir valores numa variável

A imagem seguinte mostra atividades de amostra para atualizar uma variável com um valor simples num livro gráfico. Nesta amostra, a **Get-AzVM** recupera uma única máquina virtual Azure e guarda o nome do computador para uma variável de Automação existente com um tipo de Cadeia. Não importa se a [ligação é um oleoduto ou sequência,](../automation-graphical-authoring-intro.md#links-and-workflow) uma vez que o código só espera um único objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a ligação de atividades em autoria gráfica, consulte [Links na autoria gráfica](../automation-graphical-authoring-intro.md#links-and-workflow).
- Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](../automation-first-runbook-graphical.md)
