---
title: Referência de esquema de linguagem de definição de fluxo de trabalho
description: Guia de referência para o esquema JSON e a sintaxe para a linguagem de definição de fluxo de trabalho que descreve os fluxos de trabalho nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428652"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Guia de referência de esquema para a linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure

Quando você cria um aplicativo lógico em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), seu aplicativo lógico tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que é executada em seu aplicativo lógico. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura validada pelo esquema de linguagem de definição de fluxo de trabalho. Essa referência fornece uma visão geral sobre essa estrutura e como o esquema define atributos na sua definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura de definição de fluxo de trabalho

Uma definição de fluxo de trabalho sempre inclui um gatilho para instanciar seu aplicativo lógico, além de uma ou mais ações executadas depois que o gatilho é acionado.

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
| `definition` | Sim | O elemento inicial para sua definição de fluxo de trabalho |
| `$schema` | Somente ao referenciar externamente uma definição de fluxo de trabalho | O local do arquivo de esquema JSON que descreve a versão da linguagem de definição de fluxo de trabalho, que pode ser encontrada aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Não | As definições para uma ou mais ações a serem executadas no tempo de execução do fluxo de trabalho. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo de ações: 250 |
| `contentVersion` | Não | O número de versão para sua definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. |
| `outputs` | Não | As definições para as saídas a serem retornadas de uma execução de fluxo de trabalho. Para obter mais informações, consulte [saídas](#outputs). <p><p>Máximo de saídas: 10 |
| `parameters` | Não | As definições para um ou mais parâmetros que passam os valores a serem usados no tempo de execução do aplicativo lógico. Para obter mais informações, consulte [parâmetros](#parameters). <p><p>Máximo de parâmetros: 50 |
| `staticResults` | Não | As definições para um ou mais resultados estáticos retornados por ações como saídas de simulação quando os resultados estáticos são habilitados nessas ações. Em cada definição de ação, o atributo `runtimeConfiguration.staticResult.name` referencia a definição correspondente dentro de `staticResults`. Para obter mais informações, consulte [resultados estáticos](#static-results). |
| `triggers` | Não | As definições para um ou mais gatilhos que instanciam seu fluxo de trabalho. Você pode definir mais de um gatilho, mas apenas com a linguagem de definição de fluxo de trabalho, não visualmente por meio do designer de aplicativos lógicos. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo de gatilhos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Gatilhos e ações

Em uma definição de fluxo de trabalho, as seções `triggers` e `actions` definem as chamadas que acontecem durante a execução do fluxo de trabalho. Para obter a sintaxe e mais informações sobre essas seções, consulte [gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida da implantação geralmente tem ambientes diferentes para desenvolvimento, teste, preparo e produção. Ao implantar aplicativos lógicos em vários ambientes, você provavelmente desejará usar valores diferentes, como cadeias de conexão, com base nas suas necessidades de implantação. Ou você pode ter valores que deseja reutilizar em todo o aplicativo lógico sem codificar ou alterar com frequência. Na seção `parameters` da sua definição de fluxo de trabalho, você pode definir ou editar parâmetros para os valores que seu aplicativo lógico usa no tempo de execução. Você deve definir esses parâmetros primeiro antes de poder referenciar esses parâmetros em outro lugar na sua definição de fluxo de trabalho.

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

| Atributo | Obrigatório | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*parameter-name*> | Sim | Cadeia | O nome do parâmetro que você deseja definir |
| <*parameter-type*> | Sim | int, float, String, bool, array, Object, SecureString, secureobject <p><p>**Observação**: para todas as senhas, chaves e segredos, use os tipos `securestring` ou `secureobject` porque a operação de `GET` não retorna esses tipos. Para obter mais informações sobre como proteger os parâmetros, consulte [recomendações de segurança para parâmetros de ação e de entrada](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | O tipo para o parâmetro |
| <*default-parameter-value*> | Sim | O mesmo que `type` | O valor de parâmetro padrão a ser usado se nenhum valor for especificado quando o fluxo de trabalho for instanciado. O atributo `defaultValue` é necessário para que o designer do aplicativo lógico possa mostrar corretamente o parâmetro, mas você pode especificar um valor vazio. |
| <*array-with-permitted-parameter-values*> | Não | Matriz | Uma matriz com valores que o parâmetro pode aceitar |
| <*parameter-description*> | Não | Objeto JSON | Quaisquer outros detalhes de parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md) para sua definição de fluxo de trabalho, defina os parâmetros de modelo que aceitam os valores desejados na implantação, substitua os valores codificados por referências aos parâmetros de definição de modelo ou de fluxo de trabalho, conforme apropriado, e armazene os valores a serem usados na implantação em um arquivo de [parâmetro](../azure-resource-manager/templates/parameter-files.md)separado. Dessa forma, você pode alterar esses valores mais facilmente por meio do arquivo de parâmetro sem precisar atualizar e reimplantar seu aplicativo lógico. Para obter informações confidenciais ou que devem ser protegidas, como nomes de usuários, senhas e segredos, você pode armazenar esses valores em Azure Key Vault e fazer com que o arquivo de parâmetros recupere esses valores do cofre de chaves. Para obter mais informações e exemplos sobre como definir parâmetros nos níveis de definição de modelo e de fluxo de trabalho, consulte [visão geral: automatizar a implantação para aplicativos lógicos com modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No atributo `staticResults`, defina a simulação de uma ação `outputs` e `status` que a ação retorna quando a configuração de resultado estático da ação está ativada. Na definição da ação, o atributo `runtimeConfiguration.staticResult.name` referencia o nome da definição de resultado estático dentro de `staticResults`. Saiba como você pode [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md).

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

| Atributo | Obrigatório | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*estático-Result-Definition-name*> | Sim | Cadeia | O nome de uma definição de resultado estático que uma definição de ação pode referenciar por meio de um objeto `runtimeConfiguration.staticResult`. Para obter mais informações, consulte [definições de configuração de tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Você pode usar qualquer nome exclusivo que desejar. Por padrão, esse nome exclusivo é acrescentado com um número, que é incrementado conforme necessário. |
| <*output-attributes-and-values-returned*> | Sim | Varia | Os requisitos para esses atributos variam de acordo com as diferentes condições. Por exemplo, quando o `status` é `Succeeded`, o atributo `outputs` inclui atributos e valores retornados como saídas de imitação pela ação. Se o `status` for `Failed`, o atributo `outputs` incluirá o atributo `errors`, que é uma matriz com um ou mais objetos de `message` de erro que têm informações de erro. |
| <*valores de cabeçalho*> | Não | JSON | Todos os valores de cabeçalho retornados pela ação |
| <*status-code-returned*> | Sim | Cadeia | O código de status retornado pela ação |
| <*action-status*> | Sim | Cadeia | O status da ação, por exemplo, `Succeeded` ou `Failed` |
|||||

Por exemplo, nessa definição de ação HTTP, o atributo `runtimeConfiguration.staticResult.name` faz referência `HTTP0` dentro do atributo `staticResults` em que as saídas de simulação para a ação são definidas. O atributo `runtimeConfiguration.staticResult.staticResultOptions` especifica que a configuração de resultado estático é `Enabled` na ação HTTP.

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

A ação HTTP retorna as saídas na definição de `HTTP0` dentro de `staticResults`. Neste exemplo, para o código de status, a saída de simulação é `OK`. Para valores de cabeçalho, a saída de simulação é `"Content-Type": "application/JSON"`. Para o status da ação, a saída da simulação é `Succeeded`.

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

Com o JSON, você pode ter valores literais que existem em tempo de design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Você também pode ter valores que não existem até o tempo de execução. Para representar esses valores, você pode usar *expressões*que são avaliadas em tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções](#functions), [operadores](#operators), variáveis, valores explícitos ou constantes. Em sua definição de fluxo de trabalho, você pode usar uma expressão em qualquer lugar em um valor de cadeia de caracteres JSON prefixando a expressão com o sinal de arroba (\@). Ao avaliar uma expressão que representa um valor JSON, o corpo da expressão é extraído removendo o \@ caractere e sempre resulta em outro valor JSON.

Por exemplo, para a propriedade de `customerName` definida anteriormente, você pode obter o valor da propriedade usando a função [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão e atribuir esse valor à propriedade `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

A *interpolação de cadeia de caracteres* também permite que você use várias expressões dentro de cadeias de caracteres que são encapsuladas pelo caractere de \@ e chaves ({}). Aqui está a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado é sempre uma cadeia de caracteres, tornando esse recurso semelhante à função `concat()`, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se você tiver uma cadeia de caracteres literal que começa com o caractere \@, Prefixe o caractere \@ com outro caractere \@ como um caractere de escape: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Resultado |
|------------|--------|
| "Sophia Owen" | Retornar estes caracteres: ' Sophia Owen ' |
| "matriz [1]" | Retornar estes caracteres: ' array [1] ' |
| "\@\@" | Retornar estes caracteres como uma cadeia de caracteres de um caractere: '\@' |
| " \@" | Retornar estes caracteres como uma cadeia de dois caracteres: ' \@' |
|||

Para esses exemplos, suponha que você defina "myBirthMonth" igual a "janeiro" e "myAge" igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as seguintes expressões são avaliadas:

| Expressão JSON | Resultado |
|-----------------|--------|
| "parâmetros de\@(' myBirthMonth ')" | Retornar esta cadeia de caracteres: "janeiro" |
| "\@{Parameters (' myBirthMonth ')}" | Retornar esta cadeia de caracteres: "janeiro" |
| "\@parameters('myAge')" | Retornar este número: 42 |
| "\@{parameters('myAge')}" | Retornar este número como uma cadeia de caracteres: "42" |
| "Minha idade é \@{Parameters (' myAge ')}" | Retornar esta cadeia de caracteres: "minha idade é 42" |
| "\@Concat (' my age is ', String (Parameters (' myAge '))" | Retornar esta cadeia de caracteres: "minha idade é 42" |
| "Minha idade é \@\@{Parameters (' myAge ')}" | Retornar esta cadeia de caracteres, que inclui a expressão: "minha idade é \@{Parameters (' myAge ')} ' |
|||

Quando você estiver trabalhando visualmente no designer de aplicativos lógicos, poderá criar expressões por meio do construtor de expressões, por exemplo:

![Designer de aplicativos lógicos > Construtor de expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando terminar, a expressão aparecerá para a propriedade correspondente na definição do fluxo de trabalho, por exemplo, a propriedade `searchQuery` aqui:

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

Na seção `outputs`, defina os dados que seu fluxo de trabalho pode retornar ao concluir a execução. Por exemplo, para controlar um status ou valor específico de cada execução, especifique que a saída do fluxo de trabalho retorne esses dados.

> [!NOTE]
> Ao responder a solicitações de entrada da API REST de um serviço, não use `outputs`. Em vez disso, use o tipo de ação `Response`. Para obter mais informações, consulte [gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

Aqui está a estrutura geral para uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atributo | Obrigatório | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*key-name*> | Sim | Cadeia | O nome da chave para o valor de retorno de saída |
| <*key-type*> | Sim | int, float, string, securestring, bool, array, objeto JSON | O tipo para o valor de retorno de saída |
| <*chave-valor*> | Sim | Mesmo que <*key-type*> | O valor de retorno de saída |
|||||

Para obter a saída de uma execução de fluxo de trabalho, examine o histórico de execução do aplicativo lógico e os detalhes no portal do Azure ou use a [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, por exemplo, Power BI para que você possa criar painéis.

<a name="operators"></a>

## <a name="operators"></a>Operadores

Em [expressões](#expressions) e [funções](#functions), os operadores executam tarefas específicas, como fazer referência a uma propriedade ou um valor em uma matriz.

| Operador | Tarefa |
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, empacote a cadeia de caracteres somente com aspas simples, por exemplo, `'<myString>'`. Não use aspas duplas (""), que entram em conflito com a formatação JSON em uma expressão inteira. Por exemplo: <p>**Yes**: length('Hello') </br>**Não**: comprimento ("Olá") <p>Quando você passa matrizes ou números, não precisa de Pontuação de quebra automática. Por exemplo: <p>**Sim**: comprimento ([1, 2, 3]) </br>**Não**: comprimento ("[1, 2, 3]") |
| [] | Para fazer referência a um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item em uma matriz: <p>`myArray[1]` |
| . | Para fazer referência a uma propriedade em um objeto, use o operador ponto. Por exemplo, para obter a propriedade `name` para um objeto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Para fazer referência a Propriedades nulas em um objeto sem um erro de tempo de execução, use o operador de ponto de interrogação. Por exemplo, para tratar saídas nulas de um gatilho, você pode usar esta expressão: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm seus valores de ações de tempo de execução que talvez ainda não existam quando sua definição de fluxo de trabalho começa a ser executada. Para fazer referência ou trabalhar com esses valores em expressões, você pode usar as [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) que a linguagem de definição de fluxo de trabalho fornece.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [gatilhos e ações da linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Saiba como criar e gerenciar programaticamente aplicativos lógicos com a [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
