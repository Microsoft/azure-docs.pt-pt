---
title: Como usar a propriedade "entidade esperada" de ações de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar a propriedade "entidade esperada" de um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707326"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Como usar a propriedade "entidade esperada" de ações

Este tutorial demonstra a propriedade "entidade esperada" de ações.

## <a name="video"></a>Vídeo

[![Esperado tutorial de entidade versão prévia](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Use a propriedade "entidade esperada" de uma ação para salvar a resposta do usuário a essa ação em uma entidade.

Ao adicionar entidades à propriedade "entidade esperada" de uma ação, o sistema irá:

1. Comece tentando fazer a correspondência entre entidades usando o modelo de extração de entidade baseada em aprendizado de máquina
2. Atribua o usuário inteiro expressão para $entity com base na heurística se nenhuma entidade for encontrada
3. Chame `EntityDetectionCallback`e vá para a seleção de ação.

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "novo modelo".
2. No campo "nome", digite "ExpectedEntities" e pressione Enter.
3. Clique no botão "criar".

### <a name="entity-creation"></a>Criação de entidade

1. No painel esquerdo, clique em "entidades" e, em seguida, no botão "nova entidade".
2. Selecione "personalizado treinado" para o "tipo de entidade".
3. Digite "Name" para o "nome da entidade".
4. Clique no botão "criar".

> [!NOTE]
> O tipo de entidade "customly" significa que essa entidade pode ser treinada, ao contrário de outros tipos de entidades.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "Qual é o seu nome?"
3. No campo "entidades esperadas", digite "nome".
4. Clique no botão "criar".

> [!NOTE]
> As entidades detectadas e extraídas da resposta do usuário serão salvas na entidade "nome" se essa ação for escolhida. Se nenhuma entidade for detectada, toda a resposta será salva nessa entidade.

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "Hi $name!"
3. Clique no botão "criar".

> [!NOTE]
> A entidade "Name" foi adicionada automaticamente como "entidades necessárias" por referência na resposta.

Agora você tem duas ações.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Olá".
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "Qual é seu nome?"
    - O "Hi $name!" a resposta não pode ser selecionada porque essa resposta requer que a entidade "nome" seja definida na memória do modelo agora.
5. No painel bate-papo, em que diz "digite sua mensagem...", digite "Frank".
    - "Frank" é realçado como uma entidade com base na heurística que configuramos anteriormente para salvar a resposta como a entidade.
6. Clique no botão "ações de Pontuação".
    - A entidade "Name" agora é definida como "Frank" na memória do modelo, portanto, a ação "Olá $name" é selecionável como uma ação.
7. Selecione a resposta "Olá $name!"
8. Clique no botão "salvar".

A adição de entradas alternativas treina ainda mais o modelo.

1. Na "Adicionar entrada alternativa..." , digite "I José".
    - O modelo não reconhece o nome como uma entidade, de modo que seleciona todo o bloco de texto como o valor da entidade
2. Clique na frase "I José" e, em seguida, clique no ícone de lixeira.
3. Clique em "José" e, em seguida, clique em "nome" na lista de entidades.
4. Clique em ações de pontuação.
5. Selecione a resposta "Olá, Frank!"
6. Clique no botão "salvar".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades que não poderiam ser negadas](./06-negatable-entities.md)
