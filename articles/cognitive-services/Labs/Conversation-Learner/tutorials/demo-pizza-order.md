---
title: Modelo de Conversation Learner de demonstração, ordem de pizza-serviços cognitivas da Microsoft | Microsoft Docs
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
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703428"
---
# <a name="demo-pizza-order"></a>Demonstração: Ordem de pizza
Esta demonstração ilustra um bot de pedido de pizza, dando suporte a uma ordem de pizza única por:

- reconhecendo ingredientes de pizza do usuário declarações
- Gerenciando o inventário de ingredientes e respondendo adequadamente
- lembrar pedidos anteriores e acelerar a reordenação de uma pizza idêntica

## <a name="video"></a>Vídeo

[![Visualização de pizza de demonstração](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de ordem de pizza esteja em execução

    npm run demo-pizza

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista modelo da interface do usuário da Web, clique em ordem de pizza TutorialDemo. 

## <a name="entities"></a>Entidades

O modelo contém três entidades:

- "Ingredientes" acumula os ingredientes especificados pelo usuário, se disponíveis.
- "OutofStock" sinaliza ao usuário que o ingredientes selecionado está fora do estoque
- "LastToppings" contém os ingredientes históricos da ordem anterior

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações que solicita ao usuário sua seleção de ingredientes, ingredientes acumulados e muito mais.

Duas chamadas à API também são fornecidas:

- "FinalizeOrder" manipula o preenchimento de ordem
- "UseLastToppings" processa informações históricas de ingredientes

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento

Várias caixas de diálogo de treinamento são encontradas no modelo.

![](../media/tutorial_pizza_dialogs.PNG)

Vamos treinar o modelo um pouco mais criando outra caixa de diálogo de treinamento.

1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em seguida, no botão "nova caixa de diálogo treinar".
2. No painel chat, em que diz "digite sua mensagem...", digite "encomendar uma pizza com queijo"
    - A palavra "queijo" foi extraída pelo extrator de entidade.
3. Clique no botão "ações de Pontuação".
4. Selecione a resposta "você tem queijo em sua pizza".
5. Selecionar a resposta "você gostaria de mais alguma coisa?"
6. No painel chat, em que diz "digite sua mensagem...", digite "Add cogumelos e molhos"
7. Clique no botão "ações de Pontuação".
8. Selecione a resposta "você tem queijo, cogumelos e molhos em sua pizza".
9. Selecionar a resposta "você gostaria de mais alguma coisa?"
10. No painel de bate-papo, em que diz "digite sua mensagem...", digite "remover molhos e adicionar sausage"
11. Clique no botão "ações de Pontuação".
12. Selecione a resposta "você tem queijo, cogumelos e sausage em sua pizza".
13. Selecionar a resposta "você gostaria de mais alguma coisa?"
14. No painel de bate-papo, em que diz "digite sua mensagem...", digite "Add Yam"
15. Clique no botão "ações de Pontuação".
    - O valor "Yam" foi adicionado a "OutofStock" pelo código de retorno de chamada de detecção de entidade, pois o texto não corresponde a nenhum dos ingredientes com suporte.
16. Selecione a resposta "OutOfStock"
17. Selecionar a resposta "você gostaria de mais alguma coisa?"
18. No painel chat, em que diz "digite sua mensagem...", digite "não"
    - O "no" não está marcado como qualquer tipo de intenção. Em vez disso, selecionaremos a ação relevante com base no contexto atual.
19. Clique no botão "ações de Pontuação".
20. Selecione a resposta "FinalizeOrder"
    - A seleção dessa ação resultou nos ingredientes atuais do cliente sendo salvos na entidade "LastToppings" e na exclusão da entidade "ingredientes" pelo código de retorno de chamada FinalizeOrder.
21. No painel de bate-papo, em que diz "digite sua mensagem...", digite "solicitar outro"
22. Clique no botão "ações de Pontuação".
23. Selecione a resposta "você gostaria de queijo, cogumelos e sausage?"
    - Esta ação está disponível agora devido à entidade "LastToppings" que está sendo definida.
24. No painel chat, em que diz "digite sua mensagem...", digite "Sim"
25. Clique no botão "ações de Pontuação".
26. Selecione a resposta "UseLastToppings"
27. Selecione a resposta "você tem queijo, cogumelos e sausage em sua pizza".
28. Selecionar a resposta "você gostaria de mais alguma coisa?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implantando um bot de Conversation Learner](../deploy-to-bf.md)
