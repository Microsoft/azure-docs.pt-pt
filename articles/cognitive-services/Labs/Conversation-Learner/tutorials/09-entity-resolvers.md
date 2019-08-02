---
title: Resolvedores de entidade em um modelo de Conversation Learner – serviços cognitivas do Azure | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar resolvedores de entidade no Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 60c4abf1590cb91fd460cc6a2a5ba75a225ebd80
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704031"
---
# <a name="entity-resolvers"></a>Resolvedores de entidade

Este tutorial mostra como usar resolvedores de entidade no Conversation Learner

## <a name="video"></a>Vídeo

[![Visualização do tutorial de resolvedores de entidade](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- O tipo de resolvedor é uma propriedade opcional de entidades personalizadas.
- Os resolvedores de entidade utilizam o poder dos reconhecedores de entidade pré-treinados no LUIS para fornecer detalhes adicionais e clareza para sua entidade personalizada.

## <a name="steps"></a>Passos

Inicie o home page na interface do usuário da Web.

### <a name="create-the-model"></a>Criar o modelo

1. Selecione **novo modelo**.
2. Insira **resolvedores de entidade** para **nome**.
3. Selecione **Criar**.

### <a name="create-a-pair-of-entities"></a>Criar um par de entidades

1. Selecione **entidades** no painel esquerdo e **nova entidade**.
2. Insira a **partida** para o **nome da entidade**.
3. Selecione **datetimeV2** para o **tipo**de resolvedor.
4. Selecione **Criar**. Ignore o pop-up informativo selecionando **OK**.
5. Repita as etapas 1-4 para criar uma segunda entidade chamada **Return** com o tipo de resolvedor **datetimeV2** .

![](../media/T09_entities.png)

### <a name="create-a-pair-of-actions"></a>Criar um par de ações

1. Selecione **ações** no painel esquerdo e **nova ação**.
2. Insira **você está deixando em $Departure e retornando em $Return** para **resposta do bot...** .
    - IMPORTANTE – ao digitar $ [EntityName], você precisa pressionar Enter ou clicar na entidade na lista suspensa, caso contrário Conversation Learner considerará isso como texto em vez de uma entidade.
    - Observe que o campo de **entidades necessárias** também receberá essas entidades e elas não poderão ser removidas. Isso impede que essa ação fique disponível até que ambas as entidades necessárias estejam presentes.
3. Selecione **Criar**.
4. Selecione **nova ação** para criar uma segunda ação.
5. Insira **quando você pretende viajar? para a** **resposta do bot.** ..
6. Insira a **partida** e o **retorno** para desqualificar **entidades**. Eles dizem ao nosso bot para não realizar essa ação se uma dessas entidades contiver um valor.
7. Selecione **Criar**.

![](../media/T09_actions.png)

### <a name="training"></a>Formação

1. Assista ao **treinamento: [status]** no canto superior esquerdo para **concluído**.
    - Você pode clicar no link "atualizar" se isso levar muito tempo.
    - O status de treinamento "concluído" é necessário para que nossos resolvedores de entidade funcionem quando treinamos o modelo.

2. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, clique no botão "nova caixa de diálogo de treinamento".
3. Digite o primeiro usuário expressão, "livro me a Flight". 
4. Clique no botão "ações de Pontuação".
5. Selecione a resposta "quando você pretende viajar?".
6. Como o usuário, responda com, "deixando amanhã e retornando domingo na próxima semana".
    - Observe como Conversation Learner detectou duas entidades de "data de pré-treinamento" nesse usuário.
7. No painel "detecção de entidade", selecione o texto "amanhã" e etiquete-o como "partida"
8. Também rotular o texto "domingo da próxima semana" como "Return"
9. Clique no botão "ações de Pontuação".
    - Observe como o painel "Memory" contém as datas de partida e de retorno.
    - Focalize cada um e observe como as entidades são objetos de data que capturam claramente a data do calendário real em oposição a "domingo" ou "amanhã".
10. Selecione o "você está saindo..." Resposta do bot.
11. Clique no botão "salvar".

![](../media/T09_training.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Enumerar entidades](./tutorial-enum-set-entity.md)
