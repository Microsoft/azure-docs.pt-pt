---
title: Como usar entidades que sejam negadas com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades que Negable com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704114"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Como usar entidades que não poderiam ser negadas com um modelo de Conversation Learner

Este tutorial demonstra a propriedade "Negable" de entidades.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de entidades de negação](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
A propriedade "Negable" de uma entidade permite que você rotule instâncias normais (positivas) e negativas da entidade, ensine com base em modelos positivos e negativos e limpe o valor de uma entidade existente. As entidades com seu conjunto de propriedades "Negable" são chamadas de entidades que podem ser negadas no conversa.

## <a name="steps"></a>Passos

Inicie o home page na interface do usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Digite **NegatableEntity** para o **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação de entidade

1. Selecione **entidades** no painel esquerdo e **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Insira o **nome** para o **nome da entidade**.
4. Marque **negação** para permitir que os usuários forneçam um valor de entidade, ou digamos que algo *não* seja um valor de entidade, excluindo o valor de entidade correspondente.
5. Selecione **Criar**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo e **nova ação**.
2. Digite **eu não sei seu nome.** para **resposta do bot...**
3. Insira o **nome** para qualificar os **direitos**.
4. Selecione **Criar**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo e **nova ação**.
2. Digite **eu sei seu nome. É $name.** para **resposta do bot...**
3. Selecione **Criar**.

> [!NOTE]
> A entidade de **nome** foi adicionada automaticamente como **entidades necessárias** por referência na resposta expressão.

Agora você tem duas ações.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Treinar o modelo

1. Selecione **treinar caixas de diálogo** no painel esquerdo e, em seguida, **nova caixa de diálogo treinar**.
2. Insira **Olá** para o expressão do usuário no painel de bate-papo à esquerda.
3. Selecione **ações de Pontuação**.
4. Selecione **não sei seu nome.** na lista de ações. O percentual é 100% como a única ação válida com base nas restrições.
5. Insira **meu nome é Frank** para o expressão do usuário no painel de bate-papo à esquerda.
6. Realce **Frank** e selecione **+ nome**. As entidades que não são de negação têm duas instâncias: (+) Plus adiciona ou substitui o valor; (-) menos remove o valor.
7. Selecione **ações de Pontuação**. A entidade **Name** agora é definida como **Frank** na memória do modelo **, portanto, eu sei seu nome. $Name** ação está disponível.
8. Selecione **sei seu nome. É $name.** na lista de ações.
9. Digite **meu nome não é Frank.** para o expressão do usuário no painel de bate-papo à esquerda.
10. Realce **Frank** e, em seguida, selecione **-nome** para limpar o valor da entidade **nome** .
11. Selecione **ações de Pontuação**.
12. Selecione **não sei seu nome.** na lista de ações.
13. Digite **meu nome é Susan.** para o terceiro expressão do usuário no painel de bate-papo à esquerda.
14. Realce **Susan** e **+ nome** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades com vários valores](./07-multi-value-entities.md)
