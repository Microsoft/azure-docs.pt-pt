---
title: Como criar um modelo de Conversation Learner "Olá, Mundo"-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Conversation Learner de "Olá, Mundo".
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705629"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Como criar um modelo "Olá, Mundo" com Conversation Learner

Este tutorial mostra como começar a usar o Conversation Learner, incluindo a criação de ações, ensinando o bot interativamente e fazendo correções de caixas de diálogo registradas que são provenientes de usuários finais.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de Olá, Mundo](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Requisitos
Se você ainda não fez isso, primeiro verifique se todas as etapas de instalação foram concluídas, incluindo a criação de um `.env` arquivo com sua chave de criação do Luis.  Consulte [início rápido](../quickstart.md) para obter detalhes.

Este tutorial requer que o bot do tutorial geral esteja em execução

    npm run tutorial-general

## <a name="steps"></a>Passos

Inicie o home page na interface do usuário da Web.

### <a name="create-the-model"></a>Criar o modelo
1. Clique no botão "novo modelo".
2. No campo "nome", digite "Olá, Mundo".
3. Clique no botão "criar".

Agora você deve ver a exibição do modelo que você criou.

### <a name="create-an-action"></a>Criar uma ação
1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
    - Uma ação pode ser uma mensagem de texto que Conversation Learner retorna ao usuário, a uma chamada à API ou a um cartão.
2. Na "resposta do bot..." tipo de campo "Olá".
    - Essa é a resposta que o bot retornará.
3. Clique no botão "criar".

Você criou a primeira ação que o bot pode executar, ou seja, retorna uma resposta de texto.

### <a name="train-dialogs"></a>Treinar caixas de diálogo
É aí que você treina o modelo sobre como responder ao usuário declarações.

#### <a name="first-training-dialog"></a>Primeira caixa de diálogo de treinamento

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. Digite "Hi", pressione Enter.
    - Como um exemplo do que o usuário pode dizer no início de uma conversa.
3. Clique no botão "ações de Pontuação".
4. Selecione "Olá".
    - Você acabou de concluir uma rodada completa nesta caixa de diálogo de exemplo. 
5. Digite a resposta do usuário, "adeus".
6. Clique no botão "ações de Pontuação".
7. Clique no botão "+ ação".
8. Digite "adeus!" em "resposta do bot..." e, em seguida, clique no botão "criar".
    - Observe que o bot respondeu com aquela ação que você acabou de criar.
9. Clique no botão "salvar". 
    - Isso terminará e salvará essa caixa de diálogo de treinamento.

Agora você tem uma caixa de diálogo de treinamento no modelo, juntamente com uma única entidade e duas ações.

#### <a name="second-training-dialog"></a>Segunda caixa de diálogo de treinamento
Vamos fazer mais um treinamento e ver como o bot responde.

1. Clique no botão "nova caixa de diálogo de treinamento".
2. Digite "Hi"
    - Isso é semelhante à primeira caixa de diálogo e esperamos obter uma boa pontuação do bot.
3. Clique no botão "ações de Pontuação".
    - A posição e a pontuação ainda podem não ser precisas o suficiente e podem exigir treinamento adicional.
4. Selecione "Olá".
5. Digite a resposta do usuário, "adeus".
6. Clique no botão "ações de Pontuação".
7. Selecione "adeus!"
8. Clique no botão "salvar".

### <a name="log-dialogs"></a>Caixas de diálogo de log
É aqui que você testa, exibe e corrige as conversas que você ou os usuários reais tiveram com seu bot.

#### <a name="test-the-model-as-an-end-user"></a>Testar o modelo como um usuário final
1. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, no botão "nova caixa de diálogo de log".
2. Digite "Olá,".
3. Aguarde um curto período, o bot deve responder automaticamente com "Olá"
4. Insira ' byebye '
5. Aguarde um pouco e, mais uma vez, o bot deve responder automaticamente com "Olá".
6. Clique no botão "teste concluído".

#### <a name="view-and-correct-a-user-conversation"></a>Exibir e corrigir uma conversa de usuário
Usando caixas de diálogo de log, você pode exibir a lista de conversas que os usuários mantinham com o bot. Você também pode editá-los para corrigir as respostas do bot e salvar as interações como caixas de diálogo de treinamento. Para fazer isso:
1. Na grade, clique no log da conversa.
2. Clique na última ação de bot, por exemplo, "Olá".
3. Selecione "adeus!" para corrigir o bot.
4. Clique no botão "salvar como caixa de diálogo de treinamento".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução ao treinamento](./02-intro-to-training.md)
