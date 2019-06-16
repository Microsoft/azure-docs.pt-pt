---
title: Como utilizar a propriedade de "Entidade esperado" das ações de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar a propriedade de "Entidade esperado" de um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 48a8abd401ff7191da4709c726042b566d140b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387795"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Como utilizar a propriedade de "Entidade esperado" das ações

Este tutorial demonstra a propriedade de "Entidade esperado" das ações.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do esperado de entidades](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot tutorial geral está em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Utilize a propriedade de "Entidade esperado" de uma ação para guardar a resposta do usuário para esta ação numa entidade.

Ao adicionar entidades para a propriedade de "Entidade esperado" de uma ação, o sistema irá:

1. Comece por tentar corresponder entidades com o machine learning com base em modelo de extração de entidades
2. Atribuir a expressão de utilizadores completa a $entity com base na heurística se encontram-se não existem entidades
3. Chamar `EntityDetectionCallback`e continuar a seleção de ação.

## <a name="steps"></a>Passos

### <a name="create-the-model"></a>Criar o modelo

1. Na IU da Web, clique em "Novo modelo."
2. No campo "Nome", escreva "ExpectedEntities" e prima enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação de entidade

1. No painel esquerdo, clique em "Entidades", em seguida, no botão "Nova entidade".
2. Selecione "Treinados personalizada" para o "tipo de entidade".
3. Escreva "nome" para o "nome da entidade".
4. Clique no botão "Criar".

> [!NOTE]
> O tipo de entidade "Custom treinado" significa que esta entidade pode ser treinada, ao contrário de outros tipos de entidades.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "Qual é o nome da sua?"
3. No campo de "Entidades esperado", escreva "name".
4. Clique no botão "Criar".

> [!NOTE]
> Entidades detetado e extraídos da resposta do usuário serão guardadas para a entidade de "name" se esta ação for escolhida. Se não existem entidades forem detetadas, a resposta inteira será guardada a esta entidade.

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "Hi $name!"
3. Clique no botão "Criar".

> [!NOTE]
> O "nome" entidade foi adicionado automaticamente como um "necessárias entidades" por referência na resposta.

Agora tem duas ações.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Preparar o modelo

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá."
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "O que é o nome da sua?"
    - O "Hi $name!" Não é possível selecionar a resposta como essa resposta requer o "nome" entidade ser definidos na memória do modelo agora.
5. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Frank."
    - "Francisco" está realçado como uma entidade com base na heurística, configuramos anteriormente para guardar a resposta como a entidade.
6. Clique no botão "Pontuação ações".
    - O "nome" entidade agora é definido como "Francisco" na memória do modelo, pelo que a ação de "Olá $name" é selecionável como uma ação.
7. Selecione a resposta, "Olá $name!"
8. Clique no botão "Guardar".

A adicionar trens entradas alternativo de ainda mais o modelo.

1. No campo de "Adicionar alternativo input...", escreva "Eu sou Jose."
    - O modelo não reconhece o nome como uma entidade para que ele seleciona o bloco de texto inteiro como valor da entidade
2. Clique a frase "Eu sou Jose", em seguida, clique no ícone de lixo.
3. Clique em "José", em seguida, clique em "name" na lista de entidades.
4. Clique em ações de pontuação.
5. Selecione a resposta "Olá Frank!"
6. Clique no botão "Guardar".

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entidades negatable](./06-negatable-entities.md)
