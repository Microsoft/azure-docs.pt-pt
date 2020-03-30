---
title: Referência do esquema da linguagem definição de fluxo de trabalho
description: Guia de referência para o esquema JSON e sintaxe para a Linguagem de Definição de Fluxo de Trabalho que descreve fluxos de trabalho em Aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283865"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Guia de referência schema para a linguagem de definição de fluxo de trabalho em aplicações lógicas azure

Quando cria uma aplicação lógica em [Aplicações Lógicas Azure,](../logic-apps/logic-apps-overview.md)a sua aplicação lógica tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que corre na sua aplicação lógica. Esta definição de fluxo de trabalho usa [a JSON](https://www.json.org/) e segue uma estrutura validada pelo esquema de linguagem de definição de fluxo de trabalho. Esta referência fornece uma visão geral sobre esta estrutura e como o esquema define atributos na sua definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura de definição de fluxo de trabalho

Uma definição de fluxo de trabalho inclui sempre um gatilho para instantanear a sua aplicação lógica, além de uma ou mais ações que correm após os incêndios do gatilho.

Aqui está a estrutura de alto nível para uma definição de fluxo de trabalho:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Atributo | Necessário | Descrição |
|-----------|----------|-------------|
| `definition` | Sim | O elemento de partida para a sua definição de fluxo de trabalho |
| `$schema` | Só quando se refere externamente a uma definição de fluxo de trabalho | A localização do ficheiro schema JSON que descreve a versão Idioma de Definição de Fluxo de Trabalho, que pode encontrar aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Não | As definições para uma ou mais ações a executar no tempo de funcionamento do fluxo de trabalho. Para mais informações, consulte [Triggers e ações](#triggers-actions). <p><p>Ações máximas: 250 |
| `contentVersion` | Não | O número da versão para a sua definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implementar um fluxo de trabalho, especifique um valor a utilizar. |
| `outputs` | Não | As definições para que as saídas regressem de uma corrida de fluxo de trabalho. Para mais informações, consulte [Saídas](#outputs). <p><p>Saídas máximas: 10 |
| `parameters` | Não | As definições para um ou mais parâmetros que passam os valores para usar no tempo de execução da sua aplicação lógica. Para mais informações, consulte [Parâmetros](#parameters). <p><p>Parâmetros máximos: 50 |
| `staticResults` | Não | As definições para um ou mais resultados estáticos devolvidos por ações como saídas falsas quando os resultados estáticos são ativados nessas ações. Em cada definição `runtimeConfiguration.staticResult.name` de ação, o `staticResults`atributo refere a definição correspondente no seu interior . Para mais informações, consulte [resultados estáticos](#static-results). |
| `triggers` | Não | As definições para um ou mais gatilhos que instantaneamente o seu fluxo de trabalho. Pode definir mais do que um gatilho, mas apenas com a Linguagem definição de fluxo de trabalho, não visualmente através do Logic Apps Designer. Para mais informações, consulte [Triggers e ações](#triggers-actions). <p><p>Gatilhos máximos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Gatilhos e ações

Numa definição de `triggers` fluxo `actions` de trabalho, as e secções definem as chamadas que ocorrem durante a execução do seu fluxo de trabalho. Para sintaxe e mais informações sobre estas secções, consulte os gatilhos e ações do [Workflow.](../logic-apps/logic-apps-workflow-actions-triggers.md)

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida de implantação geralmente tem diferentes ambientes para desenvolvimento, teste, encenação e produção. Ao implementar aplicações lógicas para vários ambientes, é provável que queira utilizar diferentes valores, como cordas de ligação, com base nas suas necessidades de implementação. Ou, pode ter valores que pretende reutilizar ao longo da sua aplicação lógica sem codificação de dureza ou que mudam frequentemente. Na secção da definição de `parameters` fluxo de trabalho, pode definir ou editar parâmetros para os valores que a sua aplicação lógica utiliza no tempo de execução. Deve definir estes parâmetros primeiro antes de poder fazer referência a estes parâmetros em outros lugares da sua definição de fluxo de trabalho.

Aqui está a estrutura geral para uma definição de parâmetro:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*nome do parâmetro*> | Sim | Cadeia | O nome para o parâmetro que quer definir |
| <*parâmetro-tipo*> | Sim | int, flutuante, corda, bool, matriz, objeto, corda de segurança, objeto seguro <p><p>**Nota:** Para todas as palavras-passe, `securestring` `secureobject` chaves e `GET` segredos, utilize os ou tipos porque a operação não devolve estes tipos. Para obter mais informações sobre a segurança dos [parâmetros, consulte recomendações de segurança para os parâmetros](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)de ação e de entrada . | O tipo para o parâmetro |
| <*valor de parâmetro sinuoso*> | Sim | O mesmo que`type` | O valor do parâmetro padrão a utilizar se não for especificado qualquer valor quando o fluxo de trabalho instantaneamente. O `defaultValue` atributo é necessário para que o Logic App Designer possa mostrar corretamente o parâmetro, mas pode especificar um valor vazio. |
| <*matriz-com-parâmetro permitido-valores*> | Não | Matriz | Uma matriz com valores que o parâmetro pode aceitar |
| <*parâmetro-descrição*> | Não | Objeto JSON | Quaisquer outros detalhes do parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) para a sua definição de fluxo de trabalho, defina parâmetros de modelo que aceitem os valores que pretende na implementação, substitua valores codificados com referências a parâmetros de definição de modelo ou de fluxo de trabalho, conforme apropriado, e guarde os valores a utilizar na implementação num [ficheiro de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado . Desta forma, pode alterar esses valores mais facilmente através do ficheiro parâmetro sem ter de atualizar e reimplantar a sua aplicação lógica. Para obter informações sensíveis ou devem ser protegidas, tais como nomes de utilizador, senhas e segredos, pode armazenar esses valores no Cofre de Chaves Azure e fazer com que o seu ficheiro de parâmetro recupere esses valores do seu cofre chave. Para mais informações e exemplos sobre a definição de parâmetros nos níveis de definição de modelo e de fluxo de trabalho, consulte [a visão geral: Implementação automática para aplicações lógicas com modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Gestor de Recursos Azure .

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No `staticResults` atributo, defina a `outputs` simulação de uma ação e `status` que a ação retorna quando a definição estática do resultado da ação é ligada. Na definição da ação, o `runtimeConfiguration.staticResult.name` atributo refere o nome `staticResults`para a definição de resultado estático no interior . Saiba como pode [testar aplicações lógicas com dados falsos através da criação de resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*nome de definição de resultado estático*> | Sim | Cadeia | O nome para uma definição de resultado `runtimeConfiguration.staticResult` estático que uma definição de ação pode referir através de um objeto. Para mais informações, consulte as definições de [configuração do Tempo de Execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Pode usar qualquer nome único que quiser. Por padrão, este nome único é anexado com um número, que é incrementado conforme necessário. |
| <*output-atributos-e-valores-devolvido*> | Sim | Varia | Os requisitos para estes atributos variam em função de diferentes condições. Por exemplo, `status` quando `Succeeded`o `outputs` é, o atributo inclui atributos e valores devolvidos como saídas falsas pela ação. Se `status` for, `Failed` `outputs` o atributo `errors` inclui o atributo, que é `message` uma matriz com um ou mais objetos de erro que têm informações de erro. |
| <*valores cabeçalho*> | Não | JSON | Quaisquer valores de cabeçalho devolvidos pela ação |
| <*status-code-returned*> | Sim | Cadeia | O código de estado devolvido pela ação |
| <*estatuto de ação*> | Sim | Cadeia | O estado da ação, `Succeeded` por exemplo, ou`Failed` |
|||||

Por exemplo, nesta definição `runtimeConfiguration.staticResult.name` de ação `HTTP0` HTTP, `staticResults` as referências de atributo saem do atributo onde são definidas as saídas falsas para a ação. O `runtimeConfiguration.staticResult.staticResultOptions` atributo especifica que a `Enabled` definição de resultado estático está na ação HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

A ação HTTP devolve as `HTTP0` saídas na definição interna `staticResults`. Neste exemplo, para o código de `OK`estado, a saída falsa é . Para valores de cabeçalho, a saída falsa é `"Content-Type": "application/JSON"`. Para o estado da ação, `Succeeded`a saída falsa é .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Expressões

Com a JSON, pode ter valores literais que existem no momento do design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Também pode ter valores que não existem até o tempo de execução. Para representar estes valores, pode utilizar *expressões*, que são avaliadas no tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções,](#functions) [operadores,](#operators)variáveis, valores explícitos ou constantes. Na sua definição de fluxo de trabalho, pode utilizar uma expressão em qualquer lugar\@de um valor de corda JSON, prefixando a expressão com o sinal de at (). Ao avaliar uma expressão que representa um valor JSON, o \@ corpo de expressão é extraído removendo o personagem, e sempre resulta em outro valor JSON.

Por exemplo, para o `customerName` imóvel previamente definido, pode obter o valor patrimonial utilizando a função `accountName` [de parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) numa expressão e atribuir esse valor ao imóvel:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*A interpolação* das cordas também permite usar múltiplas expressões \@ dentro de cordas{}que são embrulhadas pelo personagem e aparelhos encaracolados (). Aqui está a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado é sempre uma corda, tornando `concat()` esta capacidade semelhante à função, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se tiver uma corda literal \@ que comece com \@ o \@ personagem, prefixe o personagem com outro personagem como personagem de fuga:\@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Resultado |
|------------|--------|
| "Sophia Owen" | Devolva estes personagens: 'Sophia Owen' |
| "array[1]" | Devolver estes caracteres: 'array[1]' |
| "\@\@" | Devolva estes personagens como\@uma corda de um personagem: ' ' |
| " \@" | Devolva estes personagens como \@uma corda de dois caracteres: ' ' |
|||

Para estes exemplos, suponha que defina "myBirthMonth" igual a "janeiro" e "myAge" igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as seguintes expressões são avaliadas:

| Expressão JSON | Resultado |
|-----------------|--------|
| "parâmetros\@('myBirthMonth')" | Devolva esta corda: "janeiro" |
| "\@{parâmetros ('myBirthMonth')}" | Devolva esta corda: "janeiro" |
| "parâmetros\@('myAge')" | Devolver este número: 42 |
| "\@{parâmetros('myAge')}" | Devolva este número como uma corda: "42" |
| "A minha \@idade é {parâmetros('myAge')}" | Devolva esta corda: "A minha idade tem 42 anos" |
| "concat('A\@minha idade é ', string ('myAge')" | Devolva esta corda: "A minha idade tem 42 anos" |
| "A minha \@ \@idade é {parâmetros('myAge')}" | Devolva esta corda, que inclui \@a expressão: "A minha idade é {parâmetros('myAge')}" |
|||

Quando estiver a trabalhar visualmente no Logic Apps Designer, pode criar expressões através do construtor de expressão, por exemplo:

![Lógica apps Designer > de expressão construtor](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando terminar, a expressão aparece para a propriedade correspondente na sua definição de fluxo de trabalho, por exemplo, a `searchQuery` propriedade aqui:

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

<a name="outputs"></a>

## <a name="outputs"></a>Saídas

Na `outputs` secção, defina os dados que o seu fluxo de trabalho pode devolver quando terminar a execução. Por exemplo, para rastrear um estado ou valor específico de cada execução, especifique que a saída de fluxo de trabalho devolve esses dados.

> [!NOTE]
> Ao responder aos pedidos de entrada da API REST `outputs`de um serviço, não utilize . Em vez `Response` disso, use o tipo de ação. Para obter mais informações, consulte [os gatilhos e ações do Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-actions-triggers.md)

Aqui está a estrutura geral para uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*nome-chave*> | Sim | Cadeia | O nome-chave para o valor de retorno da saída |
| <*tipo chave*> | Sim | int, flutuante, corda, corda de segurança, bool, matriz, objeto JSON | O tipo para o valor de retorno de saída |
| <*valor-chave*> | Sim | O mesmo que <*tipo chave*> | O valor de retorno da saída |
|||||

Para obter a saída de uma execução de fluxo de trabalho, reveja o histórico de execução da sua aplicação lógica e detalhes no portal Azure ou utilize a [API Workflow REST](https://docs.microsoft.com/rest/api/logic/workflows). Também pode passar a saída para sistemas externos, por exemplo, Power BI para que possa criar dashboards.

<a name="operators"></a>

## <a name="operators"></a>Operadores

Em [expressões](#expressions) e [funções,](#functions)os operadores executam tarefas específicas, tais como referência a um imóvel ou um valor numa matriz.

| Operador | Tarefa |
|----------|------|
| ' | Para utilizar uma corda literal como entrada ou em expressões e funções, envolva `'<myString>'`a corda apenas com aspas únicas, por exemplo, . Não utilize aspas duplas (""), que entram em conflito com a formatação JSON em torno de toda uma expressão. Por exemplo: <p>**Sim:** comprimento ('Olá') </br>**Não**: comprimento("Olá") <p>Quando passas matrizes ou números, não precisas de pontuação de embrulho. Por exemplo: <p>**Sim:** comprimento([1, 2, 3]) </br>**Não**: comprimento("[1, 2, 3]") |
| [] | Para referir um valor numa posição específica (índice) numa matriz, utilize parênteses quadrados. Por exemplo, para obter o segundo item em uma matriz: <p>`myArray[1]` |
| . | Para fazer referência a uma propriedade num objeto, utilize o operador de pontos. Por exemplo, para `name` obter `customer` a propriedade para um objeto JSON: <p>`"@parameters('customer').name"` |
| ? | Para referir as propriedades nulas num objeto sem um erro de tempo de execução, utilize o operador do ponto de interrogação. Por exemplo, para manusear saídas nulas a partir de um gatilho, pode utilizar esta expressão: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm os seus valores a partir de ações de tempo de execução que podem ainda não existir quando a sua definição de fluxo de trabalho começa a funcionar. Para fazer referência ou trabalhar com estes valores em expressões, pode utilizar [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) que a Linguagem de Definição de Fluxo de Trabalho proporciona.

## <a name="next-steps"></a>Passos seguintes

* Conheça as [ações e gatilhos da definição](../logic-apps/logic-apps-workflow-actions-triggers.md) de fluxo de trabalho
* Saiba sobre a criação e gestão programática de aplicações lógicas com a [API Workflow REST](https://docs.microsoft.com/rest/api/logic/workflows)
