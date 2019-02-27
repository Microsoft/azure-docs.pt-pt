---
title: Gerir runbooks na automatização do Azure
description: Este artigo descreve como gerir runbooks na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887595"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerir runbooks na automatização do Azure

Pode adicionar um runbook de automatização do Azure, qualquer um [criar uma nova](#create-a-runbook) ou ao importar um runbook existente a partir de um ficheiro ou o [Galeria de Runbooks](automation-runbook-gallery.md). Este artigo fornece informações sobre como criar e importar runbooks a partir de um ficheiro.  Pode obter todos os detalhes do acesso a runbooks da Comunidade e os módulos no [galerias de módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Criar um runbook

Pode criar um novo runbook na automatização do Azure com um dos portais do Azure ou do Windows PowerShell. Quando o runbook tiver sido criado, pode editá-lo com informações nos [fluxo de trabalho de PowerShell de Aprendizado](automation-powershell-workflow.md) e [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique nas **adicionar um runbook** botão e, em seguida **criar um runbook novo**.
4. Introduza um **Name** para o runbook e selecione seu [tipo](automation-runbook-types.md). O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
5. Clique em **criar** para criar o runbook e abra o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Pode utilizar o [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) cmdlet para criar vazio [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Utilize o **tipo** parâmetro para especificar um dos quatro tipos de runbook.

Os comandos de exemplo seguintes mostram como criar um novo runbook está vazio.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um runbook

Pode criar um novo runbook na automatização do Azure através da importação de um script do PowerShell ou o fluxo de trabalho do PowerShell (extensão. ps1), um runbook gráfico exportado (. graphrunbook) ou um script de Python 2 (extensão. PY).  Tem de especificar o [tipo de runbook](automation-runbook-types.md) que é criada durante a importação, tendo em conta as seguintes considerações.

* R `.graphrunbook` ficheiro só pode ser importado para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks), e runbooks gráficos apenas pode ser criados a partir um `.graphrunbook` ficheiro.
* R `.ps1` ficheiro que contém um fluxo de trabalho do PowerShell só pode ser importado para um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se o ficheiro contiver vários fluxos de trabalho do PowerShell, em seguida, a importação falhará. Tem de guardar cada fluxo de trabalho no seu próprio arquivo e importar cada um separadamente.
* R `.ps1` ficheiro que não contém um fluxo de trabalho pode ser importado para um um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou uma [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se for importado para um runbook de fluxo de trabalho do PowerShell, em seguida, ela será convertida para um fluxo de trabalho e comentários estão incluídos no runbook especificando as alterações efetuadas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um runbook a partir de um ficheiro com o portal do Azure

Pode utilizar o procedimento seguinte para importar um ficheiro de script para a automatização do Azure.  

> [!NOTE]
> Tenha em atenção que só pode importar um ficheiro. ps1 para um runbook de fluxo de trabalho do PowerShell com o portal.

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique nas **adicionar um runbook** botão e, em seguida **importação**.
4. Clique em **ficheiros do Runbook** para selecionar o ficheiro a importar
5. Se o **nome** campo está ativado, então tem a opção para alterá-lo.  O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas pode alterar o tipo depois de tomar as restrições aplicáveis em conta. 
7. O runbook novo é apresentado na lista de runbooks da conta de automatização.
8. Deve [publicar o runbook](#publish-a-runbook) antes de pode executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook de fluxo de trabalho de PowerShell gráfico, tem a opção para converter para o outro tipo, se quisesse. Não é possível converter para um runbook textual.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um runbook a partir de um ficheiro de script com o Windows PowerShell

Pode utilizar o [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) cmdlet para importar um ficheiro de script como um rascunho de runbook de fluxo de trabalho do PowerShell. Se o runbook já existir, a importação falha a menos que utilize o *-força* parâmetro.

Comandos de exemplo seguintes mostram como importar um ficheiro de script para um runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testar um runbook

Quando testa um runbook, o [versão de rascunho](#publish-a-runbook) é executado e quaisquer ações que executar são concluídas. Nenhum histórico da tarefa é criado, mas a [saída](automation-runbook-output-and-messages.md#output-stream) e [aviso e erro](automation-runbook-output-and-messages.md#message-streams) fluxos são apresentados no teste de painel de saída. Mensagens para o [Stream verboso](automation-runbook-output-and-messages.md#message-streams) são apresentados no painel de resultados apenas se for o [$VerbosePreference variável](automation-runbook-output-and-messages.md#preference-variables) está definido para continuar.

Mesmo que a versão de rascunho está em execução, o runbook é executado normalmente ainda e efetua quaisquer ações sobre recursos no ambiente. Por esse motivo, deve testar apenas runbooks em recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) não é o mesmo e há nenhuma diferença no teste entre o editor de texto e o editor gráfico no portal do Azure.  

1. Abra a versão de rascunho do runbook em ambos os [editor de texto](automation-edit-textual-runbook.md) ou [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique nas **teste** botão para abrir a página de teste.
1. Se o runbook tiver parâmetros, terão de ser listados no painel esquerdo, onde pode fornecer valores para ser utilizado para o teste.
1. Se quiser executar o teste num [Runbook Worker híbrido](automation-hybrid-runbook-worker.md), em seguida, altere **configurações de execução** para **função de trabalho híbrida** e selecione o nome do grupo de destino.  Caso contrário, mantenha a predefinição **Azure** para executar o teste na cloud.
1. Clique nas **iniciar** botão para iniciar o teste.
1. Se o runbook estiver [fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [gráfico](automation-runbook-types.md#graphical-runbooks), em seguida, pode parar ou suspendê-lo enquanto ele está sendo testado com os botões por baixo do painel de resultados. Quando suspende o runbook, conclui a atividade atual antes de ser suspenso. Depois do runbook está suspenso, pode pará-lo ou reiniciá-lo.
1. Verificar a saída do runbook no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Quando criar ou importar um runbook novo, terá de o publicar antes de pode executá-lo.  Cada runbook na automatização tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações feitas à versão de rascunho. Quando a versão de rascunho deve ser disponibilizada, deve publicá-la, que substitui a versão publicada com a versão de rascunho.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o runbook no portal do Azure.
2. Clique nas **editar** botão.
3. Clique nas **Publish** botão e, em seguida **Sim** para a mensagem de verificação.

### <a name="powershell"></a>PowerShell

Pode utilizar o [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) cmdlet para publicar um runbook com o Windows PowerShell. Os comandos de exemplo seguintes mostram como publicar um runbook de exemplo.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como tirar partido do Runbook e Galeria de módulos do PowerShell, consulte o artigo [galerias de módulos e Runbooks de automatização do Azure](automation-runbook-gallery.md)
* Para saber mais sobre a edição de runbooks do PowerShell e o fluxo de trabalho do PowerShell com um editor de texto, consulte [editar runbooks textuais na automatização do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de runbooks gráficos, veja [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md)
