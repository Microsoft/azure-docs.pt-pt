---
title: Como usar ações de espera e de não espera com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar ações de espera e de não espera com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705609"
---
# <a name="wait-and-non-wait-actions"></a>Ações de espera e não espera

Este tutorial mostra a diferença entre ações de espera e ações de não espera no Conversation Learner.

## <a name="video"></a>Vídeo

[![Aguardar visualização do tutorial de não espera](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ação de espera: Depois que o sistema usar uma ação de "espera", ele deixará de executar ações e aguardará a entrada do usuário.
- Ação de não espera: Depois que o sistema executar uma ação de "não espera", ele escolherá imediatamente outra ação (sem esperar pela entrada do usuário).

## <a name="steps"></a>Passos

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na interface do usuário da Web, clique em novo modelo
2. No campo "nome", digite "aguardando não espera", pressione Enter ou clique no botão "criar".

### <a name="create-the-first-two-wait-actions"></a>Criar as duas primeiras ações de espera

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." Digite "qual pizza você deseja?".
    - Essa é uma ação de espera, portanto, deixe a caixa "aguardar resposta" marcada.
3. Clique no botão "criar".
4. Repetindo essas etapas, crie outra ação com "pizza a caminho!" como resposta do bot.

### <a name="train-using-those-wait-actions"></a>Treinar usando essas ações de espera

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Olá". 
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "o que pizza você deseja?".
5. Como o usuário, responda com "Margherita".
6. Clique no botão "ações de Pontuação".
7. Selecione a resposta "pizza sobre o caminho!".
8. Clique no botão "salvar".

### <a name="create-a-non-wait-action-while-training"></a>Criar uma ação de não espera durante o treinamento
Embora você possa criar a ação de não espera como fazia anteriormente, você também pode criá-la de dentro de uma sessão de treinamento.
1. Clique no botão "nova caixa de diálogo de treinamento".
2. Como o usuário digitado, "Olá".
3. Clique no botão "ações de Pontuação".
4. Clique no botão "+ ação". 
    - Isso o levará para a familiar caixa de diálogo "criar uma ação".
5. Digite a resposta do bot como "bem-vindo ao bot de pizza!"
6. Desmarque a caixa de seleção "aguardar resposta".
7. Clique no botão "criar".
    - Observe que o bot responde imediatamente com "bem-vindo ao bot de pizza!" e que você será solicitado novamente por outra resposta de bot. Isso ocorre porque a resposta do bot foi a ação de não espera que acabamos de criar.
9. Selecione a resposta "o que pizza você deseja?".
10. Como o usuário, responda com "Margherita".
11. Clique no botão "ações de Pontuação".
12. Selecione a resposta "pizza sobre o caminho!".
13. Clique no botão "salvar".

> [!NOTE]
> A sequência das respostas de bot com relação a ações de espera e de não espera.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução às entidades](./04-introduction-to-entities.md)
