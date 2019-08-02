---
title: Como usar entidades com vários valores com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades com vários valores com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704093"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Como usar entidades com vários valores com um modelo de Conversation Learner
Este tutorial mostra a propriedade de valores múltiplos de entidades.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de entidades com vários valores](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
As entidades com vários valores acumulam valores em uma lista, em vez de armazenar um único valor.  Essas entidades são úteis quando os usuários podem especificar mais de um valor. Ingredientes em uma pizza, por exemplo.

As entidades marcadas como vários valores terão cada instância reconhecida da entidade anexada a uma lista na memória do bot. O reconhecimento subsequente acrescenta ao valor da entidade, em vez de substituir.

## <a name="steps"></a>Passos

Inicie o home page na interface do usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Digite **MultiValueEntities** para o **nome**.
3. Selecione **Criar**.

### <a name="entity-creation"></a>Criação de entidade

1. Selecione **entidades** no painel esquerdo e **nova entidade**.
2. Selecione **personalizado treinado** para **tipo de entidade**.
3. Insira **ingredientes** para o **nome da entidade**.
4. Verifique o **valor múltiplo** para habilitar a entidade para acumular um ou mais valores.
5. Marque **negable**.
6. Selecione **Criar**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. Selecione **ações** no painel esquerdo e **nova ação**.
2. Insira **aqui seus ingredientes: $toppings** para **resposta do bot...** . O sinal de dólar à esquerda indica uma referência de entidade.
3. Selecione **Criar**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. Selecione **ações** no painel esquerdo e **nova ação**.
2. Insira **quais ingredientes você deseja?** para a **resposta do bot.** ..
3. Insira os **ingredientes** para desqualificar os **direitos**.
4. Selecione **Criar**.

Agora você tem duas ações.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Treinar o modelo

1. Selecione **treinar caixas de diálogo** no painel esquerdo e, em seguida, **nova caixa de diálogo treinar**.
2. Insira **Olá** para o expressão do usuário no painel de bate-papo à esquerda.
3. Selecione **ações de Pontuação**.
4. Selecione **quais ingredientes você deseja?** na lista de ações. O percentual é 100% como a única ação válida com base nas restrições.
5. Insira **queijo e cogumelos** para o expressão do usuário no painel de bate-papo à esquerda.
6. Realce **queijo** e selecione **+ ingredientes**.
7. Realce **cogumelos** e selecione **+ ingredientes**.
8. Selecione **ações de Pontuação**.
9. Selecione **aqui os ingredientes: $toppings** na lista de ações.
10. Digite **Add molho** para o próximo expressão do usuário no painel de bate-papo à esquerda.
11. Realce **pimentão** e selecione **+ ingredientes**.
12. Selecione **ações de Pontuação**.
13. Selecione **aqui os ingredientes: $toppings** na lista de ações.
14. Insira **remover queijo** para o terceiro expressão do usuário no painel de bate-papo à esquerda.
15. Realce **queijo** e, em seguida, selecione **-ingredientes**.
16. Selecione **ações de Pontuação**.
17. Selecione **aqui os ingredientes: $toppings** na lista de ações.

![](../media/T07_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades pré-treinadas](./08-pre-trained-entities.md)
