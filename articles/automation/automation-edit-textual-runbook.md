---
title: Edição de livros textual na Automação Azure
description: Este artigo fornece diferentes procedimentos para trabalhar com os livros powerShell e PowerShell Workflow na Azure Automation usando o editor textual.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406030"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edição de livros textual na Automação Azure

O editor textual da Azure Automation pode ser usado para editar os livros de [execução powerShell](automation-runbook-types.md#powershell-runbooks) e os livros de [execução powerShell Workflow.](automation-runbook-types.md#powershell-workflow-runbooks) Este editor tem as características típicas de outros editores de código, como o IntelliSense. Também tem codificação de cores com funcionalidades especiais adicionais para ajudá-lo no acesso a recursos comuns a livros de execução. 

O editor textual inclui uma funcionalidade para inserir código para cmdlets, ativos e livros infantis num livro de corridas. Em vez de escrever o código por si mesmo, pode selecionar a partir de uma lista de recursos disponíveis e o editor insere o código apropriado no livro de execução.

Cada livro de execução em Azure Automation tem duas versões, Draft e Published. Edita a versão Draft do livro de execução e depois publica-a para que possa ser executada. A versão Publicada não pode ser editada. Para mais informações, consulte [a Publicação de um livro de execução](manage-runbooks.md#publishing-a-runbook).

Este artigo fornece passos detalhados para o desempenho de diferentes funções com este editor. Estes não são aplicáveis a [livros gráficos.](automation-runbook-types.md#graphical-runbooks) Para trabalhar com estes livros de execução, consulte [a autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Editar um livro de corridas com o portal Azure

Utilize o seguinte procedimento para abrir um livro de execução para edição no editor textual.

1. No portal Azure, selecione a sua conta Deautomação.
2. Em **FASE DE AUTOMAÇÃO**DE PROCESSOs, selecione **Runbooks** para abrir a lista de livros de execução.
3. Escolha o livro de execução para editar e, em seguida, clique em **Editar**.
4. Editar o livro de corridas.
5. Clique em **Guardar** quando as suas edições estiverem completas.
6. Clique em **Publicar** se quiser publicar a versão mais recente do livro de execução.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Insira um cmdlet em um livro de corridas

1. Na tela do editor textual, posicione o cursor onde pretende colocar o cmdlet.
2. Expanda o nó **cmdlets** no controlo da Biblioteca.
3. Expanda o módulo que contém o cmdlet para utilizar.
4. Clique no nome cmdlet para inserir e **selecione Adicionar a tela**. Se o cmdlet tiver mais de um parâmetro definido, o editor adiciona o conjunto predefinido. Também pode expandir o cmdlet para selecionar um conjunto de parâmetros diferente.
5. Note que o código para o cmdlet é inserido com toda a sua lista de parâmetros.
6. Forneça um valor adequado em vez do valor rodeado por parênteses angulares (<>) para os parâmetros necessários. Remova os parâmetros que não precisa.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Insira o código para um livro de crianças num livro de corridas

1. Na tela do editor textual, posicione o cursor onde pretende colocar o código para o livro de [execução](automation-child-runbooks.md)infantil .
2. Expanda o nó **de Runbooks** no controlo da Biblioteca.
3. Clique no livro de execução para inserir e **selecione Adicionar à tela**.
4. O código para o livro de execução para crianças é inserido com quaisquer espaços reservados para quaisquer parâmetros de livro.
5. Substitua os espaços reservados por valores adequados para cada parâmetro.

### <a name="insert-an-asset-into-a-runbook"></a>Insira um ativo num livro de corridas

1. No controlo de Tela do editor textual, posicione o cursor onde pretende colocar o código para o livro de execução infantil.
2. Expandir o nó **assets** no controlo da Biblioteca.
3. Expanda o nó para o tipo de ativo desejado.
4. Clique no nome do ativo para inserir e **selecione Adicionar à tela**. Para [ativos variáveis,](automation-variables.md)selecione **adicionar "Obter Variável" à tela** ou adicionar **"set Variable" à tela,** dependendo se pretende obter ou definir a variável.
5. Note que o código do ativo está inserido no livro de execução.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Editar um livro de execução da Automação Azure utilizando o Windows PowerShell

Para editar um livro de execução com o Windows PowerShell, utilize o editor à sua escolha e guarde o livro de execução para um ficheiro **.ps1.** Pode utilizar o cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) para recuperar o conteúdo do livro de execução. Pode utilizar o cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) para substituir o caderno de redação existente pelo modificado.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Recupere o conteúdo de um livro de corridas utilizando o Windows PowerShell

Os comandos de exemplo seguintes exemplificam como obter o script de um runbook e guardá-lo num ficheiro de script. Neste exemplo, é obtida a versão de Rascunho. Também é possível recuperar a versão publicada do livro de corridas, embora esta versão não possa ser alterada.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Altere o conteúdo de um livro de execução utilizando o Windows PowerShell

Os comandos de amostra seguem mostrar como substituir o conteúdo existente de um livro de execução com o conteúdo de um ficheiro de script. Este é o mesmo procedimento de amostra que em [Importar um livro de execução de um ficheiro de script com windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Artigos relacionados

* [Gerir livros de corridas na Automação Azure](manage-runbooks.md)
* [Fluxo de trabalho PowerShell de aprendizagem](automation-powershell-workflow.md)
* [Autoria gráfica em Automação Azure](automation-graphical-authoring-intro.md)
* [Certificados](automation-certificates.md)
* [Ligações](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Agendas](automation-schedules.md)
* [Variáveis](automation-variables.md)
* [Referência do cmdlet do PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
