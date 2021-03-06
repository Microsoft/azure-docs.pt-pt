---
title: Acompanhe os ficheiros atualizados com uma tarefa de observador da Azure Automation
description: Este artigo diz como criar uma tarefa de observador na conta Azure Automation para ver os novos ficheiros criados numa pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97682744"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Rastreia ficheiros atualizados com uma tarefa de observador

A Azure Automation utiliza uma tarefa de observador para procurar eventos e desencadear ações com os runbooks PowerShell. A tarefa do observador contém duas partes, o observador e a ação. Um livro de execução do observador executa num intervalo definido na tarefa do observador, e produz dados para um runbook de ação.

> [!NOTE]
> As tarefas de observadores não são suportadas no Azure China Vianet 21.

> [!IMPORTANT]
> A partir de maio de 2020, a utilização de Azure Logic Apps é a forma recomendada e suportada de monitorizar eventos, agendar tarefas recorrentes e desencadear ações. Consulte [Agendar e executar tarefas, processos e fluxos de trabalho automáticos recorrentes com apps Azure Logic](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Este artigo acompanha-o através da criação de uma tarefa de observador para monitorizar quando um novo ficheiro é adicionado a um diretório. Saiba como:

* Importar um livro de bordo de observadores
* Criar uma variável de automação
* Criar um livro de ação
* Criar uma tarefa de observador
* Desencadear um observador
* Inspecione a saída

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, são necessários os seguintes artigos:

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automação](./index.yml) para manter os livros de observadores e de ação e a Tarefa do Observador.
* Um [trabalhador híbrido de runbook](automation-hybrid-runbook-worker.md) onde a tarefa do observador funciona.
* Livros powerShell. Os livros de fluxo de trabalho PowerShell não são suportados por tarefas de observadores.

## <a name="import-a-watcher-runbook"></a>Importar um livro de bordo de observadores

Este artigo usa um livro de observadores chamado **Watch-NewFile** para procurar novos ficheiros num diretório. O livro de verificação do observador recupera a última vez que se escreve os ficheiros numa pasta e olha para quaisquer ficheiros mais recentes do que aquela marca de água.

Você pode baixar o runbook da [organização Azure Automation GitHub](https://github.com/azureautomation).

1. Navegue na página de organização Azure Automation GitHub para [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Para descarregar o runbook do GitHub, selecione **Código** do lado direito da página e, em seguida, selecione **Download ZIP** para descarregar todo o código num ficheiro postal.
3. Extrair o conteúdo e [importar o livro de recortes.](manage-runbooks.md#import-a-runbook-from-the-azure-portal)

Também pode importar este livro de bordo para a sua conta Demôm automação a partir do portal utilizando os seguintes passos.

1. Abra a sua conta Demômes e clique na página Runbooks.
2. Clique **na galeria Browse** e na lista de drop-down de **Origem** selecione **GitHub**.
3. Procure no **runbook do Observador**, selecione **o livro de imagens do Observador que procura novos ficheiros num diretório** e clique em **Import**.
4. Dê ao livro de bordo um nome e descrição e clique **em OK** para importar o livro de bordo na sua conta Demôm automação.
5. **Selecione Editar** e, em seguida, clique em **Publicar.** Quando solicitado, clique em **Sim** para publicar o livro de recortes.

## <a name="create-an-automation-variable"></a>Criar uma variável de automação

Uma [variável de automação](./shared-resources/variables.md) é utilizada para armazenar os sinais temporais que o livro de aplicação anterior lê e armazena a partir de cada ficheiro.

1. Selecione **variáveis** em **Recursos Partilhados** e clique **+ Adicione uma variável.**
1. Introduza **o relógio-NewFileTimestamp** para o nome.
1. Selecione DateTime para o tipo.
1. Clique em **Criar** para criar a variável Automação.

## <a name="create-an-action-runbook"></a>Criar um livro de ação

Um runbook de ação é usado numa tarefa de observador para agir sobre os dados que lhe são transmitidos a partir de um livro de bordo do Observador. Você deve importar um livro de ação pré-definido chamado **Process-NewFile** da [organização Azure Automation GitHub](https://github.com/azureautomation).

Para criar um livro de ação:

1. Navegue na página de organização Azure Automation GitHub para [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Para descarregar o runbook do GitHub, selecione **Código** do lado direito da página e, em seguida, selecione **Download ZIP** para descarregar todo o código num ficheiro postal.
3. Extrair o conteúdo e [importar o livro de recortes.](manage-runbooks.md#import-a-runbook-from-the-azure-portal)

Também pode importar este livro de bordo na sua conta de Automação a partir do portal Azure:

1. Navegue na sua conta de Automação e selecione **Runbooks** em **Automação de Processos.**
1. Clique **na galeria Browse** e na lista de drop-down de **Origem** selecione **GitHub**.
1. Search for **Watcher action**, selecione **Ação do Observador que processa eventos desencadeados por um livro de bordo de observadores**, e clique em **Import**.
1. Dê ao livro de bordo um nome e descrição e clique **em OK** para importar o livro de bordo na sua conta Demôm automação.
1. **Selecione Editar** e, em seguida, clique em **Publicar.** Quando solicitado, clique em **Sim** para publicar o livro de recortes.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

Neste passo, configurar a tarefa do observador referenciando os livros de observadores e de ação definidos nas secções anteriores.

1. Navegue na sua conta de Automação e **selecione tarefas do Observador** em **Automatização de Processos.**
1. Selecione a página de tarefas do Observador e clique **+ Adicione uma tarefa de observador**.
1. **Insira watchMyFolder** como o nome.

1. Selecione **o observador configurar** e escolha o runbook **Watch-NewFile.**

1. Introduza os seguintes valores para os parâmetros:

   * **FOLDERPATH** - Uma pasta no Trabalhador de Runbook Híbrido onde são criados novos ficheiros, por exemplo, **d:\exemplofilos**.
   * **EXTENSÃO** - Extensão para a configuração. Deixe em branco para processar todas as extensões de ficheiros.
   * **RECURSE** - Operação recursiva. Deixe este valor como padrão.
   * **DEFINIÇÕES DE EXECUÇÃO** - Definição para executar o livro de execução. Escolha o trabalhador híbrido.

1. Clique **em OK** e, em seguida, **selecione** para voltar à página Observador.
1. Selecione **configurar ação** e escolha o livro de execução **Process-NewFile.**
1. Introduza os seguintes valores para os parâmetros:

   * **EVENTDATA** - Dados do evento. Deixe em branco. Os dados são transmitidos a partir do livro de observadores.
   * **Definições de execução** - Definição para executar o livro de execução. Deixe como Azure, como este runbook funciona na Azure Automation.

1. Clique **em OK** e, em seguida, **selecione** para voltar à página Observador.
1. Clique **em OK** para criar a tarefa de observador.

    ![Configure ação de observador da UI](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Desencadear um observador

Deve fazer um teste como descrito abaixo para garantir que a tarefa do observador funciona como esperado. 

1. Remoto para o Trabalhador de Runbook Híbrido.
2. Abra **o PowerShell** e crie um ficheiro de teste na pasta.

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

## <a name="inspect-the-output"></a>Inspecione a saída

1. Navegue na sua conta de Automação e **selecione tarefas do Observador** em **Automatização de Processos.**
1. Selecione a tarefa do observador **WatchMyFolder**.
1. Clique em **Ver streams de observadores** em **Streams** para ver se o observador encontrou o novo ficheiro e iniciou o runbook de ação.
1. Para ver os trabalhos de ação runbook, clique em **Ver os trabalhos de ação do Observador.** Cada trabalho pode ser selecionado para ver os detalhes do trabalho.

   ![Trabalhos de ação de observadores da UI](media/automation-watchers-tutorial/WatcherActionJobs.png)

A saída esperada quando o novo ficheiro é encontrado pode ser vista no seguinte exemplo:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autoria do seu próprio livro de bordo, consulte [Criar um livro de recortes PowerShell.](learn/automation-tutorial-runbook-textual-powershell.md)
