---
title: Criar uma tarefa de observador na conta Azure Automation
description: Saiba como criar uma tarefa de observador na conta Azure Automation para ver novos ficheiros criados numa pasta.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75419745"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Criar uma tarefa de observador de automação Azure para rastrear alterações de ficheiros numa máquina local

A Azure Automation utiliza tarefas de observadores para assistir a eventos e desencadear ações com os livros de execução da PowerShell. Este tutorial leva-o através da criação de uma tarefa de observadores para monitorizar quando um novo ficheiro é adicionado a um diretório.

Neste tutorial, ficará a saber como:

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

> [!NOTE]
> As tarefas dos observadores não são apoiadas na China Azure.

## <a name="import-a-watcher-runbook"></a>Importar um livro de observadores

Este tutorial usa um livro de observadores chamado **Watch-NewFile** para procurar novos ficheiros num diretório. O livro de execução do observador recupera o último tempo de escrita conhecido para os ficheiros numa pasta e olha para quaisquer ficheiros mais recentes do que essa marca de água.

Este processo de importação pode ser feito através da [Galeria PowerShell.](https://www.powershellgallery.com)

1. Navegue na página da galeria para [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Sob o **separador Automação Azure,** clique em **Implementar para automação Azure**.

Também pode importar este livro de execução para a sua conta de automação a partir do portal utilizando os seguintes passos.

1. Abra a sua conta De automação e clique na página **DeRões.**
2. Clique no botão **da galeria Browse.**
3. Procure por "Watcher runbook", selecione O livro de **execução do Observador que procura novos ficheiros num diretório** e selecione **Import**.
  ![Livro de automação de importação da UI](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Dê ao livro de recortes um nome e descrição e selecione **OK** para importar o livro de execução para a sua conta Desmitar.
1. **Selecione Editar** e, em seguida, clique em **Publicar**. Quando solicitado selecione **Sim** para publicar o livro de execução.

## <a name="create-an-automation-variable"></a>Criar uma variável de Automação

Uma [variável de automação](automation-variables.md) é usada para armazenar os carimbos de tempo que o livro de execução anterior lê e armazena de cada ficheiro.

1. Selecione **Variáveis** em **RECURSOS PARTILHADOS** e selecione **+ Adicione uma variável**.
1. Introduza "Watch-NewFileTimestamp" para o nome
1. Selecione DataTime for Type.
1. Clique no botão **Criar.** Isto cria a variável de automação.

## <a name="create-an-action-runbook"></a>Criar um livro de corridas de ação

Um livro de execução de ação é usado numa tarefa de observadores para agir sobre os dados que lhe são transmitidos a partir de um livro de execução de observadores. Os livros de execução powerShell Workflow não são suportados por tarefas de observadores, deve utilizar os livros de execução PowerShell. Deve importar um livro de execução de ação pré-definido chamado **Process-NewFile**.

Este processo de importação pode ser feito através da [Galeria PowerShell.](https://www.powershellgallery.com)

1. Navegue na página da galeria para [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Sob o **separador Automação Azure,** clique em **Implementar para automação Azure**.

Também pode importar este livro de execução para a sua conta de automação a partir do portal utilizando os seguintes passos.

1. Navegue para a sua conta de automação e selecione **Runbooks** na categoria **PROCESS AUTOMATION.**
1. Clique no botão **da galeria Browse.**
1. Procure "Ação Observador" e selecione **a ação do Observador que processa eventos desencadeados por um livro de execução de observadores** e selecione **Import**.
  ![Livro de ação de importação da UI](media/automation-watchers-tutorial/importsourceaction.png)
1. Dê ao livro de recortes um nome e descrição e selecione **OK** para importar o livro de execução para a sua conta Desmitar.
1. **Selecione Editar** e, em seguida, clique em **Publicar**. Quando solicitado selecione **Sim** para publicar o livro de execução.

## <a name="create-a-watcher-task"></a>Criar uma tarefa de observador

A tarefa do observador contém duas partes. O observador e a ação. O observador corre num intervalo definido na tarefa do observador. Os dados do livro de execução do observador são transmitidos para o livro de execução de ação. Neste passo, configura a tarefa do observador referindo-se aos cadernos de observadores e de ação definidos nos passos anteriores.

1. Navegue para a sua conta de automação e selecione **tarefas Do Observador** na categoria **DE AUTOMATIZAÇÃO** DE PROCESSOS.
1. Selecione a página de tarefas do Observador e clique em + Adicione um botão de **tarefa do observador.**
1. Introduza "WatchMyFolder" como o nome.

1. **Selecione Configure observador** e selecione o livro de execução **Watch-NewFile.**

1. Introduza os seguintes valores para os parâmetros:

   * **FOLDERPATH** - Uma pasta no trabalhador híbrido onde novos ficheiros são criados. d:\exemplofiles
   * **EXTENSÃO** - Deixe em branco processar todas as extensões de ficheiros.
   * **RECURSE** - Deixe este valor como padrão.
   * **DEFINIÇÕES DE EXECUÇÃO** - Escolha o trabalhador híbrido.

1. Clique em OK e, em seguida, Selecione para voltar à página do observador.
1. **Selecione a ação de configurar** e selecione o livro de execução "Process-NewFile".
1. Introduza os seguintes valores para os parâmetros:

   * **EVENTDATA** - Deixe em branco. Os dados são transmitidos pelo livro de execução dos observadores.
   * **Definições de execução** - Deixe como Azure como este livro de execução funciona no serviço de Automação.

1. Clique **em OK**e, em seguida, Selecione para voltar à página do observador.
1. Clique em **OK** para criar a tarefa do observador.

![Configurar a ação do observador a partir da UI](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Desencadear um observador

Para testar o observador está a funcionar como esperado, é preciso criar um ficheiro de teste.

Remotamente para o trabalhador híbrido. Abra o **PowerShell** e crie um ficheiro de teste na pasta.

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

1. Navegue para a sua conta de automação e selecione **tarefas Do Observador** na categoria **DE AUTOMATIZAÇÃO** DE PROCESSOS.
1. Selecione a tarefa do observador "WatchMyFolder".
1. Clique em **Ver streams** de observadores em **Streams** para ver se o observador encontrou o novo ficheiro e iniciou o livro de execução de ação.
1. Para ver os trabalhos de executor de ação, clique nos **trabalhos**de ação do Observador . Cada trabalho pode ser selecionado a visualização dos detalhes do trabalho.

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
> [O meu primeiro livro de corridas da PowerShell.](automation-first-runbook-textual-powershell.md)

