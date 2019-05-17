---
title: Testar consultas do Azure Stream Analytics localmente com o Visual Studio Code (pré-visualização)
description: Este artigo descreve como testar consultas localmente com o Azure Stream Analytics Tools para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827954"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testar consultas do Stream Analytics localmente com o Visual Studio Code

Pode utilizar ferramentas do Azure Stream Analytics para Visual Studio Code para testar as suas tarefas do Stream Analytics localmente com dados de exemplo.

Utilize esta opção [guia de introdução](quick-create-vs-code.md) para saber como criar uma tarefa de Stream Analytics com o Visual Studio Code.

## <a name="run-queries-locally"></a>Executar consultas localmente

Pode utilizar a extensão do Azure Stream Analytics para Visual Studio Code para testar as suas tarefas do Stream Analytics localmente com dados de exemplo.

1. Depois de criar a tarefa de Stream Analytics, utilize **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, escreva e selecione **ASA: Adicionar entrada de**.

    ![Adicionar entrada de ASA no Visual Studio code](./media/vscode-local-run/add-input.png)

2. Selecione **entrada Local**.

    ![Adicionar entrada de local do ASA no Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ nova entrada Local**.

    ![Adicionar um ASA novo local de entrada no Visual Studio code](./media/vscode-local-run/add-new-local-input.png)

4. Introduza o alias de entrada mesmo que utilizou na sua consulta.

    ![Adicionar um novo alias entrado local ASA](./media/vscode-local-run/new-local-input-alias.png)

5. Na **LocalInput_DefaultLocalStream.json** de ficheiros, introduza o caminho do ficheiro onde está localizado o ficheiro de dados local.

    ![Introduza o caminho do ficheiro local no Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Regresse ao seu editor de consultas e selecione **executar localmente**.

    ![Selecionar executar localmente no editor de consultas](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)](quick-create-vs-code.md)

* [Explorar as tarefas de Azure Stream Analytics com o Visual Studio Code (pré-visualização)](vscode-explore-jobs.md)
