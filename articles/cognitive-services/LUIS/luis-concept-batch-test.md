---
title: Teste de lote - LUIS
titleSuffix: Azure Cognitive Services
description: Use o teste de batch para trabalhar continuamente na sua aplicação para refiná-la e melhorar a compreensão de idiomas.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220052"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>Teste de lote com 1000 expressões no portal LUIS

O teste do lote valida a sua versão ativa treinada para medir a sua precisão de previsão. Um teste de lote ajuda-o a visualizar a precisão de cada intenção e entidade na sua versão ativa, exibindo resultados com um gráfico. Reveja os resultados dos testes do lote para tomar as medidas adequadas para melhorar a precisão, tais como adicionar mais declarações de exemplo a uma intenção se a sua aplicação frequentemente não identificar a intenção correta ou identificar entidades dentro da expressão.

## <a name="group-data-for-batch-test"></a>Dados de grupo para teste de batch

É importante que a expressão utilizada para fins de teste do batch estiver familiarizado com o LUIS. Se tiver um conjunto de pronunciamentos de dados, divida as expressões em três conjuntos: declarações de exemplo adicionadas a uma intenção, expressões recebidas do ponto final publicado, e expressões usadas para o teste de lote LUIS após o seu treino. 

## <a name="a-data-set-of-utterances"></a>Um conjunto de dados de expressões

Envie um ficheiro de expressões, conhecido como conjunto de *dados,* para testes de lote. O conjunto de dados é um ficheiro formado pela JSON que contém um máximo de 1.000 expressões **não duplicadas** etiquetadas. Pode testar até 10 conjuntos de dados numa aplicação. Se precisar de testar mais, elimine um conjunto de dados e adicione um novo.

|**Regras**|
|--|
|\* Nenhuma expressões com duplicado|
|expressões de 1000 ou menos|

\* Duplicatas são consideradas correspondências de cadeia exacta, não as correspondências são indexadas pela primeira vez. 

## <a name="entities-allowed-in-batch-tests"></a>Entidades permitidas em testes de batch

Todas as entidades personalizadas no modelo de aparecem no filtro de entidades de teste de batch mesmo se não existirem não existem entidades correspondentes nos dados de ficheiro batch.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>Formato de ficheiro de batch

O arquivo em lotes consiste em expressões. Cada expressão deve ter uma previsão de intenções esperada, juntamente com quaisquer [entidades aprendidas](luis-concept-entity-types.md#types-of-entities) com máquinas que espere ser detetadas. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe de lote para intenções com entidades

Utilize o modelo seguinte para iniciar seu arquivo em lotes:

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

O ficheiro de lote utiliza as propriedades **startPos** e **endPos** para observar o início e o fim de uma entidade. Os valores não são baseado em zero e devem começar ou terminar num espaço. Isso é diferente dos registos de consulta, o que utilizar propriedades startIndex e endIndex. 

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

Se não quiser testar entidades, inclua a propriedade `entities` e detete teo valor como uma matriz vazia, `[]`.


## <a name="common-errors-importing-a-batch"></a>Importação de um lote de erros comuns

Erros comuns incluem: 

> * Expressões com mais de 1000
> * Um objeto JSON de expressão que não tem uma propriedade de entidades. A propriedade pode ser uma matriz vazia.
> * Word(s) rotulado como em várias entidades
> * Etiqueta de entidade inicial ou final num espaço.

## <a name="batch-test-state"></a>Estado do teste de batch

Luis rastreia o estado do último teste de cada conjunto de dados. Isto inclui a data de tamanho (número de expressões no lote), a última execução e o último resultado (número de expressões com prevista com êxito).

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>Resultados do teste de batch

O resultado de teste do batch é um gráfico de dispersão, conhecido como uma matriz de erro. Este gráfico é uma comparação de 4 vias de expressões no arquivo em lotes e intenção prevista do modelo atual e entidades. 

Os pontos de dados nas secções **False Positive** e **False Negative** indicam erros, que devem ser investigados. Se todos os pontos de dados estiverem nas secções **True Positive** e **True Negative,** então a precisão da sua aplicação é perfeita neste conjunto de dados.

![Quatro seções de gráfico](./media/luis-concept-batch-test/chart-sections.png)

Este gráfico ajuda a localizar as expressões que prevê o LUIS incorretamente com base no seu treinamento atual. Os resultados são exibidos por região do gráfico. Selecione os pontos individuais no gráfico para consultar as informações de expressão ou selecione o nome da região para rever os resultados de expressão nessa região.

![Testes em lote](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Erros nos resultados

Erros no teste de batch indicam intenções que não estão previstas conforme indicado no ficheiro batch. Erros são indicados nas duas secções vermelhas do gráfico. 

A secção positiva falso indica que uma expressão correspondido uma intenção ou a entidade que não deve ter. O negativo falso indica que uma expressão não correspondeu a um objetivo ou a entidade que deve ter. 

## <a name="fixing-batch-errors"></a>Corrigir erros de batch

Se houver erros no teste de lote, pode seja adicionar expressões mais numa intenção, e/ou expressões com mais com a entidade para o ajudar a tornar o discrimination entre objetivos de LUIS da etiqueta. Se adicionou expressões e rotulou-as, e ainda obtém erros de previsão nos testes de lote, considere adicionar uma funcionalidade de lista de [frases](luis-concept-feature.md) com vocabulário específico para ajudar a LUIS a aprender mais rapidamente. 

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [testar um lote](luis-how-to-batch-test.md)
