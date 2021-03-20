---
title: Modelos pré-construídos para compreensão de linguagem
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e conversadores ao utilizador.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541922"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos pré-construídos para cenários comuns de utilização

O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e conversadores ao utilizador. Esta é uma forma rápida e fácil de adicionar habilidades à sua aplicação de cliente de conversação sem ter de desenhar os modelos para essas habilidades.

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Selecione **Domínios Pré-construídos** a partir da barra de ferramentas esquerda.

1. Encontre o domínio que deseja adicionado à aplicação e, em seguida, selecione Adicionar botão **de domínio.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio pré-construído do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicione uma intenção pré-construída

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Na página **Intenções,** selecione Adicionar intenção de **domínio pré-construído** a partir da barra de ferramentas acima da lista de intenções.

1. Selecione uma intenção do diálogo pop-up.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção pré-construída](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **'Fazer'.**

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade pré-construída
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Entidades** no lado esquerdo.

1. Na página **Entidades,** **selecione Adicionar entidade pré-construída.**

1. Na caixa de diálogo **de entidades pré-construídas,** selecione a entidade pré-construída.

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade pré-construída](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Concluído**. Depois de a entidade ser adicionada, não precisa de treinar a app.

## <a name="add-a-prebuilt-domain-entity"></a>Adicionar uma entidade de domínio pré-construída
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Entidades** no lado esquerdo.

1. Na página **Entidades,** **selecione Adicionar entidade de domínio pré-construída.**

1. Na caixa de diálogo **dos modelos de domínio pré-construídos,** selecione a entidade de domínio pré-construída.

1. Selecione **Concluído**. Depois de a entidade ser adicionada, não precisa de treinar a app.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para ver o modelo pré-construído a partir do ponto final de previsão

A maneira mais fácil de ver o valor de um modelo pré-construído é consultar a partir do ponto final publicado.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contenham um token de entidade pré-construída

Se tiver uma entidade de aprendizagem automática que necessite de uma característica necessária de uma entidade pré-construída, adicione uma sub-entidade à entidade de aprendizagem automática e, em seguida, adicione uma característica _necessária_ de uma entidade pré-construída.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construa o modelo a partir de .csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
