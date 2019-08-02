---
title: Como usar chamadas à API com um modelo de Conversation Learner-serviços cognitivas da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar chamadas à API com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703913"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Como adicionar chamadas de API a um modelo de Conversation Learner

Este tutorial mostra como adicionar chamadas de API ao seu modelo. As chamadas à API são funções que você define e grava em seu bot e quais Conversation Learner podem chamar.

## <a name="video"></a>Vídeo

[![Visualização do tutorial de chamadas de API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot "tutorialAPICalls. TS" esteja em execução.

    npm run tutorial-api-calls

## <a name="details"></a>Detalhes

- As chamadas à API podem ler e manipular entidades.
- As chamadas à API têm acesso ao objeto Gerenciador de memória.
- As chamadas à API podem receber argumentos.

### <a name="open-the-demo"></a>Abrir a demonstração

Na interface do usuário da Web, clique em "importar tutoriais" e selecione o modelo chamado "tutorial-14-APICalls".

### <a name="entities"></a>Entidades

Definimos uma entidade no modelo chamado `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Chamadas à API
O código para as chamadas à API é definido no seguinte arquivo: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- O `RandomGreeting` retorno de chamada retorna uma saudação aleatória `greeting` definida na matriz.
- O `Multiply` retorno de chamada Multiplica dois números passados pela ação que o chama e retorna um resultado que pode ser renderizado na interface do usuário.
    - Observe que o Gerenciador de memória é o primeiro argumento. 
    - Observe que os retornos de chamada de API podem receber várias entradas `num1string` , `num2string`neste caso e.
- O `ClearEntities` retorno de chamada limpa a entidade Number para que o usuário possa inserir outro número. 
    - Ilustra como as chamadas à API podem manipular entidades.

### <a name="actions"></a>Ações
Criamos quatro ações. Três delas são ações de API "não esperadas", com a quarta é uma ação de "texto" que pergunta ao usuário uma pergunta semelhante ao que vimos em outros tutoriais. Para ver como cada foi criado, faça o seguinte:
1. No painel esquerdo, clique em "ações" e, em seguida, clique em uma das quatro ações listadas na grade.
2. Observe os valores de cada campo no formulário que aparece.
3. Observe o `Refresh` botão ao lado do campo API.
    - Se tivéssemos de parar o bot e alterar as APIs enquanto a página da interface do usuário estiver ativa, você poderá clicar no `Refresh` botão para obter as alterações mais recentes.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, multiplicar e RandomGreeting
Todas as três ações são do tipo API. Cada um depende das funções de retorno de chamada da API para executar algum trabalho e, possivelmente, retornar um valor a ser apresentado ao usuário.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Qual número você deseja multiplicar por 12"
Essa é a ação de "texto" e simplesmente faz uma pergunta do usuário. Embora essa ação não interaja realmente com um dos retornos de chamada da API, ela solicita que o usuário responda com um número que entrará na memória de uma entidade que pode ser usada pela ação "multiplicar", que usa um dos retornos de chamada da API.


### <a name="train-dialog"></a>Caixa de diálogo treinar

Vamos percorrer uma "caixa de diálogo de treinamento".

1. No painel esquerdo, clique `Train Dialogs`no `New Train Dialog` botão.
2. Digite "Olá".
3. Clique no botão `Score Actions`.
4. Selecione `RandomGreeting`. 
    - Isso executará a chamada à API de saudação aleatória.
    - Isso não aguardará uma resposta do usuário.
5. Selecione `What number to do you want to multiply by 12?`
6. Digite um número, qualquer número e apenas um número.
    - Observe que seu número foi rotulado automaticamente como a `number` entidade.
7. Clique no botão `Score Actions`.
8. Selecione a `Multiply` ação.
    - Observe o resultado da multiplicação em 12.
    - Observe que a memória ainda contém o valor que você inseriu para`number`
9. Selecione a `ClearEntities` ação.
    - Observe que o valor da entidade `number` para foi limpo da memória.
10. Clique no botão `Save`.

Agora você viu como registrar retornos de chamada de API, seus padrões comuns e como definir argumentos e associar valores e entidades a eles.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Fichas parte 1](./15-cards.md)
