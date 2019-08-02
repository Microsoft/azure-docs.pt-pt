---
title: Como usar os retornos de chamada de sessão com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar os retornos de chamada de sessão com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703952"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Como usar retornos de chamada de sessão com um modelo de Conversation Learner

Este tutorial apresenta sessões, como elas são tratadas e Conversation Learner retornos de chamada onSessionStart e onSessionEnd.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de retornos de chamada da sessão](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot "tutorialSessionCallbacks" esteja em execução.

    npm run tutorial-session-callbacks

## <a name="details"></a>Detalhes
Este tutorial aborda o conceito de uma sessão, como as sessões são tratadas por padrão e como você pode substituir esse comportamento.

Em Conversation Learner uma sessão representa uma troca interativa ininterrupta com o bot. As sessões podem ter várias ativações, mas são encerradas programaticamente devido a inatividade se mais de trinta minutos tiver se passado.  Consulte a página de ajuda sobre "limites" para obter informações sobre como alterar essa duração do tempo limite da sessão padrão.

Esse longo período de inatividade fará com que o bot crie uma nova sessão e redefina a rede neural recorrente para seu estado inicial. Por padrão, todos os valores de entidade serão apagados. Esse comportamento padrão de limpar valores de entidade pode ser alterado conforme mostrado abaixo.

### <a name="load-the-demo-model"></a>Carregar o modelo de demonstração

Na interface do usuário da Web, clique em "importar tutoriais" e selecione o modelo chamado "Tutorial-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Código para os retornos de chamada

O código de exemplo para os dois retornos de chamada desse modelo pode `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`ser encontrado em:.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: esse método define a entidade BotName.
- OnSessionEndCallback: você pode especificar o que deseja preservar. Isso limpará todas as entidades, exceto o nome de usuário e o telefone do usuário.

Cada retorno de chamada é opcional.

### <a name="actions"></a>Ações

Quatro ações são definidas no modelo. As ações existentes são exibidas no modo de exibição de grade para "ações"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Criando uma ação de encerrar sessão (para invocação de retorno de chamada)

1. No painel esquerdo, clique em "ações" e, em seguida, no botão "nova ação".
2. Selecione "EndSession" para o "tipo de entidade".
3. Na "Data..." campo, digite "Done"
4. Clique no botão "criar".

### <a name="edit-an-existing-action"></a>Editar uma ação existente

1. Selecione a ação "Portanto, $UserName, você está $UserLocation" no modo de exibição de grade.
2. Desmarque a caixa de seleção "aguardar resposta".
3. Clique no botão "salvar".

### <a name="chaining-actions"></a>Ações de encadeamento

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Olá".
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "Olá, sou botty. Qual é seu nome? "
5. No painel chat, em que diz "digite sua mensagem...", digite "Lars"
6. Selecione a resposta "Hi Lars. Qual é seu número de telefone? "
7. No painel de bate-papo, em que diz "digite sua mensagem...", digite "555-555-5555"
8. Clique no botão "ações de Pontuação".
9. Selecione a resposta "você pode dizer botty seu local, Lars?"
10. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Seattle"
11. Clique no botão "ações de Pontuação".
12. Selecione a resposta, "Portanto, Lars, você está em Seattle"
13. Selecione a resposta "concluído"
14. Clique no botão "salvar".

### <a name="testing-the-model"></a>Testando o modelo

1. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, no botão "nova caixa de diálogo de log".
2. No painel de chat, onde diz "digite sua mensagem...", digite "Hi"
3. No painel chat, em que diz "digite sua mensagem...", digite "Lars"
4. No painel de bate-papo, em que diz "digite sua mensagem...", digite "555-555-5555"
5. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Seattle"
    - Neste ponto, todos os valores de entidade excluindo o local devem ter sido preservados.
6. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Olá"
7. No painel chat, em que diz "digite sua mensagem...", digite "Detroit"
8. Clique no botão "tempo limite da sessão".
    - Clicar nesse botão exercita a resposta do bot a longos períodos de inatividade
9. Clique no botão "OK".
10. Clique no botão "teste concluído".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamadas à API](./14-api-calls.md)
