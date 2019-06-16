---
title: Como criar um modelo de aprendiz de conversação "Hello World" - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como criar um modelo de aprendiz de conversação "Hello World".
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: fe5d21fadef8f4452ba36259dbf89cefc78230de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388048"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Como criar um modelo de "Hello World" com Aprendiz de conversação

Este tutorial mostra como começar com o aprendiz de conversação, incluindo a criação de ações, ensinando o Bot interativamente e fazer correções de caixas de diálogo com sessão iniciada que vêm de utilizadores finais.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do Hello World](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Requisitos
Se ainda não o fez, certifique-se primeiro todos os passos de configuração foram concluídos, incluindo a criação de um `.env` ficheiro com a sua chave de criação de LUIS.  Ver [guia de introdução](../quickstart.md) para obter detalhes.

Este tutorial requer que o tutorial geral Bot está em execução

    npm run tutorial-general

## <a name="steps"></a>Passos

Comece na home page na IU da Web.

### <a name="create-the-model"></a>Criar o modelo
1. Clique no botão "Novo modelo".
2. No campo "Nome", introduza "Hello World".
3. Clique no botão "Criar".

Deverá ver a vista do modelo que criou.

### <a name="create-an-action"></a>Criar uma ação
1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
    - Uma ação pode ser uma mensagem de texto que Aprendiz de conversação retorna para o utilizador, uma chamada à API ou um cartão.
2. No "Do Bot de resposta..." tipo "Olá" de campo.
    - Esta é a resposta que retornará o Bot.
3. Clique no botão "Criar".

Criar a primeira ação que pode realizar o Bot, ou seja, devolver uma resposta de texto.

### <a name="train-dialogs"></a>Preparar as caixas de diálogo
Isso é onde treiná-lo sobre como responder a expressões de utilizador.

#### <a name="first-training-dialog"></a>Primeira caixa de diálogo de treinamento

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. Tipo "Olá", prima enter.
    - Como um exemplo de que o utilizador poderá dizer no início de uma conversa.
3. Clique no botão "Pontuação ações".
4. Selecione "Olá".
    - Acabou de concluir um folheio completo nesta caixa de diálogo de exemplo. 
5. Escreva a resposta do usuário, "Goodbye".
6. Clique no botão "Pontuação ações".
7. Clique em de "+ de ação" botão.
8. Tipo "Goodbye!" em "... na resposta do Bot" campo, em seguida, clique no botão "Criar".
    - Tenha em atenção que o Bot respondeu com que ação que acabou de criar.
9. Clique no botão "Guardar". 
    - Esta ação irá terminar e guardar esta caixa de diálogo de treinamento.

Agora tem uma caixa de diálogo de treinamento no modelo, juntamente com uma única entidade e duas ações.

#### <a name="second-training-dialog"></a>Segunda caixa de diálogo de treinamento
Vamos fazer um treinamento mais e veja como o Bot responde.

1. Clique no botão "Train caixa de diálogo Novo".
2. Escreva, "Olá"
    - Isto é semelhante à primeira caixa de diálogo e esperamos obter uma boa pontuação de Bot.
3. Clique no botão "Pontuação ações".
    - A posição e a pontuação poderá ainda não ser suficientemente precisa e pode exigir o treinamento adicional.
4. Selecione "Olá".
5. Escreva a resposta do usuário, "obrigada".
6. Clique no botão "Pontuação ações".
7. Selecione "Goodbye"!
8. Clique no botão "Guardar".

### <a name="log-dialogs"></a>Caixas de diálogo de registo
É onde testa, ver e corrigir conversas que tenham tido a si ou pelos utilizadores reais com o seu Bot.

#### <a name="test-the-model-as-an-end-user"></a>Testar o modelo como um utilizador final
1. No painel esquerdo, clique em "Caixas de diálogo Log", em seguida, no botão "Registo caixa de diálogo Novo".
2. Escreva ""Olá lá".
3. Aguarde algum tempo, o Bot deve responder automaticamente com "Hello"
4. Introduza 'byebye'
5. Aguarde algum tempo, novamente o Bot deve responder automaticamente com "Olá".
6. Clique no botão "Testar feito".

#### <a name="view-and-correct-a-user-conversation"></a>Ver e corrigir uma conversa de utilizador
Utilizar a caixas de diálogo de registo, pode ver a lista de conversas utilizadores mantidos com o seu Bot. Também pode editá-los para corrigir as respostas do Bot e guardar as interações como caixas de diálogo de treinamento. Para fazê-lo:
1. Na grade, clique no registo da conversa.
2. Clique na última ação de Bot, por exemplo "Olá".
3. Selecione "Goodbye"! Para corrigir o Bot.
4. Clique no botão "Guardar como Train caixa de diálogo".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Introdução à preparação](./02-intro-to-training.md)
