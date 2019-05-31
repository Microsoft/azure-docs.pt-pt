---
title: Como utilizar o retorno de chamada de detecção de entidade com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar o retorno de chamada de detecção de entidade com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: ed254ea3db1c8f0e680cba71185e9b4505d8f200
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387642"
---
# <a name="how-to-use-entity-detection-callback"></a>Como utilizar o retorno de chamada de detecção de entidade

Este tutorial mostra o retorno de chamada de detecção de entidade e ilustra um padrão comum para a resolução de entidades.

## <a name="video"></a>Vídeo

[![Entidade deteção retorno de chamada Tutorial pré-visualização](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o `tutorialEntityDetectionCallback` bot está em execução.

    npm run tutorial-entity-detection

## <a name="details"></a>Detalhes
Retornos de chamada de detecção de entidade ativa a modificação do comportamento de reconhecimento de entidades e manipulação de entidade por meio de código. Vamos demonstrar essa funcionalidade por meio de um padrão de design de retorno de chamada de detecção de entidade típica do acompanhamento. Por exemplo, resolver "a grande apple" para "new york".

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na IU da Web, clique em "Novo modelo."
2. No campo "Nome", escreva "EntityDetectionCallback" e prima enter.
3. Clique no botão "Criar".

Três entidades são necessárias neste exemplo, então, vamos criar as três.

### <a name="create-the-custom-trained-entity"></a>Criar a entidade com formação personalizada

1. No painel esquerdo, clique em "Entidades", em seguida, no botão "Nova entidade".
2. Selecione "Treinados personalizada" para o "tipo de entidade".
3. Tipo de "Cidade" para o "nome da entidade".
4. Clique no botão "Criar".

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "Nova entidade".
2. Selecione "Programática" para o "tipo de entidade".
3. Tipo de "CityUnknown" para o "nome da entidade".
4. Clique no botão "Criar".

### <a name="create-the-first-programmatic-entity"></a>Criar a primeira entidade programática

1. Clique no botão "Nova entidade".
2. Selecione "Programática" para o "tipo de entidade".
3. Tipo de "CityResolved" para o "nome da entidade".
4. Clique no botão "Criar".

Agora, crie três ações.

### <a name="action-creation"></a>Criação de ação

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "que cidade deseja?"
3. No campo de "Entidade esperado na resposta do utilizador...", escreva "Cidade".
4. No campo de "Entidades Disqualifying", escreva "Cidade".
5. Clique no botão "Criar".
6. Clique no botão "Nova ação".
7. No "do Bot de resposta...", digite "que cidade deseja?"
8. No campo de "Entidade esperado na resposta do utilizador...", escreva "Não sei este cidade."
9. Clique no botão "Criar".
10. Clique no botão "Nova ação".
11. No "do Bot de resposta...", digite "$CityResolved é muito bom."
12. Clique no botão "Criar".

Aqui está o código de retorno de chamada:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Preparar o modelo

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá"
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "que cidade deseja?"
5. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "apple grande"
6. Clique no botão "Pontuação ações".
    - Clicar no botão aciona o retorno de chamada de detecção de entidade
    - O código de retorno de chamada define o valor de entidade CityResolved corretamente para o "new york"
7. Selecione a resposta, "new york é muito bom."

Este padrão é típico de muitos cenários de bot. Expressões de utilizador e entidades extraídas são fornecidas para sua lógica de negócio e essa lógica transforma a expressão em formato canónico, que depois é salvo em entidades programáticas, para se subsequentes da caixa de diálogo.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Retornos de chamada de sessão](./13-session-callbacks.md)
