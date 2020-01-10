---
title: Criar uma tarefa do Inspetor na conta de automação do Azure
description: Saiba como criar uma tarefa de observador na conta de automação do Azure para observar novos arquivos criados em uma pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75419745"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar tarefas do observador de automação do Azure para rastrear alterações de arquivo em um computador local

A automação do Azure usa tarefas do inspetor para inspecionar eventos e disparar ações com runbooks do PowerShell. Este tutorial orienta você pela criação de uma tarefa do observador para monitorar quando um novo arquivo é adicionado a um diretório.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um runbook do Inspetor
> * Criar uma variável de automação
> * Criar um runbook de ação
> * Criar uma tarefa do Inspetor
> * Disparar um inspetor
> * Inspecionar a saída

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o Inspetor e os runbooks de ação e a tarefa do Inspetor.
* Um [Hybrid runbook Worker](automation-hybrid-runbook-worker.md) em que a tarefa do Inspetor é executada.

> [!NOTE]
> Não há suporte para tarefas do Inspetor no Azure China.

## <a name="import-a-watcher-runbook"></a>Importar um runbook do Inspetor

Este tutorial usa um runbook do observador chamado **Watch-NewFile** para procurar novos arquivos em um diretório. O runbook do Inspetor recupera o último tempo de gravação conhecido para os arquivos em uma pasta e examina todos os arquivos mais recentes que essa marca d' água.

Esse processo de importação pode ser feito por meio do [Galeria do PowerShell](https://www.powershellgallery.com).

1. Navegue até a página da galeria para [Watch-newfile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na guia **automação do Azure** , clique em **implantar na automação do Azure**.

Você também pode importar esse runbook para sua conta de automação do portal usando as etapas a seguir.

1. Abra sua conta de automação e clique na página **Runbooks** .
2. Clique no botão **procurar na Galeria** .
3. Pesquise "runbook do Inspetor", selecione **runbook do inspetor que procura por novos arquivos em um diretório** e selecione **importar**.
  ![importar runbook de automação da interface do usuário](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dê um nome e uma descrição ao runbook e selecione **OK** para importar o runbook para sua conta de automação.
1. Selecione **Editar** e clique em **publicar**. Quando solicitado, selecione **Sim** para publicar o runbook.

## <a name="create-an-automation-variable"></a>Criar uma variável de automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de data/hora que o runbook anterior lê e armazena de cada arquivo.

1. Selecione **variáveis** em **recursos compartilhados** e selecione **+ Adicionar uma variável**.
1. Insira "Watch-NewFileTimestamp" para o nome
1. Selecione DateTime para o tipo.
1. Clique no botão **criar** . Isso cria a variável de automação.

## <a name="create-an-action-runbook"></a>Criar um runbook de ação

Um runbook de ação é usado em uma tarefa do inspetor para agir sobre os dados passados para ele de um runbook do Inspetor. Não há suporte para runbooks de fluxo de trabalho do PowerShell em tarefas do Inspetor, você deve usar runbooks do PowerShell. Você deve importar um runbook de ação predefinido chamado **process-NewFile**.

Esse processo de importação pode ser feito por meio do [Galeria do PowerShell](https://www.powershellgallery.com).

1. Navegue até a página da galeria para [process-newfile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na guia **automação do Azure** , clique em **implantar na automação do Azure**.

Você também pode importar esse runbook para sua conta de automação do portal usando as etapas a seguir.

1. Navegue até sua conta de automação e selecione **Runbooks** na categoria **automação de processo** .
1. Clique no botão **procurar na Galeria** .
1. Pesquise "ação do Inspetor" e selecione **ação do observador que processa eventos disparados por um runbook do Inspetor** e selecione **importar**.
  ![importar o runbook de ação da interface do usuário](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê um nome e uma descrição ao runbook e selecione **OK** para importar o runbook para sua conta de automação.
1. Selecione **Editar** e clique em **publicar**. Quando solicitado, selecione **Sim** para publicar o runbook.

## <a name="create-a-watcher-task"></a>Criar uma tarefa do Inspetor

A tarefa do Inspetor contém duas partes. O Inspetor e a ação. O Inspetor é executado em um intervalo definido na tarefa do Inspetor. Os dados do runbook do observador são passados para o runbook de ação. Nesta etapa, você configura a tarefa do inspetor que faz referência aos runbooks do Inspetor e da ação definidos nas etapas anteriores.

1. Navegue até sua conta de automação e selecione **tarefas do Inspetor** na categoria automação de **processo** .
1. Selecione a página tarefas do Inspetor e clique em **+ Adicionar um botão de tarefa do Inspetor** .
1. Insira "WatchMyFolder" como o nome.

1. Selecione **Configurar Inspetor** e selecione o runbook **Watch-NewFile** .

1. Insira os seguintes valores para os parâmetros:

   * **FOLDERPATH** -uma pasta no Hybrid Worker onde novos arquivos são criados. d:\examplefiles
   * **Extensão** -deixe em branco para processar todas as extensões de arquivo.
   * **Recurse** -deixe esse valor como o padrão.
   * **Configurações de execução** -escolha o trabalhador híbrido.

1. Clique em OK e, em seguida, selecione para retornar à página do Inspetor.
1. Selecione **Configurar ação** e selecione runbook "Process-NewFile".
1. Insira os seguintes valores para parâmetros:

   * **EVENTDATA** -deixe em branco. Os dados são passados do runbook do Inspetor.
   * **Configurações de execução** -deixe como Azure, pois esse runbook é executado no serviço de automação.

1. Clique em **OK**e, em seguida, selecione para retornar à página do Inspetor.
1. Clique em **OK** para criar a tarefa do Inspetor.

![Configurar ação do inspetor da interface do usuário](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Disparar um inspetor

Para testar se o inspetor está funcionando conforme o esperado, você precisa criar um arquivo de teste.

Remoto para o Hybrid Worker. Abra o **PowerShell** e crie um arquivo de teste na pasta.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

O exemplo a seguir mostra a saída esperada.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspecionar a saída

1. Navegue até sua conta de automação e selecione **tarefas do Inspetor** na categoria automação de **processo** .
1. Selecione a tarefa do Inspetor "WatchMyFolder".
1. Clique em **Exibir fluxos do Inspetor** em **fluxos** para ver se o observador encontrou o novo arquivo e iniciou o runbook de ação.
1. Para ver os trabalhos de runbook de ação, clique em **exibir trabalhos de ação do Inspetor**. Cada trabalho pode ser selecionado para exibir os detalhes do trabalho.

   ![Trabalhos de ação do inspetor da interface do usuário](media/automation-watchers-tutorial/WatcherActionJobs.png)

A saída esperada quando o novo arquivo é encontrado pode ser vista no exemplo a seguir:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar um runbook do Inspetor
> * Criar uma variável de automação
> * Criar um runbook de ação
> * Criar uma tarefa do Inspetor
> * Disparar um inspetor
> * Inspecionar a saída

Siga este link para saber mais sobre como criar seu próprio runbook.

> [!div class="nextstepaction"]
> [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

