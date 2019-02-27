---
title: Melhorar o classificador - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como melhorar a qualidade do seu classificador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.openlocfilehash: 6e97001497ebdddebe9b5b726e0526a987b6c63c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873326"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Neste guia aprenderá a melhorar a qualidade do seu classificador de serviço de visão personalizada. A qualidade do seu classificador depende da quantidade, qualidade e variedade dos dados etiquetados que fornecer e balanceada como o conjunto de dados geral é. Um bom classificador tem um conjunto de dados de treinamento equilibrada que seja representativo da qual serão submetidos para o classificador. O processo de criação de tal um classificador é iterativo; é comum para efetuar algumas rodadas de treinamento para alcançar os resultados esperados.

Este é um padrão geral para o ajudar a criar um classificador mais preciso:

1. Primeira rodada treinamento
1. Adicionar mais imagens e dados; voltar a preparar
1. Adicionar imagens com diferentes em segundo plano, o iluminação, o tamanho do objeto, o ângulo de câmera e o estilo; voltar a preparar
1. Utilizar imagens de novo para testar a predição
1. Modificar os dados de treinamento existentes, de acordo com os resultados de predição

## <a name="overfitting"></a>Overfitting

Às vezes, um classificador ficará a saber fazer predições com base nas características arbitrárias que suas imagens têm em comum. Por exemplo, se estiver a criar um classificador de maçãs versus citrus e utilizou as imagens de maçãs nas mãos e de citrus em brancas pratos, o classificador pode dar importância indevida para mãos versus pratos, em vez de maçãs versus citrus.

![Imagem da classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, utilize as seguintes orientações no treinamento com os mais variados imagens: fornecer imagens com diferentes ângulos, planos de fundo, tamanho do objeto, grupos e outras variações.

## <a name="data-quantity"></a>Quantidade de dados

O número de imagens de formação é o fator mais importante. Recomendamos que utilize, pelo menos, 50 imagens por etiqueta como ponto de partida. Com menos imagens, há um risco mais alto de overfitting e, enquanto os seus números de desempenho podem sugerir boa qualidade, o seu modelo pode apresentar dificuldades com dados do mundo real. 

## <a name="data-balance"></a>Saldo de dados

Também é importante considerar as quantidades relativas dos seus dados de treinamento. Por exemplo, o uso de 500 imagens para uma etiqueta e 50 imagens para outra etiqueta torna num conjunto de dados de treinamento desequilibrado. Isso fará com que o modelo ser mais precisa ao prever uma etiqueta que o outro. Provavelmente verá melhores resultados, se mantiver, pelo menos, um rácio de 1:2 entre a etiqueta com o menor número de imagens e a etiqueta com a maioria das imagens. Por exemplo, se a etiqueta com a maioria das imagens tem 500 imagens, a etiqueta com as imagens, pelo menos, deve ter, pelo menos, 250 imagens para treinamento.

## <a name="data-variety"></a>Variedade de dados

Certifique-se de que utilizar imagens que representem o que irá ser submetido para o classificador durante o uso normal. Caso contrário, o seu classificador poderia aprender fazer predições com base nas características arbitrárias que suas imagens têm em comum. Por exemplo, se estiver a criar um classificador de maçãs versus citrus e utilizou as imagens de maçãs nas mãos e de citrus em brancas pratos, o classificador pode dar importância indevida para mãos versus pratos, em vez de maçãs versus citrus.

![Imagem da classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir este problema, incluem uma variedade de imagens para se certificar de que o seu classificador pode generalizar bem. Veja a seguir estão algumas formas a que se pode tornar seu treinamento definidas mais diversificadas:

* __Em segundo plano:__ Fornece imagens de seu objeto à frente de planos de fundo diferentes. Fotos em contextos naturais são melhores do que fotografias na frente de planos de fundo neutras que fornecem mais informações para o classificador.

    ![Imagem dos exemplos em segundo plano](./media/getting-started-improving-your-classifier/background.png)

* __Iluminação:__ Fornecer imagens com iluminação variada (o que está, a ser utilizado com a exposição de flash, alta) e assim por diante, especialmente se as imagens usadas para predição tem iluminação diferente. Também é útil utilizar imagens com variadas saturação hue e brilho.

    ![Imagem de amostras de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

* __Tamanho do objeto:__ Fornecer imagens nas quais os objetos variam em tamanho e número (por exemplo, uma fotografia de muitas bananas e um grande plano de um único banana). Dimensionamento diferente ajuda o classificador de generalizar melhor.

    ![Imagem de amostras de tamanho](./media/getting-started-improving-your-classifier/size.png)

* __Ângulo de câmera:__ Fornece imagens feitas com ângulos de câmera diferentes. Em alternativa, se todas as suas fotografias devem ser feitas com câmeras fixas (por exemplo, câmeras de vigilância), não se esqueça de atribuir uma etiqueta diferente para cada objeto que ocorrem regularmente para evitar overfitting&mdash;interpretar relacionada com os objetos (como lampposts) como o principal recurso.

    ![Imagem de amostras de ângulo](./media/getting-started-improving-your-classifier/angle.png)

* __Estilo:__ Fornece imagens de diferentes estilos da mesma classe (por exemplo, uma variedade diferente de fruto mesmo). No entanto, se tiver objetos dos estilos drasticamente diferentes (por exemplo, o Mouse de Mickey versus um mouse da vida real), é recomendável que etiquetá-los como classes separadas para melhor representam as respetivas funcionalidades distintas.

    ![Imagem de amostras de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-prediction-images-for-further-training"></a>Utilizar imagens de previsão para obter mais treinamento

Quando utilizar ou testar o classificador de imagem ao enviar imagens para o ponto final de previsão, o serviço de visão personalizada armazena essas imagens. Em seguida, pode usá-los para melhorar o modelo.

1. Para exibir imagens submetidas para o classificador, abra a [página da web de visão personalizada](https://customvision.ai), aceda ao seu projeto e selecione o __previsões__ separador. A vista predefinida mostra imagens a partir de iteração atual. Pode utilizar o __iteração__ menu para ver as imagens submetidas durante iterações anteriores pendente.

    ![captura de ecrã das predições separador, com as imagens na vista](./media/getting-started-improving-your-classifier/predictions.png)

2. Coloque o cursor sobre uma imagem para ver as etiquetas que foram previstas pelo classificador. Imagens são ordenadas, para que aqueles que podem colocar mais melhorias para o classificador estão listados a parte superior. Para utilizar um método de classificação diferente, faça uma seleção na __ordenação__ secção. 

    Para adicionar uma imagem aos seus dados de treinamento existentes, selecione a imagem, defina a etiqueta correto (s) e clique em __guarde e feche__. A imagem será removida do __previsões__ e adicionado ao conjunto de imagens de formação. Pode vê-la ao selecionar o __imagens de formação__ separador.

    ![Imagem da página etiquetagem](./media/getting-started-improving-your-classifier/tag.png)

3. Em seguida, utilize o __Train__ botão para voltar a preparar o classificador.

## <a name="visually-inspect-predictions"></a>Inspecionar visualmente previsões

Para inspecionar as previsões de imagem, vá para o __imagens de formação__ separador, selecione a sua iteração anterior do treinamento no **iteração** menu pendente e verificar uma ou mais etiquetas no **etiquetas** secção. A exibição deverá agora apresentar uma caixa vermelha à volta de cada uma das imagens para o qual o modelo de falha prever corretamente a marca de determinado.

![Imagem de histórico de iteração](./media/getting-started-improving-your-classifier/iteration.png)

Às vezes, uma inspeção visual pode identificar padrões que, em seguida, pode corrigir adicionando mais dados de treinamento ou modificando os dados de treinamento existentes. Por exemplo, um classificador de maçãs versus limes incorretamente pode etiquetar todas as maçãs verde como limes. Em seguida, pode corrigir esse problema ao adicionar e fornecer dados de treinamento que contém imagens etiquetadas de maçãs verde.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, aprendeu várias técnicas para tornar o seu modelo de classificação de imagem personalizada mais precisas. Em seguida, saiba como testar imagens por meio de programação ao submetê-los para a API de predição.

> [!div class="nextstepaction"]
[Utilizar a API de predição](use-prediction-api.md)
