---
title: Starting a Runbook in Azure Automation (Iniciar um Runbook na Automatização do Azure)
description: Resume os diferentes métodos que podem ser usados para iniciar um livro de execução na Azure Automation e fornece detalhes sobre a utilização tanto do portal Azure como do Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7f2c0dda952959db3bffba6016f48b986016c19e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679458"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starting a Runbook in Azure Automation (Iniciar um Runbook na Automatização do Azure)

A tabela seguinte ajuda-o a determinar o método para iniciar um livro de execução em Automação Azure que seja mais adequado ao seu cenário particular. Este artigo inclui detalhes sobre o início de um livro de execução com o portal Azure e com o Windows PowerShell. Os detalhes sobre os outros métodos são fornecidos em outra documentação a que pode aceder a partir dos links abaixo.

| **Método** | **Características** |
| --- | --- |
| [Portal do Azure](#start-a-runbook-with-the-azure-portal) |<li>Método mais simples com interface de utilizador interativo.<br> <li>Formulário para fornecer valores simples de parâmetros.<br> <li>Facilmente rastrear o estado de trabalho.<br> <li>Acesso autenticado com entrada de Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Ligue da linha de comando com cmdlets Windows PowerShell.<br> <li>Pode ser incluído em solução automatizada com vários passos.<br> <li>O pedido é autenticado com certificado ou diretor de serviço de utilizador da OAuth.<br> <li>Forneça valores simples e complexos de parâmetros.<br> <li>Estado de trabalho de pista.<br> <li>Cliente necessário para suportar cmdlets PowerShell. |
| [API de Automação Azure](/rest/api/automation/) |<li>Método mais flexível, mas também mais complexo.<br> <li>Ligue a partir de qualquer código personalizado que possa fazer pedidos HTTP.<br> <li>Pedido autenticado com certificado, ou diretor de serviço de utilizador da Oauth.<br> <li>Forneça valores simples e complexos de parâmetros. *Se está a chamar um livro de execução python usando a API, a carga útil da JSON deve ser serializada.*<br> <li>Estado de trabalho de pista. |
| [Webhooks](automation-webhooks.md) |<li>Inicie o livro de corridas a partir de um único pedido http.<br> <li>Autenticado com ficha de segurança em URL.<br> <li>O cliente não pode sobrepor-se aos valores dos parâmetros especificados quando o webhook criou. O Livro de Execução pode definir um único parâmetro que é povoado com os detalhes do pedido http.<br> <li>Nenhuma capacidade de rastrear o estado de trabalho através de URL webhook. |
| [Responda ao Alerta Azure](../log-analytics/log-analytics-alerts.md) |<li>Inicie um livro em resposta ao alerta Azure.<br> <li>Configure o webhook para o livro de execução e ligue para alertar.<br> <li>Autenticado com ficha de segurança em URL. |
| [Agenda](automation-schedules.md) |<li>Inicie automaticamente o livro de recortes na agenda horária, diária, semanal ou mensal.<br> <li>Manipule o horário através do portal Azure, powerShell cmdlets ou Azure API.<br> <li>Forneça valores de parâmetros a utilizar com horário. |
| [De Outro Livro de Corridas](automation-child-runbooks.md) |<li>Use um livro de corridas como uma atividade em outro livro de execução.<br> <li>Útil para a funcionalidade utilizada por vários livros de execução.<br> <li>Forneça valores de parâmetros ao livro de execução infantil e utilize a saída no livro de execução dos pais. |

A imagem que se segue ilustra um processo detalhado passo a passo no ciclo de vida de um livro de corridas. Inclui diferentes formas de um livro de execução começar na Automatização Azure, que componentes necessários para o Trabalhador do Livro Híbrido executar livros de execução da Automação Azure e interações entre diferentes componentes. Para aprender sobre a execução de livros de automação no seu datacenter, consulte [os trabalhadores híbridos](automation-hybrid-runbook-worker.md) do livro de corridas

![Arquitetura do livro de corridas](media/automation-starting-runbook/runbooks-architecture.png)

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="runbook-parameters"></a>Parâmetros do livro de corridas

Quando inicia um livro de execução a partir do portal Azure ou do Windows PowerShell, a instrução é enviada através do serviço web Azure Automation. Este serviço não suporta parâmetros com tipos de dados complexos. Se precisa de fornecer um valor para um parâmetro complexo, então deve chamá-lo de linha a partir de outro livro de execução, como descrito em [livros infantis em Automação Azure](automation-child-runbooks.md).

O serviço web Azure Automation fornece uma funcionalidade especial para parâmetros utilizando certos tipos de dados descritos nas seguintes secções:

### <a name="named-values"></a>Valores com nome

Se o parâmetro for tipo de dados [objeto], então pode utilizar o seguinte formato JSON para enviar-lhe uma lista de valores nomeados: *{Nome1:'Value1', Nome2:'Value2', Nome3:'Value3'}*. Estes valores têm de ser tipos simples. O livro de execução recebe o parâmetro como [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) com propriedades que correspondem a cada valor nomeado.

Considere o runbook de teste abaixo, que aceita um parâmetro denominado user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Pode ser utilizado o texto seguinte para o parâmetro user.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Isto resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrizes

Se o parâmetro for uma matriz como [array] ou [string], então pode utilizar o seguinte formato JSON para enviar-lhe uma lista de valores: *[Valor1, Valor2, Valor3]*. Estes valores têm de ser tipos simples.

Considere o runbook de teste abaixo, que aceita um parâmetro denominado *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Pode ser utilizado o texto seguinte para o parâmetro user.

```input
["Joe","Smith",2,true]
```

Isto resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Credenciais

Se o parâmetro for `PSCredential`tipo de dados, pode fornecer o nome de um [ativo credencial](automation-credentials.md)da Automação Azure . O livro de execução recupera a credencial com o nome que especifica. O seguinte livro de ensaios `credential`aceita um parâmetro chamado .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O texto seguinte poderia ser utilizado para o parâmetro do utilizador, assumindo que havia um ativo credencial chamado `My Credential`.

```input
My Credential
```

Assumindo que o nome do `jsmith`utilizador na credencial é , a seguinte saída é visualizada.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Inicie um livro de corridas com o portal Azure

1. No portal Azure, selecione **Automation** e, em seguida, clique no nome de uma conta Automation.
2. No menu Hub, selecione **Runbooks**.
3. Na página 'Livros De Recorrem', selecione um livro de execução e, em seguida, clique em **Iniciar**.
4. Se o livro tiver parâmetros, é-lhe pedido que forneça valores com uma caixa de texto para cada parâmetro. Para obter mais informações sobre os parâmetros, consulte parâmetros do Livro de [Execução](#runbook-parameters).
5. No painel de trabalho, pode ver o estado do trabalho do livro de corridas.

## <a name="start-a-runbook-with-powershell"></a>Inicie um livro de corridas com a PowerShell

Pode utilizar o [Livro de Arranque aZAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) para iniciar um livro de execução com o Windows PowerShell. O seguinte código de amostra inicia um livro de execução chamado **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook`devolve um objeto de trabalho que pode usar para rastrear o estado assim que o livro de execução for iniciado. Em seguida, pode utilizar este objeto de trabalho com o [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) para determinar o estado do trabalho e a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjoboutput?view=azps-3.7.0) para recuperar a sua saída. O exemplo seguinte inicia um livro de execução chamado **Test-Runbook,** aguarda até que esteja concluído e, em seguida, exibe a sua saída.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Se o livro de execução necessitar de parâmetros, então deve fornecê-los como um [hashtable](https://technet.microsoft.com/library/hh847780.aspx). A chave do hashtable deve coincidir com o nome do parâmetro e o valor é o valor do parâmetro. O exemplo seguinte mostra como iniciar um runbook com dois parâmetros de cadeia denominados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booleano denominado Show. Para obter mais informações sobre os parâmetros, consulte parâmetros do Livro de [Execução](#runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a execução de livros de automação no seu datacenter, consulte os [Trabalhadores híbridos](automation-hybrid-runbook-worker.md)do Livro de Corridas .
* Para saber mais sobre a criação de livros modulares a utilizar por outros livros de execução para funções específicas ou comuns, consulte os Livros de [Execução infantil.](automation-child-runbooks.md)
* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
