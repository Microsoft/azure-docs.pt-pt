---
title: Referência de esquema para a linguagem de definição de fluxo de trabalho - Azure Logic Apps
description: Guia de referência do esquema de linguagem de definição de fluxo de trabalho no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 04/30/2018
ms.openlocfilehash: d80ffa862546f56e93a338a7a1db031e2cb55990
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845755"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de esquema para a linguagem de definição de fluxo de trabalho no Azure Logic Apps

Quando cria uma aplicação lógica no [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), a aplicação lógica tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que é executado na sua aplicação lógica. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura que é validada pelo esquema de linguagem de definição de fluxo de trabalho. Esta referência disponibiliza uma descrição geral sobre esta estrutura e a forma como o esquema define elementos na sua definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura de definição de fluxo de trabalho

Uma definição de fluxo de trabalho sempre inclui um acionador para instanciar a sua aplicação lógica, além de uma ou mais ações que executam após o acionador é acionado.

Esta é a estrutura de alto nível para uma definição de fluxo de trabalho:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Elemento | Necessário | Descrição |
|---------|----------|-------------|
| definição | Sim | O elemento de partida para a sua definição de fluxo de trabalho |
| $schema | Apenas quando externamente que referencia uma definição de fluxo de trabalho | A localização para o ficheiro de esquema JSON que descreve a versão de linguagem de definição de fluxo de trabalho, que pode ser encontrado aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| contentVersion | Não | O número de versão para a sua definição de fluxo de trabalho, o que é "1.0.0.0" por predefinição. Para ajudar a identificar e confirmar a definição correta quando implementar um fluxo de trabalho, especifique um valor a utilizar. |
| parameters | Não | As definições para um ou mais parâmetros que passam dados para seu fluxo de trabalho <p><p>Parâmetros máximos: 50 |
| acionadores | Não | As definições para um ou mais disparadores que criar uma instância de seu fluxo de trabalho. Pode definir mais do que um acionador, mas apenas com a linguagem de definição de fluxo de trabalho, não visualmente por meio do Designer de aplicações lógicas. <p><p>Acionadores máximos: 10 |
| Ações | Não | As definições para uma ou mais ações executar em tempo de execução do fluxo de trabalho <p><p>Ações máximas: 250 |
| saídas | Não | As definições para as saídas que retornam a partir de um fluxo de trabalho execute <p><p>Saídas máximas: 10 |
||||

## <a name="parameters"></a>Parâmetros

Na `parameters` secção, definir todos os parâmetros de fluxo de trabalho que sua definição de fluxo de trabalho utiliza na implementação, para aceitar entradas. Declarações de parâmetros e valores de parâmetros são necessários na implementação. Antes de poder utilizar estes parâmetros em outras seções de fluxo de trabalho, certifique-se de que declarar todos os parâmetros nestas secções. 

Esta é a estrutura geral para uma definição de parâmetro:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Elemento | Necessário | Tipo | Descrição |
|---------|----------|------|-------------|
| tipo | Sim | int, número de vírgula flutuante, cadeia de caracteres, securestring, booleano, matriz, objeto JSON, secureobject <p><p>**Nota**: Para todas as palavras-passe, chaves e segredos, utilize o `securestring` e `secureobject` tipos porque o `GET` operação não retorna esses tipos. Para obter mais informações sobre como proteger a parâmetros, consulte [proteger a sua aplicação lógica](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | O tipo para o parâmetro |
| defaultValue | Sim | Mesmo que `type` | O valor de parâmetro predefinido quando é especificado nenhum valor quando cria uma instância de fluxo de trabalho |
| allowedValues | Não | Mesmo que `type` | Uma matriz com valores que pode aceitar o parâmetro |
| do IdP | Não | Objeto JSON | Quaisquer outros detalhes de parâmetro, por exemplo, o nome ou uma descrição legível para a sua aplicação lógica ou fluxo ou os dados de tempo de design usados pelo Visual Studio ou outras ferramentas |
||||

## <a name="triggers-and-actions"></a>Acionadores e ações

Numa definição de fluxo de trabalho, o `triggers` e `actions` secções definem as chamadas que ocorrem durante a execução do fluxo de trabalho. Para sintaxe e obter mais informações sobre estas secções, consulte [acionadores de fluxo de trabalho e as ações](../logic-apps/logic-apps-workflow-actions-triggers.md).

## <a name="outputs"></a>Saídas

Na `outputs` secção, definir os dados que o fluxo de trabalho pode retornar quando terminar em execução. Por exemplo, para controlar um estado específico ou um valor de cada execução, especifique que a saída de fluxo de trabalho retorna esses dados.

> [!NOTE]
> Quando a responder a pedidos recebidos a partir da API de um serviço REST, não utilize `outputs`. Em alternativa, utilize o `Response` tipo de ação. Para obter mais informações, consulte [acionadores de fluxo de trabalho e as ações](../logic-apps/logic-apps-workflow-actions-triggers.md).

Esta é a estrutura geral para obter uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Elemento | Necessário | Tipo | Descrição |
|---------|----------|------|-------------|
| <*key-name*> | Sim | String | O nome da chave para a saída de valor de retorno |
| tipo | Sim | int, número de vírgula flutuante, cadeia de caracteres, securestring, booleano, matriz, objeto JSON | O tipo para o valor de retorno de saída |
| value | Sim | Mesmo que `type` | O valor de retorno de saída |
|||||

Para obter o resultado de um fluxo de trabalho execute, reveja o histórico de execuções e detalhes no portal do Azure da sua aplicação lógica ou utilize o [API do REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Também é possível passar saída a sistemas externos, por exemplo, o Power BI para que possa criar dashboards.

<a name="expressions"></a>

## <a name="expressions"></a>Expressões

Com o JSON, pode ter valores literais que existem no tempo de design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Também pode ter valores que não existem até o Runtime. Para representar estes valores, pode usar *expressões*, que é avaliado no tempo de execução. Uma expressão é uma seqüência que pode conter um ou mais [funções](#functions), [operadores](#operators), variáveis, valores explícitos ou constantes. Na sua definição de fluxo de trabalho, pode usar uma expressão em qualquer lugar num valor de cadeia de caracteres do JSON atribuindo a expressão com no início de sessão (\@). Quando a avaliação de uma expressão que representa um valor JSON, o corpo da expressão é extraído, removendo o \@ caractere e, sempre resulta em outro valor JSON.

Por exemplo, para definido anteriormente `customerName` propriedade, pode obter o valor da propriedade com o [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) numa expressão de função e atribuir esse valor para o `accountName` propriedade:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Interpolação de cadeias de caracteres* também permitem-lhe utiliza várias expressões no interior de cadeias de caracteres que são compactadas com o \@ caráter e das chaves ({}). Esta é a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado é sempre uma cadeia de caracteres, tornando essa funcionalidade semelhante do `concat()` funcione, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se tiver uma cadeia literal que começa com o \@ caráter, o prefixo a \@ caractere com outra \@ caractere como um caráter de escape: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Resultado |
|------------|--------|
| "Sophia Owen" | Devolva estes carateres: 'Sophia Owen' |
| "matriz [1]" | Devolver estes carateres: "array [1]" |
| "\@\@" | Devolver estes carateres como uma cadeia de caracteres de um caractere: '\@' |
| " \@" | Devolver estes carateres como uma cadeia de caracteres de dois caracteres: ' \@' |
|||

Para estes exemplos, vamos supor que definir "myBirthMonth" igual a "Janeiro" e "myAge", igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como são avaliadas as expressões seguintes:

| Expressão de JSON | Resultado |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Retorne essa cadeia de caracteres: "Janeiro" |
| "\@{parameters('myBirthMonth')}" | Retorne essa cadeia de caracteres: "Janeiro" |
| "\@parameters('myAge')" | Devolva este número: 42 |
| "\@{parameters('myAge')}" | Devolva este número como uma cadeia de caracteres: "42" |
| "Minha idade é \@{parameters('myAge')}" | Retorne essa cadeia de caracteres: "A minha idade é 42" |
| "\@concat ("minha idade é", string(parameters('myAge')))" | Retorne essa cadeia de caracteres: "A minha idade é 42" |
| "É a minha idade \@ \@{parameters('myAge')}" | Retorne essa cadeia de caracteres, que inclui a expressão: "Minha idade é \@{parameters('myAge')}" |
|||

Quando está trabalhando visualmente no Designer de aplicações lógicas, pode criar expressões através do Expression builder, por exemplo:

![Estruturador de aplicações lógicas > Construtor de expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando tiver terminado, a expressão será exibido para a propriedade correspondente em sua definição de fluxo de trabalho, por exemplo, o `searchQuery` propriedade aqui:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operadores

Na [expressões](#expressions) e [funções](#functions), operadores de executam tarefas específicas, como uma propriedade ou um valor numa matriz de referência.

| Operador | Tarefa |
|----------|------|
| ' | Para utilizar um literal de cadeia como entrada ou em expressões e funções, encapsule a cadeia de caracteres apenas com plicas, por exemplo, `'<myString>'`. Não utilize aspas (""), que entram em conflito com a formatação do JSON em torno de uma expressão de toda. Por exemplo: <p>**Yes**: length('Hello') </br>**No**: length("Hello") <p>Quando transmitir matrizes ou números, não precisa de pontuação de encapsulamento de aplicações. Por exemplo: <p>**Sim**: comprimento ([1, 2, 3]) </br>**No**: length("[1, 2, 3]") |
| [] | Para fazer referência a um valor numa posição específica (índice) numa matriz, utilize parênteses Retos. Por exemplo, para obter o segundo item numa matriz: <p>`myArray[1]` |
| . | Para fazer referência a uma propriedade num objeto, utilize o operador de ponto. Por exemplo, para obter o `name` propriedade para um `customer` objeto JSON: <p>`"@parameters('customer').name"` |
| ? | Para fazer referência nulas propriedades num objeto sem um erro de tempo de execução, utilize o operador de ponto de interrogação. Por exemplo, para lidar com nulo saídas a partir de um acionador, pode utilizar esta expressão: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões de obtém os valores de ações de tempo de execução que ainda poderão não existir quando a definição de fluxo de trabalho começa a ser executada. Para referenciar ou trabalhar com esses valores em expressões, pode usar [ *funções* ](../logic-apps/workflow-definition-language-functions-reference.md) que fornece a linguagem de definição de fluxo de trabalho.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [ações de linguagem de definição de fluxo de trabalho e acionadores](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Saiba mais sobre programaticamente criar e gerir aplicações lógicas com o [API de REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
