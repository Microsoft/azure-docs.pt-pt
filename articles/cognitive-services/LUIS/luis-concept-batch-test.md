---
title: Teste em lote-LUIS
titleSuffix: Azure Cognitive Services
description: Use testes em lotes para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar a compreensão da linguagem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487739"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Teste de lote com 1000 declarações no portal do LUIS

O teste em lotes valida sua versão treinada ativa para medir sua precisão de previsão. Um teste em lote ajuda a exibir a precisão de cada tentativa e entidade em sua versão ativa, exibindo os resultados com um gráfico. Examine os resultados do teste em lotes para tomar as medidas apropriadas para melhorar a precisão, como adicionar mais exemplos de declarações a uma intenção se seu aplicativo freqüentemente falhar ao identificar a intenção correta ou rotular entidades dentro do expressão.

## <a name="group-data-for-batch-test"></a>Agrupar dados para teste em lote

É importante que o declarações usado para testes em lotes seja novo no LUIS. Se você tiver um conjunto de dados de declarações, divida o declarações em três conjuntos: exemplo declarações adicionado a uma intenção, declarações recebido do ponto de extremidade publicado e declarações usado para testar o lote LUIS depois de ser treinado. 

## <a name="a-data-set-of-utterances"></a>Um conjunto de dados de declarações

Envie um arquivo em lotes de declarações, conhecido como um *conjunto de dados*, para teste em lotes. O conjunto de dados é um arquivo formatado em JSON que contém um máximo de 1.000 rotulado como declarações **não duplicado** . Você pode testar até 10 conjuntos de dados em um aplicativo. Se você precisar testar mais, exclua um conjunto de dados e, em seguida, adicione um novo.

|**Regras**|
|--|
|\* Sem declarações duplicados|
|1000 declarações ou menos|

\* Duplicatas são consideradas correspondências exatas de cadeia de caracteres, não correspondências que são indexadas primeiro. 

## <a name="entities-allowed-in-batch-tests"></a>Entidades permitidas em testes de lote

Todas as entidades personalizadas no modelo aparecem no filtro de entidades de teste em lote, mesmo que não haja entidades correspondentes nos dados do arquivo em lotes.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formato de arquivo em lotes

O arquivo em lotes consiste em declarações. Cada expressão deve ter uma previsão de intenção esperada junto com as [entidades aprendidas por computador](luis-concept-entity-types.md#types-of-entities) que você espera que sejam detectadas. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe de lote para tentativas com entidades

Use o modelo a seguir para iniciar o arquivo em lotes:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

O arquivo em lotes usa as propriedades **startPos** e **endPos** para anotar o início e o fim de uma entidade. Os valores são baseados em zero e não devem começar ou terminar com um espaço. Isso é diferente dos logs de consulta, que usam as propriedades startIndex e EndIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Modelo de sintaxe de lote para tentativas sem entidades

Use o modelo a seguir para iniciar o arquivo em lotes sem entidades:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Se você não quiser testar entidades, inclua a propriedade `entities` e defina o valor como uma matriz vazia, `[]`.


## <a name="common-errors-importing-a-batch"></a>Erros comuns ao importar um lote

Os erros comuns incluem: 

> * Mais de 1.000 declarações
> * Um objeto JSON expressão que não tem uma propriedade Entities. A propriedade pode ser uma matriz vazia.
> * Palavras rotuladas em várias entidades
> * Rótulo de entidade iniciando ou terminando em um espaço.

## <a name="batch-test-state"></a>Estado do teste de lote

LUIS rastreia o estado do último teste de cada conjunto de dados. Isso inclui o tamanho (número de declarações no lote), a data da última execução e o último resultado (número de declarações previstos com êxito).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Resultados de teste em lotes

O resultado do teste de lote é um grafo de dispersão, conhecido como matriz de erros. Esse grafo é uma comparação de 4 vias do declarações no arquivo em lotes e as entidades e a intenção prevista do modelo atual. 

Os pontos de dados nas seções **falso positivo** e **falso negativo** indicam erros, que devem ser investigados. Se todos os pontos de dados estiverem nas seções **verdadeiro positivo** e **verdadeiro negativo** , a precisão do aplicativo será perfeita nesse conjunto de dados.

![Quatro seções do gráfico](./media/luis-concept-batch-test/chart-sections.png)

Este gráfico ajuda você a encontrar declarações que o LUIS prevê incorretamente com base em seu treinamento atual. Os resultados são exibidos por região do gráfico. Selecione pontos individuais no grafo para revisar as informações de expressão ou selecione o nome da região para examinar os resultados de expressão nessa região.

![Testes em lote](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erros nos resultados

Erros no teste em lote indicam tentativas que não são previstas como observados no arquivo em lotes. Os erros são indicados nas duas seções vermelhas do gráfico. 

A seção falso positivo indica que um expressão corresponde a uma intenção ou entidade quando não deveria. O falso negativo indica que um expressão não correspondeu a uma intenção ou entidade quando deveria. 

## <a name="fixing-batch-errors"></a>Corrigindo erros de lote

Se houver erros no teste do lote, você poderá adicionar mais declarações a uma intenção, e/ou rotular mais declarações com a entidade para ajudar a LUIS a fazer a discriminação entre as intenções. Se você adicionou declarações e os rotulau e ainda receber erros de previsão no teste do lote, considere adicionar um recurso de [lista de frases](luis-concept-feature.md) com o vocabulário específico do domínio para ajudar a Luis a aprender mais rapidamente. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [testar um lote](luis-how-to-batch-test.md)
