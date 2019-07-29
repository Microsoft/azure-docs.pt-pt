---
title: O que é treinamento e modelo? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Um modelo é o sistema, que fornece tradução para um par de idiomas específico. O resultado de um treinamento bem-sucedido é um modelo. Ao treinar um modelo, três conjuntos de dados mutuamente exclusivos são DataSet de treinamento, ajustando DataSet e testando DataSet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: af3f795dc5036b23b82562e7af4582bd90b44f47
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595429"
---
# <a name="what-are-trainings-and-models"></a>O que são treinamentos e modelos?

Um modelo é o sistema, que fornece tradução para um par de idiomas específico.
O resultado de um treinamento bem-sucedido é um modelo. Durante o treinamento de um modelo, três conjuntos de dados mutuamente exclusivos são necessários: o DataSet de treinamento, o ajuste de DataSet e o teste de DataSet. Os dados do dicionário também podem ser fornecidos.

Se apenas os dados de treinamento forem fornecidos ao enfileirar um treinamento, o tradutor personalizado montará automaticamente o ajuste e o teste dos conjuntos de dados. Ele excluirá 5.000 frases dos seus dados de treinamento e usará 2.500 cada uma para montar conjuntos de testes e ajustes.

## <a name="training-dataset-for-custom-translator"></a>Conjunto de e de treinamento para o tradutor personalizado

Os documentos incluídos no conjunto de treinamento são usados pelo Tradutor personalizado como base para a criação do modelo. Durante a execução do treinamento, as frases que estão presentes nesses documentos são alinhadas (ou emparelhadas). Você pode usar o Liberties para compor seu conjunto de documentos de treinamento. Você pode incluir documentos que você acredita que são de relevância tangential em um modelo. Novamente, exclua-os em outro para ver o impacto na [Pontuação Bleu (antiestudo de avaliação bilíngüe)](what-is-bleu-score.md). Desde que você mantenha o conjunto de ajuste e a constante de conjunto de teste, sinta-se à vontade para experimentar a composição do conjunto de treinamento. Essa abordagem é uma maneira eficaz de modificar a qualidade do seu sistema de tradução.

Você pode executar vários treinamentos dentro de um projeto e comparar as pontuações de [Bleu](what-is-bleu-score.md) em todas as execuções de treinamento. Quando você estiver executando vários treinamentos para comparação, certifique-se de que os mesmos dados de ajuste/teste sejam especificados a cada vez. Além disso, certifique-se também de inspecionar os resultados manualmente na guia ["teste"](how-to-view-system-test-results.md) .

## <a name="tuning-dataset-for-custom-translator"></a>Ajustando conjunto de conjuntos para o tradutor personalizado

Os documentos paralelos incluídos neste conjunto são usados pelo Tradutor personalizado para ajustar o sistema de tradução para obter resultados ideais.

O conjunto de ajuste é usado durante o treinamento para ajustar todos os parâmetros e pesos do sistema de tradução para os valores ideais. Escolha seu conjunto de ajuste cuidadosamente: o conjunto de ajuste deve ser representativo do conteúdo dos documentos que você pretende traduzir no futuro. O conjunto de ajuste tem uma influência importante sobre a qualidade das traduções produzidas. O ajuste permite que o sistema de tradução forneça traduções mais próximas dos exemplos que você fornece no conjunto de DataSets de ajuste. Você não precisa de mais de 2500 frases como conjunto de ajuste. Para uma qualidade de tradução ideal, é recomendável selecionar o conjunto de ajuste manualmente escolhendo a seleção mais representativa de sentenças.

Ao criar seu conjunto de ajuste, escolha frases que sejam um tamanho significativo e representativo das sentenças futuras que você espera traduzir. Você também deve escolher frases que tenham palavras e frases que você pretende traduzir na distribuição aproximada esperada em suas traduções futuras. Na prática, um comprimento de frase de 8 a 18 palavras produzirá os melhores resultados, pois essas frases contêm contexto suficiente para mostrar inflexão e fornecer um comprimento de frase que seja significativo, sem ser excessivamente complexa.

Uma boa descrição do tipo de sentenças a ser usada no conjunto de ajuste é o Proseware: frases fluentes reais. Não são células de tabela, não Poems, não listas de coisas, não apenas Pontuação ou números em uma linguagem de sentença regular.

Se você selecionar manualmente o conjunto de dados de ajuste, ele não deverá ter nenhuma das mesmas frases que os dados de treinamento e teste. O conjunto de ajuste tem um impacto significativo na qualidade das traduções – escolha as sentenças com cuidado.

Se você não tiver certeza do que escolher para seu conjunto de ajuste, basta selecionar o conjunto de treinamento e permitir que o tradutor personalizado Selecione seu conjunto de ajuste para você. Quando você permite que o tradutor personalizado escolha o conjunto de ajuste automaticamente, ele usará um subconjunto aleatório de frases de seus documentos de treinamento bilíngües e excluirá essas frases do próprio material de treinamento.

## <a name="testing-dataset-for-custom-translator"></a>Testando conjunto de teste para o tradutor personalizado

Os documentos paralelos incluídos no conjunto de teste são usados para calcular a pontuação BLEU (antiestudo de avaliação bilíngüe). Essa pontuação indica a qualidade do seu sistema de tradução. Na verdade, essa Pontuação informa a você como as traduções realizadas pelo sistema de tradução resultantes desse treinamento correspondem às frases de referência no conjunto de dados de teste.

A pontuação BLEU é uma medida do Delta entre a conversão automática e a tradução de referência. Seu valor varia de 0 a 100. Uma pontuação de 0 indica que uma única palavra da referência é exibida na tradução. Uma pontuação de 100 indica que a conversão automática corresponde exatamente à referência: a mesma palavra está na mesma posição exata. A pontuação que você recebe é a média da Pontuação de BLEU para todas as frases do conjunto de testes.

O conjunto de teste deve incluir documentos paralelos onde as frases de idioma de destino são as traduções mais desejáveis das sentenças de idioma de origem correspondentes no par. Talvez você queira usar os mesmos critérios usados para compor o conjunto de ajuste. No entanto, o conjunto de testes não tem influência sobre a qualidade do sistema de tradução. Ele é usado exclusivamente para gerar a pontuação de BLEU para você e para nada mais.

Você não precisa de mais de 2.500 frases como o conjunto de testes. Quando você permite que o sistema escolha o conjunto de testes automaticamente, ele usará um subconjunto aleatório de frases de seus documentos de treinamento bilíngüe e excluirá essas frases do próprio material de treinamento.

Você pode exibir as traduções personalizadas do conjunto de teste e compará-las às traduções fornecidas no conjunto de testes, navegando até a guia teste em um modelo.
