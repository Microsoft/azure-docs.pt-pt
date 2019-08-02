---
title: Demonstração do modelo de Conversation Learner, redefinição de senha-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Conversation Learner de demonstração.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703402"
---
# <a name="demo-password-reset"></a>Demonstração: Reposição de palavra-passe
Este tutorial apresenta um bot de suporte técnico simples que pode ajudar com redefinições de senha da plataforma Conversation Learner. O modelo do bot pode aprender fluxos de caixa de diálogo não triviais e sequências com várias transformações, incluindo classes fora do domínio. A tarefa pode ser realizada sem código ou entidades.

## <a name="video"></a>Vídeo

[![Visualização de senha de demonstração](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de redefinição de senha esteja em execução

    npm run demo-password

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista modelo da interface do usuário da Web, clique em tutorial demonstração de senha redefinição. 

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações criadas para ajudar os usuários a resolver problemas comuns de senha.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento

O modelo também contém várias caixas de diálogo de treinamento, incluindo algumas que demonstram o treinamento de classe de domínio. Por exemplo, os usuários que podem solicitar instruções de direção. O bot de exemplo foi treinado em alguns para fins de demonstração e simplesmente responde informando "não pode ajudar com isso". A lista de caixas de diálogo de treinamento existentes é encontrada em "treinar caixas de diálogo" no painel esquerdo.

![](../media/tutorial_pw_reset_entities.PNG)

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "Eu perdi minha senha".
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "é que, para sua conta local ou conta Microsoft?"
5. No painel chat, em que diz "digite sua mensagem...", digite "conta local".
6. Clique no botão "ações de Pontuação".
7. Selecione a resposta "qual versão do Windows você tem?"
8. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Windows XP"
9. Clique no botão "ações de Pontuação".
10. Clique no botão "+ ação".
11. Na "resposta do bot..." , digite "SOLUTION: Como redefinir a senha no Windows XP...
12. Clique no botão "criar".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Caixas de diálogo de treinamento para cenários de fora do domínio

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, a caixa de diálogo de treinamento "brinquedos lojas" existente.
2. No painel chat, clique no expressão "lojas de brinquedos".
3. Na "Adicionar entrada alternativa..." , digite "pesquisa na Web" e pressione Enter.
4. Na "Adicionar entrada alternativa..." , digite "reservas de voo" e pressione Enter.
5. Clique no botão "salvar alterações".
6. Clique no botão "salvar edição".
7. No painel esquerdo, clique em "caixas de diálogo de log" e, em seguida, no botão "nova caixa de diálogo de log".
8. No painel chat, em que diz "digite sua mensagem...", digite "não consigo encontrar minha senha"
9. No painel chat, em que diz "digite sua mensagem...", digite "conta Microsoft"
10. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Obrigado"
11. Clique no botão "teste concluído".
12. Clique na caixa de diálogo de log "não consigo encontrar minha senha" no modo de exibição de grade.
13. No painel chat, clique na "solução" incorretamente processada: Como redefinir uma resposta de conta da Microsoft.
14. Clique no botão "+ ação".
15. Na "resposta do bot..." , digite "você é bem-vindo"
16. Clique no botão "criar".
17. Clique no botão "salvar como caixa de diálogo de treinamento".

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Demonstração-ordem de pizza](./demo-pizza-order.md)
