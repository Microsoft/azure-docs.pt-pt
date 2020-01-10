---
title: Guia de referência para funções em expressões
description: Guia de referência para funções em expressões para aplicativos lógicos do Azure e automação de energia
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: f1fd776f903b4f5b4f315147f460143c7843fdc9
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689063"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Guia de referência para usar funções em expressões para aplicativos lógicos do Azure e automatizar energia

Para definições de fluxo de trabalho em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e [automatização de energia](https://docs.microsoft.com/flow/getting-started), algumas [expressões](../logic-apps/logic-apps-workflow-definition-language.md#expressions) obtêm seus valores de ações de tempo de execução que talvez ainda não existam quando o fluxo de trabalho começa a ser executado. Para fazer referência a esses valores ou processar os valores nessas expressões, você pode usar *funções* fornecidas pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Essa página de referência se aplica aos aplicativos lógicos do Azure e à automatização de energia, mas aparece na documentação dos aplicativos lógicos do Azure. Embora essa página se refira especificamente aos aplicativos lógicos, essas funções funcionam tanto para os fluxos quanto para os aplicativos lógicos. Para obter mais informações sobre funções e expressões na automatização de energia, consulte [usar expressões em condições](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Por exemplo, você pode calcular valores usando funções matemáticas, como a [função Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add), quando você deseja a soma de inteiros ou floats. Aqui estão outras tarefas de exemplo que você pode executar com o Functions:

| Tarefa | Sintaxe da função | Resultado |
| ---- | --------------- | ------ |
| Retornar uma cadeia de caracteres em formato em minúsculas. | toLower('<*text*>') <p>Por exemplo: toLower (' Olá ') | Oi |
| Retornar um GUID (identificador global exclusivo). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Para localizar funções [com base em sua finalidade geral](#ordered-by-purpose), examine as tabelas a seguir. Ou, para obter informações detalhadas sobre cada função, consulte a [lista alfabética](#alphabetical-list).

> [!NOTE]
> Na sintaxe para definições de parâmetro, um ponto de interrogação (?) que aparece após um parâmetro significa que o parâmetro é opcional.
> Por exemplo, consulte [Getfuturtime ()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funções em expressões

Para mostrar como usar uma função em uma expressão, este exemplo mostra como você pode obter o valor do parâmetro `customerName` e atribuir esse valor à propriedade `accountName` usando a função [Parameters ()](#parameters) em uma expressão:

```json
"accountName": "@parameters('customerName')"
```

Aqui estão algumas outras maneiras gerais de usar funções em expressões:

| Tarefa | Sintaxe de função em uma expressão |
| ---- | -------------------------------- |
| Execute o trabalho com um item passando esse item para uma função. | "\@<*functionname*> (<*Item*>)" |
| 1. Obtenha o valor de *ParameterName*usando a função de `parameters()` aninhada. </br>2. Execute o trabalho com o resultado passando esse valor para *FunctionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Obtenha o resultado da função interna aninhada *FunctionName*. </br>2. passe o resultado para a função externa *functionName2*. | "\@<*functionName2*> (<*FunctionName*> (<*Item*>))" |
| 1. Obtenha o resultado de *FunctionName*. </br>2. Considerando que o resultado é um objeto com property *PropertyName*, obtenha o valor dessa propriedade. | "\@<*functionname*> (<*Item*>). <*PropertyName*>" |
|||

Por exemplo, a função `concat()` pode usar dois ou mais valores de cadeia de caracteres como parâmetros. Essa função combina essas cadeias de caracteres em uma cadeia.
Você pode passar literais de cadeia de caracteres, por exemplo, "Sophia" e "Owen" para obter uma cadeia de caracteres combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ou, você pode obter valores de cadeia de caracteres de parâmetros. Este exemplo usa a função `parameters()` em cada parâmetro `concat()` e os parâmetros `firstName` e `lastName`. Em seguida, você passa as cadeias de caracteres resultantes para a função `concat()` para que você obtenha uma cadeia de caracteres combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, ambos os exemplos atribuem o resultado à propriedade `customerName`.

Aqui estão as funções disponíveis ordenadas por sua finalidade geral ou você pode procurar as funções com base em [ordem alfabética](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funções de cadeia

Para trabalhar com cadeias de caracteres, você pode usar essas funções de cadeia de caracteres e também algumas [funções de coleta](#collection-functions).
As funções de cadeia de caracteres funcionam apenas em cadeias de caracteres.

| Função de cadeia de caracteres | Tarefa |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combine duas ou mais cadeias de caracteres e retorne a cadeia combinada. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifique se uma cadeia de caracteres termina com a subcadeia de caracteres especificada. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Retornar um número como uma cadeia de caracteres com base no formato especificado |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Retorna a posição inicial de uma subcadeia de caracteres. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retorna a posição inicial da última ocorrência de uma subcadeia de caracteres. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substitui uma subcadeia de caracteres pela cadeia de caracteres especificada e retorna a cadeia de caracteres atualizada. |
| [dividir](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retorna uma matriz que contém subcadeias de caracteres, separadas por vírgulas, de uma cadeia de caracteres maior com base em um caractere delimitador especificado na cadeia de caracteres original. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verifique se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Retornar caracteres de uma cadeia de caracteres, começando da posição especificada. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retornar uma cadeia de caracteres em formato em minúsculas. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retornar uma cadeia de caracteres em formato maiúsculo. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remova o espaço em branco à esquerda e à direita de uma cadeia de caracteres e retorne a cadeia de caracteres atualizada. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, geralmente matrizes, cadeias de caracteres e, às vezes, dicionários, você pode usar essas funções de coleção.

| Função de coleção | Tarefa |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifique se uma coleção tem um item específico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Retornar o primeiro item de uma coleção. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retorna uma coleção que tem *apenas* os itens comuns nas coleções especificadas. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando dentro de uma ação repetida em uma matriz, retorna o item atual na matriz durante a iteração atual da ação. |
| [aderir](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retornar uma cadeia de caracteres que tenha *todos* os itens de uma matriz, separados pelo caractere especificado. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retorna o último item de uma coleção. |
| [Comprimento](../logic-apps/workflow-definition-language-functions-reference.md#length) | Retornar o número de itens em uma cadeia de caracteres ou matriz. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Remover itens da frente de uma coleção e retornar todos os *outros* itens. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Retornar itens da frente de uma coleção. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retorna uma coleção que tem *todos* os itens das coleções especificadas. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funções de comparação lógica

Para trabalhar com condições, comparar valores e resultados de expressão ou avaliar vários tipos de lógica, você pode usar essas funções de comparação lógica.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [e](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifique se todas as expressões são verdadeiras. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifique se o primeiro valor é maior que o segundo valor. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, retorna um valor especificado. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifique se o primeiro valor é menor que o segundo valor. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifique se o primeiro valor é menor ou igual ao segundo valor. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifique se uma expressão é falsa. |
| [ou](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou o formato de um valor, você pode usar essas funções de conversão.
Por exemplo, você pode alterar um valor de um booliano para um inteiro.
Para obter mais informações sobre como os aplicativos lógicos tratam tipos de conteúdo durante a conversão, consulte [manipular tipos de conteúdo](../logic-apps/logic-apps-content-type.md).
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retornar uma matriz de uma única entrada especificada. Para várias entradas, consulte [CreateArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retornar a versão codificada em base64 para uma cadeia de caracteres. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retornar a versão binária para uma cadeia de caracteres codificada em base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [binário](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retornar a versão binária para um valor de entrada. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retornar a versão booliana para um valor de entrada. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Retornar o URI de dados para um valor de entrada. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retornar a versão binária para um URI de dados. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retornar a versão da cadeia de caracteres para um URI de dados. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retornar a versão binária para um URI de dados. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retorne uma cadeia de caracteres que substitui o caractere de escape por versões decodificadas. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres não seguros de URL por caracteres de escape. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Retornar um número de ponto flutuante para um valor de entrada. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retornar a versão de inteiro para uma cadeia de caracteres. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retorna o valor ou o objeto do tipo JavaScript Object Notation (JSON) para uma cadeia de caracteres ou XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retornar a versão da cadeia de caracteres para um valor de entrada. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retorne a versão codificada em URI para um valor de entrada, substituindo caracteres não seguros por URL por caracteres de escape. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retornar a versão binária para uma cadeia de caracteres codificada em URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada em URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Retornar a versão XML de uma cadeia de caracteres. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funções matemáticas

Para trabalhar com inteiros e floats, você pode usar essas funções matemáticas.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função Math | Tarefa |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retornar o resultado da adição de dois números. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retornar o resultado da divisão de dois números. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retorna o valor mais alto de um conjunto de números ou uma matriz. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retorna o valor mais baixo de um conjunto de números ou uma matriz. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retornar o resto da divisão de dois números. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retorne o produto da multiplicação de dois números. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retornar um inteiro aleatório de um intervalo especificado. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retornar uma matriz de inteiros que inicia de um número inteiro especificado. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retorne o resultado da subtração do segundo número do primeiro número. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horas, você pode usar essas funções de data e hora.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicione um número de dias a um carimbo de data/hora. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicione um número de horas a um carimbo de data/hora. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicione um número de minutos a um carimbo de data/hora. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicione um número de segundos a um carimbo de data/hora. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adicione um número de unidades de tempo a um carimbo de data/hora. Consulte também [Getfuturtime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converta um carimbo de data/hora de UTC (tempo Universal Coordenado) para o fuso horário de destino. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converta um carimbo de data/hora do fuso horário de origem para o fuso horário de destino. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converta um carimbo de data/hora do fuso horário de origem em UTC (tempo Universal Coordenado). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retorna o dia do componente do mês de um carimbo de data/hora. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retorna o dia do componente da semana de um carimbo de data/hora. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retorna o dia do componente de ano de um carimbo de data/hora. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Retornar a data de um carimbo de hora. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. Consulte também [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retorna o carimbo de data/hora atual menos as unidades de tempo especificadas. Consulte também [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Retorna o início do dia para um carimbo de data/hora. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Retornar o início da hora para um carimbo de data/hora. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retornar o início do mês para um carimbo de data/hora. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtraia um número de unidades de tempo de um carimbo de data/hora. Veja também [Getpastetime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [tiques](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Retorna o valor da propriedade de `ticks` para um carimbo de data/hora especificado. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Retornar o carimbo de data/hora atual como uma cadeia de caracteres. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funções de fluxo de trabalho

Essas funções de fluxo de trabalho podem ajudá-lo a:

* Obtenha detalhes sobre uma instância de fluxo de trabalho em tempo de execução.
* Trabalhe com as entradas usadas para criar uma instância de aplicativos lógicos ou fluxos.
* Referencie as saídas de gatilhos e ações.

Por exemplo, você pode referenciar as saídas de uma ação e usar esses dados em uma ação posterior.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de fluxo de trabalho | Tarefa |
| ----------------- | ---- |
| [Action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Retornar a saída da ação atual em tempo de execução ou valores de outros pares de nome e valor JSON. Consulte também [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Retornar a saída de `body` de uma ação em tempo de execução. Consulte também [corpo](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Retornar a saída de uma ação em tempo de execução. Consulte [saídas](../logic-apps/workflow-definition-language-functions-reference.md#outputs) e [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Retornar a saída de uma ação em tempo de execução ou valores de outros pares nome-e-valor JSON. Consulte também [ação](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [conteúdo](#body) | Retornar a saída de `body` de uma ação em tempo de execução. Consulte também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Crie uma matriz com os valores que correspondem a um nome de chave em *dados de formulário* ou saídas *de ação codificadas por formulário* . |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Retornar um único valor que corresponda a um nome de chave na saída de *formulário* de uma ação ou *codificação de formulário*. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando dentro de uma ação repetida em uma matriz, retorna o item atual na matriz durante a iteração atual da ação. |
| [los](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando dentro de um loop foreach ou until, retorna o item atual do loop especificado.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Quando dentro de um loop Until, retorna o valor de índice para a iteração atual. Você pode usar essa função dentro de loops aninhados until. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Retornar a "URL de retorno de chamada" que chama um gatilho ou uma ação. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Retorna o corpo de uma parte específica na saída de uma ação que tem várias partes. |
| [produz](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Retornar a saída de uma ação em tempo de execução. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retorne o valor para um parâmetro descrito em sua definição de fluxo de trabalho. |
| [disso](../logic-apps/workflow-definition-language-functions-reference.md#result) | Retornar as entradas e saídas de todas as ações dentro da ação com escopo especificado, como `For_each`, `Until`e `Scope`. |
| [of](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Retornar a saída de um gatilho em tempo de execução ou de outros pares de nome e valor JSON. Consulte também [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Retornar a saída de `body` de um gatilho em tempo de execução. Consulte [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Retornar um único valor correspondente a um nome de chave em *dados de formulário* ou saídas *de gatilho codificadas por formulário* . |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Retorna o corpo de uma parte específica na saída de várias partes de um gatilho. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crie uma matriz cujos valores correspondem a um nome de chave em *dados de formulário* ou saídas *de gatilho codificadas por formulário* . |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retornar a saída de um gatilho em tempo de execução ou valores de outros pares de nome e valor JSON. Consulte [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Retorna o valor de uma variável especificada. |
| [modelo](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Retornar todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funções de análise de URI

Para trabalhar com URIs (identificadores de recursos uniformes) e obter vários valores de propriedade para esses URIs, você pode usar essas funções de análise de URI.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de análise de URI | Tarefa |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Retornar o valor de `host` para um URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Retornar o valor de `path` para um URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Retorne os valores de `path` e `query` para um URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Retornar o valor de `port` para um URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Retornar o valor de `query` para um URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Retornar o valor de `scheme` para um URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funções de manipulação: JSON & XML

Para trabalhar com objetos JSON e nós XML, você pode usar essas funções de manipulação.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de manipulação | Tarefa |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicione uma propriedade e seu valor, ou par nome-valor, a um objeto JSON, e retorne o objeto atualizado. |
| [COALESCE](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retorna o primeiro valor não nulo de um ou mais parâmetros. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Remove uma propriedade de um objeto JSON e retorna o objeto atualizado. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Defina o valor para a propriedade de um objeto JSON e retorne o objeto atualizado. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Marque XML para nós ou valores que correspondam a uma expressão XPath (linguagem de caminho XML) e retorne os nós ou valores correspondentes. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Todas as funções – lista alfabética

Esta seção lista todas as funções disponíveis em ordem alfabética.

<a name="action"></a>

### <a name="action"></a>action

Retornar a saída da ação *atual* em tempo de execução ou valores de outros pares nome-e-valor JSON, que você pode atribuir a uma expressão.
Por padrão, essa função faz referência a todo o objeto Action, mas você pode opcionalmente especificar uma propriedade cujo valor você deseja.
Consulte também [ações ()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Você pode usar a função `action()` somente nestes locais:

* A propriedade `unsubscribe` para uma ação de webhook para que você possa acessar o resultado da solicitação de `subscribe` original
* A propriedade `trackedProperties` para uma ação
* A condição de loop de `do-until` para uma ação

```
action()
action().outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *propriedade* <> | Não | String | O nome da Propriedade do objeto de ação cujo valor você deseja: **nome**, **iníciotime**, **EndTime**, **entradas**, **saídas**, **status**, **código**, **TrackingID**e **clientTrackingId**. No portal do Azure, você pode encontrar essas propriedades revisando os detalhes de um histórico de execução específico. Para obter mais informações, consulte [API REST-ações de execução do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*action-output*> | String | A saída da ação ou da propriedade atual |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Retornar a saída de `body` de uma ação em tempo de execução.
Abreviação para `actions('<actionName>').outputs.body`.
Consulte [Body ()](#body) e [Actions ()](#actions).

```
actionBody('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da saída de `body` da ação que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída `body` da ação do Twitter `Get user`:

```
actionBody('Get_user')
```

E retorna esse resultado:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Retornar a saída de uma ação em tempo de execução.  e é abreviado para `actions('<actionName>').outputs`. Consulte [Actions ()](#actions). A função `actionOutputs()` resolve para `outputs()` no designer do aplicativo lógico, portanto, considere o uso de [Outputs ()](#outputs), em vez de `actionOutputs()`. Embora ambas as funções funcionem da mesma maneira, `outputs()` é preferida.

```
actionOutputs('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da saída da ação que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*output*> | String | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída da ação do Twitter `Get user`:

```
actionOutputs('Get_user')
```

E retorna esse resultado:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>ações

Retornar a saída de uma ação em tempo de execução ou valores de outros pares nome-e-valor JSON, que você pode atribuir a uma expressão. Por padrão, a função faz referência a todo o objeto Action, mas você pode opcionalmente especificar uma propriedade cujo valor você deseja.
Para versões abreviadas, consulte [actionBody ()](#actionBody), [actionOutputs ()](#actionOutputs)e [Body ()](#body).
Para a ação atual, consulte [Action ()](#action).

> [!NOTE]
> Anteriormente, você poderia usar a função `actions()` ou o elemento `conditions` ao especificar que uma ação foi executada com base na saída de outra ação. No entanto, para declarar dependências explicitamente entre as ações, agora você deve usar a propriedade `runAfter` da ação dependente.
> Para saber mais sobre a propriedade `runAfter`, consulte [detectar e tratar falhas com a propriedade runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome do objeto de ação cuja saída você deseja  |
| *propriedade* <> | Não | String | O nome da Propriedade do objeto de ação cujo valor você deseja: **nome**, **iníciotime**, **EndTime**, **entradas**, **saídas**, **status**, **código**, **TrackingID**e **clientTrackingId**. No portal do Azure, você pode encontrar essas propriedades revisando os detalhes de um histórico de execução específico. Para obter mais informações, consulte [API REST-ações de execução do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*action-output*> | String | A saída da ação ou da propriedade especificada |
||||

*Exemplo*

Este exemplo obtém o valor da propriedade `status` da ação do Twitter `Get user` em tempo de execução:

```
actions('Get_user').outputs.body.status
```

E retorna este resultado: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>adicionar

Retornar o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sim | Inteiro, float ou misto | Os números a serem adicionados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*result-sum*> | Inteiro ou float | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E retorna este resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione um número de dias a um carimbo de data/hora.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*dias*> | Sim | Número inteiro | O número positivo ou negativo de dias para adicionar |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de dias especificado  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias ao carimbo de data/hora especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E retorna este resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias do carimbo de data/hora especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E retorna este resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione um número de horas a um carimbo de data/hora.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas para adicionar |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas ao carimbo de data/hora especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas do carimbo de data/hora especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione um número de minutos a um carimbo de data/hora.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a serem adicionados |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos ao carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos do carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E retorna este resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adicione uma propriedade e seu valor, ou par nome-valor, a um objeto JSON, e retorne o objeto atualizado. Se a propriedade já existir em tempo de execução, a função falhará e lançará um erro.

```
addProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON em que você deseja adicionar uma propriedade |
| *propriedade* <> | Sim | String | O nome da propriedade a ser adicionada |
| <*valor*> | Sim | Qualquer | O valor da propriedade |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado com a propriedade especificada |
||||

Para adicionar uma propriedade filho a uma propriedade existente, use esta sintaxe:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON em que você deseja adicionar uma propriedade |
| > *de propriedade pai* < | Sim | String | O nome da propriedade pai em que você deseja adicionar a propriedade filho |
| <> *de propriedade filho* | Sim | String | O nome da propriedade filho a ser adicionada |
| <*valor*> | Sim | Qualquer | O valor a ser definido para a propriedade especificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo adiciona a propriedade `middleName` a um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) . O objeto já inclui as propriedades `firstName` e `surName`. A função atribui o valor especificado à nova propriedade e retorna o objeto atualizado:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Este é o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Este é o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo adiciona a propriedade filho `middleName` à propriedade `customerName` existente em um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) . A função atribui o valor especificado à nova propriedade e retorna o objeto atualizado:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Este é o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Este é o objeto JSON atualizado:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicione um número de segundos a um carimbo de data/hora.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a serem adicionados |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de segundos especificado  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para o carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E retorna este resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicione um número de unidades de tempo a um carimbo de data/hora.
Consulte também [Getfuturtime ()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificadas a serem adicionadas |
| <*timeUnit*> | Sim | String | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E retorna o resultado usando o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e em

Verifique se todas as expressões são verdadeiras.
Retornar true quando todas as expressões forem verdadeiras ou retornar false quando pelo menos uma expressão for false.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>,... | Sim | Booleano | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Retorna true quando todas as expressões são true. Retorna false quando pelo menos uma expressão é false. |
||||

*Exemplo 1*

Esses exemplos verificam se os valores Boolianos especificados são todos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E retorna estes resultados:

* Primeiro exemplo: as duas expressões são true; portanto, retorna `true`.
* Segundo exemplo: uma expressão é false; portanto, retorna `false`.
* Terceiro exemplo: as duas expressões são false; portanto, retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E retorna estes resultados:

* Primeiro exemplo: as duas expressões são true; portanto, retorna `true`.
* Segundo exemplo: uma expressão é false; portanto, retorna `false`.
* Terceiro exemplo: as duas expressões são false; portanto, retorna `false`.

<a name="array"></a>

### <a name="array"></a>array

Retornar uma matriz de uma única entrada especificada.
Para várias entradas, consulte [CreateArray ()](#createArray).

```
array('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres para criar uma matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Matriz | Uma matriz que contém a entrada especificada única |
||||

*Exemplo*

Este exemplo cria uma matriz da cadeia de caracteres "Olá":

```
array('hello')
```

E retorna este resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada em base64 para uma cadeia de caracteres.

```
base64('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres de entrada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada em base64 para a cadeia de caracteres de entrada |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres "Hello" em uma cadeia de caracteres codificada em base64:

```
base64('hello')
```

E retorna este resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retornar a versão binária para uma cadeia de caracteres codificada em base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres codificada em base64 a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | A versão binária para a cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 "aGVsbG8 =" em uma cadeia de caracteres binária:

```
base64ToBinary('aGVsbG8=')
```

E retorna esse resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em base64, decodificando efetivamente a cadeia de caracteres base64.
Use essa função em vez de [decodeBase64 ()](#decodeBase64).
Embora ambas as funções funcionem da mesma maneira, `base64ToString()` é preferida.

```
base64ToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres codificada em base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decodificado-Base64-cadeia de caracteres*> | String | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres codificada em base64 "aGVsbG8 =" em apenas uma cadeia de caracteres:

```
base64ToString('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Retornar a versão binária de uma cadeia de caracteres.

```
binary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | A versão binária da cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo converte a cadeia de caracteres "Hello" em uma cadeia de caracteres binária:

```
binary('hello')
```

E retorna esse resultado:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Retornar a saída de `body` de uma ação em tempo de execução.
Abreviação para `actions('<actionName>').outputs.body`.
Consulte [actionBody ()](#actionBody) e [Actions ()](#actions).

```
body('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da saída de `body` da ação que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída `body` da ação `Get user` Twitter:

```
body('Get_user')
```

E retorna esse resultado:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>booleano

Retornar a versão booliana de um valor.

```
bool(<value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | A versão booliana para o valor especificado |
||||

*Exemplo*

Esses exemplos convertem os valores especificados para valores Boolianos:

```
bool(1)
bool(0)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>COALESCE

Retorna o primeiro valor não nulo de um ou mais parâmetros.
Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>,... | Sim | Any, pode misturar tipos | Um ou mais itens a serem verificados como nulos |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, essa função retornará NULL. |
||||

*Exemplo*

Esses exemplos retornam o primeiro valor não nulo dos valores especificados, ou NULL quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>Concat

Combine duas ou mais cadeias de caracteres e retorne a cadeia combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sim | String | Pelo menos duas cadeias de caracteres para combinar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | A cadeia de caracteres criada com base nas cadeias de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cadeias de caracteres "Olá" e "mundo":

```
concat('Hello', 'World')
```

E retorna este resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>Contém

Verifique se uma coleção tem um item específico.
Retornar true quando o item for encontrado ou retornar false quando não for encontrado.
Essa função diferencia maiúsculas de minúsculas.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, essa função funciona nesses tipos de coleção:

* Uma *cadeia de caracteres* para localizar uma *subcadeia de caracteres*
* Uma *matriz* para localizar um *valor*
* Um *dicionário* para localizar uma *chave*

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres, matriz ou dicionário | A coleção a ser verificada |
| <*valor*> | Sim | Cadeia de caracteres, matriz ou dicionário, respectivamente | O item a ser localizado |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar true quando o item for encontrado. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a cadeia de caracteres "Olá, mundo" para a subcadeia de caracteres "World" e retorna true:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a cadeia de caracteres "Olá, mundo" para a subcadeia de caracteres "universo" e retorna false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converta um carimbo de data/hora de UTC (tempo Universal Coordenado) para o fuso horário de destino.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário de destino. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido no fuso horário de destino |
||||

*Exemplo 1*

Este exemplo converte um carimbo de data/hora no fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um carimbo de data/hora no fuso horário e no formato especificados:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converta um carimbo de data/hora do fuso horário de origem para o fuso horário de destino.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário de origem. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário de destino. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido no fuso horário de destino |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário de destino:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário e o formato especificados:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converta um carimbo de data/hora do fuso horário de origem em UTC (tempo Universal Coordenado).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário de origem. Para nomes de fuso horário, consulte [valores de índice de fuso horário da Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), mas talvez seja necessário remover qualquer Pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido em UTC |
||||

*Exemplo 1*

Este exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada única, consulte [matriz ()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>,... | Sim | Any, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>,...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz com base nessas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E retorna este resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Retornar um URI (Uniform Resource Identifier) de dados para uma cadeia de caracteres.

```
dataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | O URI de dados para a cadeia de caracteres de entrada |
||||

*Exemplo*

Este exemplo cria um URI de dados para a cadeia de caracteres "Olá":

```
dataUri('hello')
```

E retorna este resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retornar a versão binária para um URI (Uniform Resource Identifier) de dados.
Use essa função em vez de [decodeDataUri ()](#decodeDataUri).
Embora ambas as funções funcionem da mesma maneira, `dataUriBinary()` é preferida.

```
dataUriToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma versão binária para este URI de dados:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna esse resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retornar a versão da cadeia de caracteres para um URI (Uniform Resource Identifier) de dados.

```
dataUriToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | A versão da cadeia de caracteres para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para este URI de dados:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retorna o dia do mês de um carimbo de data/hora.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Número inteiro | O dia do mês do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número para o dia do mês deste carimbo de data/hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E retorna este resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retorna o dia da semana de um carimbo de data/hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia da semana*> | Número inteiro | O dia da semana do carimbo de data/hora especificado em que domingo é 0, segunda-feira é 1 e assim por diante |
||||

*Exemplo*

Este exemplo retorna o número para o dia da semana deste carimbo de data/hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E retorna este resultado: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retorna o dia do ano a partir de um carimbo de data/hora.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Número inteiro | O dia do ano a partir do carimbo de data/hora especificado |
||||

*Exemplo*

Este exemplo retorna o número do dia do ano deste carimbo de data/hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E retorna este resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em base64, decodificando efetivamente a cadeia de caracteres base64.
Considere o uso de [base64ToString ()](#base64ToString) em vez de `decodeBase64()`.
Embora ambas as funções funcionem da mesma maneira, `base64ToString()` é preferida.

```
decodeBase64('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres codificada em base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decodificado-Base64-cadeia de caracteres*> | String | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para uma cadeia de caracteres codificada em base64:

```
decodeBase64('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retornar a versão binária para um URI (Uniform Resource Identifier) de dados.
Considere o uso de [dataUriToBinary ()](#dataUriToBinary)em vez de `decodeDataUri()`.
Embora ambas as funções funcionem da mesma maneira, `dataUriToBinary()` é preferida.

```
decodeDataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres de URI de dados para decodificação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária para uma cadeia de caracteres de URI de dados |
||||

*Exemplo*

Este exemplo retorna a versão binária para este URI de dados:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna esse resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retorne uma cadeia de caracteres que substitui o caractere de escape por versões decodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia com os caracteres de escape a serem decodificados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A cadeia atualizada com os caracteres de escape decodificados |
||||

*Exemplo*

Este exemplo substitui os caracteres de escape nesta cadeia de caracteres por versões decodificadas:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retornar o resultado inteiro da divisão de dois números.
Para obter o resultado restante, consulte [mod ()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou float | O número que divide o *dividendo*, mas não pode ser 0 |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Número inteiro | O resultado inteiro da divisão do primeiro número pelo segundo número |
||||

*Exemplo*

Ambos os exemplos dividem o primeiro número pelo segundo número:

```
div(10, 5)
div(11, 5)
```

E retornar esse resultado: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retornar uma versão codificada do URI (Uniform Resource Identifier) para uma cadeia de caracteres, substituindo caracteres não seguros por URL por caracteres de escape.
Considere o uso de [URIComponent ()](#uriComponent)em vez de `encodeUriComponent()`.
Embora ambas as funções funcionem da mesma maneira, `uriComponent()` é preferida.

```
encodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres a ser convertida em formato codificado em URI |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*URI codificado*> | String | A cadeia de caracteres codificada em URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada em URI para esta cadeia de caracteres:

```
encodeUriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Retornar true quando a coleção estiver vazia ou retornar false quando não estiver vazio.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres, matriz ou objeto | A coleção a ser verificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando a coleção está vazia. Retornar false quando não estiver vazio. |
||||

*Exemplo*

Esses exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E retorna estes resultados:

* Primeiro exemplo: passa uma cadeia de caracteres vazia, portanto, a função retorna `true`.
* Segundo exemplo: passa a cadeia de caracteres "ABC", portanto, a função retorna `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma cadeia de caracteres termina com uma subcadeia de caracteres específica.
Retorna true quando a subcadeia de caracteres é encontrada ou retorna false quando não encontrada.
Essa função não diferencia maiúsculas de minúsculas.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | String | A subcadeia de caracteres final a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna verdadeiro quando a subcadeia de caracteres final é encontrada. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" termina com a cadeia de caracteres "mundo":

```
endsWith('hello world', 'world')
```

E retorna este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" termina com a cadeia de caracteres "universo":

```
endsWith('hello world', 'universe')
```

E retorna este resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>é igual a

Verifique se os valores, as expressões ou os objetos são equivalentes.
Retornar true quando ambos forem equivalentes ou retornar false quando eles não forem equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | Diversos | Os valores, expressões ou objetos para comparar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar true quando ambos forem equivalentes. Retornar false quando não for equivalente. |
||||

*Exemplo*

Esses exemplos verificam se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E retorna estes resultados:

* Primeiro exemplo: ambos os valores são equivalentes, portanto, a função retorna `true`.
* Segundo exemplo: os dois valores não são equivalentes, portanto, a função retorna `false`.

<a name="first"></a>

### <a name="first"></a>primeiro

Retornar o primeiro item de uma cadeia de caracteres ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeira-coleção-item*> | Qualquer | O primeiro item na coleção |
||||

*Exemplo*

Estes exemplos localizam o primeiro item nessas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E retornar esses resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de cadeia de caracteres para um número de ponto flutuante em um número de ponto flutuante real.
Você pode usar essa função somente ao passar parâmetros personalizados para um aplicativo, por exemplo, um aplicativo lógico ou um fluxo.

```
float('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres que tem um número de ponto flutuante válido para converter |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*float-value*> | Flutuante | O número de ponto flutuante para a cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia de caracteres para este número de ponto flutuante:

```
float('10.333')
```

E retorna este resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retorna um carimbo de data/hora no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplo*

Este exemplo converte um carimbo de data/hora no formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E retorna este resultado: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retornar uma matriz com valores que correspondem a um nome de chave em *dados de formulário* de uma ação ou saída *codificada por formulário* .

```
formDataMultiValues('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | A ação cuja saída tem o valor de chave desejado |
| *chave* de <> | Sim | String | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*matriz com valores de chave*>] | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz do valor da chave "Subject" na saída de dados de formulário da ação especificada ou com codificação de formulário:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E retorna o texto do assunto em uma matriz, por exemplo: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Retornar um único valor que corresponda a um nome de chave na saída de *formulário* de uma ação ou *codificação de formulário* .
Se a função encontrar mais de uma correspondência, a função gera um erro.

```
formDataValue('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | A ação cuja saída tem o valor de chave desejado |
| *chave* de <> | Sim | String | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*chave-valor*> | String | O valor na chave especificada  |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres do valor da chave "Subject" na saída de dados de formulário da ação especificada ou codificação de formulário:

```
formDataValue('Send_an_email', 'Subject')
```

E retorna o texto do assunto como uma cadeia de caracteres, por exemplo: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Retornar um número como uma cadeia de caracteres com base no formato especificado.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *número* de <> | Sim | Inteiro ou duplo | O valor que você deseja formatar. |
| <*format*> | Sim | String | Uma cadeia de caracteres de formato composto que especifica o formato que você deseja usar. Para obter as cadeias de caracteres de formato numérico com suporte, consulte [cadeias de caracteres de formato numérico padrão](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings), que têm suporte pelo `number.ToString(<format>, <locale>)`. |
| <*locale*> | Não | String | A localidade a ser usada com suporte pelo `number.ToString(<format>, <locale>)`. Se não for especificado, o valor padrão será `en-us`. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de número formatado* | String | O número especificado como uma cadeia de caracteres no formato que você especificou. Você pode converter esse valor de retorno em um `int` ou `float`. |
||||

*Exemplo 1*

Suponha que você deseja formatar o número `1234567890`. Este exemplo formata esse número como a cadeia de caracteres "1234567890, 0".

```
formatNumber(1234567890, "{0:0,0.00}", "en-us")
```

\* Exemplo 2 "

Suponha que você deseja formatar o número `1234567890`. Este exemplo formata o número para a cadeia de caracteres "1.234.567.890, 00".

```
formatNumber(1234567890, "{0:0,0.00}", "is-is")
```

*Exemplo 3*

Suponha que você deseja formatar o número `17.35`. Este exemplo formata o número para a cadeia de caracteres "$17.35".

```
formatNumber(17.36, "{0:C2}")
```

*Exemplo 4*

Suponha que você deseja formatar o número `17.35`. Este exemplo formata o número para a cadeia de caracteres "17, 35 Kr".

```
formatNumber(17.36, "{0:C2}", "is-is")
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificadas a serem adicionadas |
| <*timeUnit*> | Sim | String | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora atual seja "2018-03-01T00:00:00.0000000 Z".
Este exemplo adiciona cinco dias ao carimbo de data/hora:

```
getFutureTime(5, 'Day')
```

E retorna este resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora atual seja "2018-03-01T00:00:00.0000000 Z".
Este exemplo adiciona cinco dias e converte o resultado no formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E retorna este resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getpastetime

Retorna o carimbo de data/hora atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora atual seja "2018-02-01T00:00:00.0000000 Z".
Este exemplo subtrai cinco dias do carimbo de data/hora:

```
getPastTime(5, 'Day')
```

E retorna este resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora atual seja "2018-02-01T00:00:00.0000000 Z".
Este exemplo subtrai cinco dias e converte o resultado para o formato "D":

```
getPastTime(5, 'Day', 'D')
```

E retorna este resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifique se o primeiro valor é maior que o segundo valor.
Retornar true quando o primeiro valor for maior ou retornar false quando for menor.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é maior que o segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é maior que o segundo valor. Retorna false quando o primeiro valor é igual ou menor que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior que o segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Retornar true quando o primeiro valor for maior ou igual ou retornar false quando o primeiro valor for menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é maior ou igual ao segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é maior ou igual ao segundo valor. Retorna false quando o primeiro valor é menor que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, você pode especificar um formato diferente para o GUID que não seja o formato padrão "D", que é de 32 dígitos separados por hifens.

```
guid('<format>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | Um [especificador de formato](https://msdn.microsoft.com/library/97af8hh4) único para o GUID retornado. Por padrão, o formato é "D", mas você pode usar "N", "D", "B", "P" ou "X". |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*GUID-valor*> | String | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hifens e entre parênteses:

```
guid('P')
```

E retorna este resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifique se uma expressão é verdadeira ou falsa.
Com base no resultado, retorna um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *expressão* de <> | Sim | Booleano | A expressão a ser verificada |
| <*valueIfTrue*> | Sim | Qualquer | O valor a ser retornado quando a expressão for verdadeira |
| <*valueIfFalse*> | Sim | Qualquer | O valor a ser retornado quando a expressão é falsa |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*especificado-valor de retorno*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo retorna `"yes"` porque a expressão especificada retorna true.
Caso contrário, o exemplo retornará `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retorna a posição inicial ou o valor do índice de uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | String | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de valor de índice*>| Número inteiro | A posição inicial ou o valor do índice para a subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for encontrada, retorne o número-1. |
||||

*Exemplo*

Este exemplo localiza o valor de índice inicial para a subcadeia de caracteres "World" na cadeia de caracteres "Olá, mundo":

```
indexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Retornar a versão de inteiro para uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*integer-result*> | Número inteiro | A versão de inteiro para a cadeia de caracteres especificada |
||||

*Exemplo*

Este exemplo cria uma versão de inteiro para a cadeia de caracteres "10":

```
int('10')
```

E retorna este resultado: `10`

<a name="item"></a>

### <a name="item"></a>item

Quando usado dentro de uma ação repetida em uma matriz, retorna o item atual na matriz durante a iteração atual da ação.
Você também pode obter os valores das propriedades desse item.

```
item()
```

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Qualquer | O item atual na matriz para a iteração atual da ação |
||||

*Exemplo*

Este exemplo obtém o elemento `body` da mensagem atual para a ação "Send_an_email" dentro de uma iteração atual do loop for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Retorna o item atual de cada ciclo em um loop for-each.
Use essa função dentro do loop for-each.

```
items('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sim | String | O nome para o loop for-each |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*item*> | Qualquer | O item do ciclo atual no loop for-each especificado |
||||

*Exemplo*

Este exemplo obtém o item atual do loop for-each especificado:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Retorna o valor de índice para a iteração atual dentro de um loop Until. Você pode usar essa função dentro de loops aninhados until. 

```
iterationIndexes('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sim | String | O nome do loop Until | 
||||| 

| Valor de retorno | Tipo | Descrição | 
| ------------ | ---- | ----------- | 
| *índice* de <> | Número inteiro | O valor de índice para a iteração atual dentro do loop Until especificado | 
|||| 

*Exemplo* 

Este exemplo cria uma variável de contador e incrementa essa variável por uma durante cada iteração em um loop Until até que o valor do contador atinja cinco. O exemplo também cria uma variável que controla o índice atual para cada iteração. No loop Until, durante cada iteração, o exemplo incrementa o contador e, em seguida, atribui o valor do contador ao valor do índice atual e, em seguida, incrementa o contador. A qualquer momento, você pode determinar o número de iteração atual recuperando o valor do índice atual.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Retorna o valor ou o objeto do tipo JavaScript Object Notation (JSON) para uma cadeia de caracteres ou XML.

```
json('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia de caracteres ou XML | A cadeia de caracteres ou XML a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Tipo ou objeto nativo JSON | O valor do tipo nativo JSON ou o objeto para a cadeia de caracteres ou o XML especificado. Se a cadeia de caracteres for nula, a função retornará um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte essa cadeia de caracteres para o valor JSON:

```
json('[1, 2, 3]')
```

E retorna este resultado: `[1, 2, 3]`

*Exemplo 2*

Este exemplo converte esta cadeia de caracteres em JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E retorna esse resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 3*

Este exemplo converte este XML em JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E retorna esse resultado:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>interseção

Retorna uma coleção que tem *apenas* os itens comuns nas coleções especificadas.
Para aparecer no resultado, um item deve aparecer em todas as coleções passadas para essa função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparecerá no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,... | Sim | Matriz ou objeto, mas não ambos | As coleções de onde você deseja *apenas* os itens comuns |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*itens comuns*> | Matriz ou objeto, respectivamente | Uma coleção que tem apenas os itens comuns nas coleções especificadas |
||||

*Exemplo*

Este exemplo localiza os itens comuns entre essas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E retorna uma matriz com *apenas* estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Retornar uma cadeia de caracteres que tenha todos os itens de uma matriz e que tenha cada caractere separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Matriz | A matriz que tem os itens a serem ingressados |
| *delimitador* de <> | Sim | String | O separador que aparece entre cada caractere na cadeia de caracteres resultante |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*delimitador* de><*char1*><*char2*><*delimitador*>... | String | A cadeia de caracteres resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres de todos os itens nesta matriz com o caractere especificado como o delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E retorna este resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>última

Retorna o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o último item |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Cadeia de caracteres ou matriz, respectivamente | O último item na coleção |
||||

*Exemplo*

Estes exemplos localizam o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E retorna estes resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou o valor do índice para a última ocorrência de uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | String | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for encontrada, retorne o número-1. |
||||

*Exemplo*

Este exemplo localiza o valor de índice inicial para a última ocorrência da subcadeia de caracteres "World" na cadeia de caracteres "Olá, mundo":

```
lastIndexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="length"></a>

### <a name="length"></a>length

Retorna o número de itens em uma coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção com os itens a serem contados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Número inteiro | O número de itens na coleção |
||||

*Exemplo*

Esses exemplos contam o número de itens nessas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E retornar esse resultado: `4`

<a name="less"></a>

### <a name="less"></a>less

Verifique se o primeiro valor é menor que o segundo valor.
Retornar true quando o primeiro valor for menor ou retornar false quando o primeiro valor for maior.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é menor que o segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando o primeiro valor é menor que o segundo valor. Retorna false quando o primeiro valor é igual ou maior que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é menor que o segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é menor ou igual ao segundo valor.
Retornar true quando o primeiro valor for menor ou igual ou retornar false quando o primeiro valor for maior.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, float ou cadeia de caracteres | O primeiro valor para verificar se é menor ou igual ao segundo valor |
| <*compareTo*> | Sim | Integer, float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna true quando o primeiro valor é menor ou igual ao segundo valor. Retorna false quando o primeiro valor é maior que o segundo valor. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é menor ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Retornar a "URL de retorno de chamada" que chama um gatilho ou uma ação.
Essa função funciona apenas com gatilhos e ações para os tipos de conector **HttpWebhook** e **ApiConnectionWebhook** , mas não os tipos **manual**, **recorrência**, **http**e **APIConnection** .

```
listCallbackUrl()
```

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | A URL de retorno de chamada para um gatilho ou uma ação |
||||

*Exemplo*

Este exemplo mostra uma URL de retorno de chamada de exemplo que essa função pode retornar:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>máx

Retorne o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*núm2*>,... | Sim | Integer, float ou Both | O conjunto de números do qual você deseja o valor mais alto |
| [<*número1*>, <*núm2*>,...] | Sim | Matriz-inteiro, float ou ambos | A matriz de números da qual você deseja o valor mais alto |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*max-value*> | Inteiro ou float | O valor mais alto na matriz ou conjunto de números especificado |
||||

*Exemplo*

Esses exemplos obtêm o maior valor do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E retornar esse resultado: `3`

<a name="min"></a>

### <a name="min"></a>min.

Retorna o valor mais baixo de um conjunto de números ou uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*núm2*>,... | Sim | Integer, float ou Both | O conjunto de números do qual você deseja o valor mais baixo |
| [<*número1*>, <*núm2*>,...] | Sim | Matriz-inteiro, float ou ambos | A matriz de números da qual você deseja o valor mais baixo |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| > *de valor mín* < | Inteiro ou float | O menor valor no conjunto especificado de números ou na matriz especificada |
||||

*Exemplo*

Esses exemplos obtêm o menor valor no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E retornar esse resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>resto

Retornar o resto da divisão de dois números.
Para obter o resultado inteiro, consulte [div ()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou float | O número que divide o *dividendo*, mas não pode ser 0. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Inteiro ou float | O restante da divisão do primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E retornar esse resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Retorne o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Inteiro ou float | O número a ser multiplicado por *multiplicand2* |
| <*multiplicand2*> | Sim | Inteiro ou float | O número que múltiplos *multiplicand1* |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*product-result*> | Inteiro ou float | O produto da multiplicação do primeiro número pelo segundo número |
||||

*Exemplo*

Esses exemplos são múltiplos pelo primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E retornar esses resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Retorna o corpo de uma parte específica na saída de uma ação que tem várias partes.

```
multipartBody('<actionName>', <index>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da ação que tem a saída com várias partes |
| *índice* de <> | Sim | Número inteiro | O valor de índice para a parte que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| > do *corpo* do < | String | O corpo da parte especificada |
||||

<a name="not"></a>

### <a name="not"></a>not

Verifique se uma expressão é falsa.
Retorna true quando a expressão é false ou retorna false quando true.

```json
not(<expression>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *expressão* de <> | Sim | Booleano | A expressão a ser verificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna verdadeiro quando a expressão é falsa. Retorna falso quando a expressão é verdadeira. |
||||

*Exemplo 1*

Esses exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E retornar esses resultados:

* Primeiro exemplo: a expressão é false, portanto, a função retorna `true`.
* Segundo exemplo: a expressão é true, portanto, a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E retornar esses resultados:

* Primeiro exemplo: a expressão é false, portanto, a função retorna `true`.
* Segundo exemplo: a expressão é true, portanto, a função retorna `false`.

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Retornar true quando pelo menos uma expressão for true ou retornar false quando todos forem false.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>,... | Sim | Booleano | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorna true quando pelo menos uma expressão for true. Retorna false quando todas as expressões são false. |
||||

*Exemplo 1*

Esses exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E retornar esses resultados:

* Primeiro exemplo: pelo menos uma expressão é verdadeira, portanto, a função retorna `true`.
* Segundo exemplo: as duas expressões são false, portanto, a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E retornar esses resultados:

* Primeiro exemplo: pelo menos uma expressão é verdadeira, portanto, a função retorna `true`.
* Segundo exemplo: as duas expressões são false, portanto, a função retorna `false`.

<a name="outputs"></a>

### <a name="outputs"></a>outputs

Retornar as saídas de uma ação em tempo de execução. Use essa função, em vez de `actionOutputs()`, que resolve para `outputs()` no designer do aplicativo lógico. Embora ambas as funções funcionem da mesma maneira, `outputs()` é preferida.

```
outputs('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da saída da ação que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*output*> | String | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída da ação do Twitter `Get user`:

```
outputs('Get_user')
```

E retorna esse resultado:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parâmetros

Retorne o valor para um parâmetro descrito em sua definição de fluxo de trabalho.

```
parameters('<parameterName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Sim | String | O nome do parâmetro cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| *parâmetro de <-valor*> | Qualquer | O valor do parâmetro especificado |
||||

*Exemplo*

Suponha que você tenha esse valor JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Este exemplo obtém o valor para o parâmetro especificado:

```
parameters('fullName')
```

E retorna este resultado: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>Rand

Retornar um inteiro aleatório de um intervalo especificado, que é inclusivo apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O menor número inteiro no intervalo |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o número inteiro mais alto no intervalo que a função pode retornar |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de resultados aleatórios*> | Número inteiro | O inteiro aleatório retornado do intervalo especificado |
||||

*Exemplo*

Este exemplo obtém um inteiro aleatório do intervalo especificado, excluindo o valor máximo:

```
rand(1, 5)
```

E retorna um destes números como resultado: `1`, `2`, `3`ou `4`

<a name="range"></a>

### <a name="range"></a>amplitude

Retornar uma matriz de inteiros que inicia de um número inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| <*count*> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Matriz | A matriz com inteiros começando do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que inicia do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E retorna este resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Substitua

Substitui uma subcadeia de caracteres pela cadeia de caracteres especificada e retorna a cadeia de caracteres de resultado. Essa função diferencia maiúsculas de minúsculas.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <*oldText*> | Sim | String | A subcadeia de caracteres a ser substituída |
| <*newText*> | Sim | String | A cadeia de caracteres de substituição |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | A cadeia de caracteres atualizada após substituir a subcadeia de caracteres <p>Se a subcadeia de caracteres não for encontrada, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Este exemplo localiza a subcadeia de caracteres "Old" em "a cadeia de caracteres antiga" e substitui "Old" por "New":

```
replace('the old string', 'old', 'new')
```

E retorna este resultado: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removerproperty

Remove uma propriedade de um objeto e retorna o objeto atualizado. Se a propriedade que você tentar remover não existir, a função retornará o objeto original.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON do qual você deseja remover uma propriedade |
| *propriedade* <> | Sim | String | O nome da propriedade a ser removida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado sem a propriedade especificada |
||||

Para remover uma propriedade filho de uma propriedade existente, use esta sintaxe:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON cuja propriedade você deseja remover |
| > *de propriedade pai* < | Sim | String | O nome da propriedade pai com a propriedade filho que você deseja remover |
| <> *de propriedade filho* | Sim | String | O nome da propriedade filho a ser removida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado cuja propriedade filho você removeu |
||||

*Exemplo 1*

Este exemplo remove a propriedade `middleName` de um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) e retorna o objeto atualizado:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Este é o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Este é o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo remove a propriedade filho `middleName` de uma propriedade pai `customerName` em um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) e retorna o objeto atualizado:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Este é o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Este é o objeto JSON atualizado:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>Resultado

Retornar as entradas e saídas de todas as ações que estão dentro da ação com escopo especificado, como uma ação `For_each`, `Until`ou `Scope`. Essa função é útil, retornando os resultados de uma ação com falha para que você possa diagnosticar e manipular exceções. Para obter mais informações, consulte [obter contexto e resultados para falhas](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Sim | String | O nome da ação com escopo da qual retornar as entradas e saídas de todas as ações internas |
||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*array-object*> | Objeto de matriz | Uma matriz que contém matrizes de entradas e saídas de cada ação que aparece dentro da ação de escopo especificada |
||||

*Exemplo*

Este exemplo retorna as entradas e saídas de cada iteração de uma ação HTTP dentro de um loop de `For_each` usando a função `result()` na ação `Compose`:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Veja como o exemplo de matriz retornado pode parecer onde o objeto de `outputs` externo contém as entradas e saídas de cada iteração das ações dentro da ação `For_each`.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Defina o valor para a propriedade do objeto JSON e retorne o objeto atualizado. Se a propriedade que você tentar definir não existir, a propriedade será adicionada ao objeto. Para adicionar uma nova propriedade, use a função [AddProperty ()](#addProperty) .

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON cuja propriedade você deseja definir |
| *propriedade* <> | Sim | String | O nome da propriedade nova ou existente a ser definida |
| <*valor*> | Sim | Qualquer | O valor a ser definido para a propriedade especificada |
|||||

Para definir a propriedade Child em um objeto filho, use uma chamada de `setProperty()` aninhada em vez disso. Caso contrário, a função retornará apenas o objeto filho como saída.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *objeto* <> | Sim | Objeto | O objeto JSON cuja propriedade você deseja definir |
| > *de propriedade pai* < | Sim | String | O nome da propriedade pai com a propriedade filho que você deseja definir |
| <> *de propriedade filho* | Sim | String | O nome da propriedade filho a ser definida |
| <*valor*> | Sim | Qualquer | O valor a ser definido para a propriedade especificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo define a propriedade `surName` em um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) . A função atribui o valor especificado à propriedade e retorna o objeto atualizado:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Este é o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Este é o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exemplo 2*

Este exemplo define a propriedade filho `surName` para a propriedade pai `customerName` em um objeto JSON, que é convertido de uma cadeia de caracteres para JSON usando a função [JSON ()](#json) . A função atribui o valor especificado à propriedade e retorna o objeto atualizado:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Este é o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Este é o objeto JSON atualizado:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>saltar

Remover itens da frente de uma coleção e retornar todos os *outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Matriz | A coleção cujos itens você deseja remover |
| <*count*> | Sim | Número inteiro | Um inteiro positivo para o número de itens a serem removidos na frente |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*atualizado-> de coleção*] | Matriz | A coleção atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da parte frontal da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E retorna essa matriz com os itens restantes: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Retorna uma matriz que contém subcadeias de caracteres, separadas por vírgulas, com base no caractere delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser separada em subcadeias de caracteres com base no delimitador especificado na cadeia de caracteres original |
| *delimitador* de <> | Sim | String | O caractere na cadeia de caracteres original a ser usado como o delimitador |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*subseqüência1*>, <*subseqüência2*>,...] | Matriz | Uma matriz que contém subcadeias da cadeia de caracteres original, separadas por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcadeias da cadeia de caracteres especificada com base no caractere especificado como o delimitador:

```
split('a_b_c', '_')
```

E retorna essa matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retorna o início do dia para um carimbo de data/hora.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando no sinal de zero horas para o dia |
||||

*Exemplo*

Este exemplo localiza o início do dia para este carimbo de data/hora:

```
startOfDay('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retornar o início da hora para um carimbo de data/hora.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando na marca de minuto zero para a hora |
||||

*Exemplo*

Este exemplo localiza o início da hora para este carimbo de data/hora:

```
startOfHour('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retornar o início do mês para um carimbo de data/hora.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando no primeiro dia do mês no sinal de zero |
||||

*Exemplo*

Este exemplo retorna o início do mês para este carimbo de data/hora:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma cadeia de caracteres começa com uma subcadeia de caracteres específica.
Retorna true quando a subcadeia de caracteres é encontrada ou retorna false quando não encontrada.
Essa função não diferencia maiúsculas de minúsculas.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | String | A cadeia de caracteres inicial a ser localizada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorna true quando a subcadeia de caracteres inicial é encontrada. Retorna falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" começa com a subcadeia "Olá":

```
startsWith('hello world', 'hello')
```

E retorna este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia de caracteres "Olá, mundo" começa com a subcadeia "Greetings":

```
startsWith('hello world', 'greetings')
```

E retorna este resultado: `false`

<a name="string"></a>

### <a name="string"></a>string

Retornar a versão da cadeia de caracteres para um valor.

```
string(<value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*string-value*> | String | A versão da cadeia de caracteres para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de cadeia de caracteres para este número:

```
string(10)
```

E retorna este resultado: `"10"`

*Exemplo 2*

Este exemplo cria uma cadeia de caracteres para o objeto JSON especificado e usa o caractere de barra invertida (\\) como um caractere de escape para a aspa dupla (").

```
string( { "name": "Sophie Owen" } )
```

E retorna este resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>projeto

Retorne o resultado da subtração do segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Inteiro ou float | O número do qual subtrair o *subtraendo* |
| <*subtrahend*> | Sim | Inteiro ou float | O número a ser subtraído da *minuendo* |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou float | O resultado da subtração do segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E retorna este resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>Subcadeia

Retornar caracteres de uma cadeia de caracteres, começando da posição especificada ou índice.
Os valores de índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia cujos caracteres você deseja |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou maior que 0 que você deseja usar como a posição inicial ou o valor do índice |
| *comprimento* de <> | Sim | Número inteiro | Um número positivo de caracteres que você deseja na Subcadeia |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | Uma Subcadeia com o número especificado de caracteres, começando na posição de índice especificada na cadeia de caracteres de origem |
||||

*Exemplo*

Este exemplo cria uma subcadeia de caracteres de cinco caracteres a partir da cadeia de caracteres especificada, começando do valor de índice 6:

```
substring('hello world', 6, 5)
```

E retorna este resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtraia um número de unidades de tempo de um carimbo de data/hora.
Veja também [Getpastetime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a ser usada com o *intervalo*: "segundo", "minuto", "hora", "dia", "semana", "mês", "ano" |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia deste carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia deste carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E retorna esse resultado usando o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>ter

Retornar itens da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *coleção* de <> | Sim | Cadeia de caracteres ou matriz | A coleção cujos itens você deseja |
| <*count*> | Sim | Número inteiro | Um inteiro positivo para o número de itens que você deseja da frente |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subconjunto*> ou [< > do*subconjunto*] | Cadeia de caracteres ou matriz, respectivamente | Uma cadeia de caracteres ou matriz que tem o número especificado de itens extraídos da frente da coleção original |
||||

*Exemplo*

Esses exemplos obtêm o número especificado de itens da frente dessas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E retornar esses resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Retorna o valor da propriedade de `ticks` para um carimbo de data/hora especificado.
Um *tique* é um intervalo de 100 a nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres de um carimbo de data/hora |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Número inteiro | O número de tiques desde o carimbo de data/hora especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retornar uma cadeia de caracteres em formato em minúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em minúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toLower('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser retornada em formato de minúsculas |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | A cadeia de caracteres original em formato em minúsculas |
||||

*Exemplo*

Este exemplo converte essa cadeia de caracteres em minúsculas:

```
toLower('Hello World')
```

E retorna este resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retornar uma cadeia de caracteres em formato maiúsculo. Se um caractere na cadeia de caracteres não tiver uma versão em maiúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toUpper('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser retornada em formato maiúsculo |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | A cadeia de caracteres original em formato maiúsculo |
||||

*Exemplo*

Este exemplo converte essa cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E retorna este resultado: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>of

Retornar a saída de um gatilho em tempo de execução ou valores de outros pares nome-e-valor JSON, que você pode atribuir a uma expressão.

* Dentro de entradas de um gatilho, essa função retorna a saída da execução anterior.

* Dentro da condição de um gatilho, essa função retorna a saída da execução atual.

Por padrão, a função faz referência a todo o objeto de gatilho, mas você pode opcionalmente especificar uma propriedade cujo valor você deseja.
Além disso, essa função tem versões abreviadas disponíveis, consulte [triggerOutputs ()](#triggerOutputs) e [triggerBody ()](#triggerBody).

```
trigger()
```

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | A saída de um gatilho em tempo de execução |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Retornar a saída de `body` de um gatilho em tempo de execução.
Abreviação para `trigger().outputs.body`.
Consulte [Trigger ()](#trigger).

```
triggerBody()
```

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | A saída de `body` do gatilho |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retornar uma matriz com valores que correspondem a um nome de chave em *dados de formulário* de um gatilho ou saída *codificada por formulário* .

```
triggerFormDataMultiValues('<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *chave* de <> | Sim | String | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*matriz com valores de chave*>] | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz do valor de chave "feedUrl" em um gatilho RSS de dados de formulário ou uma saída codificada por formulário:

```
triggerFormDataMultiValues('feedUrl')
```

E retorna essa matriz como um resultado de exemplo: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retornar uma cadeia de caracteres com um único valor que corresponda a um nome de chave em *dados de formulário* de um gatilho ou saída *codificada por formulário* .
Se a função encontrar mais de uma correspondência, a função gera um erro.

```
triggerFormDataValue('<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *chave* de <> | Sim | String | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*chave-valor*> | String | O valor na chave especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres do valor de chave "feedUrl" em uma saída de dados de formulário do gatilho RSS ou com codificação de formulário:

```
triggerFormDataValue('feedUrl')
```

E retorna essa cadeia de caracteres como um resultado de exemplo: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Retorna o corpo de uma parte específica na saída de um gatilho que tem várias partes.

```
triggerMultipartBody(<index>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *índice* de <> | Sim | Número inteiro | O valor de índice para a parte que você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| > do *corpo* do < | String | O corpo da parte especificada na saída de várias partes do gatilho |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Retornar a saída de um gatilho em tempo de execução ou valores de outros pares de nome e valor JSON.
Abreviação para `trigger().outputs`.
Consulte [Trigger ()](#trigger).

```
triggerOutputs()
```

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | A saída de um gatilho em tempo de execução  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Remova o espaço em branco à esquerda e à direita de uma cadeia de caracteres e retorne a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem o espaço em branco à esquerda e à direita para remover |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | Uma versão atualizada para a cadeia de caracteres original sem espaço em branco à esquerda ou à direita |
||||

*Exemplo*

Este exemplo remove o espaço em branco à esquerda e à direita da cadeia de caracteres "Olá, Mundo":

```
trim(' Hello World  ')
```

E retorna este resultado: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Retorna uma coleção que tem *todos* os itens das coleções especificadas.
Para aparecer no resultado, um item pode aparecer em qualquer coleção passada para essa função. Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparecerá no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*Collection2*>,...  | Sim | Matriz ou objeto, mas não ambos | As coleções de onde você deseja *todos* os itens |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matriz ou objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas-sem duplicatas |
||||

*Exemplo*

Este exemplo obtém *todos* os itens dessas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E retorna este resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retornar uma versão codificada do URI (Uniform Resource Identifier) para uma cadeia de caracteres, substituindo caracteres não seguros por URL por caracteres de escape.
Use essa função em vez de [encodeUriComponent ()](#encodeUriComponent).
Embora ambas as funções funcionem da mesma maneira, `uriComponent()` é preferida.

```
uriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres a ser convertida em formato codificado em URI |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*URI codificado*> | String | A cadeia de caracteres codificada em URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada em URI para esta cadeia de caracteres:

```
uriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retornar a versão binária para um componente URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres codificada em URI a ser convertida |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | A versão binária para a cadeia de caracteres codificada em URI. O conteúdo binário é codificado em Base64 e representado por `$content`. |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia de caracteres codificada em URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E retorna esse resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retornar a versão da cadeia de caracteres para uma cadeia de caracteres codificada por URI (Uniform Resource Identifier), decodificando efetivamente a cadeia de caracteres codificada em URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres codificada em URI a ser decodificada |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A versão decodificada da cadeia de caracteres codificada em URI |
||||

*Exemplo*

Este exemplo cria a versão de cadeia de caracteres decodificada para esta cadeia de caracteres codificada em URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Retornar o valor de `host` para um URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `host` valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> *de valor de host* | String | O valor de `host` para o URI especificado |
||||

*Exemplo*

Este exemplo localiza o valor de `host` para este URI:

```
uriHost('https://www.localhost.com:8080')
```

E retorna este resultado: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Retornar o valor de `path` para um URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `path` valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| *caminho <-valor*> | String | O valor de `path` para o URI especificado. Se `path` não tiver um valor, retorne o caractere "/". |
||||

*Exemplo*

Este exemplo localiza o valor de `path` para este URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Retorne os valores de `path` e `query` para um URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `path` e os valores de `query` desejados |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | Os valores `path` e `query` para o URI especificado. Se `path` não especificar um valor, retornará o caractere "/". |
||||

*Exemplo*

Este exemplo localiza os valores de `path` e `query` para este URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Retornar o valor de `port` para um URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `port` valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor da porta*> | Número inteiro | O valor de `port` para o URI especificado. Se `port` não especificar um valor, retorne a porta padrão para o protocolo. |
||||

*Exemplo*

Este exemplo retorna o valor de `port` para este URI:

```
uriPort('http://www.localhost:8080')
```

E retorna este resultado: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Retornar o valor de `query` para um URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `query` valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*query-value*> | String | O valor de `query` para o URI especificado |
||||

*Exemplo*

Este exemplo retorna o valor de `query` para este URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>UriScheme

Retornar o valor de `scheme` para um URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `scheme` valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | O valor de `scheme` para o URI especificado |
||||

*Exemplo*

Este exemplo retorna o valor de `scheme` para este URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retornar o carimbo de data/hora atual.

```
utcNow('<format>')
```

Opcionalmente, você pode especificar um formato diferente com o parâmetro <*format*>.


| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | Um [único especificador de formato](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-mm-ddThh: mm: SS: fffffffK), que está em conformidade com o [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | A data e a hora atuais |
||||

*Exemplo 1*

Suponha que hoje seja 15 de abril de 2018 às 1:00:00 PM.
Este exemplo obtém o carimbo de data/hora atual:

```
utcNow()
```

E retorna este resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje seja 15 de abril de 2018 às 1:00:00 PM.
Este exemplo obtém o carimbo de data/hora atual usando o formato "D" opcional:

```
utcNow('D')
```

E retorna este resultado: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>as

Retorna o valor de uma variável especificada.

```
variables('<variableName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Sim | String | O nome da variável cujo valor você deseja |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*de valor variável*> | Qualquer | O valor da variável especificada |
||||

*Exemplo*

Suponha que o valor atual de uma variável "numItems" seja 20.
Este exemplo obtém o valor inteiro para essa variável:

```
variables('numItems')
```

E retorna este resultado: `20`

<a name="workflow"></a>

### <a name="workflow"></a>fluxo de trabalho

Retornar todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução.

```
workflow().<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| *propriedade* <> | Não | String | O nome da propriedade de fluxo de trabalho cujo valor você deseja <p>Um objeto de fluxo de trabalho tem estas propriedades: **nome**, **tipo**, **ID**, **local**e **execução**. O valor da propriedade **Run** também é um objeto que tem estas propriedades: **Name**, **Type**e **ID**. |
|||||

*Exemplo*

Este exemplo retorna o nome para a execução atual de um fluxo de trabalho:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Retornar a versão XML para uma cadeia de caracteres que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de caracteres com o objeto JSON a ser convertido <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Use o caractere de barra invertida (\\) como um caractere de escape para a aspa dupla ("). |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*XML-versão*> | Objeto | O XML codificado para a cadeia de caracteres ou objeto JSON especificado |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia de caracteres, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E retorna este XML de resultado:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que você tenha este objeto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Este exemplo cria XML para uma cadeia de caracteres que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E retorna este XML de resultado:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Marque XML para nós ou valores que correspondam a uma expressão XPath (linguagem de caminho XML) e retorne os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda a navegar por uma estrutura de documento XML para que você possa selecionar nós ou valores de computação no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia de caracteres XML para pesquisar nós ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para localizar os valores ou nós XML correspondentes |
|||||

| Valor de retorno | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Um nó XML quando apenas um único nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-ou- </br>[<*value1*>, <*value2*>, ...] | Matriz | Uma matriz com nós XML ou valores que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Este exemplo localiza os nós que correspondem ao nó `<name></name>` nos argumentos especificados e retorna uma matriz com esses valores de nó:

`xpath(xml(parameters('items')), '/produce/item/name')`

Aqui estão os argumentos:

* A cadeia de caracteres "Items", que contém este XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  O exemplo usa a função [Parameters ()](#parameters) para obter a cadeia de caracteres XML do argumento "Items", mas também deve converter a cadeia de caracteres em formato XML usando a função [XML ()](#xml) .

* Essa expressão XPath, que é passada como uma cadeia de caracteres:

  `"/produce/item/name"`

Aqui está a matriz de resultados com os nós que correspondem `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemplo 2*

A seguir, no exemplo 1, este exemplo localiza os nós que correspondem ao nó `<count></count>` e adiciona esses valores de nó com a função `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E retorna este resultado: `30`

*Exemplo 3*

Para este exemplo, as duas expressões localizam nós que correspondem ao nó `<location></location>`, nos argumentos especificados, que incluem XML com um namespace. 

> [!NOTE]
>
> Se você estiver trabalhando no modo de exibição de código, escape a aspa dupla (") usando o caractere de barra invertida (\\). 
> Por exemplo, você precisa usar caracteres de escape ao serializar uma expressão como uma cadeia de caracteres JSON. 
> No entanto, se você estiver trabalhando no designer de aplicativo lógico ou no editor de expressão, não será necessário escapar das aspas duplas porque o caractere de barra invertida é adicionado automaticamente à definição subjacente, por exemplo:
> 
> * Exibição de código: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor de expressão: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> Os exemplos a seguir se aplicam a expressões que você insere no editor de expressão.

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Aqui estão os argumentos:

* Esse XML, que inclui o namespace do documento XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Uma expressão XPath aqui:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Este é o nó de resultado que corresponde ao nó de `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 4*

A seguir, no exemplo 3, este exemplo localiza o valor no nó `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

E retorna este resultado: `"Paris"`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
