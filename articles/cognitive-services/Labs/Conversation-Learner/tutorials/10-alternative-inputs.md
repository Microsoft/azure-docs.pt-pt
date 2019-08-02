---
title: Como usar entradas alternativas com o Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entradas alternativas com Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704203"
---
# <a name="how-to-use-alternative-inputs"></a>Como usar entradas alternativas

Este tutorial mostra como usar o campo de entradas alternativas para o usuário declarações na interface de ensino.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de entradas alternativas](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
As entradas alternativas são um usuário declarações alternativo e semanticamente equivalente que o usuário pode ter dito em um ponto específico em uma caixa de diálogo de treinamento. Essas entradas alternativas permitem que você especifique variações de declarações de forma mais compacta sem precisar abordar cada variação em caixas de diálogo de treinamento separadas.

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "novo modelo".
2. No campo "nome", digite "AlternativeInputs" e pressione Enter.
3. Clique no botão "criar".

### <a name="entity-creation"></a>Criação de entidade

1. No painel esquerdo, clique em "entidades" e, em seguida, no botão "nova entidade".
2. Selecione "personalizado treinado" para o "tipo de entidade".
3. Digite "City" para o "nome da entidade".
4. Clique no botão "criar".

![](../media/T10_actions.png)

Agora, vamos criar três ações.

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "qual cidade?"
3. Na "entidade esperada na resposta do usuário..." , digite "City".
4. No campo "desqualificando os direitos", digite "City".
5. Clique no botão "criar".

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "o clima em $city é provavelmente ensolarado".
3. Clique no botão "criar".

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>Criar a terceira ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "Tente perguntar o clima".
3. No campo "desqualificando os direitos", digite "City".
4. Clique no botão "criar".

![](../media/T10_action_create_3.png)

Agora você tem três ações.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Qual é o clima?"
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "Qual é a cidade?"
5. No painel chat, em que diz "digite sua mensagem...", digite "Denver"
6. Clique no botão "ações de Pontuação".
7. Selecione a resposta, "o clima em Denver é provavelmente ensolarado".
8. Clique no botão "enviar alterações".

![](../media/T10_training_1.png)

Vamos treinar o modelo mais criando outra caixa de diálogo de treinamento.

### <a name="second-model-train-dialog"></a>Caixa de diálogo treinar segundo modelo

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "o que você pode fazer?"
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "Tente perguntar ao clima".
5. No painel de bate-papo, onde diz "digite sua mensagem...", digite "Qual é o clima em Seattle?"
6. Clique em "Seattle" e em "cidade" na lista de entidades.
7. Clique no botão "ações de Pontuação".
8. Selecione a resposta, "o clima em Seattle é provavelmente ensolarado".
9. Clique no botão "enviar alterações".

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Caixa de diálogo de treinamento do terceiro modelo usando entrada alternativa

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "ajuda"
3. Clique no botão "ações de Pontuação".
    - O modelo é incerto para a melhor opção, portanto, escolheria o mais alto percentil por padrão.
4. Clique no botão "abandonar ensinando" e, em seguida, no botão "confirmar".

![](../media/T10_training_3.png)

Vamos ajustar melhor o sistema usando entradas alternativas. Você pode adicionar uma entrada alternativa enquanto ensina ou posterior.

1. No painel esquerdo, clique em "treinar caixas de diálogo" e selecione "o que você pode fazer?" na lista de caixas de diálogo de treinamento.
1. Clique no "o que você pode fazer?" expressão no painel de chat.
1. Na "Adicionar entrada alternativa..." , digite "Help" e pressione Enter.
1. Clique no botão "salvar alterações".

![](../media/T10_training_4.png)

Vamos adicionar outra entrada alternativa para lidar com o Houston.

1. Clique no "Qual é o clima em Seattle?" expressão no painel de chat.
1. Na "Adicionar entrada alternativa..." , digite "Previsão para Houston" e pressione Enter.
   - A mensagem de erro realça que as entradas alternativas de fato devem ser semanticamente equivalentes e conter as mesmas entidades que o expressão original; Não apenas os mesmos valores de entidades. A presença das mesmas entidades é necessária.
1. Clique em "Houston" e selecione "cidade" na lista de entidades.
1. Na "Adicionar entrada alternativa..." , digite "Previsão para Seattle" e pressione Enter.
1. Clique em "Seattle" e selecione "cidade" na lista de entidades.
1. Clique no botão "salvar alterações".
1. Clique no botão "salvar edição".

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>Testando o modelo

1. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, na "caixa de diálogo novo log".
2. No painel de bate-papo, em que diz "digite sua mensagem...", digite "ajude-me"
3. No painel chat, em que diz "digite sua mensagem...", digite "Previsão para Denver"

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Caixas de diálogo de log](./11-log-dialogs.md)
