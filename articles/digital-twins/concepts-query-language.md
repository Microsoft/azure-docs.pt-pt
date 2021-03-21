---
title: Linguagem da consulta
titleSuffix: Azure Digital Twins
description: Compreenda os fundamentos da linguagem de consulta Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490981"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para Azure Digital Twins

Recorde-se que o centro das Gémeas Digitais Azure é o [gráfico gémeo](concepts-twins-graph.md), construído a partir de gémeos digitais e relacionamentos. 

Este gráfico pode ser questionado para obter informações sobre os gémeos digitais e as relações que contém. Essas consultas são escritas numa linguagem de consulta personalizada do tipo SQL, denominada **linguagem de consulta do Azure Digital Twins**. Isto é semelhante ao [ioT Hub linguagem de consulta](../iot-hub/iot-hub-devguide-query-language.md) com muitas características comparáveis.

Este artigo descreve os fundamentos da linguagem de consulta e as suas capacidades. Para exemplos mais detalhados de sintaxe de consulta e como executar pedidos de consulta, consulte [*Como-a-fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).

## <a name="about-the-queries"></a>Sobre as consultas

Você pode usar a linguagem de consulta Azure Digital Twins para recuperar gémeos digitais de acordo com os seus...
* propriedades (incluindo [propriedades de tags)](how-to-use-tags.md)
* modelos
* relationships
  - propriedades das relações

Para submeter uma consulta ao serviço a partir de uma aplicação de cliente, utilizará a [**API de Consulta**](/rest/api/digital-twins/dataplane/query)de Gémeos Digitais Azure. Uma forma de usar a API é através de um dos [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) para Azure Digital Twins.

### <a name="considerations-for-querying"></a>Considerações para consulta

Ao escrever consultas para Azure Digital Twins, tenha em mente as seguintes considerações:
* **Lembre-se da sensibilidade** do caso : Todas as operações de consulta Azure Digital Twins são sensíveis a casos, por isso tenha cuidado para utilizar os nomes exatos definidos nos modelos. Se os nomes dos imóveis estiverem mal escritos ou mal arquivados, o conjunto de resultados está vazio sem erros devolvidos.
* **Escape citações individuais**: Se o seu texto de consulta incluir um único carácter de citação nos dados, a citação terá de ser escapada com o `\` personagem. Aqui está um exemplo que lida com um valor patrimonial de *D'Souza:*

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Referência: Expressões e condições

Esta secção descreve os operadores e funções que estão disponíveis para escrever consultas Azure Digital Twins. Por exemplo, consultas que ilustram a utilização destas funcionalidades, consulte [*Como fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).

### <a name="operators"></a>Operadores

São apoiados os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Lógico |`AND`, `OR`, `NOT` |
| Comparação | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>Funções

São suportadas as seguintes funções de verificação e fundição:

| Função | Descrição |
| -------- | ----------- |
| `IS_DEFINED` | Devolve um Boolean indicando se a propriedade foi atribuída um valor. Isto só é suportado quando o valor é um tipo primitivo. Os tipos primitivos incluem cordas, booleanos, numéricos, ou `null` . `DateTime`, tipos de objetos e matrizes não são suportados. |
| `IS_OF_MODEL` | Devolve um valor Boolean que indica se o gémeo especificado corresponde ao tipo de modelo especificado |
| `IS_BOOL` | Devolve um valor Boolean que indica se o tipo de expressão especificada é um Boolean. |
| `IS_NUMBER` | Devolve um valor Boolean que indica se o tipo de expressão especificada é um número. |
| `IS_STRING` | Devolve um valor Boolean que indica se o tipo de expressão especificada é uma corda. |
| `IS_NULL` | Devolve um valor Boolean que indica se o tipo de expressão especificada é nulo. |
| `IS_PRIMITIVE` | Devolve um valor Boolean que indica se o tipo de expressão especificada é primitivo (corda, booleana, numérico ou `null` . |
| `IS_OBJECT` | Devolve um valor Boolean que indica se o tipo de expressão especificada é um objeto JSON. |

As seguintes funções de corda são suportadas:

| Função | Descrição |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Devolve um Boolean indicando se a primeira expressão de corda começa com a segunda. |
| `ENDSWITH(x, y)` | Devolve um Boolean indicando se a primeira expressão de corda termina com a segunda. |

## <a name="query-limitations"></a>Limitações de consulta

Esta secção descreve limitações da linguagem de consulta.

* Tempo: Pode haver um atraso de até 10 segundos antes que as alterações no seu caso sejam refletidas em consultas. Por exemplo, se completar uma operação como criar ou eliminar gémeos com a API DigitalTwins, o resultado pode não ser imediatamente refletido nos pedidos da API de Consulta. Esperar por um curto período deve ser suficiente para ser resolvido.
* Não há subqueries suportados dentro da `FROM` declaração.
* `OUTER JOIN` a semântica não é suportada, o que significa que se a relação tem uma classificação de zero, então toda a "linha" é eliminada do conjunto de resultados de saída.
* A profundidade transversal de gráficos é restrita a cinco `JOIN` níveis por consulta.
* As relações em Azure Digital Twins não podem ser questionadas como entidades independentes; também precisa fornecer informações sobre a origem gémea de que a relação vem. Isto significa que existem algumas restrições à `JOIN` operação, que são usadas para consultar relações, para garantir que a consulta declara os gémeos(s) onde a consulta começa. Por exemplo, consulte [*Consulta por relação*](how-to-query-graph.md#query-by-relationship) no *Como-a-fazer: Consultar o* artigo de gráfico duplo.

## <a name="next-steps"></a>Passos seguintes

Saiba como escrever consultas e ver exemplos de código do cliente em [*Como-a-fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).