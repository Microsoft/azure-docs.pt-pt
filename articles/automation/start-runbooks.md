---
title: Starting a Runbook in Azure Automation (Iniciar um Runbook na Automatização do Azure)
description: Este artigo diz como iniciar um livro na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b5c5166785ad8c82c114fb7193cd49716536b408
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896601"
---
# <a name="start-a-runbook-in-azure-automation"></a>Starting a Runbook in Azure Automation (Iniciar um Runbook na Automatização do Azure)

A tabela que se segue ajuda-o a determinar o método para iniciar um livro de recortes na Azure Automation que seja mais adequado ao seu cenário específico. Este artigo inclui detalhes sobre o início de um livro com o portal Azure e com o Windows PowerShell. Os detalhes sobre os outros métodos são fornecidos em outra documentação que você pode aceder a partir dos links abaixo.

| **Método** | **Características** |
| --- | --- |
| [Portal do Azure](#start-a-runbook-with-the-azure-portal) |<li>Método mais simples com interface de utilizador interativa.<br> <li>Forma para fornecer valores de parâmetros simples.<br> <li>Facilmente rastreia o estado de trabalho.<br> <li>Acesso autenticado com sinal de Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Chamada da linha de comando com cmdlets Windows PowerShell.<br> <li>Pode ser incluído em funcionalidade automatizada com vários passos.<br> <li>O pedido é autenticado com certificado ou o principal/principal do utilizador da OAuth.<br> <li>Fornecer valores de parâmetros simples e complexos.<br> <li>Estado de trabalho de pista.<br> <li>Cliente obrigado a suportar cmdlets PowerShell. |
| [Azure Automation API](/rest/api/automation/) |<li>Método mais flexível, mas também mais complexo.<br> <li>Ligue de qualquer código personalizado que possa fazer pedidos HTTP.<br> <li>Pedido autenticado com certificado, ou Oauth user principal/principal de serviço.<br> <li>Fornecer valores de parâmetros simples e complexos. *Se está a chamar um livro python usando a API, a carga JSON deve ser serializada.*<br> <li>Estado de trabalho de pista. |
| [Webhooks](automation-webhooks.md) |<li>Inicie o livro de execução a partir de um único pedido HTTP.<br> <li>Autenticado com ficha de segurança em URL.<br> <li>O cliente não pode sobrepor os valores dos parâmetros especificados quando o webhook foi criado. O runbook pode definir um único parâmetro que é preenchido com os detalhes do pedido HTTP.<br> <li>Nenhuma capacidade de rastrear o estado de trabalho através da URL webhook. |
| [Responda ao alerta de Azure](../azure-monitor/platform/alerts-overview.md) |<li>Inicie um livro de bordo em resposta ao alerta do Azure.<br> <li>Configure webhook para runbook e link para alerta.<br> <li>Autenticado com ficha de segurança em URL. |
| [Agenda](./shared-resources/schedules.md) |<li>Inicie automaticamente o runbook em horários horárias, diárias, semanais ou mensais.<br> <li>Manipular o calendário através do portal Azure, cmdlets PowerShell ou Azure API.<br> <li>Forneça valores de parâmetros a serem utilizados com o horário. |
| [De Outro Runbook](automation-child-runbooks.md) |<li>Use um livro de bordo como atividade em outro livro de bordo.<br> <li>Útil para a funcionalidade utilizada por vários runbooks.<br> <li>Forneça valores de parâmetros para o livro de crianças e utilize a saída no livro de bordo dos pais. |

A imagem a seguir ilustra um processo detalhado passo a passo no ciclo de vida de um livro de corridas. Inclui diferentes formas de um runbook começar na Azure Automation, que componentes necessários para o Trabalhador De Runbook Híbrido executar os runbooks da Azure Automation e interações entre diferentes componentes. Para saber sobre a execução de livros de automação no seu datacenter, consulte [os trabalhadores híbridos do runbook](automation-hybrid-runbook-worker.md)

![Arquitetura de runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Trabalhar com parâmetros de runbook

Quando inicia um livro de bordo a partir do portal Azure ou do Windows PowerShell, a instrução é enviada através do serviço web Azure Automation. Este serviço não suporta parâmetros com tipos de dados complexos. Se precisar de fornecer um valor para um parâmetro complexo, então deve chamá-lo de inline a partir de outro runbook, conforme descrito em [livros infantis em Azure Automation](automation-child-runbooks.md).

O serviço web Azure Automation fornece uma funcionalidade especial para parâmetros utilizando certos tipos de dados, conforme descrito nas seguintes secções:

### <a name="named-values"></a>Valores com nome

Se o parâmetro for o tipo de dados [objeto], então pode utilizar o seguinte formato JSON para enviar-lhe uma lista de valores nomeados: *{Nome1:'Value1', Nome2:'Value2', Nome3:'Valor3'}*. Estes valores têm de ser tipos simples. O livro de bordo recebe o parâmetro como [um PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) com propriedades que correspondem a cada valor nomeado.

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

Se o parâmetro for uma matriz como [matriz] ou [string]] então pode utilizar o seguinte formato JSON para lhe enviar uma lista de valores: *[Valor1, Valor2, Valor3]*. Estes valores têm de ser tipos simples.

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

Se o parâmetro for o tipo `PSCredential` de dados, pode fornecer o nome de um ativo [credencial](./shared-resources/credentials.md)Azure Automation . O livro de recortes recupera a credencial com o nome que especifica. O seguinte livro de ensaios aceita um parâmetro chamado `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O texto que se segue poderia ser utilizado para o parâmetro do utilizador, assumindo que havia um ativo credencial chamado `My Credential` .

```input
My Credential
```

Assumindo que o nome de utilizador na credencial `jsmith` é, é apresentada a seguinte saída.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Inicie um livro com o portal Azure

1. No portal Azure, **selecione Automação** e, em seguida, clique no nome de uma conta Automation.
2. No menu Hub, selecione **Runbooks**.
3. Na página Runbooks, selecione um livro de bordo e, em seguida, clique em **Iniciar**.
4. Se o livro de bordo tiver parâmetros, é solicitado que forneça valores com uma caixa de texto para cada parâmetro. Para obter mais informações sobre os parâmetros, consulte [os parâmetros do runbook](#work-with-runbook-parameters).
5. No painel job, pode ver o estado do trabalho de runbook.

## <a name="start-a-runbook-with-powershell"></a>Inicie um livro com PowerShell

Pode utilizar o [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar um livro com o Windows PowerShell. O seguinte código de amostra inicia um livro de ensaios chamado **Test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` retorna um objeto de trabalho que pode usar para rastrear o estado uma vez que o livro de execução é iniciado. Em seguida, pode utilizar este objeto de trabalho com [a Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) para determinar o estado do trabalho e [a Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) para recuperar a sua saída. O exemplo a seguir inicia um livro de ensaios chamado **Test-Runbook,** espera até estar concluído e, em seguida, exibe a sua saída.

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

Se o livro de bordo necessitar de parâmetros, deve fornecer-lhes como [haxixe](/powershell/module/microsoft.powershell.core/about/about_hash_tables). A chave do haxixe deve corresponder ao nome do parâmetro e o valor é o valor do parâmetro. O exemplo seguinte mostra como iniciar um runbook com dois parâmetros de cadeia denominados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booleano denominado Show. Para obter mais informações sobre os parâmetros, consulte [os parâmetros do runbook](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Próximos passos

* Para obter detalhes sobre a gestão de runbooks, consulte [Gerir os runbooks na Azure Automation](manage-runbooks.md).
* Para obter detalhes da PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
* Para resolver problemas com a execução do livro de [recortes, consulte os problemas do livro de resolução de problemas](troubleshoot/runbooks.md).
