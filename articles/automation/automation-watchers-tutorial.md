---
title: Rastreie ficheiros atualizados com uma tarefa de observador de Automação Azure
description: Este artigo diz como criar uma tarefa de observador na conta Azure Automation para ver novos ficheiros criados numa pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 3369a807410e9e959e8091d5b16c8480803d26bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830587"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Rastreie ficheiros atualizados com uma tarefa de observador

A Azure Automation usa uma tarefa de observadores para procurar eventos e desencadear ações com os livros de execução da PowerShell. A tarefa do observador contém duas partes, o observador e a ação. Um livro de execução de observadores funciona num intervalo definido na tarefa do observador, e produz dados para um livro de execução de ação. 

> [!NOTE]
> As tarefas de observadores não são suportadas na Vianet 21 da China Azure.

> [!IMPORTANT]
> A partir de maio de 2020, a utilização de Apps Lógicas Azure é a forma suportada de monitorizar eventos, agendar tarefas recorrentes e desencadear ações. Consulte [agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicações lógicas azure](https://docs.microsoft.com/azure/logic-apps/concepts-schedule-automated-recurring-tasks-workflows).

Este tutorial leva-o através da criação de uma tarefa de observadores para monitorizar quando um novo ficheiro é adicionado a um diretório. Saiba como:

> [!div class="checklist"]
> * Importar um livro de observadores
> * Criar uma variável de Automação
> * Criar um livro de corridas de ação
> * Criar uma tarefa de observador
> * Desencadear um observador
> * Inspecione a saída

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter os cadernos de observadores e de ação e a Tarefa observadora.
* Um trabalhador híbrido de livro de [corridas](automation-hybrid-runbook-worker.md) onde a tarefa do observador funciona.
* Livros de execução da PowerShell. Os livros de execução powerShell Workflow não são suportados por tarefas de observadores.

## <a name="import-a-watcher-runbook"></a>Importar um livro de observadores

Este tutorial usa um livro de observadores chamado **Watch-NewFile** para procurar novos ficheiros num diretório. O livro de execução do observador recupera o último tempo de escrita conhecido para os ficheiros numa pasta e olha para quaisquer ficheiros mais recentes do que essa marca de água.

Este processo de importação pode ser feito através da [Galeria PowerShell.](https://www.powershellgallery.com)

1. Navegue na página da galeria para [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Sob o **separador Automação Azure,** clique em **Implementar para automação Azure**.

Também pode importar este livro de execução para a sua conta de automação a partir do portal utilizando os seguintes passos.

1. Abra a sua conta De automação e clique na página DeRões.
2. Clique na **galeria Browse**.
3. Procure o livro de **execução do Observador**, selecione o livro de **execução do Observador que procura novos ficheiros num diretório**, e clique em **Importar**.
  ![Livro de automação de importação da UI](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Dê ao livro de recortes um nome e descrição e clique **EM OK** para importar o livro de execução para a sua conta Deautomação.
5. **Selecione Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique **em Sim** para publicar o livro de execução.

## <a name="create-an-automation-variable"></a>Criar uma variável de Automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de tempo que o livro de execução anterior lê e armazena de cada ficheiro.

1. Selecione **Variáveis** em **Recursos Partilhados** e clique **+ Adicione uma variável**.
1. Introduza o watch-NewFileTimestamp para o nome.
1. Selecione DateTime para o tipo.
1. Clique em **Criar** para criar a variável Automação.

## <a name="create-an-action-runbook"></a>Criar um livro de corridas de ação

Um livro de execução de ação é usado numa tarefa de observadores para agir sobre os dados que lhe são transmitidos a partir de um livro de execução de observadores. Deve importar um livro de execução de ação pré-definido chamado **Process-NewFile** da [PowerShell Gallery](https://www.powershellgallery.com). 

Para criar um livro de corridas de ação:

1. Navegue na página da galeria para [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Sob o **separador Automação Azure,** clique em **Implementar para automação Azure**.

Também pode importar este livro de execução para a sua conta Deautomação a partir do portal Azure:

1. Navegue para a sua conta de Automação e selecione **Runbooks** em **Automação de Processos**.
1. Clique na **galeria Browse**.
1. Procure **a ação do Observador**, selecione **a ação do Observador que processa eventos desencadeados por um livro**de execução de observadores, e clique em **Importar**.
  ![Livro de ação de importação da UI](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê ao livro de recortes um nome e descrição e clique **EM OK** para importar o livro de execução para a sua conta Deautomação.
1. **Selecione Editar** e, em seguida, clique em **Publicar**. Quando solicitado, clique **em Sim** para publicar o livro de execução.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

Neste passo, configura a tarefa do observador referindo-se aos cadernos de observadores e de ação definidos nas secções anteriores.

1. Navegue para a sua conta de Automação e selecione **tarefas do Observador** no âmbito da **Automatização de Processos**.
1. Selecione a página de tarefas do Observador e clique **+ Adicione uma tarefa de observador**.
1. Introduza **watchMyFolder** como o nome.

1. **Selecione Configure observador** e escolha o livro de execução **Watch-NewFile.**

1. Introduza os seguintes valores para os parâmetros:

   * **FOLDERPATH** - Uma pasta no Trabalhador do Livro Híbrido onde novos ficheiros são criados, por exemplo, **d:\exemplofiles**.
   * **EXTENSÃO** - Extensão para a configuração. Deixe em branco processar todas as extensões de ficheiros.
   * **RECURSE** - Operação recursiva. Deixe este valor como padrão.
   * **DEFINIÇÕES DE EXECUÇÃO** - Definição para executar o livro de execução. Escolha o trabalhador híbrido.

1. Clique **em OK**e, em seguida, **Selecione** para voltar à página Observador.
1. **Selecione configurar a ação** e escolha o livro de execução **Processo-NovoFicheiro.**
1. Introduza os seguintes valores para os parâmetros:

   * **EVENTDATA** - Dados do evento. Deixe em branco. Os dados são transmitidos pelo livro de execução dos observadores.
   * **Definições de execução** - Definição para executar o livro de execução. Deixe como Azure, já que este livro corre na Azure Automation.

1. Clique **em OK**e, em seguida, **Selecione** para voltar à página Observador.
1. Clique em **OK** para criar a tarefa do observador.

![Configurar a ação do observador a partir da UI](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Desencadear um observador

Deve eexecutar um teste como descrito abaixo para garantir que a tarefa do observador funcione como esperado. 

1. Remoto no Trabalhador do Livro Híbrido. 
2. Abra o **PowerShell** e crie um ficheiro de teste na pasta.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

O exemplo que se segue mostra a saída esperada.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Inspecione a saída

1. Navegue para a sua conta de Automação e selecione **tarefas do Observador** no âmbito da **Automatização de Processos**.
1. Selecione a tarefa do observador **WatchMyFolder**.
1. Clique em **Ver streams** de observadores em **Streams** para ver se o observador encontrou o novo ficheiro e iniciou o livro de execução de ação.
1. Para ver os trabalhos de corrida, clique em Ver empregos de **ação de observadores**. Cada trabalho pode ser selecionado para ver os detalhes do trabalho.

   ![Trabalhos de ação de observadores da UI](media/automation-watchers-tutorial/WatcherActionJobs.png)

A saída esperada quando o novo ficheiro for encontrado pode ser vista no seguinte exemplo:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Importar um livro de observadores
> * Criar uma variável de Automação
> * Criar um livro de corridas de ação
> * Criar uma tarefa de observador
> * Desencadear um observador
> * Inspecione a saída

Siga este link para saber mais sobre a autoria do seu próprio livro de corridas.

> [!div class="nextstepaction"]
> [Criar runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md)