---
title: Teste de lote - LUIS
titleSuffix: Azure Cognitive Services
description: Utilize testes de lote para trabalhar continuamente na sua aplicação para refiná-la e melhorar a sua compreensão linguística.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220052"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Teste de lote com 1000 expressões no portal LUIS

O teste do lote valida a sua versão ativa treinada para medir a sua precisão de previsão. Um teste de lote ajuda-o a visualizar a precisão de cada intenção e entidade na sua versão ativa, exibindo resultados com um gráfico. Reveja os resultados dos testes do lote para tomar as medidas adequadas para melhorar a precisão, tais como adicionar mais declarações de exemplo a uma intenção se a sua aplicação frequentemente não identificar a intenção correta ou identificar entidades dentro da expressão.

## <a name="group-data-for-batch-test"></a>Dados de grupo para o teste do lote

É importante que as declarações utilizadas para os testes de lote sejam novas para o LUIS. Se tiver um conjunto de pronunciamentos de dados, divida as expressões em três conjuntos: declarações de exemplo adicionadas a uma intenção, expressões recebidas do ponto final publicado, e expressões usadas para o teste de lote LUIS após o seu treino. 

## <a name="a-data-set-of-utterances"></a>Um conjunto de dados de expressões

Envie um ficheiro de expressões, conhecido como conjunto de *dados,* para testes de lote. O conjunto de dados é um ficheiro formado pela JSON que contém um máximo de 1.000 expressões **não duplicadas** etiquetadas. Pode testar até 10 conjuntos de dados numa aplicação. Se precisar de testar mais, elimine um conjunto de dados e adicione um novo.

|**Regras**|
|--|
|*Sem declarações duplicadas|
|1000 expressões ou menos|

*Os duplicados são considerados fósforos de corda exatos, não fósforos que são tokenizados primeiro. 

## <a name="entities-allowed-in-batch-tests"></a>Entidades permitidas em testes de lote

Todas as entidades personalizadas do modelo aparecem no filtro de entidades de teste de lote mesmo que não existam entidades correspondentes nos dados do ficheiro do lote.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formato de ficheiro de lote

O ficheiro do lote consiste em expressões. Cada expressão deve ter uma previsão de intenções esperada, juntamente com quaisquer [entidades aprendidas](luis-concept-entity-types.md#types-of-entities) com máquinas que espere ser detetadas. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe de lote para intenções com entidades

Utilize o seguinte modelo para iniciar o seu ficheiro de lote:

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

O ficheiro de lote utiliza as propriedades **startPos** e **endPos** para observar o início e o fim de uma entidade. Os valores são baseados em zero e não devem começar ou terminar num espaço. Isto é diferente dos registos de consulta, que usam propriedades startIndex e endIndex. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>Modelo de sintaxe de lote para intenções sem entidades

Utilize o seguinte modelo para iniciar o seu ficheiro de lote sem entidades:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

Se não quiser testar entidades, `entities` inclua o imóvel e `[]`detetete o valor como uma matriz vazia, .


## <a name="common-errors-importing-a-batch"></a>Erros comuns importando um lote

Os erros comuns incluem: 

> * Mais de 1.000 expressões
> * Um objeto json que não tem uma propriedade de entidades. A propriedade pode ser uma matriz vazia.
> * Palavras ou palavras rotuladas em várias entidades
> * Etiqueta de entidade começando ou terminando em um espaço.

## <a name="batch-test-state"></a>Estado de teste de lote

Luis rastreia o estado do último teste de cada conjunto de dados. Isto inclui o tamanho (número de expressões no lote), data de última execução e último resultado (número de expressões bem previstas).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Resultados dos testes do lote

O resultado do teste do lote é um gráfico de dispersão, conhecido como uma matriz de erro. Este gráfico é uma comparação de 4 vias das expressões no ficheiro do lote e das intenções e entidades previstas do modelo atual. 

Os pontos de dados nas secções **False Positive** e **False Negative** indicam erros, que devem ser investigados. Se todos os pontos de dados estiverem nas secções **True Positive** e **True Negative,** então a precisão da sua aplicação é perfeita neste conjunto de dados.

![Quatro secções de gráfico](./media/luis-concept-batch-test/chart-sections.png)

Este gráfico ajuda-o a encontrar declarações que a LUIS prevê incorretamente com base na sua formação atual. Os resultados são apresentados por região do gráfico. Selecione pontos individuais no gráfico para rever as informações de expressão ou selecione o nome da região para rever os resultados da expressão nessa região.

![Testes em lote](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erros nos resultados

Erros no teste do lote indicam intençãos que não são previstas como observada no ficheiro do lote. Erros são indicados nas duas secções vermelhas do gráfico. 

A secção falsa mente positiva indica que uma expressão corresponde a uma intenção ou entidade quando não deveria. O falso negativo indica que uma expressão não corresponde a uma intenção ou entidade quando deveria. 

## <a name="fixing-batch-errors"></a>Corrigir erros de lote

Se houver erros nos testes de lote, pode adicionar mais declarações a uma intenção, e/ou rotular mais expressões com a entidade para ajudar a LUIS a fazer a discriminação entre intenções. Se adicionou expressões e rotulou-as, e ainda obtém erros de previsão nos testes de lote, considere adicionar uma funcionalidade de lista de [frases](luis-concept-feature.md) com vocabulário específico para ajudar a LUIS a aprender mais rapidamente. 

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [testar um lote](luis-how-to-batch-test.md)
