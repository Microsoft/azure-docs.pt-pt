---
title: O que é treino e modelo? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Um modelo é o sistema, que fornece tradução para um par de idiomas específico. O resultado de um treino bem sucedido é um modelo. Ao treinar um modelo, são necessários três conjuntos de dados mutuamente exclusivos, afinação de dados e o conjunto de dados de teste.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 71f1e3f460fa58b999af0a60c8cffa90c8ac8cd4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219458"
---
# <a name="what-are-trainings-and-models"></a>O que são treinos e modelos?

Um modelo é o sistema, que fornece tradução para um par de idiomas específico.
O resultado de um treino bem sucedido é um modelo. Ao treinar um modelo, são necessários três tipos de documentos mutuamente exclusivos: formação, afinação e testes. O tipo de documento do dicionário também pode ser fornecido. Por favor, refira-se ao alinhamento da [frase.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

Se apenas forem fornecidos dados de treino na fila de um treino, o Tradutor Personalizado reunirá automaticamente os dados de afinação e teste. Utilizará um subconjunto aleatório de frases dos seus documentos de formação e excluirá estas frases dos próprios dados de formação.

## <a name="training-document-type-for-custom-translator"></a>Tipo de documento de treino para tradutor personalizado

Os documentos incluídos no conjunto de formação são usados pelo Tradutor Personalizado como base para a construção do seu modelo. Durante a execução do treino, as frases presentes nestes documentos estão alinhadas (ou emparelhadas). Pode tomar liberdades na composição do seu conjunto de documentos de treino. Pode incluir documentos que acredita serem de relevância tangencial num modelo. Mais uma vez, exclua-os noutro para ver o impacto na [pontuação BLEU (Avaliação Bilingue)](what-is-bleu-score.md)pontuação . Desde que mantenha o conjunto de afinação e o conjunto de testes constantes, sinta-se à vontade para experimentar a composição do conjunto de treino. Esta abordagem é uma forma eficaz de modificar a qualidade do seu sistema de tradução.

Você pode executar vários treinos dentro de um projeto e comparar as [pontuações de BLEU](what-is-bleu-score.md) em todos os treinos. Quando estiver a realizar vários treinos para comparação, certifique-se de que os mesmos dados de afinação/teste são especificados de cada vez. Certifique-se também de que inspecione os resultados manualmente no separador ["Testing".](how-to-view-system-test-results.md)

## <a name="tuning-document-type-for-custom-translator"></a>Tipo de documento de afinação para tradutor personalizado

Os documentos paralelos incluídos neste conjunto são utilizados pelo Tradutor Personalizado para afinar o sistema de tradução para obter resultados ideais.

Os dados de afinação são utilizados durante o treino para ajustar todos os parâmetros e pesos do sistema de tradução aos valores ideais. Escolha cuidadosamente os seus dados de afinação: os dados de afinação devem ser representativos do conteúdo dos documentos que pretende traduzir no futuro. Os dados de afinação têm uma grande influência na qualidade das traduções produzidas. A sintonização permite que o sistema de tradução forneça traduções mais próximas das amostras que fornece nos dados de afinação. Não precisa de mais de 2500 frases nos seus dados de afinação. Para uma ótima qualidade de tradução, é aconselhável selecionar manualmente o conjunto de afinação, escolhendo a seleção mais representativa das frases.

Ao criar o seu conjunto de afinação, escolha frases que sejam uma duração significativa e representativa das frases futuras que espera traduzir. Você também deve escolher frases que têm palavras e frases que pretende traduzir na distribuição aproximada que você espera nas suas traduções futuras. Na prática, uma duração de frase de 7 a 10 palavras produzirá os melhores resultados, porque estas frases contêm contexto suficiente para mostrar inflexão e fornecer uma frase de comprimento que é significativa, sem ser excessivamente complexa.

Uma boa descrição do tipo de frases a utilizar no conjunto de afinação é a prosa: frases fluentes reais. Nem células de mesa, nem poemas, nem listas de coisas, não só pontuação, nem números numa frase - linguagem regular.

Se selecionar manualmente os seus dados de afinação, não deve ter nenhuma das mesmas frases que os seus dados de treino e teste. Os dados de afinação têm um impacto significativo na qualidade das traduções - escolha as frases cuidadosamente.

Se não tiver a certeza do que escolher para os seus dados de afinação, basta selecionar os dados de treino e deixar o Tradutor Personalizado selecionar os dados de afinação para si. Quando deixar o Tradutor Personalizado escolher automaticamente os dados de afinação, utilizará um subconjunto aleatório de frases dos seus documentos de treino bilingues e excluirá estas frases do próprio material de treino.

## <a name="testing-dataset-for-custom-translator"></a>Teste conjunto de dados para tradutor personalizado

Os documentos paralelos incluídos no conjunto de testes são utilizados para calcular a pontuação BLEU (Avaliação Bilingue) pontuação. Esta pontuação indica a qualidade do seu sistema de tradução. Esta partitura realmente diz-lhe como as traduções feitas pelo sistema de tradução resultantes deste treino correspondem às frases de referência no conjunto de dados de teste.

A pontuação BLEU é uma medição do delta entre a tradução automática e a tradução de referência. O seu valor varia entre 0 e 100. Uma pontuação de 0 indica que nem uma única palavra da referência aparece na tradução. Uma pontuação de 100 indica que a tradução automática corresponde exatamente à referência: a mesma palavra está exatamente na mesma posição. A pontuação que recebe é a média de pontuação bleu para todas as frases dos dados de teste.

Os dados do teste devem incluir documentos paralelos em que as frases linguísticas-alvo são as traduções mais desejáveis das frases linguísticas de origem correspondentes no par de alvos-alvo de origem. Pode querer utilizar os mesmos critérios utilizados para compor os dados de afinação. No entanto, os dados de teste não têm qualquer influência sobre a qualidade do sistema de tradução. É usado exclusivamente para gerar a pontuação BLEU para si.

Não precisa de mais de 2.500 frases como dados de teste. Quando deixar o sistema escolher o conjunto de testes automaticamente, utilizará um subconjunto aleatório de frases dos seus documentos de treino bilingues e excluirá estas frases do próprio material de treino.

Pode visualizar as traduções personalizadas do conjunto de testes e compará-las com as traduções fornecidas no seu conjunto de testes, navegando para o separador de teste dentro de um modelo.
