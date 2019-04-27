---
title: Como utilizar a ramificação e anular operações com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar a ramificação e anular operações com um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cca7b42c83be3ca428509ea48c387a1c35ddb35a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640197"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Como utilizar operações de desfazer e de ramificação
Neste tutorial, iremos abordará desfazer e operações de ramificação.


## <a name="details"></a>Detalhes
### <a name="undo"></a>Anular
Permite ao desenvolvedor para a última escolha de entrada ou a ação de utilizador de "desfazer". Em segundo plano, "desfazer", na verdade, cria uma nova caixa de diálogo e replays-lo até ao passo anterior.  Isso significa que a API de retorno de chamada de detecção de entidade e chama-se na caixa de diálogo será chamada novamente.

### <a name="branch"></a>Branch
Cria uma nova caixa de diálogo train, que começa da mesma forma como um existente treinar a caixa de diálogo – Isso economiza o esforço de caixa de diálogo Inserir manualmente novamente ativa. Em segundo plano, "ramo" cria uma nova caixa de diálogo e replays a caixa de diálogo train existentes até ao passo selecionado.  Isso significa que a API de retorno de chamada de detecção de entidade e chama-se na caixa de diálogo será chamada novamente.


## <a name="requirements"></a>Requisitos
Este tutorial requer que o Bot que recebe pedidos de pizzas está em execução:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Abrir ou importar a demonstração

Se já trabalhou o tutorial de ordenação de pizza, em seguida, basta abra esse modelo da lista na IU da web. Caso contrário, terá de clicar em "Importar tutorial" e selecione o modelo com o nome "Demonstração PizzaOrder".

## <a name="undo"></a>Anular

Eis um exemplo de como ver o `Undo` recurso em ação:

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento
1. No painel esquerdo, clique em "Caixas de diálogo Train", em seguida, clique no `New Train Dialog` botão.
2. Escreva "Ordenar uma pizza".
3. Clique no botão `Score Actions`.
4. Clique para selecionar "O que deseja no seu pizza?"
5. Escreva "nada".
6. Clique no botão `Undo`.
    - A última entrada for removida, deixando a última resposta de Bot de "O que deseja no seu pizza?"

## <a name="branch"></a>Branch

Para esta demonstração, vamos abrir uma caixa de diálogo Train existente e criar uma nova caixa de diálogo de formação do mesmo pela ramificação.

1. No painel esquerdo, clique em "Caixas de diálogo Train".
2. Tenha em atenção a grade, deverá ver apenas um treinamento que começa com "new order".
3. Na grade, clique em "new order" para abrir a caixa de diálogo Train existente.
4. Clique na última "não" na caixa de diálogo.
5. Clique no ícone de "Ramo", encontra-se circulado em vermelho nesta imagem:
    - ![](../media/tutorial15_branch.PNG)
    - A caixa de diálogo Train inteiro antes do "não" é copiada para uma nova caixa de diálogo de comboio.
    - Desta forma, poupa re-inserção anterior muda para explorar uma nova conversa "ramo" a partir deste ponto.
6. Escreva "Sim", pressiono introduza.
7. Clique no botão `Score Actions`.
    - Neste momento o Bot escolhe automaticamente uma resposta, mas não gostamos a resposta, de modo que vamos alterá-la.
8. Clique na última resposta de Bot.
    - Diga-nisto irá selecionar uma resposta diferente.
9. Selecione "UseLastToppings".
10. Clique no botão `Score Actions`.
    - Novamente o Bot escolhe automaticamente uma resposta. Ele deve dizer, "Tiver sausage, queijo e cresce no seu pizza.". 
    - Desta vez, como a resposta para que isso é.
11. Clique no botão `Score Actions`.
    - Novamente o Bot escolhe automaticamente uma resposta, deve dizer, "Gostaria que qualquer outra coisa?"
12. Escreva "não".
13. Clique no botão `Save Branch`.
14. Tenha em atenção que a grade tem agora duas treinamentos que começa com "new order".
    - Uma delas é a que utilizou para se ramifique de.
    - E o outro é a versão branched que guardou.
    - Clique em cada um deles para verificar essas expectativas.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Controlo de versões e etiquetagem](./18-version-tag.md)
