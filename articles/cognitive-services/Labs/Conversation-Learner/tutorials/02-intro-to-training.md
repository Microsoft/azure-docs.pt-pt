---
title: Introdução ao preparar um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como preparar um modelo, incluindo ramificações e edição anterior treinamento por meio de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 0bf5b71a4b0f51a586febbdaeaf8caba03c5b25a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387924"
---
# <a name="introduction-to-training"></a>Introdução à preparação

Este tutorial mostra as noções básicas de preparar um modelo, ramificação desativar um treinamento de novos com base num treinamento anterior e editar uma resposta de Bot para alterá-la.

## <a name="video"></a>Vídeo

[![Introdução à pré-visualização Tutorial de preparação](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ações: Uma resposta de Bot a intervenção do utilizador.
- Train: A forma como etapa, ensinamos um Bot para responder à entrada do usuário.
- Ramificação: A modificação de uma entrada do usuário dentro de uma caixa de diálogo Train guardadas com o objetivo de criar uma nova caixa de diálogo de formação que começa da mesma maneira que o original, mas leva à conversa em outra direção.

## <a name="steps"></a>Passos

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na IU da Web, clique em novo modelo
2. Para o "nome", escreva "Inspirar Bot". Em seguida, clique em criar.

### <a name="create-an-action"></a>Criar uma ação

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. Na "resposta do Bot", insira "Olá! Quer esteja inspirado hoje? ".
    - Deixe todos os outros campos e caixas de verificação com sua configuração padrão.
3. Clique em Criar.

### <a name="first-training-and-creating-another-action-while-training"></a>Treinamento em primeiro lugar e a criação de outra ação ao treinamento

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "hello". 
    - Isso simula o lado do usuário da conversa.
3. Clique em "Score ações".
4. Selecione a resposta, "Olá! Quer esteja inspirado hoje? ".
5. Como o utilizador responda com, "Sim".
6. Clique em "Score ações".
7. Clique em de "+ de ação" botão. 
    - Isto leva-o para a caixa de diálogo "Criar uma ação" familiar.
8. Escreva em resposta ao Bot, como, "Está fantástico!"
9. Clique em Criar.
10. Tenha em atenção que o Bot responda imediatamente.
11. Clique no botão "Guardar".

### <a name="branch-a-second-training-off-of-the-first-training"></a>Uma segunda treinamento fora o primeiro treinamento para filiais
1. Clique na linha de grelha que resume o treinamento primeiro. 
    - Isto permite-lhe ver e editar o treinamento existente.
2. Clique em "Sim" resposta do usuário. 
    - Isto irá expor os controles de edição.
3. Clique no ícone do ramo. 
    - Será apresentada uma linha de comandos para uma entrada do usuário diferentes para uma nova conversa.
4. Tipo de "não", introduza a visita ou clique no botão "Criar". 
    - Neste momento, terá uma nova instância de uma caixa de diálogo de comboio, original permanece inalterado.
5. Clique em "Score ações".
6. Clique na resposta incorreta o Bot e que apenas apareceu.
7. Clique em de "+ de ação" botão 
    - para que podemos criar uma nova ação para o Bot responder com.
8. Escreva em resposta ao Bot como "não há problema! Tenha um ótimo dia!"
9. Clique em Criar
10. Tenha em atenção que o Bot responda imediatamente.
11. Clique no botão "Guardar".

### <a name="test-the-trainings"></a>Testar os Treinamentos
1. No painel esquerdo, clique em "Caixas de diálogo Log", em seguida, "Registo de caixa de diálogo Novo".
2. Escreva a mensagem "Olá". 
3. Tenha em atenção que o Bot automaticamente responde de forma que com formação.
4. Escreva "Sim" a resposta de utilizador.
5. Tenha em atenção a resposta de Bot, mostra que o treinamento primeiro está a funcionar.
6. Clique no botão "Tempo limite da sessão". Isso informa ao aprendiz de conversação, queremos começar novamente, ignorando o folheio conversacionais que acabou de realizar local.
7. Escreva a mensagem "Olá". 
8. Tenha em atenção que o Bot automaticamente responde de forma que com formação.
9. Escreva a resposta do usuário, "não".
10. Tenha em atenção a resposta de Bot, mostra que o segundo treinamento está a funcionar.
11. Clique no botão "Testar feito".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aguarde e não espera ações](./03-wait-vs-nonwait-actions.md)
