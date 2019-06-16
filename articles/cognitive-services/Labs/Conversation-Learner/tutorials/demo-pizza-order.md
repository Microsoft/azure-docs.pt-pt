---
title: Modelo de aprendiz de conversação de demonstração, a ordem de pizza - serviços cognitivos da Microsoft | Documentos da Microsoft
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
ms.openlocfilehash: 010245480d8e1f59d5c1b92a9e717f73b5ba7f4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389148"
---
# <a name="demo-pizza-order"></a>Demonstração: Ordem de pizza
Essa demonstração ilustra uma bot, com suporte de pedido por de pizza único de pedido de pizza:

- RECONHECENDO sabores de pizza da expressão de utilizador
- Gerir o inventário de sabores e responder adequadamente
- pedidos anteriores de memorizar e acelerando a reordenação de uma pizza idêntico

## <a name="video"></a>Vídeo

[![Demo Pizza Preview](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de ordem de pizza está em execução

    npm run demo-pizza

### <a name="open-the-demo"></a>Abra a demonstração

Na lista de modelo da IU da web, clique no pedido de Pizza TutorialDemo. 

## <a name="entities"></a>Entidades

O modelo contém três entidades:

- "Sabores" acumula sabores de especificado do utilizador, se disponível.
- "OutofStock" sinaliza o utilizador que selecionado ingredientes está fora de estoque
- "LastToppings" contém os histórico sabores de sua ordem anterior

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações que solicita o usuário a seleção de sabor, sabores acumulados e muito mais.

Duas chamadas de API são fornecidas também:

- "FinalizeOrder" manipula o preenchimento de pedidos
- "UseLastToppings" processa informações de históricos sabores

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento

Caixas de diálogo de treinamento vários encontram-se no modelo.

![](../media/tutorial_pizza_dialogs.PNG)

Vamos treiná-lo um pouco mais com a criação de outra caixa de diálogo preparar.

1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, no botão "Train caixa de diálogo Novo".
2. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Ordenar uma pizza com queijo"
    - A palavra "queijo" foi extraída pelo extrator de entidades.
3. Clique no botão "Pontuação ações".
4. Selecione a resposta, "Tiver queijo no seu pizza".
5. Selecione a resposta, "Gostaria que qualquer outra coisa?"
6. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Adicionar cresce e peppers"
7. Clique no botão "Pontuação ações".
8. Selecione a resposta, "Tiver queijo, cresce e peppers no seu pizza."
9. Selecione a resposta, "Gostaria que qualquer outra coisa?"
10. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Remover peppers e adicionar sausage"
11. Clique no botão "Pontuação ações".
12. Selecione a resposta, "Tiver queijo, cresce e sausage no seu pizza."
13. Selecione a resposta, "Gostaria que qualquer outra coisa?"
14. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Adicionar yam"
15. Clique no botão "Pontuação ações".
    - O valor de "yam" foi adicionado ao "OutofStock", o código de retorno de chamada de detecção de entidade, como o texto não correspondeu a quaisquer ingredientes suportados.
16. Selecione a resposta, "OutOfStock"
17. Selecione a resposta, "Gostaria que qualquer outra coisa?"
18. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "não"
    - "Não" não está marcado como qualquer tipo de intenção. Em vez disso, selecionaremos a ação relevante com base no contexto atual.
19. Clique no botão "Pontuação ações".
20. Selecione a resposta, "FinalizeOrder"
    - Selecionar esta ação resultou em sabores de atual do cliente obter guardadas para a entidade "LastToppings" e a exclusão da entidade "Sabores" pelo código de retorno de chamada FinalizeOrder.
21. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "ordenar outro"
22. Clique no botão "Pontuação ações".
23. Selecione a resposta, "Gostaria que queijo, cresce e sausage?"
    - Esta ação está agora disponível devido a entidade "LastToppings" definida.
24. No painel de bate-papo, em que se lê "... sua mensagem de tipo", escreva "Sim"
25. Clique no botão "Pontuação ações".
26. Selecione a resposta, "UseLastToppings"
27. Selecione a resposta, "Tiver queijo, cresce e sausage no seu pizza."
28. Selecione a resposta, "Gostaria que qualquer outra coisa?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementar um bot de aprendiz de conversação](../deploy-to-bf.md)
