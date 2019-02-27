---
title: Iniciar um runbook na automatização do Azure
description: Resume os diferentes métodos que podem ser utilizados para iniciar um runbook na automatização do Azure e fornece detalhes sobre como utilizar o portal do Azure e o Windows PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c3c936a3d547389de3681b4f82f329c4978742d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886626"
---
# <a name="start-a-runbook-in-azure-automation"></a>Iniciar um runbook na automatização do Azure

A tabela seguinte ajuda-o a determinar o método para iniciar um runbook na automatização do Azure mais adequada ao seu cenário específico. Este artigo inclui detalhes sobre como iniciar um runbook com o portal do Azure e com o Windows PowerShell. Obter detalhes sobre os outros métodos são fornecidos em outras documentações que pode aceder a partir das ligações abaixo.

| **Método** | **Características** |
| --- | --- |
| [Portal do Azure](#start-a-runbook-with-the-azure-portal) |<li>Método mais simples com a interface de utilizador interativa.<br> <li>Formulário para fornecer valores de parâmetros simples.<br> <li>Controle facilmente o estado da tarefa.<br> <li>Acesso autenticado com o início de sessão do Azure no. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Chamar a partir da linha de comandos com cmdlets do Windows PowerShell.<br> <li>Pode ser incluído numa solução automatizada com vários passos.<br> <li>Pedido é autenticado com o certificado ou o utilizador OAuth principal / serviço principal.<br> <li>Forneça os valores de parâmetro simples e complexas.<br> <li>Controlar o estado da tarefa.<br> <li>Cliente necessário para suportar os cmdlets do PowerShell. |
| [API de automatização do Azure](/rest/api/automation/) |<li>Método mais flexível, mas também mais complexa.<br> <li>Chamar a partir de qualquer código personalizado que possa fazer pedidos HTTP.<br> <li>Solicitação autenticada com o certificado ou o utilizador Oauth principal / serviço principal.<br> <li>Forneça os valores de parâmetro simples e complexas. *Se estiver a chamar um runbook de Python com a API, o payload JSON deve ser serializado.*<br> <li>Controlar o estado da tarefa. |
| [Webhooks](automation-webhooks.md) |<li>Inicie o runbook a partir de único pedido HTTP.<br> <li>Autenticar com o token de segurança no URL.<br> <li>Cliente não pode substituir os valores de parâmetros especificados durante a criação do webhook. Runbook pode definir o único parâmetro que é preenchido com os detalhes de pedido HTTP.<br> <li>Sem capacidade de controlar o estado da tarefa por meio do URL do webhook. |
| [Responder a alerta do Azure](../log-analytics/log-analytics-alerts.md) |<li>Inicie um runbook em resposta ao alerta do Azure.<br> <li>Configure o webhook para o runbook e o link para o alertar.<br> <li>Autenticar com o token de segurança no URL. |
| [Agenda](automation-schedules.md) |<li>Inicie automaticamente o runbook numa agenda hora a hora, diária, semanal ou mensal.<br> <li>Manipular o agendamento através do portal do Azure, a cmdlets do PowerShell ou a API do Azure.<br> <li>Fornece valores de parâmetros para ser utilizado com a agenda. |
| [A partir de outro Runbook](automation-child-runbooks.md) |<li>Utilize um runbook como uma atividade no outro runbook.<br> <li>Útil para a funcionalidade usada por vários runbooks.<br> <li>Fornecer valores de parâmetros para o runbook subordinado e utilize a saída no runbook principal. |

A imagem seguinte ilustra o processo passo a passo detalhado no ciclo de vida de um runbook. Ele inclui formas diferentes de um runbook for iniciado na automatização do Azure, os componentes necessários para a função de trabalho de Runbook híbrida executar runbooks de automatização do Azure e as interações entre os diferentes componentes. Para saber mais sobre a execução de runbooks de automatização no seu datacenter, consulte o [os runbook workers híbridos](automation-hybrid-runbook-worker.md)

![Arquitetura de Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Iniciar um runbook com o portal do Azure

1. No portal do Azure, selecione **automatização** e, em seguida, clique no nome de uma conta de automatização.
2. No Hub menu, selecione **Runbooks**.
3. Sobre o **Runbooks** página, selecione um runbook e, em seguida, clique em **iniciar**.
4. Se o runbook tiver parâmetros, lhe for pedido para fornecer valores com uma caixa de texto para cada parâmetro. Para obter mais informações sobre parâmetros, consulte [parâmetros do Runbook](#Runbook-parameters).
5. Sobre o **tarefa** página, pode ver o estado do trabalho do runbook.

## <a name="start-a-runbook-with-powershell"></a>Iniciar um runbook com o PowerShell

Pode utilizar o [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) para iniciar um runbook com o Windows PowerShell. O código de exemplo seguinte inicia um runbook denominado Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook retorna um objeto de tarefa que pode utilizar para monitorizar o estado, uma vez que o runbook é iniciado. Em seguida, pode utilizar este objeto de tarefa com [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) para determinar o estado da tarefa e [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para obter o resultado. O código de exemplo seguinte inicia um runbook denominado Test-Runbook, aguarda até que ele foi concluída e, em seguida, apresenta o resultado.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Se o runbook necessitar de parâmetros, tem de fornecê-los como um [hashtable](https://technet.microsoft.com/library/hh847780.aspx). A chave da tabela hash tem de corresponder ao nome do parâmetro e o valor é o valor do parâmetro. O exemplo seguinte mostra como iniciar um runbook com dois parâmetros de cadeia denominados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booleano denominado Show. Para obter mais informações sobre parâmetros, consulte [parâmetros do Runbook](#Runbook-parameters) abaixo.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parâmetros do Runbook

Quando inicia um runbook a partir do portal do Azure ou do Windows PowerShell, a instrução é enviada através do serviço web automatização do Azure. Este serviço não suporta parâmetros com tipos de dados complexos. Se tiver de fornecer um valor para um parâmetro complexo, tem de chamá-lo inline a partir de outro runbook conforme descrito em [runbooks subordinados na automatização do Azure](automation-child-runbooks.md).

O serviço da web de automatização do Azure fornece a funcionalidade especial para os parâmetros com determinados tipos de dados, conforme descrito nas seções a seguir:

### <a name="named-values"></a>Valores com nome

Se o parâmetro é o tipo de dados [object], então pode usar o seguinte formato JSON para lhe enviar uma lista de valores nomeados: *{Name1: 'Value1', Name2: 'Value2', Name3: 'Value3'}*. Estes valores têm de ser tipos simples. O runbook recebe o parâmetro como uma [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) com propriedades correspondentes a cada valor nomeado.

Considere o runbook de teste abaixo, que aceita um parâmetro de nome de utilizador.

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

O texto seguinte poderia ser utilizado para o parâmetro de utilizador.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Isso resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>matrizes

Se o parâmetro for uma matriz, como [array] ou [string []], em seguida, pode utilizar o seguinte formato JSON para lhe enviar uma lista de valores: *[valor1, valor2, Value3]*. Estes valores têm de ser tipos simples.

Considere o runbook de teste abaixo, que aceita um parâmetro denominado *utilizador*.

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

O texto seguinte poderia ser utilizado para o parâmetro de utilizador.

```input
["Joe","Smith",2,true]
```

Isso resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Credenciais

Se o parâmetro é o tipo de dados **PSCredential**, em seguida, pode fornecer o nome de uma automatização do Azure [recurso de credencial](automation-credentials.md). O runbook obtém a credencial com o nome que especificar.

Considere o runbook de teste abaixo, que aceita um parâmetro denominado credencial.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O texto seguinte poderia ser utilizado para o parâmetro de user, supondo que não havia um recurso de credencial denominado *minha credencial*.

```input
My Credential
```

Supondo que o nome de utilizador na credencial é *jsmith*, isso resulta na seguinte saída:

```output
jsmith
```

## <a name="next-steps"></a>Passos Seguintes

* A arquitetura de runbook no artigo atual fornece uma visão geral de runbooks que gerem recursos no Azure e no local com a função de trabalho de Runbook híbrida. Para saber mais sobre a execução de runbooks de automatização no seu datacenter, consulte [os Runbook Workers híbridos](automation-hybrid-runbook-worker.md).
* Para saber mais sobre os runbooks modulares criar a ser utilizado por outros runbooks para funções comuns ou específicas, consulte [Runbooks subordinados](automation-child-runbooks.md).
