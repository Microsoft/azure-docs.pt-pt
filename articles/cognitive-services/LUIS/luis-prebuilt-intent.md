---
title: Objetivos pré-criados
titleSuffix: Azure Cognitive Services
description: LUIS inclui um conjunto de objetivos criados previamente para adicionar rapidamente os cenários de usuário comum e conversacionais.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: abdbd0672fbb29f3936e4bda6f71e41c0f287329
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221229"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Adicionar intenções criados previamente para objetivos comuns 

LUIS inclui um conjunto de objetivos pré-criados dos domínios criados previamente para adicionar rapidamente intenções e expressões comuns. Esta é uma forma rápida e fácil adicionar capacidades para a sua aplicação de cliente conversacionais sem ter de modelos para essas habilidades de design. 

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção pré-criados

1. Sobre o **as minhas aplicações** página, selecione a sua aplicação. Esta ação abre a aplicação para o **criar** secção da aplicação. 

1. Sobre o **intenções** página, selecione **adicionar intenção pré-criados** da barra de ferramentas acima da lista de objetivos. 

1. Selecione o **Utilities.Cancel** intenção da caixa de diálogo pop-up. 

    ![Adicionar intenções pré-criados](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selecione o **feito** botão.

## <a name="train-and-test"></a>Formação e teste

1. Depois da intenção é adicionada, preparar a aplicação, selecionando **treinar** na parte superior, com o botão direito barra de ferramentas. 

1. Testar o novo objetivo selecionando **teste** na barra de ferramentas certa. 

1. Na caixa de texto, introduza expressões para cancelar:

    |Expressão de teste|Classificação da predição|
    |--|:--|
    |Pretende cancelar a minha voo.|0,67|
    |Cancele a compra.|0.52|
    |Cancele a reunião.|0.56|

    ![Testar a intenção pré-criados](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Entidades pré-concebidas](./luis-prebuilt-entities.md)
