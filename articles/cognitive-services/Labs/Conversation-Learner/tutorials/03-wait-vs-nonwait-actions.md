---
title: Como utilizar wait e ações de não-espera com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar wait e ações de não-espera com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 57a58b008792130c82a86ff4a5801f4bb39aabad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387910"
---
# <a name="wait-and-non-wait-actions"></a>Aguarde e não espera ações

Este tutorial mostra a diferença entre as ações de espera e as ações de não-espera no Aprendiz de conversação.

## <a name="video"></a>Vídeo

[![Aguarde vs Tutorial de não-espera pré-visualização](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Aguarde a ação: Depois do sistema demora uma ação "wait", irá parar a realizar ações e aguarde pela intervenção do utilizador.
- Ação de não-espera: Depois do sistema executa uma ação de "não espera", ele escolherá imediatamente outra ação (sem aguardando entrada do usuário).

## <a name="steps"></a>Passos

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na IU da Web, clique em novo modelo
2. No campo "Nome", escreva "não-espera Aguardar", prima enter ou clique no botão "Criar".

### <a name="create-the-first-two-wait-actions"></a>Criar as primeiras duas ações de espera

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "que pizza deseja?".
    - Esta é uma ação de espera, portanto, deixe a caixa de "Aguardar resposta" selecionada.
3. Clique no botão "Criar".
4. Repetir essas etapas, criar outra ação com "Pizza a caminho!". como resposta ao Bot.

### <a name="train-using-those-wait-actions"></a>Treinar com essas ações de espera

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá". 
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "que pizza faria isso como?".
5. Como o utilizador responder com "Margherita".
6. Clique no botão "Pontuação ações".
7. Selecione a resposta, a "Pizza a caminho!".
8. Clique no botão "Guardar".

### <a name="create-a-non-wait-action-while-training"></a>Criar uma ação de não-espera ao treinamento
Embora poderia criar a ação de não-espera, como fez anteriormente, também pode criá-lo a partir dentro de uma sessão de treinamento.
1. Clique no botão "Train caixa de diálogo Novo".
2. Como o tipo de utilizador na, "Olá".
3. Clique no botão "Pontuação ações".
4. Clique em de "+ de ação" botão. 
    - Isto leva-o para a caixa de diálogo "Criar uma ação" familiar.
5. Escreva na resposta do Bot como, "Bem-vindo ao Bot de Pizza!"
6. Desmarque a caixa de verificação "Aguardar resposta".
7. Clique no botão "Criar".
    - Tenha em atenção que o responde de Bot imediatamente com, "Bem-vindo ao Bot de Pizza!" e que está novamente um pedido de resposta de Bot outro. Isto acontece porque a resposta do Bot foi a ação de não-espera que acabamos de criar.
9. Selecione a resposta, "que pizza faria isso como?".
10. Como o utilizador responder com "Margherita".
11. Clique no botão "Pontuação ações".
12. Selecione a resposta, a "Pizza a caminho!".
13. Clique no botão "Guardar".

> [!NOTE]
> A seqüência das respostas bot com respeito a espera e as ações de não-espera.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução às entidades](./04-introduction-to-entities.md)
