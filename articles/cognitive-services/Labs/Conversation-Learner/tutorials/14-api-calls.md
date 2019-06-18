---
title: Como utilizar a API chama-se com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Aprenda a usar chamadas de API com um modelo de aprendiz de conversação.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 1f7c7c72703d7c3134dd2acdcc466fc0182fa38a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389947"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>Como adicionar chamadas de API para um modelo de aprendiz de conversação

Este tutorial mostra como adicionar chamadas de API para o seu modelo. Chamadas à API são funções que definem e escrever no seu Bot, e que pode chamar Aprendiz de conversação.

## <a name="video"></a>Vídeo

[![Pré-visualização de Tutorial de chamadas da API](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot "tutorialAPICalls.ts" está em execução.

    npm run tutorial-api-calls

## <a name="details"></a>Detalhes

- Chamadas de API podem ler e manipular entidades.
- Chamadas de API tem acesso ao objeto de Gestor de memória.
- Chamadas de API podem conter argumentos.

### <a name="open-the-demo"></a>Abra a demonstração

Na IU da web, clique em "Importar tutorial" e selecione o modelo com o nome "Tutorial-14-APICalls".

### <a name="entities"></a>Entidades

Definimos uma entidade no modelo chamado `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>Chamadas de API
O código para as chamadas à API é definido neste ficheiro: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- O `RandomGreeting` retorno de chamada retorna uma saudação aleatória definida no `greeting` matriz.
- O `Multiply` retorno de chamada será multiplica dois números transmitidos pela ação que chama e devolve um resultado que pode ser processado na interface do Usuário.
    - Tenha em atenção que o Gestor de memória é o primeiro argumento. 
    - Tenha em atenção que retornos de chamada de API podem demorar várias entradas, neste caso `num1string` e `num2string`.
- O `ClearEntities` retorno de chamada limpa a entidade de número, para que o utilizador pode introduzir outro número. 
    - Ilustra como chamadas de API podem manipular entidades.

### <a name="actions"></a>Ações
Criamos quatro ações. Três deles são ações de API "Não-Wait", com o quarto é uma ação de "Text", que pede ao usuário uma pergunta semelhante para o que temos visto nos outros tutoriais. Para ver como cada uma foi criado faça o seguinte:
1. No painel esquerdo, clique em "Ações", em seguida, clique em um dos quatro ações listadas na grade.
2. Repare nos valores de cada campo no formulário que é apresentado.
3. Tenha em atenção o `Refresh` botão junto ao campo da API.
    - Se estivéssemos parar o Bot e tornar a alterar para as APIs, enquanto estiver em execução, a página da interface do Usuário, em seguida, pode clicar no `Refresh` botão para recolher as alterações mais recentes.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, multiplicar e RandomGreeting
Todas as três estas ações são o tipo de API. Cada contam com as funções de retorno de chamada de API para executar algum trabalho e, possivelmente, retornar um valor a ser apresentados ao utilizador.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"O número que pretende multiplicar por 12"
Esta é a ação de "Text" e ele simplesmente solicita uma pergunta do utilizador. Embora esta ação não, na verdade, interagir com um dos retornos de chamada de API, ele avisa o usuário responda com um número que irão entrar em memória de uma entidade que, em seguida, pode ser utilizada pela ação "Multiplicar" que utilize um dos retornos de chamada de API.


### <a name="train-dialog"></a>Caixa de diálogo preparar

Vamos guiá-lo por meio de uma "treinamento caixa de diálogo".

1. No painel esquerdo, clique em `Train Dialogs`, em seguida, o `New Train Dialog` botão.
2. Escreva "hello".
3. Clique no botão `Score Actions`.
4. Selecione `RandomGreeting`. 
    - Esta ação irá executar a chamada da API de saudação aleatório.
    - Isto não irá esperar por uma resposta do utilizador.
5. Selecione `What number to do you want to multiply by 12?`
6. Escreva um número, qualquer número e apenas um número.
    - Tenha em atenção que o número foi identificado automaticamente como o `number` entidade.
7. Clique no botão `Score Actions`.
8. Selecione o `Multiply` ação.
    - Observe o resultado da multiplicação por 12.
    - Tenha em atenção que a memória ainda contém o valor introduzido para `number`
9. Selecione o `ClearEntities` ação.
    - Tenha em atenção que o valor para a entidade `number` foi limpo da memória.
10. Clique no botão `Save`.

Agora já viu como registar retornos de chamada de API, os padrões comuns e como definir argumentos e associar valores e entidades nos mesmos.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Cartões parte 1](./15-cards.md)
