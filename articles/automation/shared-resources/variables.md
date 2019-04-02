---
title: Recursos de variável na automatização do Azure
description: Recursos de variável são valores que estão disponíveis para todos os runbooks e configurações de DSC na automatização do Azure.  Este artigo explica os detalhes de variáveis e como trabalhar com os mesmos na criação de texto e gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc26c0357dcb071c4c75e8684fe47144a04177e4
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807043"
---
# <a name="variable-assets-in-azure-automation"></a>Recursos de variável na automatização do Azure

Recursos de variável são valores que estão disponíveis para todos os runbooks e configurações de DSC na conta de automatização. Podem ser geridos no portal do Azure, PowerShell, dentro de um runbook ou configuração DSC. Variáveis de automatização são úteis para os seguintes cenários:

- Partilhe um valor entre vários runbooks ou configurações de DSC.

- Partilhe um valor entre várias tarefas do mesmo runbook ou configuração DSC.

- Gerir um valor a partir do portal ou da linha de comando do PowerShell que é utilizada pelos runbooks ou configurações de DSC, como um conjunto comum de itens de configuração, como específicos de lista de nomes de VMS, um grupo de recursos específico, um nome de domínio do AD e muito mais.  

Uma vez que as variáveis da automatização são mantidas, estão disponíveis mesmo que o runbook ou configuração DSC falha. Este comportamento permite que um valor definido por um runbook que, em seguida, é utilizado por outro ou que é utilizado pelo mesmo runbook ou configuração de DSC da próxima vez que for executada.

Quando uma variável é criada, pode especificar que ele é armazenado encriptado. Variáveis encriptadas são armazenadas em segurança na automatização do Azure, e seu valor não pode ser obtido a partir da [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet que é fornecido como parte do módulo Azure PowerShell. É a única forma que um valor encriptado pode ser obtido a partir da **Get-AutomationVariable** atividade num runbook ou configuração DSC.

>[!NOTE]
>Os elementos seguros na automatização do Azure incluem as credenciais, certificados, ligações e variáveis encriptadas. Esses ativos são encriptados e armazenados na automatização do Azure com uma chave exclusiva que é gerada para cada conta de automatização. Esta chave é armazenada num sistema gerido Key Vault. Antes de armazenar um recurso seguro, a chave é carregada a partir do Key Vault e, em seguida, utilizada para encriptar o elemento. Este processo é gerido pela automatização do Azure.

## <a name="variable-types"></a>Tipos de variáveis

Quando cria uma variável com o portal do Azure, tem de especificar um tipo de dados da lista pendente para que o portal pode apresentar o controlo apropriado para introduzir o valor da variável. A variável não é restrita a esse tipo de dados. Tem de definir a variável com o Windows PowerShell se pretender especificar um valor de um tipo diferente. Se especificar **não definido**, em seguida, o valor da variável define **$null**, e tem de definir o valor com o [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet ou **Set-AutomationVariable** atividade. Não é possível criar ou alterar o valor para um tipo de variável complexos no portal, mas pode fornecer um valor de qualquer tipo com o Windows PowerShell. Tipos complexos são retornados como uma [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Pode armazenar diversos valores para uma única variável através da criação de uma matriz ou uma tabela de hash e salvá-lo para a variável.

Seguem-se uma lista de tipos de variáveis disponíveis na automatização:

* String
* Número inteiro
* DateTime
* Booleano
* Null

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell

Para AzureRM, os cmdlets na tabela seguinte são utilizados para criar e gerir recursos de credencial da automatização com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azurerm](/powershell/azure/overview), que está disponível para utilização nos runbooks de automatização e configurações de DSC.

| Cmdlets | Descrição |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Obtém o valor de uma variável existente.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Cria uma nova variável e define seu valor.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Remove uma variável existente.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Define o valor de uma variável existente.|

## <a name="activities"></a>Atividades

As atividades na tabela seguinte são utilizadas para aceder a credenciais num runbook e configurações de DSC.

| Atividades | Descrição |
|:---|:---|
|Get-AutomationVariable|Obtém o valor de uma variável existente.|
|Set-AutomationVariable|Define o valor de uma variável existente.|

> [!NOTE]
> Deve evitar utilizar variáveis no parâmetro – Name de **Get-AutomationVariable** num runbook ou configuração DSC, uma vez que isso pode dificultar a deteção de dependências entre runbooks ou configuração de DSC e automatização variáveis no tempo de design.

As funções na tabela seguinte são utilizadas para aceder e recuperar variáveis num Python2 runbook.

|Funções do Python2|Descrição|
|:---|:---|
|automationassets.get_automation_variable|Obtém o valor de uma variável existente. |
|automationassets.set_automation_variable|Define o valor de uma variável existente. |

> [!NOTE]
> Tem de importar o módulo "automationassets" na parte superior do seu runbook de Python para acessar as funções de recurso.

## <a name="creating-a-new-automation-variable"></a>Criando uma nova variável de automatização

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Para criar uma nova variável com o portal do Azure

1. A partir da sua conta de automatização, clique nas **ativos** mosaico e, em seguida, no **ativos** painel, selecione **variáveis**.
2. Sobre o **variáveis** mosaico, selecione **adicionar uma variável**.
3. Preencha as opções na **nova variável** painel e clique em **criar** guardar a variável nova.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Para criar uma nova variável com o Windows PowerShell

O [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet cria uma nova variável e define o valor. Pode recuperar o valor usando [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Se o valor for um tipo simples, em seguida, é devolvido daquele mesmo tipo. Se for um tipo complexo, em seguida, um **PSCustomObject** é devolvido.

Comandos de exemplo seguintes mostram como criar uma variável de cadeia de caracteres de tipo e, em seguida, retornar seu valor.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Comandos de exemplo seguintes mostram como criar uma variável com um tipo complexo e, em seguida, retornar as respetivas propriedades. Neste caso, uma máquina virtual de objeto do **Get-AzureRmVm** é utilizado.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Usando uma variável num runbook ou configuração DSC

Utilize o **Set-AutomationVariable** atividade defina o valor de uma variável de automatização num runbook do PowerShell ou configuração DSC e o **Get-AutomationVariable** recuperá-la. Não deve utilizar o **Set-AzureRMAutomationVariable** ou **Get-AzureRMAutomationVariable** cmdlets num runbook ou configuração DSC, pois são menos eficientes do que as atividades de fluxo de trabalho. Também não é possível obter o valor das variáveis seguros com **Get-AzureRMAutomationVariable**. A única forma de criar uma nova variável de dentro de um runbook ou configuração DSC está a utilizar o [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet.

### <a name="textual-runbook-samples"></a>Exemplos do textual runbook

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Configurando e Recuperando um valor simples de uma variável

Comandos de exemplo seguintes mostram como definir e obter uma variável num textual runbook. Neste exemplo, presume-se que as variáveis do tipo número inteiro denominado *NumberOfIterations* e *NumberOfRunnings* e uma variável de seqüência de caracteres de tipo denominada *SampleMessage* ter foi criado.

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

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Configurando e Recuperando um objeto complexo numa variável

O código de exemplo seguinte mostra como atualizar uma variável com um valor complexo num textual runbook. Neste exemplo, uma máquina virtual do Azure, obtida com **Get-AzureVM** e guardada numa variável de automatização existente.  Conforme explicado [tipos de variáveis](#variable-types), isso é armazenado como um PSCustomObject.

```powershell
$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
Set-AutomationVariable -Name "MyComplexVariable" -Value $vm
```

No código a seguir, o valor é recuperado da variável e utilizado para iniciar a máquina virtual.

```powershell
$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
if ($vmObject.PowerState -eq 'Stopped') {
    Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
}
```

#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Configurando e recuperando uma coleção numa variável

O código de exemplo seguinte mostra como utilizar uma variável com uma coleção de valores complexos num textual runbook. Neste exemplo, várias máquinas virtuais do Azure são recuperadas com **Get-AzureVM** e guardada numa variável de automatização existente. Conforme explicado [tipos de variáveis](#variable-types), isso é armazenado como uma coleção de PSCustomObjects.

```powershell
$vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}
Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms
```

No código a seguir, a coleção é recuperada da variável e utilizada para iniciar a cada máquina virtual.

```powershell
$vmValues = Get-AutomationVariable -Name "MyComplexVariable"
ForEach ($vmValue in $vmValues)
{
    if ($vmValue.PowerState -eq 'Stopped') {
        Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
    }
}
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Configurando e recuperando uma variável no Python2

O código de exemplo seguinte mostra como utilizar uma variável, defina uma variável e lidar com uma exceção para uma variável não existente num Python2 runbook.

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

Um runbook gráfico, adicione a **Get-AutomationVariable** ou **conjunto-AutomationVariable** , clicando na variável no painel de biblioteca do editor gráfico e ao selecionar a atividade é que.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Definir valores numa variável

A imagem seguinte mostra as atividades de exemplo para atualizar uma variável com um valor simples num runbook gráfico. Neste exemplo, **Get-AzureRmVM** obtém uma única máquina virtual do Azure e o nome do computador guarda uma variável de automatização existente com um tipo de cadeia de caracteres. Não importa se o [link é um pipeline ou a sequência](../automation-graphical-authoring-intro.md#links-and-workflow) , uma vez que só espera um único objeto na saída.

![Definir variável simple](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximos Passos

- Para saber mais sobre como ligar atividades em conjunto na criação de gráficos, veja [Links na criação de gráficos](../automation-graphical-authoring-intro.md#links-and-workflow)
- Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
