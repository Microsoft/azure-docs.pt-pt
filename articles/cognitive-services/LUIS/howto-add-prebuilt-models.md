---
title: Modelos pré-construídos para compreensão da linguagem
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e de utilizadores conversacionais.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013535"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicione modelos pré-construídos para cenários de utilização comuns 

O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e de utilizadores conversacionais. Esta é uma forma rápida e fácil de adicionar habilidades à sua aplicação de cliente conversacional sem ter que projetar os modelos para essas habilidades. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Na página **My Apps,** selecione a sua aplicação. Isto abre a sua aplicação para a secção **Build** da app. 

1. Selecione **domínios pré-construídos** a partir da barra de ferramentas esquerda. 

1. Encontre o domínio que deseja adicionar à aplicação e, em seguida, selecione adicionar botão de **domínio.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio pré-construído do calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicione uma intenção pré-construída

1. Na página **My Apps,** selecione a sua aplicação. Isto abre a sua aplicação para a secção **Build** da app. 

1. Na página **Intenções,** selecione Adicionar intenção de **domínio pré-construído** a partir da barra de ferramentas acima da lista de intenções. 

1. Selecione os **Utilitários.Cancele** a intenção do diálogo pop-up. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção pré-construída](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Done.**

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade pré-construída

1. Abra a sua aplicação clicando no seu nome na página **minhas apps** e, em seguida, clique em **Entidades** no lado esquerdo. 

1. Na página **Entidades,** clique em **Adicionar entidade pré-construída.**

1. Em Adicionar caixa de diálogo de **entidades pré-construídas,** selecione a entidade pré-construída. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade pré-construída](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Done** (Concluído). Depois de adicionada a entidade, não precisa de treinar a app. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para ver modelo pré-construído a partir do ponto final da previsão

A maneira mais fácil de ver o valor de um modelo pré-construído é consultar a partir do ponto final publicado. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contenham um símbolo de entidade pré-construída
 
Se tiver uma entidade aprendida por máquinas que esteja limitada por uma entidade pré-construída, adicione um subcomponente à entidade aprendida por máquinas, adicione então um constrangimento de uma entidade pré-construída.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construir modelo a partir de .csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
