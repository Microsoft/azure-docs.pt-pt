---
title: Modelo de aprendiz de conversação de demonstração, palavra-passe reposta - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como criar um modelo de aprendiz de conversação de demonstração.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 15aa3a8346087908cf77f1f68db916cc2c184448
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389881"
---
# <a name="demo-password-reset"></a>Demonstração: Reposição de palavras-passe
Este tutorial demonstra um bot de suporte técnico simples que pode ajudar na reposições de palavra-passe com tecnologia de aprendiz de conversação. Modelo do bot pode saber mais fluxos de caixa de diálogo não trivial e ative a multi sequências incluindo um classes de fora do domínio. A tarefa pode ser feita sem código ou entidades.

## <a name="video"></a>Vídeo

[![Pré-visualização de palavra-passe de demonstração](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de reposição de palavra-passe está em execução

    npm run demo-password

### <a name="open-the-demo"></a>Abra a demonstração

Na lista de modelo da IU da web, clique na reposição de palavra-passe de demonstração do Tutorial. 

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações concebido para ajudar utilizadores a resolver problemas comuns de palavra-passe.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento

O modelo também contém vários Train caixas de diálogo, incluindo algumas que demonstram fora de treinamento de classe de domínio. Por exemplo, os utilizadores que podem pedir, como obter referências de local. O bot de exemplo foi treinado em algumas para fins de demonstração, e simplesmente responde por que ele diz "não pode ajudar com isso." A lista de caixas de diálogo de Train existente é encontrada em "Caixas de diálogo Train" no painel esquerdo.

![](../media/tutorial_pw_reset_entities.PNG)

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "perdeu minha palavra-passe de i."
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "Isso é para a sua conta local ou uma conta Microsoft?"
5. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "local da conta."
6. Clique no botão "Pontuação ações".
7. Selecione a resposta, "qual versão do Windows tem?"
8. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "windows xp"
9. Clique no botão "Pontuação ações".
10. Clique em de "+ de ação" botão.
11. No "do Bot de resposta...", digite "solução: Como repor palavra-passe no Windows XP..."
12. Clique no botão "Criar".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Caixas de diálogo de treinamento para cenários de fora do domínio

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, o existente "brinquedo lojas" Train caixa de diálogo.
2. No painel de bate-papo, clique a expressão "brinquedo lojas".
3. No campo de "Adicionar alternativo input...", tipo "pesquisa na web" e clique em Inserir.
4. No campo de "Adicionar alternativo input...", reservas tipo"voo" e clique em Inserir.
5. Clique no botão "Guardar alterações".
6. Clique no botão "Editar guardar".
7. No painel esquerdo, clique em "Caixas de diálogo Log", em seguida, no botão "Registo caixa de diálogo Novo".
8. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "i não é possível encontrar a minha palavra-passe"
9. No painel de bate-papo, em que se lê "... sua mensagem de tipo", tipo de "Conta Microsoft"
10. No painel de bate-papo, em que se lê "... sua mensagem de tipo", tipo de "Obrigado"
11. Clique no botão "Testar feito".
12. Clique em diálogo de registo "i não é possível encontrar a minha palavra-passe" da vista de grelha.
13. No painel de bate-papo, clique em incorretamente processado "solução: Resposta de como repor uma palavra-passe da conta do Microsoft".
14. Clique em de "+ de ação" botão.
15. No "do Bot de resposta...", digite "é boas-vindas"
16. Clique no botão "Criar".
17. Clique no botão "Guardar como Train caixa de diálogo".

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Demonstração - order de pizza](./demo-pizza-order.md)
