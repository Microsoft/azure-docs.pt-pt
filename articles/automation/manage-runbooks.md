---
title: Gerenciar runbooks na automação do Azure
description: Este artigo descreve como gerenciar runbooks na automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a477811e46d97375d4dce4d83072dda60ca797c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717221"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerenciar runbooks na automação do Azure

Você pode adicionar um runbook à automação do Azure [criando um novo](#create-a-runbook) ou importando um runbook existente de um arquivo ou da [Galeria de runbooks](automation-runbook-gallery.md). Este artigo fornece informações sobre como criar e importar runbooks de um arquivo.  Você pode obter todos os detalhes sobre como acessar runbooks e módulos da Comunidade em [galerias de runbook e de módulo para a automação do Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Criar um runbook

Você pode criar um novo runbook na automação do Azure usando um dos portais do Azure ou o Windows PowerShell. Depois que o runbook tiver sido criado, você poderá editá-lo usando informações no [fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e, em seguida, **crie um novo runbook**.
4. Digite um **nome** para o runbook e selecione seu [tipo](automation-runbook-types.md). O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
5. Clique em **criar** para criar o runbook e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Você pode usar o cmdlet [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) para criar um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)vazio. Use o parâmetro **Type** para especificar um dos quatro tipos de runbook.

Os comandos de exemplo a seguir mostram como criar um novo runbook vazio.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um runbook

Você pode criar um novo runbook na automação do Azure importando um script do PowerShell ou um fluxo de trabalho do PowerShell (extensão. ps1), um runbook gráfico exportado (. graphrunbook) ou um script Python 2 (extensão. py).  Você deve especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração as considerações a seguir.

* Um `.graphrunbook` arquivo só pode ser importado para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks), e runbooks gráficos só podem ser criados `.graphrunbook` a partir de um arquivo.
* Um `.ps1` arquivo que contém um fluxo de trabalho do PowerShell só pode ser importado para um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o arquivo contiver vários fluxos de trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importar cada um separadamente.
* Um `.ps1` arquivo que contém um fluxo de trabalho do PowerShell não deve ser importado para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), pois não pode ser reconhecido pelo mecanismo de script do PowerShell.
* Um `.ps1` arquivo que não contém um fluxo de trabalho pode ser importado para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se ele for importado para um runbook de fluxo de trabalho do PowerShell, ele será convertido em um fluxo de trabalho e os comentários serão incluídos no runbook especificando as alterações feitas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um runbook de um arquivo com o portal do Azure

Você pode usar o procedimento a seguir para importar um arquivo de script para a automação do Azure.

> [!NOTE]
> Observe que você só pode importar um arquivo. ps1 para um runbook de fluxo de trabalho do PowerShell usando o Portal.

1. No portal do Azure, abra a sua conta da Automatização.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e, em seguida, em **importar**.
4. Clique em **arquivo de runbook** para selecionar o arquivo a ser importado
5. Se o campo **nome** estiver habilitado, você terá a opção de alterá-lo.  O nome do runbook tem de começar com uma letra e pode ter letras, números, carateres de sublinhado e travessões.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterar o tipo depois de levar em conta as restrições aplicáveis.
7. O novo runbook aparece na lista de runbooks para a conta de automação.
8. Você deve [publicar o runbook](#publish-a-runbook) antes de poder executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook de fluxo de trabalho gráfico do PowerShell, você tem a opção de converter para outro tipo, se desejar. Não é possível converter em um runbook textual.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um runbook de um arquivo de script com o Windows PowerShell

Você pode usar o cmdlet [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) para importar um arquivo de script como um runbook de fluxo de trabalho do PowerShell de rascunho. Se o runbook já existir, a importação falhará, a menos que você use o parâmetro *-Force* .

Os comandos de exemplo a seguir mostram como importar um arquivo de script para um runbook.

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

Quando você testa um runbook, a [versão de rascunho](#publish-a-runbook) é executada e todas as ações que ele executa são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [saída](automation-runbook-output-and-messages.md#output-stream) e de [aviso e de erro](automation-runbook-output-and-messages.md#message-streams) são exibidos no painel saída de teste. As mensagens para o [fluxo detalhado](automation-runbook-output-and-messages.md#message-streams) serão exibidas no painel de saída somente se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) for definida como continuar.

Embora a versão de rascunho esteja sendo executada, o runbook ainda é executado normalmente e executa qualquer ação em relação aos recursos no ambiente. Por esse motivo, você só deve testar runbooks em recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo, e não há nenhuma diferença no teste entre o editor de texto e o editor gráfico no portal do Azure.

1. Abra a versão de rascunho do runbook no editor de [texto](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique no botão **testar** para abrir a página de teste.
1. Se o runbook tiver parâmetros, eles serão listados no painel esquerdo, onde você pode fornecer valores a serem usados para o teste.
1. Se você quiser executar o teste em um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md), altere **as configurações de execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o **Azure** padrão para executar o teste na nuvem.
1. Clique no botão **Iniciar** para iniciar o teste.
1. Se o runbook for um [fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou [gráfico](automation-runbook-types.md#graphical-runbooks), você poderá interrompê-lo ou suspendê-lo enquanto estiver sendo testado com os botões abaixo do painel de saída. Quando suspende o runbook, conclui a atividade atual antes de ser suspenso. Depois do runbook está suspenso, pode pará-lo ou reiniciá-lo.
1. Inspecione a saída do runbook no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Ao criar ou importar um novo runbook, você deve publicá-lo antes de poder executá-lo.  Cada runbook na automação tem um rascunho e uma versão publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações feitas à versão de rascunho. Quando a versão de rascunho deve ser disponibilizada, você a publica, o que substitui a versão publicada pela versão de rascunho.

### <a name="azure-portal"></a>Portal do Azure

1. Abra o runbook no portal do Azure.
2. Clique no botão **Editar** .
3. Clique no botão **publicar** e, em seguida, em **Sim** para a mensagem de verificação.

### <a name="powershell"></a>PowerShell

Você pode usar o cmdlet [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) para publicar um runbook com o Windows PowerShell. Os comandos de exemplo a seguir mostram como publicar um runbook de exemplo.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como você pode se beneficiar do runbook e da Galeria de módulos do PowerShell, consulte [galerias de runbook e de módulo para a automação do Azure](automation-runbook-gallery.md)
* Para saber mais sobre como editar runbooks do PowerShell e do fluxo de trabalho do PowerShell com um editor de texto, consulte [editando runbooks textuais na automação do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de runbook gráfico, consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md)
