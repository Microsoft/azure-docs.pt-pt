---
title: Teste Azure Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando Código de Estúdio Visual
description: Este artigo descreve como testar consultas localmente contra a entrada de transmissão ao vivo usando Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: 944c1a8cc4606c5e614f17cd0590331826527a7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892190"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Teste Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando Código de Estúdio Visual

Pode utilizar ferramentas de análise Azure Stream para Código de Estúdio Visual para testar os seus trabalhos stream Analytics localmente contra a entrada em direto. A entrada pode vir de uma fonte como Azure Event Hubs ou Azure IoT Hub. Os resultados de saída são enviados como ficheiros JSON para uma pasta no seu projeto chamada **LocalRunOutputs**.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [.NET Core SDK](https://dotnet.microsoft.com/download) e reinicie o Código do Estúdio Visual.

* Use [este quickstart](quick-create-visual-studio-code.md) para aprender a criar um trabalho stream Analytics usando o Código de Estúdio Visual.

## <a name="define-a-live-stream-input"></a>Defina uma entrada de fluxo ao vivo

1. Clique com o botão direito na pasta **Inputs** no seu projeto Stream Analytics. Em seguida, selecione **ASA: Adicione a entrada** no menu de contexto.

   ![Adicionar entrada da pasta Entradas](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Também pode selecionar **Ctrl+Shift+P** para abrir a paleta de comando e introduzir **ASA: Adicionar Entrada**.

   ![Adicionar entrada Stream Analytics no Código do Estúdio Visual](./media/quick-create-visual-studio-code/add-input.png)

2. Escolha um tipo de fonte de entrada na lista de drop-down.

   ![Selecione o hub IoT como a opção de entrada](./media/quick-create-visual-studio-code/iot-hub.png)

3. Se adicionar a entrada da paleta de comando, escolha o script de consulta stream Analytics que utilizará a entrada. Deve ser automaticamente povoado com o caminho do ficheiro para **myASAproj.asaql**.

   ![Selecione um script Stream Analytics no Código do Estúdio Visual](./media/quick-create-visual-studio-code/asa-script.png)

4. Escolha **Selecione as suas Subscrições Azure** do menu suspenso.

    ![Selecione a partir de subscrições](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Configure o ficheiro JSON recentemente gerado. Pode utilizar a função CodeLens para o ajudar a introduzir uma cadeia, selecionar a partir de uma lista de drop-down ou alterar o texto diretamente no ficheiro. A imagem que se segue mostra **Selecionar das suas Subscrições** como exemplo.

   ![Configure a entrada no Código do Estúdio Visual](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Entrada de pré-visualização

Para se certificar de que os dados de entrada estão a chegar, selecione **dados de pré-visualização** no ficheiro de configuração de entrada ao vivo a partir da linha superior. Alguns dados de entrada provêm de um hub IoT e são mostrados na janela de pré-visualização. A pré-visualização pode demorar alguns segundos a aparecer.

 ![Pré-visualização da entrada ao vivo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte ao seu editor de consulta e selecione **Executar localmente.** Em seguida, **selecione Use Live Input** da lista de drop-down.

![Selecione "Executar localmente" no editor de consultas](./media/vscode-local-run/run-locally.png)

![Selecione "Use Live Input"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

O resultado é mostrado na janela certa e refrescado a cada 3 segundos. Pode selecionar **Executar** para testar novamente. Também pode selecionar **Abrir na pasta** para ver os ficheiros de resultados no File Explorer e abri-los com o Código do Estúdio Visual ou uma ferramenta como o Excel. Note que os ficheiros de resultados só estão disponíveis no formato JSON.

O tempo padrão para o trabalho começar a criar saída está definido para **Agora**. Pode personalizar o tempo selecionando o botão **de início de saída** na janela de resultados.

![Ver resultado de execução local](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passos seguintes

* [Explore os trabalhos do Azure Stream Analytics com o Código do Estúdio Visual (pré-visualização)](visual-studio-code-explore-jobs.md)

* [Configurar os gasodutos CI/CD utilizando o pacote npm](setup-cicd-vs-code.md)
