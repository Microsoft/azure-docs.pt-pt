---
title: Rotular imagens mais rapidamente com marcas sugeridas
titleSuffix: Azure Cognitive Services
description: Neste guia, você aprenderá a usar marcas sugeridas para rotular um grande número de imagens mais rapidamente ao treinar modelos de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 31b8dfc234ac99d6f04061d6596e3dc8113e8d0f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213842"
---
# <a name="label-images-faster-with-suggested-tags"></a>Rotular imagens mais rapidamente com marcas sugeridas

Neste guia, você aprenderá a usar o recurso de marcas sugeridas para rotular um grande número de imagens mais rapidamente ao treinar um modelo de Visão Personalizada. 

Quando você marca imagens para um modelo de Visão Personalizada, o serviço usa a iteração mais recente treinada do modelo para prever os rótulos de imagens não marcadas. Em seguida, ele mostra essas previsões como marcas sugeridas, com base nas incertezas de limite de confiança e previsão selecionadas. Em seguida, você pode confirmar ou alterar as sugestões, acelerando o processo de marcação manual das imagens para treinamento.

## <a name="when-to-use-suggested-tags"></a>Quando usar marcas sugeridas

Tenha em mente as seguintes limitações:

* Solicite apenas marcas sugeridas para imagens cujo conteúdo já tenha sido treinado uma vez. Não obtenha sugestões para uma nova marca que você está apenas começando a treinar.
* Você só pode usar marcas sugeridas em imagens que não são marcadas; Você não pode obter sugestões para marcas adicionais em uma imagem já marcada.

> [!IMPORTANT]
> O recurso de marcas sugeridas usa o mesmo [modelo de preços](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) que previsões regulares. Na primeira vez que você disparar marcas sugeridas para um conjunto de imagens, será cobrado o mesmo para chamadas de previsão. Depois disso, o serviço armazena os resultados para as imagens selecionadas em um banco de dados por 30 dias e você pode acessá-las a qualquer momento gratuitamente dentro desse período. Após 30 dias, você será cobrado se solicitar suas marcas sugeridas novamente.

## <a name="suggested-tags-workflow"></a>Fluxo de trabalho de marcas sugeridas

As etapas a seguir mostram como usar o recurso de marcas sugeridas:

1. Carregue todas as suas imagens de treinamento em seu projeto Visão Personalizada.
1. Rotule a parte do conjunto de dados, escolhendo um número igual de imagens para cada marca.
    > [!TIP]
    > Certifique-se de usar todas as marcas para as quais você deseja sugestões mais tarde.
1. Inicie o processo de treinamento.
1. Quando o treinamento estiver concluído, navegue até a exibição não **marcada** e selecione o botão **obter marcas sugeridas** no painel esquerdo.
    > [!div class="mx-imgBorder"]
    > ![O botão marcas sugeridas é mostrado na guia imagens não marcadas.](./media/suggested-tags/suggested-tags-button.png)
1. Selecione o conjunto de imagens para as quais você deseja sugestões. Você só deve obter sugestões de marcas iniciais para uma parte das imagens não marcadas. Você obterá sugestões de marca melhores à medida que iterar o processo.
1. Confirme as marcas sugeridas, corrigindo as que não estão corretas.
    > [!TIP]
    > Imagens com marcas sugeridas são classificadas por sua incerteza de previsão (valores mais baixos indicam maior confiança). Você pode alterar a ordem de classificação com a opção **classificar por incerteza** . Se você definir o pedido como **alto para baixo**, poderá corrigir as previsões de alta incerteza primeiro e, em seguida, confirmar rapidamente as baixas incertezas.
    * Em projetos de classificação de imagens, você pode selecionar e confirmar as marcas em lotes. Filtre a exibição por uma determinada marca sugerida, desmarque as imagens que estão marcadas incorretamente e confirme o restante em um lote.
        > [!div class="mx-imgBorder"]
        > ![As marcas sugeridas são exibidas no modo de lote para IC com filtros.](./media/suggested-tags/ic-batch-mode.png)

        Você também pode usar marcas sugeridas no modo de imagem individual selecionando uma imagem da galeria.

        ![As marcas sugeridas são exibidas no modo de imagem individual para IC.](./media/suggested-tags/ic-individual-image-mode.png)
    * Em projetos de detecção de objetos, não há suporte para confirmações de lote, mas você ainda pode filtrar e classificar por marcas sugeridas para uma experiência de rotulação mais organizada. As miniaturas de suas imagens não marcadas mostrarão uma sobreposição de caixas delimitadas que indicam os locais das marcas sugeridas. Se você não selecionar um filtro de marca sugerido, todas as imagens não marcadas serão exibidas sem sobreposição de caixas delimitadoras.
        > [!div class="mx-imgBorder"]
        > ![As marcas sugeridas são exibidas no modo de lote para OD com filtros.](./media/suggested-tags/od-batch-mode.png)

        Para confirmar as marcas de detecção de objeto, você precisa aplicá-las a cada imagem individual na galeria.

        ![As marcas sugeridas são exibidas no modo de imagem individual para OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Inicie o processo de treinamento novamente.
1. Repita as etapas anteriores até estar satisfeito com a qualidade da sugestão.

## <a name="next-steps"></a>Passos seguintes

Siga um guia de início rápido para começar a criar e treinar um projeto Visão Personalizada.

* [Criar um classificador](getting-started-build-a-classifier.md)
* [Criar um detector de objeto](get-started-build-detector.md)