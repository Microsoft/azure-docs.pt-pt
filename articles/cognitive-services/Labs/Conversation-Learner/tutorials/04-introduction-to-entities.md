---
title: Como usar entidades com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707342"
---
# <a name="introduction-to-entities"></a>Introdução às entidades

Este tutorial apresenta entidades, desqualificando entidades, entidades necessárias e seu uso dentro de Conversation Learner.

## <a name="video"></a>Vídeo

[![Introdução à visualização do tutorial de entidades](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Requisitos

Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

As entidades capturam as informações que o bot precisa para executar sua tarefa, seja por meio da extração do usuário declarações ou da atribuição por código personalizado. As próprias entidades também podem restringir a disponibilidade da ação, sendo explicitamente classificadas como "obrigatória" ou "desqualificando".

- As entidades necessárias devem estar presentes na memória do modelo para que a ação esteja disponível
- As entidades de desqualificação *não* devem estar presentes na memória do modelo para que a ação esteja disponível

Este tutorial se concentra em entidades personalizadas. Entidades pré-treinados, de vários valores e que podem ser negadas e entidades programáticas são introduzidas em outros tutoriais.

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "novo modelo".
2. No campo "nome", digite "IntroToEntities" e pressione Enter.
3. Clique no botão "criar".

### <a name="entity-creation"></a>Criação de entidade

1. No painel esquerdo, clique em "entidades" e, em seguida, no botão "nova entidade".
2. Selecione "personalizado treinado" para o "tipo de entidade".
3. Digite "City" para o "nome da entidade".
4. Clique no botão "criar".

> [!NOTE]
> O tipo de entidade "customly" significa que essa entidade pode ser treinada, ao contrário de outros tipos de entidades.

### <a name="create-the-actions"></a>Criar as ações

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "não sei qual cidade você deseja".
3. No campo "desqualificando entidades", digite "City".
4. Clique no botão "criar".

> [!NOTE]
> Adicionar a entidade "City" para "desqualificar entidades" desqualificaria essa ação da consideração do bot quando a entidade "City" for definida na memória do bot.

Agora, crie uma segunda ação.

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "o clima na $city é provavelmente ensolarado".
3. Clique no botão "criar".

> [!NOTE]
> A entidade "City" foi adicionada automaticamente na lista de entidades necessárias por referência na resposta.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Olá".
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "não sei qual cidade você deseja".
5. Como o usuário, responda com "Seattle".
6. Clique no botão "ações de Pontuação".
7. Selecione a resposta, "o clima em $city é provavelmente ensolarado".
8. Clique no botão "salvar".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidade esperada](./05-expected-entity.md)
