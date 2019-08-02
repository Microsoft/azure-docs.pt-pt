---
title: Como registrar caixas de diálogo em um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como registrar caixas de diálogo em um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703977"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Como registrar caixas de diálogo em um modelo de Conversation Learner

Este tutorial demonstra como as caixas de diálogo de log são empregadas para treinar melhor Conversation Learner modelos de interações registradas com usuários reais.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de caixas de diálogo de log](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

e o modelo meteorológico criado nos tutoriais anteriores.

## <a name="details"></a>Detalhes
Caixas de diálogo de log são logs gravados da interação do bot com usuários finais. Ao aproveitar essas caixas de diálogo de log, você pode corrigir rótulos de entidade e seleções de ação para melhorar o desempenho do modelo e o desempenho geral do sistema.

## <a name="steps"></a>Passos

Na interface do usuário da Web, clique em "importar tutoriais" e selecione o modelo chamado "Tutorial-11-LogDialogs".

Esse modelo contém uma entidade chamada "City" e ações projetadas para responder a consultas sobre o clima nessa cidade. Duas caixas de diálogo de treinamento foram usadas para treinar o modelo para que as expectativas de desempenho sejam um pouco baixas. O modelo melhoraria com treinamento e exposição adicionais para interações reais do usuário.

### <a name="create-a-new-conversation"></a>Criar uma nova conversa

1. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, no botão "nova caixa de diálogo de log".
2. No painel de chat, em que diz "digite sua mensagem...", digite "a previsão do clima de Austin"
3. Clique no botão "teste concluído".
4. Clique na caixa de diálogo de log "previsão do clima de Austin" na lista.
5. Clique no expressão "previsão do clima de Austin" no painel de bate-papo.
6. Clique em "Austin" e, em seguida, clique em "cidade" na lista de entidades.
7. Clique no botão "enviar alterações".
    - Essa alteração no valor da entidade causa alterações de downstream na conversa, pois temos novos valores de entidade na memória. As ações posteriores provavelmente se tornarão inválidas, especialmente aquelas que envolvem a entidade "City".
8. Clique no "qual cidade?" expressão no painel de chat.
9. Selecione a resposta "o clima em Austin é provavelmente ensolarado".
10. Clique no botão "salvar como caixa de diálogo de treinamento".
    - O treinamento é iniciado imediatamente

![](../media/T11_logdialog.png)

Uma última observação. Dependendo das necessidades dos negócios, o recurso de registro em log de conversa pode ser desativado acessando configurações e desmarcando "conversas de log".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Retorno de chamada de detecção de entidade](./12-entity-detection-callback.md)
