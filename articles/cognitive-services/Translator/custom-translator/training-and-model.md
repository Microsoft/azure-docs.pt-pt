---
title: O que é treino e modelo? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Um modelo é o sistema, que fornece tradução para um par de idiomas específicos. O resultado de um treino bem sucedido é um modelo. Ao treinar um modelo, são necessários três conjuntos de dados de formação mutuamente exclusivos, afinação de conjunto de dados e conjunto de dados de teste.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897939"
---
# <a name="what-are-trainings-and-models"></a>O que são treinos e modelos?

Um modelo é o sistema, que fornece tradução para um par de idiomas específicos.
O resultado de um treino bem sucedido é um modelo. Ao treinar um modelo, são necessários três tipos de documentos mutuamente exclusivos: treino, afinação e testes. O tipo de documento do dicionário também pode ser fornecido. Consulte o [alinhamento da frase](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Se apenas forem fornecidos dados de formação na fila de uma formação, o Custom Tradutor reunirá automaticamente dados de afinação e teste. Utilizará um subconjunto aleatório de frases dos seus documentos de treino e excluirá estas frases dos dados de formação em si.

## <a name="training-document-type-for-custom-translator"></a>Tipo de documento de formação para tradutor personalizado

Os documentos incluídos no conjunto de formação são utilizados pelo Tradutor Personalizado como base para a construção do seu modelo. Durante a execução do treino, as frases que estão presentes nestes documentos estão alinhadas (ou emparelhadas). Pode tomar liberdades na composição do seu conjunto de documentos de treino. Pode incluir documentos que acredita serem de relevância tangencial num modelo. Mais uma vez exclua-os noutro para ver o impacto na [pontuação BLEU (Avaliação Bilingue)](what-is-bleu-score.md). Desde que mantenha o conjunto de afinação e o teste constantes, sinta-se à vontade para experimentar a composição do conjunto de treino. Esta abordagem é uma forma eficaz de modificar a qualidade do seu sistema de tradução.

Você pode executar vários treinos dentro de um projeto e comparar as [pontuações bleu](what-is-bleu-score.md) em todas as corridas de treino. Quando estiver a realizar vários treinos para comparação, certifique-se de que os mesmos dados de afinação/teste são especificados de cada vez. Certifique-se também de que inspeciona os resultados manualmente no [separador "Teste".](how-to-view-system-test-results.md)

## <a name="tuning-document-type-for-custom-translator"></a>Tipo de documento de afinação para tradutor personalizado

Documentos paralelos incluídos neste conjunto são utilizados pelo Tradutor Personalizado para sintonizar o sistema de tradução para obter resultados ótimos.

Os dados de afinação são utilizados durante o treino para ajustar todos os parâmetros e pesos do sistema de tradução aos valores ideais. Escolha cuidadosamente os seus dados de afinação: os dados de afinação devem ser representativos do conteúdo dos documentos que pretende traduzir no futuro. Os dados de afinação têm uma grande influência na qualidade das traduções produzidas. A sintonização permite que o sistema de tradução forneça traduções mais próximas das amostras que fornece nos dados de afinação. Não precisa de mais de 2500 frases nos seus dados de afinação. Para uma ótima qualidade de tradução, recomenda-se selecionar manualmente o conjunto de sintonização, escolhendo a seleção mais representativa das frases.

Ao criar o seu conjunto de afinação, escolha frases que sejam uma duração significativa e representativa das frases futuras que espera traduzir. Deve também escolher frases que tenham palavras e frases que pretende traduzir na distribuição aproximada que espera nas suas futuras traduções. Na prática, uma duração de 7 a 10 palavras produzirá os melhores resultados, porque estas frases contêm contexto suficiente para mostrar inflexão e fornecer um comprimento de frase que é significativo, sem ser excessivamente complexo.

Uma boa descrição do tipo de frases a usar no conjunto de afinação é prosa: frases fluentes reais. Nem células de mesa, nem poemas, nem listas de coisas, não só pontuação, ou números numa frase - linguagem regular.

Se selecionar manualmente os seus dados de afinação, este não deverá ter nenhuma das mesmas frases que os seus dados de treino e teste. Os dados de afinação têm um impacto significativo na qualidade das traduções - escolha cuidadosamente as frases.

Se não tiver a certeza do que escolher para os seus dados de afinação, basta selecionar os dados de treino e deixar o Custom Tradutor selecionar os dados de afinação para si. Quando deixar o Tradutor Personalizado escolher os dados de afinação automaticamente, utilizará um subconjunto aleatório de frases dos seus documentos de treino bilingue e excluirá estas frases do próprio material de treino.

## <a name="testing-dataset-for-custom-translator"></a>Conjunto de dados de teste para tradutor personalizado

Os documentos paralelos incluídos no conjunto de testes são utilizados para calcular a pontuação BLEU (Understudy de avaliação bilingue). Esta pontuação indica a qualidade do seu sistema de tradução. Esta partitura realmente lhe diz como as traduções feitas pelo sistema de tradução resultantes deste treino correspondem às frases de referência no conjunto de dados de teste.

A pontuação BLEU é uma medição do delta entre a tradução automática e a tradução de referência. O seu valor varia entre 0 e 100. Uma pontuação de 0 indica que nem uma única palavra da referência aparece na tradução. Uma pontuação de 100 indica que a tradução automática corresponde exatamente à referência: a mesma palavra está exatamente na mesma posição. A pontuação que recebe é a média de pontuação BLEU para todas as frases dos dados de teste.

Os dados relativos ao ensaio devem incluir documentos paralelos em que as frases-alvo são as traduções mais desejáveis das frases linguísticas de origem correspondentes no par de alvos de origem. Pode querer utilizar os mesmos critérios utilizados para compor os dados de sintonização. No entanto, os dados dos testes não têm qualquer influência sobre a qualidade do sistema de tradução. É utilizado exclusivamente para gerar a pontuação BLEU para si.

Não precisa de mais de 2.500 frases como dados de teste. Quando deixar o sistema escolher automaticamente o conjunto de testes, utilizará um subconjunto aleatório de frases dos seus documentos de treino bilingues e excluirá estas frases do próprio material de treino.

Pode ver as traduções personalizadas do conjunto de testes e compará-las com as traduções fornecidas no seu conjunto de testes, navegando no separador de teste dentro de um modelo.