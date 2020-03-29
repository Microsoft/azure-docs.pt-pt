---
title: Teste e retre um modelo - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como testar uma imagem e depois usá-la para retreinar o seu modelo no serviço Custom Vision.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73721197"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Teste e retre um modelo com serviço de visão personalizada

Depois de treinar o seu modelo, pode testá-lo rapidamente usando uma imagem armazenada localmente ou uma imagem online. O teste usa a iteração mais recentemente treinada.

## <a name="test-your-model"></a>Testar o seu modelo

1. A partir da página web Custom [Vision,](https://customvision.ai)selecione o seu projeto. Selecione **Quick Test** à direita da barra de menu superior. Esta ação abre uma janela com a marca **Quick Test**.

    ![O botão Quick Test é mostrado no canto superior direito da janela.](./media/test-your-model/quick-test-button.png)

2. Na janela **De Teste Rápido,** clique no campo **De enviar imagem** e introduza o URL da imagem que pretende utilizar para o seu teste. Se pretender utilizar uma imagem armazenada localmente, clique no botão **de ficheiros locais e** selecione um ficheiro de imagem local.

    ![Imagem da página de imagem de submissão](./media/test-your-model/submit-image.png)

A imagem que selecionar aparece no meio da página. Em seguida, os resultados aparecem abaixo da imagem na forma de uma tabela com duas colunas, **etiquetas** rotuladas e **confiança**. Depois de ver os resultados, pode fechar a janela **de Teste Rápido.**

Agora pode adicionar esta imagem de teste ao seu modelo e depois retreinar o seu modelo.

## <a name="use-the-predicted-image-for-training"></a>Use a imagem prevista para o treino

Para utilizar a imagem submetida anteriormente para treino, utilize os seguintes passos:

1. Para visualizar as imagens submetidas ao classificador, abra a [página web](https://customvision.ai) da Visão Personalizada e selecione o separador __Previsões.__

    ![Imagem do separador de previsões](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > A visão padrão mostra imagens da iteração atual. Pode utilizar o campo de descida da __Iteração__ para ver imagens submetidas durante iterações anteriores.

2. Pairar sobre uma imagem para ver as etiquetas que foram previstas pelo classificador.

    > [!TIP]
    > As imagens estão classificadas, para que as imagens que podem trazer mais ganhos para o classificador estejam no topo. Para selecionar uma triagem diferente, utilize a secção __Ordenar.__

    Para adicionar uma imagem aos seus dados de treino, selecione a imagem, selecione a etiqueta e, em seguida, selecione __Guardar e fechar__. A imagem é removida das __Previsões__ e adicionada às imagens de treino. Pode vê-lo selecionando o separador __Training Images.__

    ![Imagem da página de marcação](./media/test-your-model/tag-image.png)

3. Utilize o botão __Train__ para retreinar o classificador.

## <a name="next-steps"></a>Passos seguintes

[Melhorar o seu classificador](getting-started-improving-your-classifier.md)
