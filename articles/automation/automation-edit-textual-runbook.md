---
title: Editar runbooks textuais na automatização do Azure
description: Este artigo fornece procedimentos diferentes para trabalhar com runbooks do PowerShell e o fluxo de trabalho do PowerShell na automatização do Azure com o editor de texto.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bd220b6ff1102c2ef2f436e0af013f253b5820a3
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478290"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Editar runbooks textuais na automatização do Azure

O editor de texto na automatização do Azure pode ser utilizado para editar [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Isso tem os recursos típicos de outros editores de código, como o intellisense e a codificação por cores com recursos especiais adicionais para o ajudar a aceder a recursos comuns a runbooks. Este artigo fornece passos detalhados para a execução de funções diferentes com este editor.

O editor de texto inclui uma funcionalidade para inserir código para cmdlets, recursos e runbooks subordinados num runbook. Em vez de digitar o código por conta própria, pode selecionar a partir de uma lista de recursos disponíveis e o código correto inserido no runbook.

Cada runbook na automatização do Azure tem duas versões de rascunho e publicado. Editar a versão de rascunho do runbook e, em seguida, publicá-lo para que ele pode ser executado. Não é possível editar a versão publicada. Para obter mais informações, consulte [publicar um runbook](manage-runbooks.md#publish-a-runbook).

Para trabalhar com [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks), consulte [a criação de gráficos na automatização do Azure](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Para editar um runbook com o portal do Azure

Utilize o procedimento seguinte para abrir um runbook para edição no editor de texto.

1. No portal do Azure, selecione a sua conta de automatização.
2. Sob **AUTOMATIZAÇÃO de processos**, selecione **Runbooks** para abrir a lista de runbooks.
3. Selecione o runbook que pretende editar e, em seguida, clique nas **editar** botão.
4. Edite o runbook.
5. Clique em **guardar** quando as suas edições forem concluídas.
6. Clique em **publicar** se pretender que a versão mais recente de rascunho do runbook a ser publicado.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Para inserir um cmdlet num runbook

1. Na tela do editor de texto, posicione o cursor em que pretende colocar o cmdlet.
2. Expanda a **Cmdlets** nó no controlo da biblioteca.
3. Expanda o módulo que contém o cmdlet que pretende utilizar.
4. Com o cmdlet para inserir e selecione o botão direito **adicionar à tela**. Se o cmdlet tem mais do que um parâmetro definido, em seguida, o conjunto padrão será adicionado. Também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferentes.
5. O código para o cmdlet é inserido com a lista completa de parâmetros.
6. Forneça um valor adequado em vez do tipo de dados rodeado por chavetas <> para quaisquer parâmetros necessários. Remova quaisquer parâmetros que não é necessário.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Para inserir código para um runbook subordinado de um runbook

1. Na tela do editor de texto, posicione o cursor onde pretende colocar o código para o [runbook subordinado](automation-child-runbooks.md).
2. Expanda a **Runbooks** nó no controlo da biblioteca.
3. Com o runbook a inserir e selecione o botão direito **adicionar à tela**.
4. O código para o runbook subordinado é inserido com quaisquer espaços reservados para quaisquer parâmetros do runbook.
5. Substitua os marcadores de posição pelos valores adequados para cada parâmetro.

### <a name="to-insert-an-asset-into-a-runbook"></a>Para inserir um recurso de um runbook

1. Na tela do editor de texto, posicione o cursor onde pretende colocar o código para o runbook subordinado.
2. Expanda a **ativos** nó no controlo da biblioteca.
3. Expanda o nó para o tipo de recurso que pretende.
4. Com o elemento para inserir e selecione o botão direito **adicionar à tela**. Para [recursos de variável](automation-variables.md), selecione **adicionar "Obter variável" à tela** ou **adicionar "Definir variável" à tela** dependendo se pretende obter ou definir a variável.
5. O código para o elemento é inserido no runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Para editar um runbook da automatização do Azure com o Windows PowerShell

Para editar um runbook com o Windows PowerShell, utilize o editor à sua escolha e guardá-lo para um `.ps1` ficheiro. Pode utilizar o [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) cmdlet para obter o conteúdo do runbook e, em seguida [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) cmdlet para substituir o existente rascunho de runbook com o modificar um.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Para obter os conteúdos de um Runbook com o Windows PowerShell

Comandos de exemplo seguintes mostram como obter o script de um runbook e guardá-lo para um ficheiro de script. Neste exemplo, a versão de rascunho é recuperada. Também é possível obter a versão publicada do runbook, embora não pode ser alterada nesta versão.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Para alterar o conteúdo de um Runbook com o Windows PowerShell

Comandos de exemplo seguintes mostram como substituir o conteúdo existente de um runbook com o conteúdo de um ficheiro de script. Este é o mesmo procedimento de exemplo como na [para importar um runbook a partir de um ficheiro de script com o Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Artigos relacionados

* [Gerir runbooks na automatização do Azure](manage-runbooks.md)
* [Fluxo de trabalho do PowerShell de aprendizagem](automation-powershell-workflow.md)
* [Gráfico de criação na automatização do Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Ligações](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)

