---
title: Editar livros de texto na Azure Automation
description: Este artigo diz como usar o editor de texto da Azure Automation para trabalhar com os livros de fluxo de trabalho PowerShell e PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8d6b786ffaf309e147de27e8cd8be314a3d8a5fb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896991"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Editar livros de texto na Azure Automation

Pode utilizar o editor textual da Azure Automation para editar [os livros de execução PowerShell](automation-runbook-types.md#powershell-runbooks) e [os livros de fluxo de trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Este editor tem as características típicas de outros editores de código, como o IntelliSense. Também usa codificação de cores com características especiais adicionais para ajudá-lo a aceder a recursos comuns a runbooks. 

O editor textual inclui uma funcionalidade para inserir código para cmdlets, ativos e livros infantis num livro de recortes. Em vez de escrever o código por si mesmo, você pode selecionar a partir de uma lista de recursos disponíveis e o editor insere o código apropriado no livro de bordo.

Cada livro de bordo da Azure Automation tem duas versões, Draft e Published. Edita a versão Draft do livro de recortes e depois publica-a para que possa ser executada. A versão Publicada não pode ser editada. Para mais informações, consulte [publicar um livro de recortes.](manage-runbooks.md#publish-a-runbook)

Este artigo fornece passos detalhados para o desempenho de diferentes funções com este editor. Estes não são aplicáveis a [livros gráficos.](automation-runbook-types.md#graphical-runbooks) Para trabalhar com estes runbooks, consulte [a autoria gráfica na Azure Automation.](automation-graphical-authoring-intro.md)

## <a name="edit-a-runbook-with-the-azure-portal"></a>Editar um livro com o portal Azure

1. No portal Azure, selecione a sua conta Automation.
2. No **âmbito da AUTOMATIZAÇÃO DO PROCESSO,** selecione **Runbooks** para abrir a lista de runbooks.
3. Escolha o livro de execução para editar e, em seguida, clique em **Editar.**
4. Edite o livro de recortes.
5. Clique **em Guardar** quando as suas edições estiverem completas.
6. Clique **em Publicar** se quiser publicar a versão mais recente do rascunho do runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Insira um cmdlet num livro de bordo

1. Na tela do editor textual, posicione o cursor onde pretende colocar o cmdlet.
2. Expanda o nó **Cmdlets** no controlo da Biblioteca.
3. Expanda o módulo que contém o cmdlet para utilizar.
4. Clique com o botão direito para inserir e **selecione Adicionar à tela**. Se o cmdlet tiver mais de um parâmetro definido, o editor adiciona o conjunto predefinido. Também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. Note que o código para o cmdlet é inserido com toda a sua lista de parâmetros.
6. Fornecer um valor adequado no lugar do valor rodeado por suportes angulares (<>) para os parâmetros necessários. Remova quaisquer parâmetros que não precise.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Insira o código para um livro de corridas de crianças num livro de recortes

1. Na tela do editor textual, posicione o cursor onde pretende colocar o código para o [livro infantil](automation-child-runbooks.md).
2. Expanda o nó **runbooks** no controlo da Biblioteca.
3. Clique com o botão direito no runbook para inserir e **selecione Adicionar à tela**.
4. O código para o livro de crianças é inserido com qualquer espaço reservado para quaisquer parâmetros de livro de bordo.
5. Substitua os espaços reservados por valores adequados para cada parâmetro.

### <a name="insert-an-asset-into-a-runbook"></a>Insira um ativo num livro de execução

1. No controlo de tela do editor textual, posicione o cursor onde pretende colocar o código para o livro de aplicação da criança.
2. Expandir o nó **de Ativos** no controlo da Biblioteca.
3. Expanda o nó para o tipo de ativo pretendido.
4. Clique com o botão direito no nome do ativo para inserir e **selecione Adicionar à tela**. Para [ativos variáveis](./shared-resources/variables.md), selecione **"Obter Variável" para tela** ou Adicione **"set Variable" à tela,** dependendo se pretende obter ou definir a variável.
5. Note que o código do ativo está inserido no livro de recortes.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Editar um runbook Azure Automation utilizando o Windows PowerShell

Para editar um livro de bordo com o Windows PowerShell, utilize o editor da sua escolha e guarde o livro de execução para um ficheiro **.ps1.** Pode utilizar o [cmdlet Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) para recuperar o conteúdo do livro de bordo. Pode utilizar o cmdlet  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) para substituir o rascunho existente por um livro modificado.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recupere o conteúdo de um livro de execução utilizando o Windows PowerShell

Os comandos de exemplo seguintes exemplificam como obter o script de um runbook e guardá-lo num ficheiro de script. Neste exemplo, é obtida a versão de Rascunho. Também é possível recuperar a versão publicada do runbook, embora esta versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Alterar o conteúdo de um livro de execução utilizando o Windows PowerShell

Os seguintes comandos de amostra mostram como substituir o conteúdo existente de um livro de bordo pelo conteúdo de um ficheiro de script. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Próximos passos

* [Gerir os runbooks na Azure Automation](manage-runbooks.md).
* [Learning PowerShell workflow](automation-powershell-workflow.md).
* [Autoria gráfica na Azure Automation.](automation-graphical-authoring-intro.md)
* [Certificados](./shared-resources/certificates.md).
* [Ligações](automation-connections.md).
* [Credenciais](./shared-resources/credentials.md).
* [Horários.](./shared-resources/schedules.md)
* [Variáveis.](./shared-resources/variables.md)
* [Referência de cmdlet PowerShell](/powershell/module/az.automation).
