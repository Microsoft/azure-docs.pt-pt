---
title: Como usar as operações de ramificação e desfazer com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar as operações de ramificação e desfazer com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f4f3024451696dbd0244d9da39cba67b49447af1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703629"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Como usar as operações de ramificação e desfazer
Neste tutorial, vamos examinar as operações de desfazer e ramificação.


## <a name="details"></a>Detalhes
### <a name="undo"></a>Anular
Permite que o desenvolvedor "desfaça" a última opção de entrada ou ação do usuário. Nos bastidores, "desfazer" realmente cria uma nova caixa de diálogo e a repete na etapa anterior.  Isso significa que o retorno de chamada da entidade e chamadas à API na caixa de diálogo serão chamados novamente.

### <a name="branch"></a>Ramo
Cria uma nova caixa de diálogo de treinamento, que começa da mesma maneira que uma caixa de diálogo de treinamento existente – isso economiza o esforço de reinserção manual da caixa de diálogo. Nos bastidores, "Branch" cria uma nova caixa de diálogo e repete a caixa de diálogo de treinamento existente para a etapa selecionada.  Isso significa que o retorno de chamada da entidade e chamadas à API na caixa de diálogo serão chamados novamente.


## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot que usa pedidos de pizza esteja em execução:

    npm run demo-pizza

### <a name="open-or-import-the-demo"></a>Abrir ou importar a demonstração

Se você já trabalhou no tutorial de pedidos de pizza, basta abrir esse modelo na lista na interface do usuário da Web. Caso contrário, será necessário clicar em "importar tutoriais" e selecionar o modelo chamado "demo-PizzaOrder".

## <a name="undo"></a>Anular

Aqui está um exemplo de como ver o `Undo` recurso em ação:

### <a name="training-dialogs"></a>Caixas de diálogo de treinamento
1. No painel esquerdo, clique em "treinar caixas de diálogo" e, em `New Train Dialog` seguida, clique no botão.
2. Digite "ordenar uma pizza".
3. Clique no botão `Score Actions`.
4. Clique para selecionar "o que você deseja em sua pizza?"
5. Digite "qualquer coisa".
6. Clique no botão `Undo`.
    - A última entrada é removida, deixando a última resposta de bot de "o que você gostaria em sua pizza?"

## <a name="branch"></a>Ramo

Para esta demonstração, vamos abrir uma caixa de diálogo de treinamento existente e criar uma nova caixa de diálogo de treinamento por meio de ramificação.

1. No painel esquerdo, clique em "treinar caixas de diálogo".
2. Observe a grade, você deve ver apenas um treinamento que começa com "novo pedido".
3. Na grade, clique em "nova ordem" para abrir a caixa de diálogo de treinamento existente.
4. Clique no último "não" na caixa de diálogo.
5. Clique no ícone "Branch", ele está circulado em vermelho nesta imagem:
    - ![](../media/tutorial15_branch.PNG)
    - A caixa de diálogo de treinamento inteira antes de "não" é copiada em uma nova caixa de diálogo de treinamento.
    - Isso evita que você reinsira o que antes é para explorar uma nova conversa "Branch" deste ponto.
6. Digite "Sim", pressione Enter.
7. Clique no botão `Score Actions`.
    - Neste ponto, o bot escolhe automaticamente uma resposta, mas não gostamos da resposta, portanto, vamos alterá-la.
8. Clique na última resposta do bot.
    - Isso nos permitirá selecionar uma resposta diferente.
9. Selecione "UseLastToppings".
10. Clique no botão `Score Actions`.
    - Novamente, o bot escolhe automaticamente uma resposta. Deve-se dizer, "você tem sausage, queijo e cogumelos em sua pizza". 
    - Desta vez, gostaríamos da resposta para que possamos mantê-la.
11. Clique no botão `Score Actions`.
    - Novamente, o bot escolhe automaticamente uma resposta, ele deve dizer, "você gostaria de mais alguma coisa?"
12. Digite "no".
13. Clique no botão `Save Branch`.
14. Observe que a grade agora tem dois treinamentos que começam com "novo pedido".
    - Um deles é aquele que você costumava ramificar.
    - E a outra é a versão ramificada que você acabou de salvar.
    - Clique em cada um deles para verificar essas expectativas.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Controle de versão e marcação](./18-version-tag.md)
