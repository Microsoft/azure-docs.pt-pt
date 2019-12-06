---
title: Editando runbooks textuais na automação do Azure
description: Este artigo fornece procedimentos diferentes para trabalhar com runbooks do PowerShell e do fluxo de trabalho do PowerShell na automação do Azure usando o editor de texto.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850861"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editando runbooks textuais na automação do Azure

O editor de texto na automação do Azure pode ser usado para editar [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e Runbooks de fluxo de [trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Isso tem os recursos típicos de outros editores de código, como IntelliSense e codificação por cores, com recursos especiais adicionais para ajudá-lo a acessar recursos comuns a runbooks. Este artigo fornece etapas detalhadas para executar funções diferentes com esse editor.

O editor de texto inclui um recurso para inserir código para cmdlets, ativos e runbooks filho em um runbook. Em vez de digitar o código por conta própria, você pode selecionar em uma lista de recursos disponíveis e ter o código apropriado inserido no runbook.

Cada runbook na automação do Azure tem duas versões, rascunho e publicado. Edite a versão de rascunho do runbook e, em seguida, publique-o para que ele possa ser executado. Não é possível editar a versão publicada. Para obter mais informações, consulte [publicando um runbook](manage-runbooks.md#publish-a-runbook).

Para trabalhar com [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks), consulte [criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure

Use o procedimento a seguir para abrir um runbook para edição no editor de texto.

1. Na portal do Azure, selecione sua conta de automação.
2. Em **automação de processo**, selecione **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook que você deseja editar e clique no botão **Editar** .
4. Edite o runbook.
5. Clique em **guardar** quando as suas edições forem concluídas.
6. Clique em **publicar** se pretender que a versão mais recente de rascunho do runbook a ser publicado.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para inserir um cmdlet em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o cmdlet.
2. Expanda o nó **cmdlets** no controle biblioteca.
3. Expanda o módulo que contém o cmdlet que você deseja usar.
4. Clique com o botão direito do mouse no cmdlet a ser inserido e selecione **Adicionar à tela**. Se o cmdlet tiver mais de um conjunto de parâmetros, o conjunto padrão será adicionado. Você também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. O código para o cmdlet é inserido com sua lista completa de parâmetros.
6. Forneça um valor apropriado no lugar do tipo de dados entre chaves < > para quaisquer parâmetros necessários. Remova todos os parâmetros desnecessários.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir o código de um runbook filho em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código para o [runbook filho](automation-child-runbooks.md).
2. Expanda o nó **Runbooks** no controle biblioteca.
3. Clique com o botão direito do mouse no runbook a ser inserido e selecione **Adicionar à tela**.
4. O código para o runbook filho é inserido com quaisquer espaços reservados para quaisquer parâmetros de runbook.
5. Substitua os espaços reservados pelos valores apropriados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um ativo em um runbook

1. Na tela do editor de texto, posicione o cursor onde você deseja colocar o código para o runbook filho.
2. Expanda o nó **ativos** no controle biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique com o botão direito do mouse no ativo a ser inserido e selecione **Adicionar à tela**. Para [ativos variáveis](automation-variables.md), selecione **Adicionar "obter variável" para tela** ou **Adicionar "definir variável" à tela** , dependendo se você deseja obter ou definir a variável.
5. O código para o ativo é inserido no runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um runbook de automação do Azure usando o Windows PowerShell

Para editar um runbook com o Windows PowerShell, use o editor de sua escolha e salve-o em um arquivo de `.ps1`. Você pode usar o cmdlet [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) para recuperar o conteúdo do runbook e, em seguida, o cmdlet [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) para substituir o runbook de rascunho existente por um modificado.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para Obter os Conteúdos de um Runbook com o Windows PowerShell

Comandos de exemplo seguintes mostram como obter o script de um runbook e guardá-lo para um ficheiro de script. Neste exemplo, a versão de rascunho é recuperada. Também é possível recuperar a versão publicada do runbook, embora essa versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para Alterar os Conteúdos de um Runbook com o Windows PowerShell

Os comandos de exemplo seguintes mostram como substituir o conteúdo existente de um runbook pelo conteúdo de um ficheiro de script. Esse é o mesmo procedimento de exemplo do [para importar um runbook de um arquivo de script com o Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Artigos relacionados

* [Gerenciar runbooks na automação do Azure](manage-runbooks.md)
* [Conhecendo o fluxo de trabalho do PowerShell](automation-powershell-workflow.md)
* [Criação gráfica na automação do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Ligações](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)

