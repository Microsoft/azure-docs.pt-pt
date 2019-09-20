---
title: Referência de esquema para linguagem de definição de fluxo de trabalho – aplicativos lógicos do Azure
description: Guia de referência para esquema de linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: c84791cb30622350b3e6d6356abd4580636c4ddf
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385341"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de esquema para linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure

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

| Atributo | Requerido | Descrição |
|-----------|----------|-------------|
| `definition` | Sim | O elemento inicial para sua definição de fluxo de trabalho |
| `$schema` | Somente ao referenciar externamente uma definição de fluxo de trabalho | O local do arquivo de esquema JSON que descreve a versão da linguagem de definição de fluxo de trabalho, que pode ser encontrada aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Não | As definições para uma ou mais ações a serem executadas no tempo de execução do fluxo de trabalho. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo de ações: 250 |
| `contentVersion` | Não | O número de versão para sua definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. |
| `outputs` | Não | As definições para as saídas a serem retornadas de uma execução de fluxo de trabalho. Para obter mais informações, consulte [saídas](#outputs). <p><p>Máximo de saídas: 10 |
| `parameters` | Não | As definições para um ou mais parâmetros que passam os valores a serem usados no tempo de execução do aplicativo lógico. Para obter mais informações, consulte [parâmetros](#parameters). <p><p>Máximo de parâmetros: 50 |
| `staticResults` | Não | As definições para um ou mais resultados estáticos retornados por ações como saídas de simulação quando os resultados estáticos são habilitados nessas ações. Em cada definição de ação, `runtimeConfiguration.staticResult.name` o atributo faz referência à definição `staticResults`correspondente dentro de. Para obter mais informações, consulte [resultados estáticos](#static-results). |
| `triggers` | Não | As definições para um ou mais gatilhos que instanciam seu fluxo de trabalho. Você pode definir mais de um gatilho, mas apenas com a linguagem de definição de fluxo de trabalho, não visualmente por meio do designer de aplicativos lógicos. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo de gatilhos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Acionadores e ações

Em uma definição de fluxo de `triggers` trabalho `actions` , as seções e definem as chamadas que acontecem durante a execução do fluxo de trabalho. Para obter a sintaxe e mais informações sobre essas seções, consulte [gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida da implantação geralmente tem ambientes diferentes para desenvolvimento, teste, preparo e produção. Ao implantar aplicativos lógicos em vários ambientes, você provavelmente desejará usar valores diferentes, como cadeias de conexão, com base nas suas necessidades de implantação. Ou você pode ter valores que deseja reutilizar em todo o aplicativo lógico sem codificar ou alterar com frequência. Na `parameters` seção de sua definição de fluxo de trabalho, você pode definir ou editar parâmetros para os valores que seu aplicativo lógico usa no tempo de execução. Você deve definir esses parâmetros primeiro antes de poder referenciar esses parâmetros em outro lugar na sua definição de fluxo de trabalho.

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

| Atributo | Requerido | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*parameter-name*> | Sim | String | O nome do parâmetro que você deseja definir |
| <*parameter-type*> | Sim | int, float, String, bool, array, Object, SecureString, secureobject <p><p>**Nota**: Para todas as senhas, chaves e segredos, use os `securestring` tipos `secureobject` ou porque a `GET` operação não retorna esses tipos. Para obter mais informações sobre como proteger os parâmetros, consulte [recomendações de segurança para parâmetros de ação e de entrada](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | O tipo para o parâmetro |
| <*default-parameter-value*> | Sim | Mesmo que`type` | O valor de parâmetro padrão a ser usado se nenhum valor for especificado quando o fluxo de trabalho for instanciado. O `defaultValue` atributo é necessário para que o designer do aplicativo lógico possa mostrar corretamente o parâmetro, mas você pode especificar um valor vazio. |
| <*array-with-permitted-parameter-values*> | Não | Array | Uma matriz com valores que o parâmetro pode aceitar |
| <*parameter-description*> | Não | Objeto JSON | Quaisquer outros detalhes de parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para sua definição de fluxo de trabalho, defina os parâmetros de modelo que aceitam os valores desejados na implantação, substitua os valores codificados por referências aos parâmetros de definição de modelo ou de fluxo de trabalho como apropriado, e armazene os valores a serem usados na implantação em um [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)separado. Dessa forma, você pode alterar esses valores mais facilmente por meio do arquivo de parâmetro sem precisar atualizar e reimplantar seu aplicativo lógico. Para obter informações confidenciais ou que devem ser protegidas, como nomes de usuários, senhas e segredos, você pode armazenar esses valores em Azure Key Vault e fazer com que o arquivo de parâmetros recupere esses valores do cofre de chaves. Para obter mais informações e exemplos sobre como definir parâmetros nos níveis de definição do modelo e [do fluxo de trabalho, consulte Visão geral: Automatize a implantação de aplicativos lógicos com modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Azure Resource Manager.

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No atributo, defina a simulação `outputs` de uma ação e `status` a ação retornará quando a configuração de resultado estático da ação estiver ativada. `staticResults` Na definição da ação, o `runtimeConfiguration.staticResult.name` atributo faz referência ao nome da definição de resultado estática dentro. `staticResults` Saiba como você pode [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md).

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

| Atributo | Requerido | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*static-Result-Definition-Name*> | Sim | Cadeia | O nome de uma definição de resultado estático que uma definição de ação pode referenciar por meio de um `runtimeConfiguration.staticResult` objeto. Para obter mais informações, consulte [definições de configuração de tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Você pode usar qualquer nome exclusivo que desejar. Por padrão, esse nome exclusivo é acrescentado com um número, que é incrementado conforme necessário. |
| <*output-attributes-and-values-returned*> | Sim | Varia | Os requisitos para esses atributos variam de acordo com as diferentes condições. Por exemplo, quando o `status` é `Succeeded`, o `outputs` atributo inclui atributos e valores retornados como saídas de imitação pela ação. `status` `message` Se for `errors` , o `outputs` atributo incluirá o atributo, que é uma matriz com um ou mais objetos de erro que têm informações de erro. `Failed` |
| <*valores de cabeçalho*> | Não | JSON | Todos os valores de cabeçalho retornados pela ação |
| <*status-code-returned*> | Sim | Cadeia | O código de status retornado pela ação |
| <*action-status*> | Sim | Cadeia | O status da ação, por exemplo, `Succeeded` ou`Failed` |
|||||

Por exemplo, nessa definição de ação http, o `runtimeConfiguration.staticResult.name` atributo faz `HTTP0` referência dentro `staticResults` do atributo onde as saídas de simulação para a ação são definidas. O `runtimeConfiguration.staticResult.staticResultOptions` atributo especifica que a configuração de resultado estático `Enabled` está na ação http.

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

A ação http retorna as saídas na definição `HTTP0` dentro `staticResults`de. Neste exemplo, para o código de status, a saída de simulação `OK`é. Para valores de cabeçalho, a saída de `"Content-Type": "application/JSON"`simulação é. Para o status da ação, a saída da simulação `Succeeded`é.

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

Você também pode ter valores que não existem até o tempo de execução. Para representar esses valores, você pode usar *expressões*que são avaliadas em tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções](#functions), [operadores](#operators), variáveis, valores explícitos ou constantes. Em sua definição de fluxo de trabalho, você pode usar uma expressão em qualquer lugar em um valor de cadeia de caracteres JSON prefixando\@a expressão com o sinal de arroba (). Ao avaliar uma expressão que representa um valor JSON, o corpo da expressão é extraído removendo o \@ caractere e sempre resulta em outro valor JSON.

Por exemplo, para a propriedade definida `customerName` anteriormente, você pode obter o valor da propriedade usando a função Parameters [()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão e atribuir `accountName` esse valor à propriedade:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

A interpolação de *cadeia de caracteres* também permite que você use várias expressões dentro \@ de cadeias de caracteres que{}são encapsuladas pelo caractere e chaves (). Aqui está a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado é sempre uma cadeia de caracteres, tornando esse recurso semelhante `concat()` à função, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se você tiver uma cadeia de caracteres literal que começa \@ com o caractere, \@ Prefixe o \@ caractere com outro caractere como um caractere de escape:\@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Resultado |
|------------|--------|
| "Sophia Owen" | Retornar estes caracteres: ' Sophia Owen ' |
| "matriz [1]" | Retornar estes caracteres: ' array [1] ' |
| "\@\@" | Retornar estes caracteres como uma cadeia de caracteres de um caractere\@: ' ' |
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
| "\@Parameters (' myBirthMonth ')" | Retornar esta cadeia de caracteres: 1o |
| "\@{Parameters (' myBirthMonth ')}" | Retornar esta cadeia de caracteres: 1o |
| "\@parameters('myAge')" | Retornar este número: 42 |
| "\@{parameters('myAge')}" | Retornar este número como uma cadeia de caracteres: "42" |
| "Minha idade é \@{Parameters (' myAge ')}" | Retornar esta cadeia de caracteres: "Minha idade é 42" |
| "\@Concat (' my age is ', String (Parameters (' myAge ')))" | Retornar esta cadeia de caracteres: "Minha idade é 42" |
| "Minha idade é \@ \@{Parameters (' myAge ')}" | Retorne esta cadeia de caracteres, que inclui a expressão: "Minha idade é \@{Parameters (' myAge ')} ' |
|||

Quando você estiver trabalhando visualmente no designer de aplicativos lógicos, poderá criar expressões por meio do construtor de expressões, por exemplo:

![Designer de aplicativos lógicos > Construtor de expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando terminar, a expressão aparecerá para a propriedade correspondente na definição do fluxo de trabalho, por exemplo, `searchQuery` a propriedade aqui:

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

## <a name="outputs"></a>outputs

`outputs` Na seção, defina os dados que seu fluxo de trabalho pode retornar ao concluir a execução. Por exemplo, para controlar um status ou valor específico de cada execução, especifique que a saída do fluxo de trabalho retorne esses dados.

> [!NOTE]
> Ao responder a solicitações de entrada da API REST de um serviço, não use `outputs`. Em vez disso, `Response` use o tipo de ação. Para obter mais informações, consulte [gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

Aqui está a estrutura geral para uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Atributo | Requerido | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*key-name*> | Sim | Cadeia | O nome da chave para o valor de retorno de saída |
| <*key-type*> | Sim | int, float, string, securestring, bool, array, objeto JSON | O tipo para o valor de retorno de saída |
| <*chave-valor*> | Sim | Mesmo que <*key-type*> | O valor de retorno de saída |
|||||

Para obter a saída de uma execução de fluxo de trabalho, examine o histórico de execução do aplicativo lógico e os detalhes no portal do Azure ou use a [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, por exemplo, Power BI para que você possa criar painéis.

<a name="operators"></a>

## <a name="operators"></a>Operadores

Em [expressões](#expressions) e [funções](#functions), os operadores executam tarefas específicas, como fazer referência a uma propriedade ou um valor em uma matriz.

| Operator | Tarefa |
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, empacote a cadeia de caracteres somente com aspas simples `'<myString>'`, por exemplo,. Não use aspas duplas (""), que entram em conflito com a formatação JSON em uma expressão inteira. Por exemplo: <p>**Yes**: length('Hello') </br>**Não**: comprimento ("Olá") <p>Quando você passa matrizes ou números, não precisa de Pontuação de quebra automática. Por exemplo: <p>**Sim**: comprimento ([1, 2, 3]) </br>**Não**: comprimento ("[1, 2, 3]") |
| [] | Para fazer referência a um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item em uma matriz: <p>`myArray[1]` |
| . | Para fazer referência a uma propriedade em um objeto, use o operador ponto. Por exemplo, para obter a `name` propriedade para um `customer` objeto JSON: <p>`"@parameters('customer').name"` |
| ? | Para fazer referência a Propriedades nulas em um objeto sem um erro de tempo de execução, use o operador de ponto de interrogação. Por exemplo, para tratar saídas nulas de um gatilho, você pode usar esta expressão: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Algumas expressões obtêm seus valores de ações de tempo de execução que talvez ainda não existam quando sua definição de fluxo de trabalho começa a ser executada. Para fazer referência ou trabalhar com esses valores em expressões, você pode usar as [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) que a linguagem de definição de fluxo de trabalho fornece.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [gatilhos e ações da linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Saiba como criar e gerenciar programaticamente aplicativos lógicos com a [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
