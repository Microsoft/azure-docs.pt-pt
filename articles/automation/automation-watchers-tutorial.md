---
title: Criar uma tarefa de observador na conta de automatização do Azure
description: Saiba como criar uma tarefa de observador na conta de automatização do Azure para ver a existência de novos ficheiros criados numa pasta.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 7d7ebc7493ff8e272e35abafeedd8fbc52b986ab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002153"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar tarefas para controlar alterações de arquivo numa máquina local de um observador de automatização do Azure

A automatização do Azure utiliza tarefas de observador para observar eventos e disparar ações com runbooks do PowerShell. Este tutorial explica como criar uma tarefa de observador para monitorizar quando é adicionado um novo ficheiro para um diretório.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um runbook de observador
> * Criar uma variável de automatização
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Acionar um observador
> * Verifique os resultados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para reter os runbooks de observador e ação e a tarefa de observador.
* R [runbook worker híbrido](automation-hybrid-runbook-worker.md) onde é executada a tarefa de observador.

## <a name="import-a-watcher-runbook"></a>Importar um runbook de observador

Este tutorial utiliza um runbook de observador denominado **assista NewFile** procurar novos ficheiros num diretório. O runbook de observador obtém a última hora de gravação conhecidos para os ficheiros numa pasta e analisa todos os ficheiros mais recentes do que esse limite de tamanho. Neste passo, é possível importar este runbook para a sua conta de automatização.

1. Abra a sua conta de automatização e clique nas **Runbooks** página.
2. Clique nas **Galeria de procura** botão.
3. Procure "Runbook de observador", selecione **runbook de observador que procura por novos ficheiros num diretório** e selecione **importação**.
  ![Importar runbook da automatização a partir da interface do Usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Atribua ao runbook, um nome e descrição e selecione **OK** para importar o runbook para sua conta de automatização.
1. Selecione **edite** e, em seguida, clique em **Publish**. Quando lhe for pedido selecione **Sim** para publicar o runbook.

## <a name="create-an-automation-variable"></a>Criar uma variável de automatização

Uma [variável de automatização](automation-variables.md) é utilizada para armazenar os carimbos de data / que o runbook anterior lê e armazena de cada arquivo.

1. Selecione **variáveis** sob **recursos PARTILHADOS** e selecione **+ adicionar uma variável**.
1. Introduza "Watch-NewFileTimestamp" para o nome
1. Selecione DateTime para o tipo.
1. Clique nas **criar** botão. Esta ação cria a variável de automatização.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é utilizado numa tarefa de observador para tomar decisões sobre os dados passados para ele, a partir de um runbook de observador. Runbooks de fluxo de trabalho do PowerShell não são suportadas pelas tarefas de observador, tem de utilizar runbooks do PowerShell. Neste passo, atualizar importar um runbook de ação predefinido denominado "NewFile de processo".

1. Navegue para a sua conta de automatização e selecione **Runbooks** sob a **AUTOMATIZAÇÃO de processos** categoria.
1. Clique nas **Galeria de procura** botão.
1. Procure "Ação de observador" e selecione **ação de observador que processa os eventos acionados por um runbook de observador** e selecione **importação**.
  ![Importar runbook de ação de interface do Usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Atribua ao runbook, um nome e descrição e selecione **OK** para importar o runbook para sua conta de automatização.
1. Selecione **edite** e, em seguida, clique em **Publish**. Quando lhe for pedido selecione **Sim** para publicar o runbook.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

A tarefa de observador contém duas partes. O observador e ação. O observador é executado num intervalo definido na tarefa de observador. Dados a partir do runbook de observador são passados para o runbook de ação. Neste passo, irá configurar a tarefa de observador referenciar os runbooks de observador e ação definidos nos passos anteriores.

1. Navegue para a sua conta de automatização e selecione **tarefas de observador** sob a **AUTOMATIZAÇÃO de processos** categoria.
1. Selecione a página de tarefas de observador e clique em **+ adicionar uma tarefa de observador** botão.
1. Introduza "WatchMyFolder" como o nome.

1. Selecione **configurar observador** e selecione o **Watch NewFile** runbook.

1. Introduza os seguintes valores para os parâmetros:

   * **FOLDERPATH** -uma pasta na função de trabalho híbrida, onde são criados novos ficheiros. d:\examplefiles
   * **EXTENSÃO** -deixe em branco para processar todas as extensões de ficheiro.
   * **RECURSE** -deixe este valor como predefinição.
   * **CONFIGURAÇÕES de execução de** -selecionar função de trabalho híbrida.

1. Clique em OK e, em seguida, selecione para voltar à página de observador.
1. Selecione **configurar a ação** e selecione o runbook "NewFile de processo".
1. Introduza os seguintes valores para parâmetros:

   * **EVENTDATA** -deixe em branco. Dados são passados do runbook de observador.  
   * **Definições de execução** -deixe como o Azure como este runbook é executado no serviço de automatização.

1. Clique em **OK**e, em seguida, selecione para voltar à página de observador.
1. Clique em **OK** para criar a tarefa de observador.

![Configurar a ação de observador de interface do Usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Acionar um observador

Para testar o observador está a funcionar conforme esperado, tem de criar um ficheiro de teste.

Remotamente na função de trabalho híbrida. Open **PowerShell** e crie um ficheiro de teste na pasta.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

O exemplo seguinte mostra a saída esperada.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Verifique os resultados

1. Navegue para a sua conta de automatização e selecione **tarefas de observador** sob a **AUTOMATIZAÇÃO de processos** categoria.
1. Selecione a tarefa de observador "WatchMyFolder".
1. Clique em **ver fluxos de observador** sob **fluxos** para ver que o observador foi encontrado o ficheiro novo e iniciou o runbook de ação.
1. Para ver as tarefas de runbook de ação, clique nas **ver tarefas de ação de observador**. Cada tarefa pode ser selecionado o modo de exibição detalhes da tarefa.

   ![Tarefas de ação de observador de interface do Usuário](media/automation-watchers-tutorial/WatcherActionJobs.png)

O resultado esperado quando o novo arquivo for encontrado pode ser visto no exemplo a seguir:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar um runbook de observador
> * Criar uma variável de automatização
> * Criar um runbook de ação
> * Criar uma tarefa de observador
> * Acionar um observador
> * Verifique os resultados

Siga esta ligação para saber mais sobre a criação de seu próprio runbook.

> [!div class="nextstepaction"]
> [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

