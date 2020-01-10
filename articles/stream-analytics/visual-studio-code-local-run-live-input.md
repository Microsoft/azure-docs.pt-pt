---
title: Testar Azure Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code
description: Este artigo descreve como testar consultas localmente em relação à entrada de fluxo ao vivo usando Azure Stream Analytics Tools para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660856"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code

Você pode usar as ferramentas de Azure Stream Analytics para Visual Studio Code para testar seus trabalhos de Stream Analytics localmente em relação à entrada de fluxo ao vivo. A entrada pode vir de uma fonte como hubs de eventos do Azure ou Hub IoT do Azure. Os resultados de saída são enviados como arquivos JSON para uma pasta em seu projeto chamada **LocalRunOutputs**.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download) e reinicie o Visual Studio Code.

* Use [este guia de início rápido](quick-create-vs-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definir uma entrada de fluxo ao vivo

1. Clique com o botão direito do mouse na pasta **entradas** em seu projeto Stream Analytics. Em seguida, selecione **asa: Adicionar entrada** no menu de contexto.

   ![Adicionar entrada da pasta entradas](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Você também pode selecionar **Ctrl + Shift + P** para abrir a paleta de comandos e inserir **asa: Adicionar entrada**.

   ![Adicionar Stream Analytics entrada no Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Escolha um tipo de fonte de entrada na lista suspensa.

   ![Selecione o Hub IoT como a opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Se você adicionou a entrada da paleta de comandos, escolha o Stream Analytics script de consulta que usará a entrada. Ele deve ser preenchido automaticamente com o caminho do arquivo para **myASAproj. asaql**.

   ![Selecione um script de Stream Analytics no Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Escolha **selecionar em suas assinaturas do Azure** no menu suspenso.

    ![Selecionar entre assinaturas](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configure o arquivo JSON recém-gerado. Você pode usar o recurso CodeLens para ajudá-lo a inserir uma cadeia de caracteres, selecionar em uma lista suspensa ou alterar o texto diretamente no arquivo. A captura de tela a seguir mostra a **seleção de suas assinaturas** como um exemplo.

   ![Configurar entrada no Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Visualizar entrada

Para verificar se os dados de entrada estão chegando, selecione **Visualizar dados** em seu arquivo de configuração de entrada ao vivo na linha superior. Alguns dados de entrada vêm de um hub IoT e são mostrados na janela de visualização. A visualização pode levar alguns segundos para aparecer.

 ![Visualizar entrada ao vivo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte para o editor de consultas e selecione **executar localmente**. Em seguida, selecione **usar entrada ao vivo** na lista suspensa.

![Selecione "executar localmente" no editor de consultas](./media/vscode-local-run/run-locally.png)

![Selecione "usar entrada ao vivo"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

O resultado é mostrado na janela direita e atualizado a cada 3 segundos. Você pode selecionar **executar** para testar novamente. Você também pode selecionar **abrir na pasta** para ver os arquivos de resultado no explorador de arquivos e abri-los com Visual Studio Code ou uma ferramenta como o Excel. Observe que os arquivos de resultado estão disponíveis apenas no formato JSON.

A hora padrão para o trabalho começar a criar a saída é definida como **Now**. Você pode personalizar o tempo selecionando o botão **hora de início da saída** na janela resultado.

![Exibir resultado da execução local](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passos seguintes

* [Explorar Azure Stream Analytics trabalhos com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)

* [Configurar pipelines de CI/CD usando o pacote NPM](setup-cicd-vs-code.md)
