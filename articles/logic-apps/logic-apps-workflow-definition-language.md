---
title: Referência de esquema de definição de fluxo de trabalho
description: Guia de referência para o esquema JSON e sintaxe para a linguagem de definição de fluxo de trabalho que descreve fluxos de trabalho em Apps lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87078142"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Guia de referência de Schema para a linguagem de definição de fluxo de trabalho em apps lógicas Azure

Quando cria uma aplicação lógica em [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)a sua aplicação lógica tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que funciona na sua aplicação lógica. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura que é validada pelo esquema de linguagem de definição de fluxo de trabalho. Esta referência fornece uma visão geral sobre esta estrutura e como o esquema define atributos na definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura de definição de fluxo de trabalho

Uma definição de fluxo de trabalho inclui sempre um gatilho para instantaneamente a sua aplicação lógica, além de uma ou mais ações que funcionam após o disparo.

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

| Atributo | Obrigatório | Descrição |
|-----------|----------|-------------|
| `definition` | Sim | O elemento de partida para a definição de fluxo de trabalho |
| `$schema` | Só quando referenciar externamente uma definição de fluxo de trabalho | A localização do ficheiro de esquema JSON que descreve a versão Idioma de Definição de Fluxo de Trabalho, que pode encontrar aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | As definições para uma ou mais ações para executar em tempo de execução do fluxo de trabalho. Para obter mais informações, consulte [Triggers e ações](#triggers-actions). <p><p>Máximas ações: 250 |
| `contentVersion` | No | O número da versão para a definição de fluxo de trabalho, que é "1.0.0.0" por predefinição. Para ajudar a identificar e confirmar a definição correta ao implementar um fluxo de trabalho, especifique um valor a utilizar. |
| `outputs` | No | As definições para as saídas regressarem de uma execução de fluxo de trabalho. Para mais informações, consulte [outputs](#outputs). <p><p>Saídas máximas: 10 |
| `parameters` | No | As definições para um ou mais parâmetros que passam os valores a utilizar no tempo de funcionamento da sua aplicação lógica. Para obter mais informações, consulte [parâmetros.](#parameters) <p><p>Parâmetros máximos: 50 |
| `staticResults` | No | As definições para um ou mais resultados estáticos devolvidos por ações como saídas falsas quando os resultados estáticos são ativados nessas ações. Em cada definição de ação, o `runtimeConfiguration.staticResult.name` atributo refere a definição correspondente no interior `staticResults` . Para obter mais informações, consulte [os resultados estáticos.](#static-results) |
| `triggers` | No | As definições para um ou mais gatilhos que instantaneamente o seu fluxo de trabalho. Pode definir mais do que um gatilho, mas apenas com a Linguagem de Definição de Fluxo de Trabalho, não visualmente através do Design de Aplicações Lógicas. Para obter mais informações, consulte [Triggers e ações](#triggers-actions). <p><p>Gatilhos máximos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Gatilhos e ações

Numa definição de fluxo de trabalho, as `triggers` secções e `actions` as secções definem as chamadas que acontecem durante a execução do seu fluxo de trabalho. Para sintaxe e mais informações sobre estas secções, consulte [os gatilhos e ações do Workflow](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida de implantação geralmente tem diferentes ambientes para desenvolvimento, teste, encenação e produção. Ao implementar aplicações lógicas para vários ambientes, é provável que pretenda utilizar diferentes valores, como cadeias de ligação, com base nas suas necessidades de implementação. Ou pode ter valores que pretende reutilizar ao longo da sua aplicação lógica sem hardcoding ou que mude com frequência. Na secção de definição de fluxo de `parameters` trabalho, pode definir ou editar parâmetros para os valores que a sua aplicação lógica utiliza em tempo de execução. Deve definir estes parâmetros primeiro antes de poder referenciar estes parâmetros em outro lugar na sua definição de fluxo de trabalho.

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

| Atributo | Necessário | Tipo | Description |
|-----------|----------|------|-------------|
| <*nome de parâmetro*> | Sim | String | O nome para o parâmetro que quer definir |
| <*tipo de parâmetro*> | Yes | int, flutuar, corda, bool, matriz, objeto, securestring, secureobject <p><p>**Nota:** Para todas as palavras-passe, chaves e segredos, utilize os `securestring` ou tipos porque a `secureobject` `GET` operação não devolve estes tipos. Para obter mais informações sobre a fixação de parâmetros, consulte [recomendações de segurança para ações e parâmetros de entrada](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | O tipo para o parâmetro |
| <*padrão-parâmetro-valor*> | Yes | O mesmo que `type` | O valor do parâmetro padrão a utilizar se não for especificado qualquer valor quando o fluxo de trabalho instantaneamente for. O `defaultValue` atributo é necessário para que o Designer de Aplicações Lógicas possa mostrar corretamente o parâmetro, mas pode especificar um valor vazio. |
| <*matriz-com-parâmetro-valores permitidos*> | No | Matriz | Uma matriz com valores que o parâmetro pode aceitar |
| <*parâmetro-descrição*> | No | Objeto JSON | Quaisquer outros detalhes do parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para a definição de fluxo de trabalho, defina os parâmetros do modelo que aceite os valores que deseja na implementação, substitua valores codificados por referências aos parâmetros de definição de modelo ou fluxo de trabalho conforme apropriado, e guarde os valores a utilizar na implementação num ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md)separados. Desta forma, pode alterar esses valores mais facilmente através do ficheiro de parâmetros sem ter de atualizar e redistribuir a sua aplicação lógica. Para informações sensíveis ou que devem ser protegidas, tais como nomes de utilizador, palavras-passe e segredos, pode armazenar esses valores no Cofre da Chave Azure e pedir ao seu ficheiro de parâmetro para recuperar esses valores do cofre da chave. Para obter mais informações e exemplos sobre a definição de parâmetros nos níveis de definição de modelo e fluxo de trabalho, consulte [visão geral: Automatize a implementação de aplicações lógicas com modelos de Gestor de Recursos Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No `staticResults` atributo, defina a simulação de uma ação `outputs` e que a ação `status` retorna quando a definição de resultado estático da ação é ligada. Na definição da ação, o `runtimeConfiguration.staticResult.name` atributo refere o nome para a definição de resultados estáticos no interior `staticResults` . Saiba como pode [testar aplicações lógicas com dados falsos, configurando resultados estáticos.](../logic-apps/test-logic-apps-mock-data-static-results.md)

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

| Atributo | Necessário | Tipo | Description |
|-----------|----------|------|-------------|
| <*nome de definição de resultados estáticos*> | Sim | String | O nome para uma definição de resultado estático que uma definição de ação pode referenciar através de um `runtimeConfiguration.staticResult` objeto. Para obter mais informações, consulte [as definições de configuração do tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Pode usar o nome único que quiser. Por padrão, este nome único é anexado com um número, que é incrementado conforme necessário. |
| <*saída-atributos-e-valores-devolvidos*> | Yes | Varia | Os requisitos para estes atributos variam em função de diferentes condições. Por exemplo, quando o `status` é , o atributo inclui `Succeeded` `outputs` atributos e valores devolvidos como saídas falsas pela ação. Se `status` `Failed` for, o `outputs` atributo inclui o `errors` atributo, que é um conjunto com um ou mais objetos de erro `message` que têm informações de erro. |
| <*cabeçalho-valores*> | No | JSON | Quaisquer valores de cabeçalho devolvidos pela ação |
| <*estado-código devolvido*> | Sim | String | O código de estado devolvido pela ação |
| <*estado de ação*> | Sim | String | O estado da ação, por exemplo, `Succeeded` ou `Failed` |
|||||

Por exemplo, nesta definição de ação HTTP, o `runtimeConfiguration.staticResult.name` atributo refere referências `HTTP0` dentro do atributo onde são `staticResults` definidas as saídas falsas para a ação. O `runtimeConfiguration.staticResult.staticResultOptions` atributo especifica que a definição de resultados está na ação `Enabled` HTTP.

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

A ação HTTP devolve as saídas na `HTTP0` definição interior `staticResults` . Neste exemplo, para o código de estado, a saída de simulação é `OK` . Para os valores do cabeçalho, a saída de simulação é `"Content-Type": "application/JSON"` . Para o estado da ação, a saída falsa é `Succeeded` .

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

## <a name="expressions"></a>Expressions (Expressões)

Com o JSON, você pode ter valores literais que existem no tempo de design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Também pode ter valores que não existem até ao tempo de execução. Para representar estes valores, pode utilizar expressões , que são *avaliadas* no tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções,](#functions) [operadores,](#operators) [variáveis, valores explícitos](./logic-apps-create-variables-store-values.md)ou constantes. Na definição de fluxo de trabalho, pode utilizar uma expressão em qualquer lugar do valor da cadeia JSON, préfixando a expressão com o sinal de at-sign \@ (). Ao avaliar uma expressão que representa um valor JSON, o corpo de expressão é extraído removendo o \@ personagem, e resulta sempre em outro valor JSON.

Por exemplo, para o imóvel previamente `customerName` definido, você pode obter o valor da propriedade usando a função [de parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) numa expressão e atribuir esse valor ao `accountName` imóvel:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*A interpolação de* cordas também permite usar múltiplas expressões dentro de cordas que são embrulhadas pelo \@ personagem e aparelhos encaracolados {} (). Aqui está a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado é sempre uma corda, tornando esta capacidade semelhante à `concat()` função, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se tiver uma corda literal que comece com o \@ personagem, prefixe o \@ personagem com outro \@ personagem como personagem de fuga: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Resultado |
|------------|--------|
| "Sophia Owen" | Devolva estes personagens: 'Sophia Owen' |
| "matriz[1]" | Devolva estes caracteres: 'matriz[1]' |
| "\@\@" | Devolva estes caracteres como uma corda de um personagem: \@ ' |
| " \@" | Devolva estes caracteres como uma corda de dois caracteres: \@ ' |
|||

Para estes exemplos, suponha que define "myBirthMonth" igual a "janeiro" e "myAge" igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as seguintes expressões são avaliadas:

| Expressão JSON | Resultado |
|-----------------|--------|
| " \@ parâmetros ('myBirthMonth')" | Devolva esta cadeia: "janeiro" |
| " \@ {parâmetros ('myBirthMonth')}" | Devolva esta cadeia: "janeiro" |
| " \@ parâmetros ('myAge')" | Devolva este número: 42 |
| " \@ {parâmetros('myAge')}" | Devolva este número como uma corda: "42" |
| "A minha idade é \@ {parâmetros('myAge')}" | Devolva esta corda: "A minha idade é 42" |
| " \@ concat ('A minha idade é ', string (parâmetros('myAge'))" | Devolva esta corda: "A minha idade é 42" |
| "A minha idade é \@ \@ {parâmetros('myAge')}" | Devolva esta corda, que inclui a expressão: "A minha idade é \@ {parâmetros('myAge')} |
|||

Quando está a trabalhar visualmente no Logic Apps Designer, pode criar expressões através do construtor de expressões, por exemplo:

![Construtor de expressão de > de apps lógicas](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando termina, a expressão aparece para o imóvel correspondente na definição de fluxo de trabalho, por exemplo, a `searchQuery` propriedade aqui:

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

Na `outputs` secção, defina os dados que o seu fluxo de trabalho pode devolver quando terminar de correr. Por exemplo, para rastrear um estado ou valor específico de cada execução, especifique que a saída de fluxo de trabalho devolve esses dados.

> [!NOTE]
> Ao responder aos pedidos de entrada da API REST de um serviço, não utilize `outputs` . Em vez disso, use o `Response` tipo de ação. Para obter mais informações, consulte [os gatilhos e ações do Workflow](../logic-apps/logic-apps-workflow-actions-triggers.md).

Aqui está a estrutura geral para uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atributo | Necessário | Tipo | Description |
|-----------|----------|------|-------------|
| <*nome-chave*> | Sim | String | O nome-chave para o valor de retorno de saída |
| <*tipo-chave*> | Yes | int, flutuar, string, securestring, bool, array, objeto JSON | O tipo para o valor de retorno de saída |
| <*valor-chave*> | Yes | O mesmo que <*tipo-chave*> | O valor de retorno da saída |
|||||

Para obter a saída de uma execução de fluxo de trabalho, reveja o histórico de execução da sua aplicação lógica e detalhes no portal Azure ou use o [Workflow REST API](/rest/api/logic/workflows). Também pode passar a saída para sistemas externos, por exemplo, Power BI para que possa criar dashboards.

<a name="operators"></a>

## <a name="operators"></a>Operadores

Em [expressões](#expressions) e [funções,](#functions)os operadores executam tarefas específicas, como referência de um imóvel ou de um valor num conjunto.

| Operador | Tarefa |
|----------|------|
| ' | Para utilizar uma corda literal como entrada ou em expressões e funções, envolva a corda apenas com aspas únicas, por exemplo, `'<myString>'` . Não utilize aspas duplas (""), que entram em conflito com o JSON formando em torno de toda uma expressão. Por exemplo: <p>**Sim:** comprimento ('Olá') </br>**Não**: comprimento ("Olá") <p>Quando se passam matrizes ou números, não é preciso embrulhar pontuação. Por exemplo: <p>**Sim:** comprimento([1, 2, 3]) </br>**No**: comprimento("[1, 2, 3]") |
| [] | Para fazer referência a um valor numa posição específica (índice) numa matriz, utilize suportes quadrados. Por exemplo, para obter o segundo item numa matriz: <p>`myArray[1]` |
| . | Para fazer referência a uma propriedade num objeto, utilize o operador de pontos. Por exemplo, para obter a `name` propriedade para um objeto `customer` JSON: <p>`"@parameters('customer').name"` |
| ? | Para fazer referência a propriedades nulas num objeto sem um erro de tempo de execução, utilize o operador de ponto de interrogação. Por exemplo, para lidar com saídas nulas a partir de um gatilho, pode utilizar esta expressão: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm os seus valores de ações de tempo de execução que podem ainda não existir quando a sua definição de fluxo de trabalho começa a funcionar. Para fazer referência ou trabalhar com estes valores em expressões, pode utilizar [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) que a Linguagem de Definição de Fluxo de Trabalho fornece.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [ações e gatilhos de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Saiba sobre a criação e gestão programática de apps lógicas com a [API de Fluxo de Trabalho REST](/rest/api/logic/workflows)
