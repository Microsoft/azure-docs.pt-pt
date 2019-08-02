---
title: Como usar o retorno de chamada de detecção de entidade com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o retorno de chamada de detecção de entidade com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703996"
---
# <a name="how-to-use-entity-detection-callback"></a>Como usar o retorno de chamada de detecção de entidade

Este tutorial mostra o retorno de chamada de detecção de entidade e ilustra um padrão comum para resolver entidades.

## <a name="video"></a>Vídeo

[![Demonstração do tutorial de retorno de chamada da entidade](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o `tutorialEntityDetectionCallback` bot esteja em execução.

    npm run tutorial-entity-detection

## <a name="details"></a>Detalhes
Os retornos de chamada de detecção de entidade permitem a modificação do comportamento de reconhecimento de entidade e a manipulação de entidade por meio de código Demonstraremos essa funcionalidade examinando um padrão de design de chamada de detecção de entidade típico. Por exemplo, resolver "a grande Apple" para "Nova York".

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "novo modelo".
2. No campo "nome", digite "EntityDetectionCallback" e pressione Enter.
3. Clique no botão "criar".

Três entidades são necessárias neste exemplo, portanto, vamos criar as três.

### <a name="create-the-custom-trained-entity"></a>Criar a entidade treinada personalizada

1. No painel esquerdo, clique em "entidades" e, em seguida, no botão "nova entidade".
2. Selecione "personalizado treinado" para o "tipo de entidade".
3. Digite "City" para o "nome da entidade".
4. Clique no botão "criar".

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "nova entidade".
2. Selecione "programático" para o "tipo de entidade".
3. Digite "CityUnknown" para o "nome da entidade".
4. Clique no botão "criar".

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "nova entidade".
2. Selecione "programático" para o "tipo de entidade".
3. Digite "CityResolved" para o "nome da entidade".
4. Clique no botão "criar".

Agora, crie três ações.

### <a name="action-creation"></a>Criação de ação

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Na "resposta do bot..." , digite "qual cidade você deseja?"
3. Na "entidade esperada na resposta do usuário..." , digite "City".
4. No campo "desqualificando entidades", digite "City".
5. Clique no botão "criar".
6. Clique no botão "nova ação".
7. Na "resposta do bot..." , digite "qual cidade você deseja?"
8. Na "entidade esperada na resposta do usuário..." , digite "Eu não sei esta cidade".
9. Clique no botão "criar".
10. Clique no botão "nova ação".
11. Na "resposta do bot..." , digite "$CityResolved é muito bom".
12. Clique no botão "criar".

Este é o código de retorno de chamada:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel de chat, onde diz "digite sua mensagem...", digite "Hi"
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "qual cidade você deseja?"
5. No painel de bate-papo, onde diz "digite sua mensagem...", digite "Big Apple"
6. Clique no botão "ações de Pontuação".
    - Clicar no botão dispara o retorno de chamada de detecção de entidade
    - O código de retorno de chamada define o valor da entidade CityResolved corretamente como "Nova York"
7. Selecione a resposta "Nova York é muito boa".

Esse padrão é típico de muitos cenários de bot. As entidades declarações e extraídas do usuário são fornecidas à sua lógica de negócios, e essa lógica transforma o expressão em formato canônico, que é então salvo em entidades programáticas, para a ativação subsequente da caixa de diálogo.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Retornos de chamada de sessão](./13-session-callbacks.md)
