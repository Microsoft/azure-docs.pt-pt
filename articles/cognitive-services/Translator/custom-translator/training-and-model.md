---
title: O que é o treinamento e o modelo? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um modelo é o sistema, que fornece a tradução para um par de idioma específico. O resultado de um treinamento bem-sucedido é um modelo. Quando preparar um modelo, três conjuntos de dados mutuamente exclusivos são necessários conjunto de dados de treinamento, ajuste o conjunto de dados e teste do conjunto de dados.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: f6ac9647a969816cada2e4c644c17b4618cf14e8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735582"
---
# <a name="what-are-trainings-and-models"></a>Quais são os modelos e treinamentos?

Um modelo é o sistema, que fornece a tradução para um par de idioma específico.
O resultado de um treinamento bem-sucedido é um modelo. Quando preparar um modelo, três conjuntos de dados mutuamente exclusivos são necessários: conjunto de dados de treinamento, ajuste o conjunto de dados e teste de conjunto de dados. Também podem ser fornecidos dados de dicionário.

Se apenas dados de treinamento são fornecidos quando um treinamento de colocação em fila, Translator personalizado automaticamente irá montar ajuste e de teste de conjuntos de dados. Ele irá excluir 5.000 frases a partir dos seus dados de treinamento e utilizar 2500 cada montar um ajuste e conjuntos de testes.

## <a name="training-dataset-for-custom-translator"></a>Conjunto de dados de treinamento para Translator personalizado

Documentos incluídos no conjunto de preparação são utilizados pelo tradutor personalizada como base para a criação de seu modelo. Durante a execução de treinamento, as frases que estão presentes nesses documentos são alinhadas (ou emparelhadas). Pode tirar liberties no seu conjunto de documentos de treinamento de composição. Pode incluir documentos que ache que são de tangential relevância num modelo. Novamente excluí-los em outro para ver o impacto na [pontuação BLEU (bilingue avaliação Understudy)](what-is-bleu-score.md). Desde que mantiver o conjunto de ajuste e constante de conjunto de teste, fique à vontade experimentar a composição de conjunto de treinamento. Essa abordagem é uma forma eficaz para modificar a qualidade do seu sistema de tradução.

Pode executar vários treinamentos dentro de um projeto e compare os [pontuações BLEU](what-is-bleu-score.md) em todas as execuções de preparação. Quando estiver a executar vários treinamentos para comparação, certifique-se de que a otimização mesmo / dados de teste são especificados a cada vez. Também certificar-se de que também inspecionar os resultados em manualmente os ["Teste"](how-to-view-system-test-results.md) separador.

## <a name="tuning-dataset-for-custom-translator"></a>Otimização de conjunto de dados para o Translator personalizado

Documentos paralelos incluídos neste conjunto são usados pelo tradutor personalizado para otimizar o sistema de tradução para obter melhores resultados.

O conjunto de otimização é utilizado durante o treinamento para ajustar a todos os parâmetros e pesos do sistema de tradução para os valores ideal. Escolha o ajuste defina cuidadosamente: o conjunto de otimização deve ser representativo do conteúdo dos documentos que pretende converter no futuro. O conjunto de ajuste tem uma grande influência sobre a qualidade das traduções produzido. Ajuste permite que o sistema de tradução oferecer traduções que estão mais próximos para os exemplos de que fornecer o conjunto de dados de otimização. Não é necessário mais de 2500 frases quanto ajustar o conjunto. Para a qualidade da tradução ideal, é recomendado para selecionar o conjunto de ajuste manualmente ao escolher a seleção mais representativa das frases.

Ao criar o seu conjunto de otimização, escolha as frases que são um comprimento significativo e representativo das sentenças futuras que pretende converter. Também deve escolher as frases que tenham palavras e expressões que pretende traduzir na distribuição aproximada que espera em suas traduções de futuras. Na prática, um comprimento de sentença de palavras de 8 a 18 produzirá os melhores resultados, porque essas frases contém suficiente contexto para mostrar flexão e fornecer um comprimento de expressão que é significativo, sem ser excessivamente complexa.

Uma boa descrição do tipo de frases para utilizar no conjunto de otimização é prosa: frases fluentes reais. Não células da tabela, não poems, não as listas de coisas, não apenas a pontuação ou números numa frase - idioma regular.

Se selecionar manualmente o conjunto de dados de otimização, ele não deve ter qualquer uma das frases mesmo como seus dados de treinamento e testes. O conjunto de ajuste tem um impacto significativo na qualidade das traduções - escolher as frases cuidadosamente.

Se não tiver a certeza que escolher para o seu conjunto de ajuste, basta selecionar o conjunto de treinamento e deixar o Translator personalizado, selecione o seu conjunto de otimização para. Quando permitir que o tradutor de personalizada a escolher o conjunto de ajuste automaticamente, irá utilizar um subconjunto aleatório das frases dos seus documentos de treinamento bilingue e excluir essas frases do material de treinamento em si.

## <a name="testing-dataset-for-custom-translator"></a>Conjunto de dados de teste para Translator personalizado

Paralelos documentos incluídos no conjunto de teste são utilizados para calcular a pontuação de BLEU (bilingue avaliação Understudy). Esta pontuação indica a qualidade do seu sistema de tradução. Esta pontuação que realmente lhe diz como as traduções feitas pelo sistema de tradução resultante desta formação correspondem as frases de referência no conjunto de dados de teste.

A pontuação de BLEU é uma medida das diferenças entre a tradução automática e a tradução de referência. Seus intervalos de valores de 0 a 100. Uma pontuação igual a 0 indica que não uma única palavra da referência é apresentado na tradução. Uma pontuação igual a 100 indica que a tradução automática corresponde exatamente a referência: a mesma palavra está na mesma posição exata. A pontuação que receber é a média de pontuação BLEU para todas as frases do conjunto de teste.

O conjunto de teste deve incluir documentos paralelos em que as frases de idioma de destino são as traduções mais desejáveis das sentenças de linguagem de código correspondente no par. Pode querer utilizar os mesmos critérios que é usado para compor o conjunto de otimização. No entanto, o conjunto de teste tem não influência sobre a qualidade do sistema de tradução. Ele é usado exclusivamente para gerar a pontuação de BLEU para e para mais nada.

Não precisa mais de 2.500 frases como o conjunto de teste. Quando permitir que o sistema escolher automaticamente o conjunto de teste, ele será usam um subconjunto aleatório das sentenças dos seus documentos de treinamento bilingue e excluir essas frases do material de treinamento em si.

Pode ver as traduções personalizadas do conjunto de teste e compará-los para as traduções fornecidas no seu conjunto de teste, ao navegar para o separador de teste dentro de um modelo.
