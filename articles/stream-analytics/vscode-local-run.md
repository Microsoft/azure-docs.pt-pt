---
title: Testar Azure Stream Analytics consultas com Visual Studio Code
description: Este artigo descreve como testar consultas localmente com Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924976"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Testar Stream Analytics consultas localmente com Visual Studio Code

Você pode usar as ferramentas de Azure Stream Analytics para Visual Studio Code para testar seus trabalhos de Stream Analytics localmente com dados de exemplo.

Use este guia de [início rápido](quick-create-vs-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos
* Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).
* Reinicie Visual Studio Code.
 
## <a name="run-queries-locally"></a>Executar consultas localmente

Você pode usar a extensão de Azure Stream Analytics para Visual Studio Code para testar seus trabalhos de Stream Analytics localmente com dados de exemplo.

1. Depois de criar seu trabalho de Stream Analytics, use **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, digite e selecione **asa: Adicionar entrada**.

    ![Adicionar entrada do ASA no Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Selecione **entrada local**.

    ![Adicionar entrada local do ASA no Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ nova entrada local**.

    ![Adicionar uma nova entrada local do ASA no Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Insira o mesmo alias de entrada que você usou em sua consulta.

    ![Adicionar um novo alias de entrada local do ASA](./media/vscode-local-run/new-local-input-alias.png)

5. No arquivo **LocalInput_DefaultLocalStream. JSON** , insira o caminho do arquivo no qual o arquivo de dados local está localizado.

    ![Insira o caminho do arquivo local no Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Volte para o editor de consultas e selecione **executar localmente**.

    ![Selecione executar localmente no editor de consultas](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Passos seguintes

* [Criar um trabalho de nuvem Azure Stream Analytics no Visual Studio Code (visualização)](quick-create-vs-code.md)

* [Explorar Azure Stream Analytics trabalhos com Visual Studio Code (versão prévia)](vscode-explore-jobs.md)
