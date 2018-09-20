---
title: Testar e voltar a preparar um modelo - serviço de visão personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como testar uma imagem e, em seguida, utilizá-lo para voltar a preparar o modelo.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 5830257cf246e059cbccb654462f709df981e06b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367955"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testar e voltar a preparar um modelo com o serviço de visão personalizada

Depois de preparar o seu modelo, pode testar rapidamente a nele através de uma imagem armazenada localmente ou uma imagem online. O teste utiliza a iteração mais recentemente treinada.

## <a name="test-your-model"></a>Testar o seu modelo

1. Partir do [página da web de visão personalizada](https://customvision.ai), selecione seu projeto. Selecione **teste rápido** à direita da barra de menus superior. Esta ação abre uma janela rotulada **teste rápido**.

    ![O botão de teste rápida é mostrado no canto superior direito da janela.](./media/test-your-model/quick-test-button.png)

2. Na **teste rápido** janela, clique no **Submeter imagem** campo e introduza o URL da imagem que pretende utilizar para o teste. Se pretender utilizar uma imagem armazenada localmente em vez disso, clique nas **navegar nos ficheiros locais** botão e selecione um ficheiro de imagem local.

    ![Imagem da página de imagem de envio](./media/test-your-model/submit-image.png)

A imagem que selecionou é apresentado no meio da página. Em seguida, os resultados são apresentados abaixo da imagem na forma de uma tabela com duas colunas, denominadas **etiquetas** e **confiança**. Depois de ver os resultados, poderá fechar o **teste rápido** janela.

Pode agora adicionar esta imagem de teste ao seu modelo e, em seguida, voltar a preparar seu modelo.

## <a name="use-the-predicted-image-for-training"></a>Utilize a imagem de prevista para treinamento.

Para utilizar a imagem submetida anteriormente para treinamento, utilize os seguintes passos:

1. Para exibir imagens submetidas para o classificador, abra a [página da web de visão personalizada](https://customvision.ai) e selecione o __previsões__ separador.

    ![Imagem do separador de previsões](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > A vista predefinida mostra imagens a partir de iteração atual. Pode utilizar o __iteração__ pendente do campo para exibir imagens submetidas durante iterações anteriores.

2. Coloque o cursor sobre uma imagem para ver as etiquetas que foram previstas pelo classificador.

    > [!TIP]
    > Imagens estão ordenadas, para que as imagens que podem levar os maioria dos ganhos para o classificador estejam na parte superior. Para selecionar uma classificação diferente, utilize o __ordenação__ secção.

    Para adicionar uma imagem aos seus dados de treinamento, selecione a imagem, selecione a etiqueta e, em seguida, selecione __guarde e feche__. A imagem é removida das __previsões__ e adicionados às imagens de formação. Pode vê-la ao selecionar o __imagens de formação__ separador.

    ![Imagem da página etiquetagem](./media/test-your-model/tag-image.png)

3. Utilize o __Train__ botão para voltar a preparar o classificador.

## <a name="next-steps"></a>Passos Seguintes

[Melhorar o seu classificador](getting-started-improving-your-classifier.md)