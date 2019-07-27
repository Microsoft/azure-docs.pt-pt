---
title: Melhorando seu classificador-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como melhorar a qualidade do classificador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: d71c750185589fd488df70b63fd48e9e674ee3dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561040"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar seu classificador

Neste guia, você aprenderá a melhorar a qualidade de seu classificador de Serviço de Visão Personalizada. A qualidade do classificador depende da quantidade, da qualidade e da variedade dos dados rotulados que você fornece e de quão equilibrada é o conjunto geral. Um bom classificador tem um conjunto de e de treinamento balanceado que representa o que será enviado para o classificador. O processo de criação desse classificador é iterativo; é comum levar algumas rodadas de treinamento para alcançar os resultados esperados.

Veja a seguir um padrão geral para ajudá-lo a criar um classificador mais preciso:

1. Treinamento de primeira rodada
1. Adicione mais imagens e equilibre dados; readaptar
1. Adicionar imagens com plano de fundo variado, iluminação, tamanho do objeto, ângulo da câmera e estilo; readaptar
1. Usar nova (s) imagem (ões) para testar a previsão
1. Modificar os dados de treinamento existentes de acordo com os resultados da previsão

## <a name="prevent-overfitting"></a>Impedir superajuste

Às vezes, um classificador aprenderá a fazer previsões com base em características arbitrárias que suas imagens têm em comum. Por exemplo, se você estiver criando um classificador para maçãs versus cítrico e tiver usado imagens de maçãs em mãos e de cítrico em chapas brancas, o classificador poderá dar importância indevida a mãos vs. pratos, em vez de maçãs vs. cítrico.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir esse problema, use as seguintes diretrizes de treinamento com imagens mais variadas: fornecer imagens com diferentes ângulos, planos de fundo, tamanho do objeto, grupos e outras variações.

## <a name="data-quantity"></a>Quantidade de dados

O número de imagens de treinamento é o fator mais importante. É recomendável usar pelo menos 50 imagens por rótulo como um ponto de partida. Com menos imagens, há um risco maior de superajuste e, embora seus números de desempenho possam sugerir boa qualidade, seu modelo pode se esforçar com dados reais. 

## <a name="data-balance"></a>Saldo de dados

Também é importante considerar as quantidades relativas dos seus dados de treinamento. Por exemplo, o uso de imagens 500 para um rótulo e 50 imagens para outro rótulo faz um conjunto de um de treinamento desbalanceado. Isso fará com que o modelo seja mais preciso para prever um rótulo do que outro. É provável que você veja resultados melhores se mantiver pelo menos uma proporção de 1:2 entre o rótulo com o menor número de imagens e o rótulo com a maioria das imagens. Por exemplo, se o rótulo com a maioria das imagens tiver 500 imagens, o rótulo com o mínimo de imagens deverá ter pelo menos 250 imagens para treinamento.

## <a name="data-variety"></a>Variedade de dados

Certifique-se de usar imagens representadoras do que serão enviadas para o classificador durante o uso normal. Caso contrário, seu classificador poderia aprender a fazer previsões com base em características arbitrárias que suas imagens têm em comum. Por exemplo, se você estiver criando um classificador para maçãs versus cítrico e tiver usado imagens de maçãs em mãos e de cítrico em chapas brancas, o classificador poderá dar importância indevida a mãos vs. pratos, em vez de maçãs vs. cítrico.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir esse problema, inclua uma variedade de imagens para garantir que o classificador também possa generalizar. Veja abaixo algumas maneiras de tornar seu treinamento definido mais diversificado:

* __Seguindo__ Forneça imagens do seu objeto na frente de diferentes planos de fundo. As fotos em contextos naturais são melhores do que as fotos na frente de planos de fundo neutros, pois fornecem mais informações para o classificador.

    ![Imagem de exemplos de plano de fundo](./media/getting-started-improving-your-classifier/background.png)

* __Ilumina__ Forneça imagens com iluminação variada (ou seja, obtida com flash, alta exposição e assim por diante), especialmente se as imagens usadas para previsão têm iluminação diferente. Também é útil usar imagens com saturação, matiz e brilho variados.

    ![Imagem de amostras de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

* __Tamanho do objeto:__ Forneça imagens nas quais os objetos variam em tamanho e número (por exemplo, uma foto de vários bananas e closeup de um único banana). O dimensionamento diferente ajuda o classificador a generalizar melhor.

    ![Imagem de amostras de tamanho](./media/getting-started-improving-your-classifier/size.png)

* __Ângulo da câmera:__ Fornecer imagens obtidas com ângulos de câmera diferentes. Como alternativa, se todas as suas fotos precisarem ser executadas com câmeras fixas (como câmeras de vigilância), certifique-se de atribuir um rótulo diferente a cada objeto que ocorre regularmente para evitar&mdash;sobreajuste de objetos não relacionados (como lampposts) como o principal recurso.

    ![Imagem de exemplos de ângulo](./media/getting-started-improving-your-classifier/angle.png)

* __Estilo__ Forneça imagens de diferentes estilos da mesma classe (por exemplo, diferentes variedades da mesma fruta). No entanto, se você tiver objetos de estilos drasticamente diferentes (como o Mickey Mouse versus um mouse da vida real), recomendamos rotulá-los como classes separadas para representar melhor seus recursos distintos.

    ![Imagem de exemplos de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images"></a>Imagens negativas

Em algum momento em seu projeto, talvez seja necessário adicionar _amostras negativas_ para ajudar a tornar seu classificador mais preciso. Exemplos negativos são aqueles que não correspondem a nenhuma das outras marcas. Ao carregar essas imagens, aplique o rótulo **negativo** especial a elas.

> [!NOTE]
> O Serviço de Visão Personalizada dá suporte a alguma manipulação automática de imagem negativa. Por exemplo, se você estiver criando um classificador Grape vs. banana e enviar uma imagem de um sapato para previsão, o classificador deverá pontuar essa imagem como perto de 0% para Grape e banana.
> 
> Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens usadas no treinamento, é provável que o modelo classifique as imagens negativas como uma classe rotulada devido às grandes semelhanças. Por exemplo, se você tiver um classificador laranja versus grapefruit e alimentar uma imagem de um Clementine, ele poderá pontuar o Clementine como um laranja porque muitos recursos do Clementine se assemelham àqueles de laranjas. Se suas imagens negativas forem dessa natureza, recomendamos que você crie uma ou mais marcas adicionais (como **outras**) e rotule as imagens negativas com essa marca durante o treinamento para permitir que o modelo se diferencie melhor entre essas classes.

## <a name="use-prediction-images-for-further-training"></a>Usar imagens de previsão para treinamento adicional

Quando você usa ou testa o classificador de imagem enviando imagens para o ponto de extremidade de previsão, o serviço de Visão Personalizada armazena essas imagens. Você pode usá-los para melhorar o modelo.

1. Para exibir imagens enviadas ao classificador, abra a [página da web visão personalizada](https://customvision.ai), vá para seu projeto e selecione a guia __previsões__ . O modo de exibição padrão mostra imagens da iteração atual. Você pode usar o  menu suspenso iteração para exibir imagens enviadas durante as iterações anteriores.

    ![captura de tela da guia previsões, com imagens na exibição](./media/getting-started-improving-your-classifier/predictions.png)

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador. As imagens são classificadas de forma que as que podem trazer mais melhorias ao classificador sejam listadas na parte superior. Para usar um método de classificação diferente, faça uma seleção na seção __classificar__ . 

    Para adicionar uma imagem aos dados de treinamento existentes, selecione a imagem, defina as marcações corretas e clique em __salvar e fechar__. A imagem será removida das __previsões__ e adicionada ao conjunto de imagens de treinamento. Você pode exibi-lo selecionando a guia __imagens de treinamento__ .

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag.png)

3. Em seguida, use o botão __treinar__ para treinar novamente o classificador.

## <a name="visually-inspect-predictions"></a>Inspecionar previsões visualmente

Para inspecionar previsões de imagem, vá para a guia __imagens de treinamento__ , selecione a iteração de treinamento anterior no menu suspenso **iteração** e marque uma ou mais marcas na seção **marcas** . A exibição agora deve exibir uma caixa vermelha em torno de cada uma das imagens para as quais o modelo não conseguiu prever corretamente a marca especificada.

![Imagem do histórico de iteração](./media/getting-started-improving-your-classifier/iteration.png)

Às vezes, uma inspeção visual pode identificar padrões que você pode corrigir adicionando mais dados de treinamento ou modificando dados de treinamento existentes. Por exemplo, um classificador para maçãs vs. verde-limão pode rotular incorretamente todas as maçãs verdes como verde-limão. Você pode corrigir esse problema adicionando e fornecendo dados de treinamento que contêm imagens marcadas de maçãs verdes.

## <a name="next-steps"></a>Passos Seguintes

Neste guia, você aprendeu várias técnicas para tornar o modelo de classificação de imagem personalizada mais preciso. Em seguida, saiba como testar imagens programaticamente enviando-as para a API de previsão.

> [!div class="nextstepaction"]
> [Usar a API de previsão](use-prediction-api.md)
