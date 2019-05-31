---
title: Como utilizar os retornos de chamada de sessão com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar os retornos de chamada de sessão com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: eeed0718a1feb170dbbaa783ec0a840c7829c02e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390004"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Como utilizar os retornos de chamada de sessão com um modelo de aprendiz de conversação

Este tutorial apresenta sessões, como são processados e onSessionStart Aprendiz de conversação e onSessionEnd retornos de chamada.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial do sessão retornos de chamada](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot "tutorialSessionCallbacks" está em execução.

    npm run tutorial-session-callbacks

## <a name="details"></a>Detalhes
Este tutorial abrange o conceito de uma sessão, como as sessões são tratados por predefinição e como pode substituir esse comportamento.

Aprendiz de conversação uma sessão representa um, sem interrupções exchange interativa com o bot. Sessões pode ter vários folheio, mas por meio de programação ser terminada devido a inatividade se superior a trinta minutos ter passado.  Consulte a página de ajuda "Limites" para obter informações sobre como alterar esta duração de tempo limite de sessão predefinido.

Este período de tempo de inatividade fará com que o bot criar uma nova sessão e repor a rede neural recorrente para seu estado inicial. Por predefinição, todos os valores de entidade irão ser desmarcados. Este comportamento predefinido de limpar os valores da entidade pode ser alterado, como mostrado abaixo.

### <a name="load-the-demo-model"></a>Carregar o modelo de demonstração

Na IU da web, clique em "Importar tutorial" e selecione o modelo com o nome "Tutorial-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Código para os retornos de chamada

Código de exemplo para dois retornos de chamada esse modelo podem ser encontrados em: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Este método define a entidade de BotName.
- OnSessionEndCallback: pode especificar o que deseja preservar. Isto irá eliminar todas as entidades, exceto o nome de utilizador e o telefone do utilizador.

Cada chamada de retorno é opcional.

### <a name="actions"></a>Ações

Quatro ações são definidas no modelo. As ações existentes são apresentadas na vista de grelha para "Ações"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Criação de ação de sessão final um (para a invocação de retorno de chamada)

1. No painel esquerdo, clique em "Ações", em seguida, no botão "Nova ação".
2. Selecione "ENDSESSION" para o "tipo de entidade".
3. No campo de "Dados....", escreva "Concluído"
4. Clique no botão "Criar".

### <a name="edit-an-existing-action"></a>Editar uma ação existente

1. Selecione o "então, $UserName, está no $UserLocation" ação a partir da vista de grelha.
2. Desmarque a caixa de verificação "Aguardar resposta".
3. Clique no botão "Guardar".

### <a name="chaining-actions"></a>Encadeamento de ações

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá."
3. Clique no botão "Pontuação ações".
4. Selecione a resposta "Olá eu sou Botty. O que é o nome da sua?"
5. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Lars"
6. Selecione a resposta "Olá Lars. O que é o número de telefone?"
7. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "555-555-5555"
8. Clique no botão "Pontuação ações".
9. Selecione a resposta, "pode dizer Botty sua localização, Lars?"
10. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Seattle"
11. Clique no botão "Pontuação ações".
12. Selecione a resposta "Para que, Lars, que esteja em Seattle"
13. Selecione a resposta, "Concluída"
14. Clique no botão "Guardar".

### <a name="testing-the-model"></a>Testar o modelo

1. No painel esquerdo, clique em "Caixas de diálogo Log", em seguida, no botão "Registo caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Olá"
3. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Lars"
4. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "555-555-5555"
5. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Seattle"
    - Neste momento todos os valores de entidade, excluindo a localização devem foram preservados.
6. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "hello"
7. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Detroit"
8. Clique no botão "Tempo limite da sessão".
    - Clicar neste botão exercita a resposta ao bot para longos períodos de inatividade
9. Clique no botão "OK".
10. Clique no botão "Testar feito".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamadas de API](./14-api-calls.md)
