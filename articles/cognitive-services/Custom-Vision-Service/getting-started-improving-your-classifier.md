---
title: Melhorar o seu classificador - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Neste artigo você vai aprender como a quantidade, qualidade e variedade de dados podem melhorar a qualidade do seu classificador no serviço De Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: a77d3d5c1225fdd85e27db20cdae23e0c77a5e28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271363"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Neste guia vai aprender a melhorar a qualidade do seu classificador de Serviço de Visão Personalizada. A qualidade do seu classificador depende da quantidade, qualidade e variedade dos dados rotulados que fornece e quão equilibrado é o conjunto de dados. Um bom classificador tem um conjunto de dados de formação equilibrado que é representativo do que será submetido ao classificador. O processo de construção de tal classificador é iterativo; é comum fazer algumas rondas de treino para alcançar os resultados esperados.

O seguinte é um padrão geral para ajudá-lo a construir um classificador mais preciso:

1. Treino de primeira ronda
1. Adicione mais imagens e equilibre dados; retreinar
1. Adicione imagens com fundo variado, iluminação, tamanho do objeto, ângulo da câmara e estilo; retreinar
1. Utilize novas imagens para testar a previsão
1. Modificar os dados de formação existentes de acordo com os resultados da previsão

## <a name="prevent-overfitting"></a>Evitar a sobremontagem

Às vezes, um classificador aprenderá a fazer previsões com base em características arbitrárias que as suas imagens têm em comum. Por exemplo, se estiver a criar um classificador para maçãs vs. citrinos, e tiver usado imagens de maçãs nas mãos e de citrinos em pratos brancos, o classificador pode dar uma importância indevida às mãos vs. pratos, em vez de maçãs vs. citrinos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, utilize as seguintes orientações sobre o treino com imagens mais variadas: forneça imagens com diferentes ângulos, fundos, tamanho do objeto, grupos e outras variações.

## <a name="data-quantity"></a>Quantidade de dados

O número de imagens de treino é o fator mais importante. Recomendamos a utilização de pelo menos 50 imagens por etiqueta como ponto de partida. Com menos imagens, existe um maior risco de sobremontagem, e embora os seus números de desempenho possam sugerir boa qualidade, o seu modelo pode lutar com dados do mundo real. 

## <a name="data-balance"></a>Saldo de dados

Também é importante considerar as quantidades relativas dos seus dados de treino. Por exemplo, a utilização de 500 imagens para uma etiqueta e 50 imagens para outra etiqueta cria um conjunto de dados de treino desequilibrado. Isto fará com que o modelo seja mais preciso na previsão de uma etiqueta do que noutra. É provável que veja melhores resultados se mantiver pelo menos uma relação de 1:2 entre a etiqueta com menos imagens e o rótulo com mais imagens. Por exemplo, se o rótulo com mais imagens tiver 500 imagens, o rótulo com menos imagens deve ter pelo menos 250 imagens para treino.

## <a name="data-variety"></a>Variedade de dados

Certifique-se de que utiliza imagens representativas do que será submetido ao classificado durante a utilização normal. Caso contrário, o seu classificador poderia aprender a fazer previsões com base em características arbitrárias que as suas imagens têm em comum. Por exemplo, se estiver a criar um classificador para maçãs vs. citrinos, e tiver usado imagens de maçãs nas mãos e de citrinos em pratos brancos, o classificador pode dar uma importância indevida às mãos vs. pratos, em vez de maçãs vs. citrinos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, inclua uma variedade de imagens para garantir que o seu classificador pode generalizar bem. Abaixo estão algumas formas de tornar o seu conjunto de treino mais diversificado:

* __Antecedentes:__ Forneça imagens do seu objeto em frente a diferentes origens. As fotos em contextos naturais são melhores do que as fotos em frente a fundos neutros, uma vez que fornecem mais informações para o classificador.

    ![Imagem de amostras de fundo](./media/getting-started-improving-your-classifier/background.png)

* __Iluminação:__ Forneça imagens com iluminação variada (isto é, tirada com flash, alta exposição, e assim por diante), especialmente se as imagens utilizadas para a previsão tiverem iluminação diferente. Também é útil usar imagens com saturação variada, matiz e brilho.

    ![Imagem de amostras de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

* __Tamanho do objeto:__ Forneça imagens nas quais os objetos variam em tamanho e número (por exemplo, uma foto de cachos de bananas e um close-up de uma única banana). Um tamanho diferente ajuda o classificador a generalizar-se melhor.

    ![Imagem de amostras de tamanho](./media/getting-started-improving-your-classifier/size.png)

* __Ângulo da câmara:__ Forneça imagens tiradas com diferentes ângulos de câmara. Em alternativa, se todas as suas fotos tão bem como câmaras fixas (como câmaras de vigilância), certifique-se de atribuir uma etiqueta diferente a cada objeto que ocorra regularmente para evitar a interpretação excessiva &mdash; de objetos não relacionados (como postes de iluminação) como a função principal.

    ![Imagem de amostras de ângulo](./media/getting-started-improving-your-classifier/angle.png)

* __Estilo:__ Fornecer imagens de diferentes estilos da mesma classe (por exemplo, diferentes variedades do mesmo fruto). No entanto, se tiver objetos de estilos drasticamente diferentes (como Mickey Mouse vs. um rato da vida real), recomendamos que os rotule como classes separadas para melhor representar as suas características distintas.

    ![Imagem de amostras de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Imagens negativas

Em algum momento do seu projeto, poderá ter de adicionar _amostras negativas_ para ajudar a tornar o seu classificador mais preciso. As amostras negativas são aquelas que não correspondem a nenhuma das outras etiquetas. Ao fazer o upload destas imagens, aplique-lhes a etiqueta **negativa** especial.

> [!NOTE]
> O Serviço de Visão Personalizada suporta algum manuseamento automático de imagens negativas. Por exemplo, se estiver a construir um classificador de uva vs banana e apresentar uma imagem de um sapato para previsão, o classificador deve marcar essa imagem como perto de 0% tanto para a uva como para a banana.
> 
> Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens utilizadas no treino, é provável que o modelo classifique as imagens negativas como uma classe rotulada devido às grandes semelhanças. Por exemplo, se você tem um classificador de laranja vs toranja, e você se alimenta em uma imagem de uma clementina, ele pode marcar a clementina como uma laranja porque muitas características da clementina se assemelham às de laranjas. Se as suas imagens negativas forem desta natureza, recomendamos que crie uma ou mais etiquetas adicionais (como **Outras)** e rotule as imagens negativas com esta etiqueta durante o treino para permitir que o modelo se distinga melhor entre estas classes.

## <a name="use-prediction-images-for-further-training"></a>Use imagens de previsão para mais treino

Quando utiliza ou testa o classificador de imagem submetendo imagens ao ponto final de previsão, o serviço Custom Vision armazena essas imagens. Em seguida, pode usá-los para melhorar o modelo.

1. Para visualizar as imagens submetidas ao classificador, abra a [página web da Visão Personalizada,](https://customvision.ai)vá ao seu projeto e selecione o separador __Previsões.__ A vista padrão mostra imagens da iteração atual. Pode utilizar o menu de entrega de __iteração__ para visualizar as imagens submetidas durante as iterações anteriores.

    ![screenshot do separador previsões, com imagens à vista](./media/getting-started-improving-your-classifier/predictions.png)

2. Pairar sobre uma imagem para ver as etiquetas que foram previstas pelo classificador. As imagens são classificadas de modo a que as que podem trazer mais melhorias ao classificador estejam listadas no topo. Para utilizar um método de triagem diferente, faça uma seleção na secção __'Classificar'.__ 

    Para adicionar uma imagem aos dados de treino existentes, selecione a imagem, desfaça a(s) etiquetas corretas e clique em __Guardar e fechar__. A imagem será removida das __Previsões__ e adicionada ao conjunto de imagens de treino. Pode vê-lo selecionando o separador __Imagens de Treino.__

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag.png)

3. Em seguida, utilize o botão __Train__ para voltar a treinar o classificador.

## <a name="visually-inspect-predictions"></a>Fiscalize visualmente as previsões

Para inspecionar as previsões de imagem, vá ao __separador Imagens de Treino,__ selecione a sua iteração de treino anterior no menu de entrega de **Iteration** e verifique uma ou mais etiquetas na secção **Tags.** A vista deve agora exibir uma caixa vermelha em torno de cada uma das imagens para as quais o modelo não previu corretamente a etiqueta dada.

![Imagem da história da iteração](./media/getting-started-improving-your-classifier/iteration.png)

Por vezes, uma inspeção visual pode identificar padrões que pode então corrigir adicionando mais dados de treino ou modificando os dados de treino existentes. Por exemplo, um classificador para maçãs vs. limas pode rotular incorretamente todas as maçãs verdes como limas. Em seguida, pode corrigir este problema adicionando e fornecendo dados de treino que contenham imagens marcadas de maçãs verdes.

## <a name="next-steps"></a>Passos seguintes

Neste guia, aprendeu várias técnicas para tornar o seu modelo de classificação de imagem personalizado mais preciso. Em seguida, aprenda a testar as imagens programáticamente submetendo-as à API de Previsão.

> [!div class="nextstepaction"]
> [Utilizar a API de predição](use-prediction-api.md)
