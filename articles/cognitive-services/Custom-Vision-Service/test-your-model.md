---
title: Testar e treinar um modelo - Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como testar uma imagem e depois usá-la para treinar o seu modelo no serviço De Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 736d135323e2a2df38a13dc46189e2a243fbe381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100097539"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Teste e retreine um modelo com Serviço de Visão Personalizada

Depois de treinar o seu modelo, pode testá-lo rapidamente usando uma imagem armazenada localmente ou uma imagem online. O teste usa a iteração mais recente.

## <a name="test-your-model"></a>Testar o seu modelo

1. A partir da [página web da Visão Personalizada,](https://customvision.ai)selecione o seu projeto. Selecione **Teste Rápido** à direita da barra de menu superior. Esta ação abre uma janela com a etiqueta **Quick Test**.

    ![O botão De Teste Rápido é mostrado no canto superior direito da janela.](./media/test-your-model/quick-test-button.png)

2. Na janela **Teste Rápido,** clique no campo **Imagem de Submissão** e introduza o URL da imagem que pretende utilizar para o seu teste. Se pretender utilizar uma imagem armazenada localmente, clique no botão **de ficheiros locais Browse** e selecione um ficheiro de imagem local.

    ![Imagem da página de imagem de submissão](./media/test-your-model/submit-image.png)

A imagem selecionada aparece no meio da página. Em seguida, os resultados aparecem abaixo da imagem sob a forma de uma tabela com duas colunas, etiquetas **e** **confiança**. Depois de ver os resultados, pode fechar a janela **do Teste Rápido.**

Pode agora adicionar esta imagem de teste ao seu modelo e, em seguida, voltar a treinar o seu modelo.

## <a name="use-the-predicted-image-for-training"></a>Use a imagem prevista para o treino

Para utilizar a imagem submetida anteriormente para treino, utilize os seguintes passos:

1. Para visualizar as imagens submetidas ao classificador, abra a [página web 'Visão Personalizada'](https://customvision.ai) e selecione o __separador Previsões.__

    ![Imagem do separador previsões](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > A vista padrão mostra imagens da iteração atual. Pode utilizar o campo __de iteração__ para visualizar as imagens submetidas durante as iterações anteriores.

2. Pairar sobre uma imagem para ver as etiquetas que foram previstas pelo classificador.

    > [!TIP]
    > As imagens estão classificadas, para que as imagens que podem trazer mais ganhos para o classificador estejam no topo. Para selecionar uma classificação diferente, utilize a secção __'Classificar'.__

    Para adicionar uma imagem aos seus dados de treino, selecione a imagem, selecione a etiqueta e, em seguida, __selecione Guardar e fechar__. A imagem é removida das __Previsões__ e adicionada às imagens de treino. Pode vê-lo selecionando o separador __Imagens de Treino.__

    ![Imagem da página de marcação](./media/test-your-model/tag-image.png)

3. Utilize o botão __Train__ para voltar a treinar o classificador.

## <a name="next-steps"></a>Passos seguintes

[Melhore o seu modelo](getting-started-improving-your-classifier.md)
