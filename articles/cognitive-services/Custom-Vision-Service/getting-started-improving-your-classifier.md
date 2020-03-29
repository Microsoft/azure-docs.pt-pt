---
title: Melhorar o seu classificador - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Neste artigo você vai aprender como a quantidade, qualidade e variedade de dados podem melhorar a qualidade do seu classificador no serviço Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: c2858d5f9bca662cbbcd48b2345a7dc2c7ae48b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718541"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Neste guia, você aprenderá a melhorar a qualidade do seu classificador de Serviço de Visão Personalizada. A qualidade do seu classificador depende da quantidade, qualidade e variedade dos dados rotulados que fornece e do equilíbrio geral do conjunto de dados. Um bom classificador tem um conjunto de dados de formação equilibrado que é representativo do que será submetido ao classificador. O processo de construção de tal classificador é iterativo; é comum fazer algumas rondas de treino para alcançar os resultados esperados.

O seguinte é um padrão geral para ajudá-lo a construir um classificador mais preciso:

1. Treino de primeira ronda
1. Adicionar mais imagens e dados de balanço; retreinar
1. Adicione imagens com diferentes imagens de fundo, iluminação, tamanho do objeto, ângulo da câmara e estilo; retreinar
1. Utilize novas imagens para testar a previsão
1. Modificar os dados de formação existentes de acordo com os resultados da previsão

## <a name="prevent-overfitting"></a>Evitar o excesso de montagem

Por vezes, um classificador aprenderá a fazer previsões com base em características arbitrárias que as suas imagens têm em comum. Por exemplo, se estiver a criar um classificador para maçãs vs. citrinos, e tiver usado imagens de maçãs nas mãos e de citrinos em placas brancas, o classificador pode dar uma importância indevida às mãos vs. pratos, em vez de maçãs vs. citrinos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, utilize as seguintes orientações sobre o treino com imagens mais variadas: forneça imagens com diferentes ângulos, fundos, tamanho do objeto, grupos e outras variações.

## <a name="data-quantity"></a>Quantidade de dados

O número de imagens de treino é o fator mais importante. Recomendamos a utilização de pelo menos 50 imagens por etiqueta como ponto de partida. Com menos imagens, há um maior risco de excesso de montagem, e embora os seus números de desempenho possam sugerir boa qualidade, o seu modelo pode lutar com dados do mundo real. 

## <a name="data-balance"></a>Saldo de dados

Também é importante considerar as quantidades relativas dos seus dados de treino. Por exemplo, a utilização de 500 imagens para uma etiqueta e 50 imagens para outra etiqueta faz com que um conjunto de dados de treino desequilibrado. Isto fará com que o modelo seja mais preciso na previsão de um rótulo do que outro. É provável que veja melhores resultados se mantiver pelo menos uma relação de 1:2 entre a etiqueta com menos imagens e a etiqueta com mais imagens. Por exemplo, se a etiqueta com mais imagens tiver 500 imagens, a etiqueta com menos imagens deverá ter pelo menos 250 imagens para treino.

## <a name="data-variety"></a>Variedade de dados

Certifique-se de que utiliza imagens representativas do que será submetido ao classificador durante a utilização normal. Caso contrário, o seu classificador poderia aprender a fazer previsões com base em características arbitrárias que as suas imagens têm em comum. Por exemplo, se estiver a criar um classificador para maçãs vs. citrinos, e tiver usado imagens de maçãs nas mãos e de citrinos em placas brancas, o classificador pode dar uma importância indevida às mãos vs. pratos, em vez de maçãs vs. citrinos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, inclua uma variedade de imagens para garantir que o seu classificador possa generalizar bem. Abaixo estão algumas formas de tornar o seu conjunto de treino mais diversificado:

* __Antecedentes:__ Forneça imagens do seu objeto em frente a diferentes origens. As fotos em contextos naturais são melhores do que as fotos em frente a fundos neutros, pois fornecem mais informações para o classificador.

    ![Imagem de amostras de fundo](./media/getting-started-improving-your-classifier/background.png)

* __Iluminação:__ Forneça imagens com iluminação variada (isto é, tirada com flash, alta exposição, e assim por diante), especialmente se as imagens usadas para previsão tiverem iluminação diferente. Também é útil usar imagens com variada saturação, tonalidade e brilho.

    ![Imagem de amostras de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

* __Tamanho do objeto:__ Forneça imagens em que os objetos variam em tamanho e número (por exemplo, uma foto de cachos de bananas e um close-up de uma única banana). Tamanhos diferentes ajudam o classificador a generalizar melhor.

    ![Imagem de amostras de tamanho](./media/getting-started-improving-your-classifier/size.png)

* __Ângulo da câmara:__ Forneça imagens captadas com diferentes ângulos de câmara. Em alternativa, se todas as suas fotos forem tiradas com câmaras fixas (como câmaras de vigilância),&mdash;certifique-se de atribuir uma etiqueta diferente a cada objeto que ocorre regularmente para evitar a montagem excessiva de objetos não relacionados (como postes) como a principal característica.

    ![Imagem de amostras de ângulo](./media/getting-started-improving-your-classifier/angle.png)

* __Estilo:__ Forneça imagens de diferentes estilos da mesma classe (por exemplo, diferentes variedades do mesmo fruto). No entanto, se tiver objetos de estilos drasticamente diferentes (como mickey mouse vs. um rato real), recomendamos que os rotule como classes separadas para melhor representar as suas características distintas.

    ![Imagem de amostras de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Imagens negativas

Em algum momento do seu projeto, você pode precisar adicionar _amostras negativas_ para ajudar a tornar o seu classificador mais preciso. Amostras negativas são aquelas que não correspondem a nenhuma das outras etiquetas. Quando enviar estas imagens, aplique-lhes a etiqueta **Negativa** especial.

> [!NOTE]
> O Serviço de Visão Personalizada suporta um manuseamento automático de imagem negativa. Por exemplo, se estiver a construir uma uva vs. banana classificada e apresentar uma imagem de um sapato para previsão, o classificador deve marcar essa imagem tão perto de 0% tanto para a uva como para a banana.
> 
> Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens utilizadas no treino, é provável que o modelo classifique as imagens negativas como uma classe rotulada devido às grandes semelhanças. Por exemplo, se você tem um classificador de laranja vs. toranja, e você se alimenta em uma imagem de uma clementina, pode marcar a clementina como uma laranja porque muitas características da clementina se assemelham às de laranjas. Se as suas imagens negativas forem desta natureza, recomendamos que crie uma ou mais etiquetas adicionais (como **outras)** e rotule as imagens negativas com esta etiqueta durante o treino para permitir que o modelo se distinge melhor entre estas classes.

## <a name="use-prediction-images-for-further-training"></a>Utilize imagens de previsão para mais treinos

Quando utiliza ou testa o classificador de imagem enviando imagens para o ponto final da previsão, o serviço Custom Vision armazena essas imagens. Em seguida, pode usá-los para melhorar o modelo.

1. Para ver as imagens submetidas ao classificador, abra a [página web da Visão Personalizada,](https://customvision.ai)vá ao seu projeto e selecione o separador __Previsões.__ A visão padrão mostra imagens da iteração atual. Pode utilizar o menu de entrega da __Iteração__ para ver as imagens submetidas durante as iterações anteriores.

    ![screenshot do separador de previsões, com imagens à vista](./media/getting-started-improving-your-classifier/predictions.png)

2. Pairar sobre uma imagem para ver as etiquetas que foram previstas pelo classificador. As imagens são classificadas de modo que as que podem trazer mais melhorias para o classificador são listadas no topo. Para utilizar um método de classificação diferente, faça uma seleção na secção __Ordenar.__ 

    Para adicionar uma imagem aos dados de treino existentes, selecione a imagem, detete a etiqueta ou s corretas e clique em __Guardar e fechar__. A imagem será removida das __Previsões__ e adicionada ao conjunto de imagens de treino. Pode vê-lo selecionando o separador __Training Images.__

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag.png)

3. Em seguida, use o botão __Train__ para retreinar o classificador.

## <a name="visually-inspect-predictions"></a>Inspecione visualmente as previsões

Para inspecionar as previsões de imagem, vá ao separador __Training Images,__ selecione a sua iteração de treino anterior no menu **iteração** suspenso e verifique uma ou mais etiquetas sob a secção **Tags.** A vista deve agora exibir uma caixa vermelha em torno de cada uma das imagens para as quais o modelo não previu corretamente a etiqueta dada.

![Imagem da história da iteração](./media/getting-started-improving-your-classifier/iteration.png)

Por vezes, uma inspeção visual pode identificar padrões que pode então corrigir adicionando mais dados de treino ou modificando os dados de treino existentes. Por exemplo, um classificador para maçãs vs. limas pode rotular incorretamente todas as maçãs verdes como limas. Em seguida, pode corrigir este problema adicionando e fornecendo dados de treino que contenham imagens marcadas de maçãs verdes.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu várias técnicas para tornar o seu modelo de classificação de imagem personalizado mais preciso. Em seguida, aprenda a testar as imagens programáticamente, submetendo-as à API de Previsão.

> [!div class="nextstepaction"]
> [Utilizar a API de predição](use-prediction-api.md)
