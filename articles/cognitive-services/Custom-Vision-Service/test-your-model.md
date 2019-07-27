---
title: Testar e treinar novamente um modelo-Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como testar uma imagem e usá-la para treinar novamente o modelo.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 3f78f0b992581a44b030387f1bd0e37664df4cfd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560904"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testar e treinar novamente um modelo com Serviço de Visão Personalizada

Depois de treinar seu modelo, você pode testá-lo rapidamente usando uma imagem armazenada localmente ou uma imagem online. O teste usa a iteração treinada mais recentemente.

## <a name="test-your-model"></a>Testar o seu modelo

1. Na [página da Web do visão personalizada](https://customvision.ai), selecione seu projeto. Selecione **teste rápido** à direita da barra de menus superior. Essa ação abre uma janela rotulada **teste rápido**.

    ![O botão de teste rápido é mostrado no canto superior direito da janela.](./media/test-your-model/quick-test-button.png)

2. Na janela de **teste rápido** , clique no campo **Enviar imagem** e insira a URL da imagem que você deseja usar para o teste. Se você quiser usar uma imagem armazenada localmente, clique no botão **procurar arquivos locais** e selecione um arquivo de imagem local.

    ![Imagem da página Enviar imagem](./media/test-your-model/submit-image.png)

A imagem selecionada aparece no meio da página. Em seguida, os resultados aparecem abaixo da imagem na forma de uma tabela com duas colunas, rotuladas como **marcas** e **confiança**. Depois de exibir os resultados, você pode fechar a janela de **teste rápido** .

Agora você pode adicionar essa imagem de teste ao seu modelo e, em seguida, treinar novamente seu modelo.

## <a name="use-the-predicted-image-for-training"></a>Usar a imagem prevista para treinamento

Para usar a imagem enviada anteriormente para treinamento, use as seguintes etapas:

1. Para exibir as imagens enviadas ao classificador, abra a [página da Web do visão personalizada](https://customvision.ai) e selecione a guia __previsões__ .

    ![Imagem da guia previsões](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > O modo de exibição padrão mostra imagens da iteração atual. Você pode usar o  campo suspenso de iteração para exibir imagens enviadas durante as iterações anteriores.

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador.

    > [!TIP]
    > As imagens são classificadas para que as imagens que podem trazer mais ganhos para o classificador fiquem na parte superior. Para selecionar uma classificação diferente, use a seção __classificar__ .

    Para adicionar uma imagem aos seus dados de treinamento, selecione a imagem, selecione a marca e, em seguida, selecione __salvar e fechar__. A imagem é removida das __previsões__ e adicionada às imagens de treinamento. Você pode exibi-lo selecionando a guia __imagens de treinamento__ .

    ![Imagem da página de marcação](./media/test-your-model/tag-image.png)

3. Use o botão __treinar__ para treinar novamente o classificador.

## <a name="next-steps"></a>Passos Seguintes

[Melhorar seu classificador](getting-started-improving-your-classifier.md)
