---
title: Teste Azure Stream Analytics questiona localmente contra entrada de fluxo ao vivo usando código de estúdio visual
description: Este artigo descreve como testar consultas localmente contra a entrada em direto do fluxo, utilizando ferramentas de análise de fluxo de azure stream para código de estúdio visual.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660856"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Test Stream Analytics questiona localmente contra entrada de fluxo ao vivo usando código de estúdio visual

Pode utilizar ferramentas de análise de fluxo de azure stream para código de estúdio visual para testar os seus trabalhos de Streaming Analytics localmente contra a entrada em direto. A entrada pode vir de uma fonte como O Hubs de Eventos Azure ou O Hub Azure IoT. Os resultados da saída são enviados como ficheiros JSON para uma pasta no seu projeto chamada **LocalRunOutputs**.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [.NET Core SDK](https://dotnet.microsoft.com/download) e reinicie o Código do Estúdio Visual.

* Use [este quickstart](quick-create-vs-code.md) para aprender a criar um trabalho stream analytics usando o Código de Estúdio Visual.

## <a name="define-a-live-stream-input"></a>Defina uma entrada em direto

1. Clique na pasta **Inputs** no seu projeto Stream Analytics. Em seguida, selecione **ASA: Adicione a entrada** no menu de contexto.

   ![Adicione a entrada da pasta Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Também pode selecionar **Ctrl+Shift+P** para abrir a paleta de comando e introduzir **ASA: Adicionar Entrada**.

   ![Adicione entrada stream analytics no Código do Estúdio Visual](./media/quick-create-vs-code/add-input.png)

2. Escolha um tipo de fonte de entrada na lista de lançamentos.

   ![Selecione hub IoT como a opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Se adicionar a entrada da paleta de comando, escolha o script de consulta Stream Analytics que utilizará a entrada. Deve ser automaticamente povoado com o caminho de ficheiro para **myASAproj.asaql**.

   ![Selecione um script Stream Analytics no Código do Estúdio Visual](./media/quick-create-vs-code/asa-script.png)

4. Escolha **selecione entre as suas Assinaturas Azure** a partir do menu suspenso.

    ![Selecione a partir de subscrições](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Configure o ficheiro JSON recentemente gerado. Pode utilizar a função CodeLens para o ajudar a introduzir uma cadeia, selecionar a partir de uma lista de drop-down ou alterar o texto diretamente no ficheiro. A imagem a seguir mostra **Select a partir das suas Subscrições** como exemplo.

   ![Configure a entrada no Código do Estúdio Visual](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Visualização de entrada

Para se certificar de que os dados de entrada estão a chegar, selecione **dados de Pré-visualização** no seu ficheiro de configuração de entrada ao vivo a partir da linha superior. Alguns dados de entrada vêm de um hub IoT e são mostrados na janela de pré-visualização. A pré-visualização pode demorar alguns segundos a aparecer.

 ![Pré-visualizar entrada ao vivo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte ao seu editor de consulta e selecione **Run localmente**. Em seguida, selecione **Use A Entrada Ao Vivo** da lista de lançamentos.

![Selecione "Run localmente" no editor de consulta](./media/vscode-local-run/run-locally.png)

![Selecione "Use A Entrada Ao Vivo"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

O resultado é mostrado na janela certa e refrescado a cada 3 segundos. Pode selecionar **Correr** para testar novamente. Também pode selecionar **Open in folder** para ver os ficheiros de resultados no File Explorer e abri-los com o Visual Studio Code ou uma ferramenta como o Excel. Note que os ficheiros de resultados estão disponíveis apenas no formato JSON.

O tempo padrão para o trabalho começar a criar saída está definido para **Agora**. Pode personalizar o tempo selecionando o botão de início de **saída** na janela de resultados.

![Ver resultado da execução local](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Passos seguintes

* [Explore os trabalhos do Azure Stream Analytics com código de estúdio visual (pré-visualização)](visual-studio-code-explore-jobs.md)

* [Configurar os gasodutos CI/CD utilizando o pacote npm](setup-cicd-vs-code.md)
