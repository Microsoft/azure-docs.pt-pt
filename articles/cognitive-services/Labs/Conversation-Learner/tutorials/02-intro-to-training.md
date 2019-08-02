---
title: Introdução ao treinamento de um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como treinar um modelo, incluindo ramificação e edição de treinamento anterior por meio de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705618"
---
# <a name="introduction-to-training"></a>Introdução ao treinamento

Este tutorial mostra as noções básicas de treinamento de um modelo, a ramificação de um novo treinamento com base em um treinamento anterior e a edição de uma resposta de bot para alterá-la.

## <a name="video"></a>Vídeo

[![Introdução à visualização do tutorial de treinamento](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ações Uma resposta de bot para entrada do usuário.
- Trem: A maneira como ensinamos um bot a responder à entrada do usuário.
- Ramificação A modificação de uma entrada do usuário em uma caixa de diálogo de treinamento salva com a finalidade de criar uma nova caixa de diálogo de treinamento que começa da mesma forma que a original, mas leva a conversa em uma direção diferente.

## <a name="steps"></a>Passos

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na interface do usuário da Web, clique em novo modelo
2. Para o "nome", digite "inspirar bot". Em seguida, clique em criar.

### <a name="create-an-action"></a>Criar uma ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. No campo "resposta do bot", digite "Olá! Gostaria de ser inspirado hoje? ".
    - Deixe todos os outros campos e caixas de seleção em sua configuração padrão.
3. Clique em Criar.

### <a name="first-training-and-creating-another-action-while-training"></a>Primeiro treinamento e criação de outra ação durante o treinamento

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Olá". 
    - Isso simula o lado do usuário da conversa.
3. Clique em "ações de Pontuação".
4. Selecione a resposta "Olá! Gostaria de ser inspirado hoje? ".
5. Como o usuário, responda com "Sim".
6. Clique em "ações de Pontuação".
7. Clique no botão "+ ação". 
    - Isso o levará para a familiar caixa de diálogo "criar uma ação".
8. Digite a resposta do bot como "você é incrível!"
9. Clique em Criar.
10. Observe que o bot responde imediatamente.
11. Clique no botão "salvar".

### <a name="branch-a-second-training-off-of-the-first-training"></a>Ramificar um segundo treinamento do primeiro treinamento
1. Clique na linha de grade que resume o primeiro treinamento. 
    - Isso permite que você exiba e edite o treinamento existente.
2. Clique na resposta do usuário "Sim". 
    - Isso vai expor os controles de edição.
3. Clique no ícone de ramificação. 
    - Isso abrirá um prompt para uma entrada de usuário diferente para uma nova conversa.
4. Digite "não", pressione Enter ou clique no botão "criar". 
    - Neste ponto, você terá uma nova instância de uma caixa de diálogo de treinamento, a original permanecerá inalterada.
5. Clique em "ações de Pontuação".
6. Clique na resposta incorreta do bot que acabou de aparecer.
7. Clique no botão "+ ação" 
    - para que possamos criar uma nova ação para o bot responder.
8. Digite a resposta do bot como "sem problema! Tenha um ótimo dia! "
9. Clique em Criar
10. Observe que o bot responde imediatamente.
11. Clique no botão "salvar".

### <a name="test-the-trainings"></a>Testar os treinamentos
1. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, em "nova caixa de diálogo de log".
2. Digite a mensagem "Olá". 
3. Observe que o bot responde automaticamente da maneira que o treinamos.
4. Digite a resposta do usuário, "Sim".
5. Observe a resposta do bot, que mostra que o primeiro treinamento está funcionando.
6. Clique no botão "tempo limite da sessão". Isso informa Conversation Learner queremos começar novamente, ignorando as ativações de conversação que acabaram de acontecer.
7. Digite a mensagem "Olá". 
8. Observe que o bot responde automaticamente da maneira que o treinamos.
9. Digite a resposta do usuário, "não".
10. Observe a resposta do bot, que mostra que o segundo treinamento está funcionando.
11. Clique no botão "teste concluído".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ações de espera e não espera](./03-wait-vs-nonwait-actions.md)
