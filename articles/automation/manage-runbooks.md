---
title: Gerir livros de corridas na Automação Azure
description: Este artigo descreve como gerir livros de corridas na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 4531e5d483508ca99722182f97c6a7aa0e0b68f3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191117"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerir livros de corridas na Automação Azure

Pode adicionar um livro de execução à Azure [Automation, criando um novo](#create-a-runbook) ou [importando um existente](#import-a-runbook) a partir de um ficheiro ou da Galeria [Runbook.](automation-runbook-gallery.md) Este artigo fornece informações sobre a criação e importação de livros de execução a partir de um ficheiro. Você pode obter todos os detalhes de aceder a livros e módulos comunitários em [Runbook e galerias de módulos para A Automação Azure](automation-runbook-gallery.md).

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Criar um runbook

Pode criar um novo livro de corridas em Azure Automation utilizando um dos portais Azure ou Windows PowerShell. Uma vez criado o livro de execução, pode editá-lo usando informações em [Learning PowerShell Workflow](automation-powershell-workflow.md) e [autorgráfico em Automação Azure](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Crie um livro de corridas no portal Azure

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do centro, selecione **Runbooks** sob Automatização de **Processos** para abrir a lista de livros de execução.
3. Clique **em Criar um livro de execução**.
4. Introduza um nome para o livro de execução e selecione o seu [Tipo](automation-runbook-types.md). O nome do livro de corridas deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** o livro de execução e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Crie um livro de corridas com a PowerShell

Pode utilizar o cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) para criar um livro de execução de [fluxo de trabalho powerShell](automation-runbook-types.md#powershell-workflow-runbooks)vazio . Utilize o parâmetro *Tipo* para especificar um dos tipos de livro definidos para **O Livro New-AzAutomationRunbook**.

O exemplo que se segue mostra como criar um novo livro vazio.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importar um livro de corridas

Pode criar um novo livro de corridas em Azure Automation, importando um script PowerShell ou PowerShell Workflow ( **.ps1),** um livro gráfico exportado **(.graphrunbook),** ou um guião Python 2 **(.py).**  Deve especificar o tipo de livro de [execução](automation-runbook-types.md) que é criado durante a importação, tendo em conta as seguintes considerações.

* Um ficheiro **.ps1** que não contenha um fluxo de trabalho pode ser importado para um livro de [execução powerShell](automation-runbook-types.md#powershell-runbooks) ou um livro de [execução powerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se o importar num livro de fluxos de trabalho powerShell, é convertido num fluxo de trabalho. Neste caso, os comentários estão incluídos no livro de execução para descrever as alterações que foram feitas.

* Um ficheiro **.ps1** contendo um PowerShell Workflow só pode ser importado num livro de [execução powerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Se o ficheiro contiver múltiplos fluxos de trabalho powerShell, a importação falha. Deve guardar cada fluxo de trabalho para o seu próprio ficheiro e importar cada um separadamente.

* Um ficheiro **.ps1** que contenha um PowerShell Workflow não deve ser importado num livro de [execução powerShell](automation-runbook-types.md#powershell-runbooks), uma vez que o motor de script PowerShell não o reconhece.

* Um ficheiro **.graphrunbook** só pode ser importado num novo [livro de execução gráfico](automation-runbook-types.md#graphical-runbooks). Note que só pode criar um livro de execução gráfico a partir de um ficheiro **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importar um livro de execução de um ficheiro com o portal Azure

Pode utilizar o seguinte procedimento para importar um ficheiro de script para a Automação Azure.

> [!NOTE]
> Só é possível importar um ficheiro **.ps1** num livro de fluxo sinuoso powerShell utilizando o portal.

1. No portal do Azure, abra a sua conta da Automatização.
2. A partir do centro, selecione **Runbooks** sob Automatização de **Processos** para abrir a lista de livros de execução.
3. Clique **em importar um livro de execução**.
4. Clique no **ficheiro Runbook** e selecione o ficheiro para importar.
5. Se o campo **Nome** estiver ativado, tem a opção de alterar o nome do livro de execução. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O tipo de livro de [execução](automation-runbook-types.md) é automaticamente selecionado, mas pode alterar o tipo depois de ter em conta as restrições aplicáveis.
7. Clique em **Criar**. O novo livro de execução aparece na lista de livros de execução para a conta Automation.
8. Tem de publicar o livro de [execução](#publish-a-runbook) antes de o poder executar.

> [!NOTE]
> Depois de importar um livro de execução gráfico ou um livro de execução gráfico powerShell Workflow, pode convertê-lo para outro tipo. No entanto, não é possível converter um destes livros gráficos num livro textual.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importar um runbook a partir de um ficheiro de script com o Windows PowerShell

Utilize o cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) para importar um ficheiro script como um projeto de livro de execução powerShell Workflow. Se o livro de execução já existir, a importação falha a menos que utilize o parâmetro *Force* com o cmdlet.

O exemplo que se segue mostra como importar um ficheiro de script num livro de corridas.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testar um runbook

Quando testa um livro de execução, a [versão Draft](#publish-a-runbook) é executada e quaisquer ações que executa são concluídas. Não é criado histórico de trabalho, mas os fluxos [de saída](automation-runbook-output-and-messages.md#output-stream) e aviso [e erro](automation-runbook-output-and-messages.md#message-streams) são apresentados no painel de saída do Teste. As mensagens para o [fluxo Verbose](automation-runbook-output-and-messages.md#message-streams) só são apresentadas no painel de saída se a [variável $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) estiver definida para **continuar**.

Mesmo que o projeto de versão esteja a ser executado, o livro de execução ainda executa normalmente e executa quaisquer ações contra recursos no ambiente. Por esta razão, só deve testar livros de execução sobre recursos não produtivos.

O procedimento para testar cada tipo de livro de [execução](automation-runbook-types.md) é o mesmo. Não há diferença nos testes entre o editor textual e o editor gráfico no portal Azure.

1. Abra a versão Draft do livro de execução no [editor textual](automation-edit-textual-runbook.md) ou no [editor gráfico.](automation-graphical-authoring-intro.md)
1. Clique no botão **Teste** para abrir a página de Teste.
1. Se o livro tiver parâmetros, estão listados no painel esquerdo, onde pode fornecer valores a utilizar para o teste.
1. Se quiser eexecutar o teste num Trabalhador híbrido do livro de [corridas,](automation-hybrid-runbook-worker.md)altere as **Definições** de Execução para **O Trabalhador Híbrido** e selecione o nome do grupo alvo.  Caso contrário, mantenha o **Azure** padrão para executar o teste na nuvem.
1. Clique no botão **Iniciar** para iniciar o teste.
1. Pode utilizar os botões sob o painel de saída para parar ou suspender um [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) ou um livro [gráfico](automation-runbook-types.md#graphical-runbooks) enquanto está a ser testado. Quando suspende o runbook, conclui a atividade atual antes de ser suspenso. Depois do runbook está suspenso, pode pará-lo ou reiniciá-lo.
1. Inspecione a saída do livro de execução no painel de saída.

## <a name="publish-a-runbook"></a>Publicar um livro de corridas

Quando criar ou importar um novo livro de execução, deve publicá-lo antes de o poder executar. Cada livro de execução em Azure Automation tem uma versão Draft e uma versão Publicada. Apenas a versão publicada está disponível para ser executado, e apenas a versão de rascunho pode ser editada. A versão publicada não é afetada por quaisquer alterações feitas à versão de rascunho. Quando a versão Do Projeto deve ser disponibilizada, publica-a, sobressando a versão atual publicada com a versão Draft.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publique um livro de corridas no portal Azure

1. Abra o livro de corridas no portal Azure.
2. Clique em **Editar**.
3. Clique em **Publicar** e, em **seguida, sim** em resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publicar um runbook com o PowerShell

Utilize o cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) para publicar um livro de execução com o Windows PowerShell. O exemplo que se segue mostra como publicar um livro de amostras.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Passos seguintes

* Para saber como pode beneficiar da Galeria de Módulos Runbook e PowerShell, consulte [Runbook e galerias de módulos para a Automação Azure.](automation-runbook-gallery.md)
* Para saber mais sobre a edição de livros de execução PowerShell e PowerShell Workflow com um editor textual, consulte [livros textuais de edição em Automação Azure](automation-edit-textual-runbook.md).
* Para saber mais sobre a autoria de um livro gráfico, consulte [a autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)
