---
title: IntelliSense em ferramentas Azure Stream Analytics para Código de Estúdio Visual
description: Este artigo descreve como usar funcionalidades IntelliSense em ferramentas Azure Stream Analytics para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394400"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense em ferramentas Azure Stream Analytics para Código de Estúdio Visual

O IntelliSense está disponível para [o Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) nas [ferramentas Azure Stream Analytics para o Código VS](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). O IntelliSense é um auxílio de conclusão de código que inclui uma série de funcionalidades: Membros da Lista, Informações paramétricas, Informações Rápidas e Palavra Completa. As funcionalidades intelliSense são por vezes chamadas por outros nomes como "conclusão de código", "assistência de conteúdo" e "insinuação de código".

![Demonstração intelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Funcionalidades IntelliSense

As funcionalidades IntelliSense nas ferramentas Stream Analytics para código VS são alimentadas por um serviço de idiomas. Um serviço linguístico analisa o seu código fonte e fornece conclusões inteligentes de código com base na semântica linguística. Se um serviço de idiomas souber possíveis conclusões, surgem sugestões do IntelliSense à medida que escreve. Se continuar a escrever, uma lista de membros, como variáveis e métodos, é filtrada apenas para incluir membros que contenham os caracteres que escreveu. Quando pressiona `Tab` `Enter` as teclas ou teclas, o IntelliSense insere o membro selecionado.

Pode desencadear o IntelliSense em qualquer janela do editor digitando um personagem de gatilho, como o personagem `.`do ponto .

![intellisense auto-conclusão](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> O widget de sugestões suporta a filtragem CamelCase. Pode escrever as letras que estão revestidas num nome de método para limitar as sugestões. Por exemplo, "cra" irá rapidamente trazer "createApplication".

### <a name="types-of-completions"></a>Tipos de conclusões

As ferramentas Stream Analytics para vs Code IntelliSense oferecem diferentes tipos de conclusões, incluindo sugestões de servidores de idiomas, snippets e simples conclusões texuais baseadas em palavras.

|       |         |       |
| ----- | ------- | ----- |
| Palavras-chave | `keyword`
| Funções | `build-in function`, `user defined function`  |
| Nome do conjunto de dados| `input`, `output`, `intermediate result set`|
| Nome da coluna do conjunto de dados|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Conclusão do nome

Além da conclusão automática da palavra-chave, as ferramentas Stream Analytics para o Código VS lêa a lista de nomes de entrada e saída de trabalho, bem como os nomes das colunas nas suas fontes de dados quando estão configuradas. A extensão lembra-se desta informação para fornecer capacidades de conclusão de nomes que são úteis para introduzir declarações com poucas teclas:

Durante a codificação, não precisa de deixar o editor para realizar pesquisas sobre nomes de entrada de emprego, nomes de saída e nomes de colunas. Pode manter o seu contexto, encontrar a informação de que necessita, inserir elementos diretamente no seu código e fazer com que o IntelliSense complete a sua dactilografia para si.

Tenha em anote que precisa de configurar a entrada local ou a entrada ao vivo e guardar o ficheiro de configuração para poder utilizar a conclusão do nome.

![conclusão do nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informação do Parâmetro

A opção IntelliSense **Parameter Info** abre uma lista de parâmetros que fornece informações sobre o número, nomes e tipos dos parâmetros que são exigidos por uma função. O parâmetro em negrito indica o próximo parâmetro que é necessário à medida que escreve uma função.

A lista de parâmetros também é apresentada para funções aninhadas. Se escrever uma função como parâmetro para outra função, a lista de parâmetros apresenta os parâmetros para a função interna. Em seguida, quando a lista de parâmetros de função interna está completa, a lista de parâmetros reverte para exibir os parâmetros de função exterior.

![informação de parâmetro](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informações Rápidas

Conforme fornecido pelo serviço de idiomas, pode ver **Informações Rápidas** para cada identificador no seu código. Alguns exemplos de identificadores são a entrada, a saída, um conjunto de resultados intermédios ou função. Quando move o ponteiro do rato sobre um identificador, a sua declaração é exibida numa janela pop-up. As propriedades e os dados schemas para as inputs, se configurados, e conjunto de dados intermédios são mostrados.

![informação rápida](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Resolução de problemas IntelliSense

Este problema é causado pela falta de configuração de entrada que fornece dados. Pode verificar se uma [entrada local](visual-studio-code-local-run.md#define-a-local-input) ou uma entrada [ao vivo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) foi configurada corretamente.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho azure Stream Analytics no Código do Estúdio Visual](quick-create-vs-code.md)
* [Teste Stream Analytics consulta localmente com dados de amostra usando código de estúdio visual](visual-studio-code-local-run.md)
* [Test Stream Analytics questiona localmente contra entrada de fluxo ao vivo usando código de estúdio visual](visual-studio-code-local-run-live-input.md)
