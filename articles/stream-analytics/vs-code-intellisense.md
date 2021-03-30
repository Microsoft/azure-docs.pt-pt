---
title: IntelliSense em ferramentas Azure Stream Analytics para Código de Estúdio Visual
description: Este artigo descreve como usar funcionalidades IntelliSense em ferramentas Azure Stream Analytics para Código de Estúdio Visual.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491575"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense em ferramentas Azure Stream Analytics para Código de Estúdio Visual

O IntelliSense está disponível para as ferramentas [Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) in [Azure Stream Analytics para o Código VS.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) O IntelliSense é um auxílio de conclusão de código que inclui uma série de funcionalidades: Membros da Lista, Informações de Parâmetros, Informações Rápidas e Palavra Completa. As funcionalidades intelliSense são por vezes chamadas por outros nomes como "code completion", "content assist" e "code hinting".

![Demonstração de IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Características do IntelliSense

As funcionalidades IntelliSense nas ferramentas Stream Analytics para código VS são alimentadas por um serviço de idioma. Um serviço de idiomas analisa o seu código fonte e fornece conclusões inteligentes de código com base na semântica linguística. Se um serviço linguístico souber possíveis conclusões, as sugestões do IntelliSense aparecem à medida que escreve. Se continuar a escrever, uma lista de membros, como variáveis e métodos, é filtrada apenas para incluir membros que contenham os caracteres que escreveu. Quando pressiona as `Tab` `Enter` teclas ou teclas, o IntelliSense insere o membro selecionado.

Pode ativar o IntelliSense em qualquer janela de editor, digitando um personagem de gatilho, como o personagem do ponto `.` .

![auto-intellisense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> As sugestões widget suportam a filtragem CamelCase. Pode escrever as letras que são superiorizadas num nome de método para limitar as sugestões. Por exemplo, "cra" vai rapidamente trazer "createApplication".

### <a name="types-of-completions"></a>Tipos de acabamentos

As ferramentas stream Analytics para o código VS IntelliSense oferecem diferentes tipos de acabamentos, incluindo sugestões de servidores de idiomas, snippets e simples conclusões texuais baseadas em palavras.

|Conclusão     |  Tipo       |
| ----- | ------- |
| Palavras-chave | `keyword`
| Funções | `build-in function`, `user defined function`  |
| Nome do conjunto de dados| `input`, `output`, `intermediate result set`|
| Nome da coluna do conjunto de dados|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Conclusão do nome

Além da conclusão automática das palavras-chave, as ferramentas Stream Analytics para O Código VS lê a lista de nomes de entradas e saídas de trabalho, bem como os nomes das colunas nas suas fontes de dados quando estão configuradas. A extensão lembra-se desta informação para fornecer capacidades de conclusão de nome que são úteis para introduzir declarações com poucas teclas:

Durante a codificação, não é necessário deixar o editor para realizar pesquisas sobre nomes de entradas de emprego, nomes de saída e nomes de colunas. Pode manter o seu contexto, encontrar a informação de que necessita, inserir elementos diretamente no seu código e fazer com que o IntelliSense complete a sua dactilografia por si.

Note que precisa de configurar a entrada local ou a entrada ao vivo e guardar o ficheiro de configuração para poder utilizar a conclusão do nome.

![conclusão do nome](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informação de Parâmetros

A opção **Informação de Parâmetros** IntelliSense abre uma lista de parâmetros que fornece informações sobre o número, nomes e tipos dos parâmetros que são exigidos por uma função. O parâmetro em negrito indica o parâmetro seguinte que é necessário à medida que digita uma função.

A lista de parâmetros também é apresentada para funções aninhadas. Se escrever uma função como parâmetro para outra função, a lista de parâmetros apresenta os parâmetros para a função interna. Em seguida, quando a lista de parâmetros de função interna estiver completa, a lista de parâmetros volta a apresentar os parâmetros da função externa.

![informação de parâmetros](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Informações Rápidas

Conforme fornecido pelo serviço de idiomas, pode ver **Quick Info** para cada identificador no seu código. Alguns exemplos de identificadores são a entrada, a saída, um conjunto de resultados intermédios ou função. Quando se desloca o ponteiro do rato sobre um identificador, a sua declaração é apresentada numa janela pop-up. As propriedades e esquemas de dados para entradas, se configurados, e conjunto de dados intermédios são mostrados.

![informação rápida](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Resolução de problemas IntelliSense

Este problema é causado pela falta de configuração de entrada que fornece dados. Pode verificar se uma [entrada local](visual-studio-code-local-run.md#define-a-local-input) ou entrada [ao vivo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) foi configurada corretamente.

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho Azure Stream Analytics em Código de Estúdio Visual](quick-create-visual-studio-code.md)
* [Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual](visual-studio-code-local-run.md)
* [Teste Stream Analytics consulta localmente contra entrada de transmissão ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)