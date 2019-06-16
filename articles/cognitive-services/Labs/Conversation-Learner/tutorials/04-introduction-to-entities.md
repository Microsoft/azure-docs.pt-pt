---
title: Como utilizar entidades com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar entidades com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 3d9e2498a23ad49eb014cb0f81c819f3f63eef5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387808"
---
# <a name="introduction-to-entities"></a>Introdução às entidades

Este tutorial apresenta entidades, Disqualifying entidades, entidades necessárias e seu uso dentro de aprendiz de conversação.

## <a name="video"></a>Vídeo

[![Introdução à pré-visualização de Tutorial do entidades](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Requisitos

Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Entidades capturam as partes de informações que o Bot precisa executar sua tarefa, quer através de extração de expressões de utilizador ou atribuição pelo código personalizado. As próprias entidades também podem restringir a disponibilidade de ação por explicitamente a ser classificado como "Required" ou "Disqualifying".

- Entidades necessárias tem de estar presentes na memória do modelo para que a ação para estar disponível
- Disqualifying entidades têm *não* estar presente na memória do modelo para que a ação para estar disponível

Este tutorial concentra-se em entidades personalizadas. Com múltiplos valores, Negatable entidades e entidades programática previamente treinados, são introduzidas nos outros tutoriais.

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na IU da Web, clique em "Novo modelo."
2. No campo "Nome", escreva "IntroToEntities" e prima enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação de entidade

1. No painel esquerdo, clique em "Entidades", em seguida, no botão "Nova entidade".
2. Selecione "Treinados personalizada" para o "tipo de entidade".
3. Tipo de "city" para o "nome da entidade".
4. Clique no botão "Criar".

> [!NOTE]
> O tipo de entidade "Custom treinado" significa que esta entidade pode ser treinada, ao contrário de outros tipos de entidades.

### <a name="create-the-actions"></a>Criar as ações

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "Não sei que desejar de localidade."
3. No campo de "Entidades Disqualifying", escreva "Cidade".
4. Clique no botão "Criar".

> [!NOTE]
> Adicionar a "Cidade" entidade para "Entidades Disqualifying" seria desqualificar esta ação de consideração o Bot, quando o argumento "city" entidade é definido na memória do Bot.

Agora, crie uma segunda ação.

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "o clima no $city é provavelmente ensolarado".
3. Clique no botão "Criar".

> [!NOTE]
> O argumento "city" entidade foi adicionado automaticamente na lista de entidades necessárias por referência na resposta.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Preparar o modelo

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "hello".
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "Não sei que desejar de localidade."
5. Como o utilizador responder com "Porto".
6. Clique no botão "Pontuação ações".
7. Selecione a resposta, "Clima em $city é provavelmente ensolarado".
8. Clique no botão "Guardar".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidade esperada](./05-expected-entity.md)
