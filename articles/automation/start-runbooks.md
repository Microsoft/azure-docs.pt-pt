---
title: Iniciar um runbook na automação do Azure
description: Resume os diferentes métodos que podem ser usados para iniciar um runbook na automação do Azure e fornece detalhes sobre como usar o portal do Azure e o Windows PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a6ef8d81e8a2845e62bf25d0bba4d6967cca65a4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849416"
---
# <a name="start-a-runbook-in-azure-automation"></a>Iniciar um runbook na automação do Azure

A tabela a seguir ajuda a determinar o método para iniciar um runbook na automação do Azure que é mais adequada para seu cenário específico. Este artigo inclui detalhes sobre como iniciar um runbook com o portal do Azure e com o Windows PowerShell. Os detalhes sobre os outros métodos são fornecidos em outra documentação que você pode acessar nos links abaixo.

| **Método** | **Características** |
| --- | --- |
| [Portal do Azure](#start-a-runbook-with-the-azure-portal) |<li>Método mais simples com interface do usuário interativa.<br> <li>Formulário para fornecer valores de parâmetro simples.<br> <li>Rastreie facilmente o estado do trabalho.<br> <li>Acesso autenticado com o Azure entrar. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Chame da linha de comando com cmdlets do Windows PowerShell.<br> <li>Pode ser incluído em uma solução automatizada com várias etapas.<br> <li>A solicitação é autenticada com certificado ou entidade de serviço/entidade de usuário OAuth.<br> <li>Forneça valores de parâmetro simples e complexos.<br> <li>Acompanhe o estado do trabalho.<br> <li>Cliente necessário para dar suporte a cmdlets do PowerShell. |
| [API de automação do Azure](/rest/api/automation/) |<li>Método mais flexível, mas também mais complexo.<br> <li>Chame de qualquer código personalizado que possa fazer solicitações HTTP.<br> <li>Solicitação autenticada com certificado ou entidade de usuário/entidade de serviço OAuth.<br> <li>Forneça valores de parâmetro simples e complexos. *Se você estiver chamando um runbook do Python usando a API, a carga JSON deverá ser serializada.*<br> <li>Acompanhe o estado do trabalho. |
| [Webhooks](automation-webhooks.md) |<li>Iniciar o runbook a partir de uma solicitação HTTP única.<br> <li>Autenticado com token de segurança na URL.<br> <li>O cliente não pode substituir os valores de parâmetro especificados quando o webhook foi criado. O runbook pode definir um único parâmetro que é populado com os detalhes da solicitação HTTP.<br> <li>Não é possível acompanhar o estado do trabalho por meio da URL do webhook. |
| [Responder ao alerta do Azure](../log-analytics/log-analytics-alerts.md) |<li>Inicie um runbook em resposta ao alerta do Azure.<br> <li>Configure o webhook para o runbook e vincule ao alerta.<br> <li>Autenticado com token de segurança na URL. |
| [Agenda](automation-schedules.md) |<li>Iniciar automaticamente o runbook em uma agenda de hora, diária, semanal ou mensal.<br> <li>Manipule o agendamento por meio de portal do Azure, cmdlets do PowerShell ou API do Azure.<br> <li>Forneça os valores de parâmetro a serem usados com o agendamento. |
| [De outro runbook](automation-child-runbooks.md) |<li>Use um runbook como uma atividade em outro runbook.<br> <li>Útil para a funcionalidade usada por vários runbooks.<br> <li>Forneça valores de parâmetro para o runbook filho e use a saída no runbook pai. |

A imagem a seguir ilustra o processo passo a passo detalhado no ciclo de vida de um runbook. Ele inclui diferentes maneiras de um runbook iniciar na automação do Azure, quais componentes são necessários para Hybrid Runbook Worker executar runbooks de automação do Azure e interações entre componentes diferentes. Para saber mais sobre a execução de runbooks de automação em seu datacenter, consulte [Hybrid runbook Workers](automation-hybrid-runbook-worker.md)

![Arquitetura do runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Iniciar um runbook com o portal do Azure

1. No portal do Azure, selecione **automação** e, em seguida, clique no nome de uma conta de automação.
2. No menu Hub, selecione **Runbooks**.
3. Na página **Runbooks** , selecione um runbook e clique em **Iniciar**.
4. Se o runbook tiver parâmetros, você será solicitado a fornecer valores com uma caixa de texto para cada parâmetro. Para obter mais informações sobre parâmetros, consulte [parâmetros de runbook](#runbook-parameters).
5. Na página **trabalho** , você pode exibir o status do trabalho de runbook.

## <a name="start-a-runbook-with-powershell"></a>Iniciar um runbook com o PowerShell

Você pode usar o [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) para iniciar um runbook com o Windows PowerShell. O código de exemplo seguinte inicia um runbook denominado Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook retorna um objeto de trabalho que você pode usar para controlar seu status depois que o runbook é iniciado. Você pode usar esse objeto de trabalho com [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) para determinar o status do trabalho e [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para obter sua saída. O código de exemplo seguinte inicia um runbook denominado Test-Runbook, aguarda pela sua conclusão e, em seguida, apresenta o resultado.

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

Se o runbook exigir parâmetros, você deverá fornecê-los como uma [tabela de hash](https://technet.microsoft.com/library/hh847780.aspx). A chave da tabela de hash deve corresponder ao nome do parâmetro e o valor é o valor do parâmetro. O exemplo seguinte mostra como iniciar um runbook com dois parâmetros de cadeia denominados FirstName e LastName, um número inteiro denominado RepeatCount e um parâmetro booleano denominado Show. Para obter mais informações sobre parâmetros, consulte [parâmetros de runbook](#runbook-parameters) abaixo.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parâmetros do Runbook

Quando você inicia um runbook do portal do Azure ou do Windows PowerShell, a instrução é enviada por meio do serviço Web de automação do Azure. Este serviço não dá suporte a parâmetros com tipos de dados complexos. Se você precisar fornecer um valor para um parâmetro complexo, deverá chamá-lo embutido de outro runbook, conforme descrito em [Runbooks filho na automação do Azure](automation-child-runbooks.md).

O serviço Web de automação do Azure fornece uma funcionalidade especial para parâmetros usando determinados tipos de dados, conforme descrito nas seções a seguir:

### <a name="named-values"></a>Valores com nome

Se o parâmetro for do tipo de dados [Object], você poderá usar o seguinte formato JSON para enviar a ele uma lista de valores nomeados: *{Nome1: ' value1 ', Nome2: ' value2 ', Name3: ' Value3 '}* . Estes valores têm de ser tipos simples. O runbook recebe o parâmetro como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) com propriedades que correspondem a cada valor nomeado.

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

Isso resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Matrizes

Se o parâmetro for uma matriz, como [array] ou [String []], você poderá usar o seguinte formato JSON para enviar a ele uma lista de valores: *[value1, value2, Value3]* . Estes valores têm de ser tipos simples.

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

Isso resulta na seguinte saída:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Credenciais

Se o parâmetro for do tipo de dados **PSCredential**, você poderá fornecer o nome de um [ativo de credencial](automation-credentials.md)de automação do Azure. O runbook recupera a credencial com o nome que você especificar.

Considere o runbook de teste abaixo, que aceita um parâmetro denominado credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

O texto a seguir pode ser usado para o parâmetro de usuário supondo que havia um ativo de credencial chamado *minha credencial*.

```input
My Credential
```

Supondo que o nome de usuário na credencial era *jsmith*, isso resulta na seguinte saída:

```output
jsmith
```

## <a name="next-steps"></a>Passos seguintes

* A arquitetura de runbook no artigo atual fornece uma visão geral de alto nível dos runbooks que gerenciam recursos no Azure e no local com o Hybrid Runbook Worker. Para saber mais sobre como executar runbooks de automação em seu datacenter, consulte [Hybrid runbook Workers](automation-hybrid-runbook-worker.md).
* Para saber mais sobre a criação de runbooks modulares a serem usados por outros runbooks para funções específicas ou comuns, consulte [Runbooks filho](automation-child-runbooks.md).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
