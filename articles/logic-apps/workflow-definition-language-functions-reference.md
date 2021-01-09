---
title: Guia de referência para funções em expressões
description: Guia de referência para funções em expressões para Apps LógicaS Azure e Automatização de Energia
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 222f6ebacb6139ca26a6f1cdd0f896270c9b2fc2
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034300"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Guia de referência para a utilização de funções em expressões para Apps lógicas Azure e Automatização de Energia

Para definições de fluxo de trabalho em [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Power Automamate,](/flow/getting-started) [algumas expressões](../logic-apps/logic-apps-workflow-definition-language.md#expressions) obtêm os seus valores de ações de tempo de execução que podem ainda não existir quando o seu fluxo de trabalho começa a funcionar. Para fazer referência a estes valores ou processar os valores destas expressões, pode utilizar *funções fornecidas* pela Linguagem de Definição de [Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md)

> [!NOTE]
> Esta página de referência aplica-se tanto a Azure Logic Apps como a Power Automamate, mas aparece na documentação Azure Logic Apps. Embora esta página se refira especificamente a aplicações lógicas, estas funções funcionam tanto para fluxos como para aplicações lógicas. Para obter mais informações sobre funções e expressões no Power Automamate, consulte [expressões de utilização em condições](/flow/use-expressions-in-conditions).

Por exemplo, pode calcular valores utilizando funções matemáticas, como a função [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) quando se quer a soma de inteiros ou boias. Aqui estão outras tarefas exemplo que pode executar com funções:

| Tarefa | Sintaxe de função | Resultado |
| ---- | --------------- | ------ |
| Devolva uma corda em formato minúsculo. | toLower ('<*texto*>') <p>Por exemplo: toLower ('Olá') | "Olá" |
| Devolva um identificador globalmente único (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Para encontrar funções [com base no seu propósito geral,](#ordered-by-purpose)reveja as seguintes tabelas. Ou, para obter informações detalhadas sobre cada função, consulte a [lista alfabética.](#alphabetical-list)

## <a name="functions-in-expressions"></a>Funções em expressões

Para mostrar como usar uma função numa expressão, este exemplo mostra como pode obter o valor do `customerName` parâmetro e atribuir esse valor à propriedade utilizando a `accountName` função de [parâmetros numa](#parameters) expressão:

```json
"accountName": "@parameters('customerName')"
```

Aqui estão outras formas gerais de utilizar funções em expressões:

| Tarefa | Sintaxe de função numa expressão |
| ---- | -------------------------------- |
| Executar o trabalho com um item, passando este item para uma função. | " \@ < *funName*>(<*item*>)" |
| 1. Obtenha o valor do nome do *parâmetro* utilizando a função aninhada. `parameters()` </br>2. Realizar trabalho com o resultado passando esse valor para *funçõesName*. | " \@ < *funçãoName*> (parâmetros(parâmetros *<name*>)" |
| 1. Obtenha o resultado da *função* de função interna aninhadaName . </br>2. Passe o resultado para a *função exteriorName2*. | " \@ < *funçãoName2* *>(função <Name*>(<*item*>))) " |
| 1. Obtenha o resultado da *funçãoName*. </br>2. Dado que o resultado é um objeto com *propriedade propriedadeName,* obtenha o valor dessa propriedade. | " \@ < *funName*>(<*item*>).<*propriedadeName*>" |
|||

Por exemplo, a `concat()` função pode tomar dois ou mais valores de corda como parâmetros. Esta função combina essas cordas numa corda. Podes passar em cadeias literais, por exemplo, "Sophia" e "Owen" para que tenhas uma corda combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ou pode obter valores de cordas a partir de parâmetros. Este exemplo utiliza a `parameters()` função em cada `concat()` parâmetro e nos `firstName` `lastName` parâmetros. Em seguida, passa as cordas resultantes para a função de `concat()` modo a obter uma corda combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, ambos os exemplos atribuem o resultado à `customerName` propriedade.

Aqui estão algumas outras notas sobre funções em expressões:

* Os parâmetros de função são avaliados da esquerda para a direita.

* Na sintaxe para definições de parâmetros, um ponto de interrogação (?) que aparece após um parâmetro significa que o parâmetro é opcional. Por exemplo, consulte [getFutureTime()](#getFutureTime).

As seguintes secções organizam funções com base no seu propósito geral, ou pode navegar nestas funções por [ordem alfabética](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funções de cadeia

Para trabalhar com cordas, pode utilizar estas funções de corda e também [algumas funções de recolha.](#collection-functions) As funções de corda funcionam apenas em cordas.

| Função de corda | Tarefa |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combine duas ou mais cordas e devolva a corda combinada. |
| [terminaWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifique se uma corda termina com o sub-adcção especificado. |
| [formatoNumbre](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Devolva um número como uma cadeia com base no formato especificado |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gere um identificador globalmente único (GUID) como uma corda. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Volte a colocar a posição inicial para um sub-adiscão. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Volte a colocar a posição inicial para a última ocorrência de um sub-adcões. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Devolva o número de itens numa cadeia ou matriz. |
| [substituir](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substitua um sub-adc de sublagem com a cadeia especificada e devolva a cadeia atualizada. |
| [divisão](../logic-apps/workflow-definition-language-functions-reference.md#split) | Devolva uma matriz que contenha sublutos, separados por vírgulas, de uma corda maior baseada num personagem delimiter especificado na cadeia original. |
| [começacom](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verifique se uma corda começa com um sub-adcção específico. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Devolva os caracteres de uma corda, a partir da posição especificada. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Devolva uma corda em formato minúsculo. |
| [toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Devolva uma corda em formato maiúscula. |
| [guarnição](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remova o espaço branco de uma corda e devolva a corda atualizada. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, geralmente matrizes, cordas e, por vezes, dicionários, você pode usar estas funções de coleção.

| Função de recolha | Tarefa |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifique se uma coleção tem um item específico. |
| [vazio](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. |
| [primeiro](../logic-apps/workflow-definition-language-functions-reference.md#first) | Devolva o primeiro item de uma coleção. |
| [intersecção](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Devolva uma coleção que tenha *apenas* os itens comuns em todas as coleções especificadas. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando estiver dentro de uma ação repetida sobre uma matriz, volte a colocar o item atual na matriz durante a iteração atual da ação. |
| [juntar-se](../logic-apps/workflow-definition-language-functions-reference.md#join) | Devolva uma corda que tenha *todos os* itens de uma matriz, separada pelo carácter especificado. |
| [último](../logic-apps/workflow-definition-language-functions-reference.md#last) | Devolva o último item de uma coleção. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Devolva o número de itens numa cadeia ou matriz. |
| [saltar](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens. |
| [tomar](../logic-apps/workflow-definition-language-functions-reference.md#take) | Devolva os artigos da parte da frente de uma coleção. |
| [união](../logic-apps/workflow-definition-language-functions-reference.md#union) | Devolva uma coleção que tenha *todos os* itens das coleções especificadas. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funções de comparação lógica

Para trabalhar com condições, comparar valores e resultados de expressão, ou avaliar vários tipos de lógica, pode utilizar estas funções de comparação lógica. Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Se utilizar funções ou condições lógicas para comparar valores, os valores nulos são convertidos em valores de corda vazia `""` (). O comportamento das condições difere quando se compara com uma corda vazia em vez de um valor nulo. Para obter mais informações, consulte a [função de corda().](#string) 

| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifique se todas as expressões são verdadeiras. |
| [é igual a](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifique se o primeiro valor é maior do que o segundo valor. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [se](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [menos](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifique se o primeiro valor é inferior ao segundo valor. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifique se o primeiro valor é inferior ou igual ao segundo valor. |
| [não](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifique se uma expressão é falsa. |
| [ou](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou formato de um valor, pode utilizar estas funções de conversão. Por exemplo, pode alterar um valor de um Boolean para um inteiro. Para obter mais informações sobre como as Aplicações Lógicas lidam com os tipos de conteúdo durante a conversão, consulte [os tipos de conteúdo handle](../logic-apps/logic-apps-content-type.md). Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> A Azure Logic Apps converte automaticamente valores entre alguns tipos de dados, o que significa que não é preciso realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [matriz](../logic-apps/workflow-definition-language-functions-reference.md#array) | Devolva uma matriz a partir de uma única entrada especificada. Para várias entradas, consulte [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retornar a versão codificada base64 para uma cadeia. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Devolva a versão binária para uma cadeia codificada base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Devolva a versão de corda para uma corda codificada base64. |
| [binário](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Devolva a versão binária por um valor de entrada. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Devolva a versão Boolean por um valor de entrada. |
| [criarArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Devolva os dados URI por um valor de entrada. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Devolva a versão binária para um URI de dados. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Devolva a versão de cadeia para um URI de dados. |
| [descodificarBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Devolva a versão de corda para uma corda codificada base64. |
| [descodificarDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Devolva a versão binária para um URI de dados. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retornar uma corda que substitua os caracteres de fuga por versões descodificadas. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retorna uma corda que substitui caracteres inseguros por URL por caracteres de fuga. |
| [flutuante](../logic-apps/workflow-definition-language-functions-reference.md#float) | Devolva um número de ponto flutuante para obter um valor de entrada. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Devolva a versão inteiro por uma corda. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retornar o valor ou objeto do tipo JavaScript (JSON) para uma cadeia ou XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Devolva a versão de corda para obter um valor de entrada. |
| [uriComponente](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retornar a versão codificada uri por um valor de entrada substituindo caracteres inseguros por URL por caracteres de fuga. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Devolva a versão binária para uma corda codificada uri. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Devolva a versão de corda para uma corda codificada uri. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Devolva a versão XML para uma corda. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Conversões implícitas do tipo de dados

As Azure Logic Apps convertem-se automaticamente ou implicitamente entre alguns tipos de dados, para que não tenha de converter manualmente estes tipos. Por exemplo, se utilizar valores não-cordas onde as cordas são esperadas como entradas, as Aplicações Lógicas convertem automaticamente os valores de não-cordas em cordas.

Por exemplo, suponha que um gatilho devolva um valor numérico como saída:

`triggerBody()?['123']`

Se utilizar esta saída numérica onde se espera a entrada de cordas, como um URL, as Aplicações Lógicas convertem automaticamente o valor numa cadeia utilizando a notação de suspensão encaracolada `{}` ( )

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Codificação e descodagem da Base64

A Logic Apps executa automaticamente ou implicitamente a codificação ou descodagem base64, para que não tenha de realizar manualmente estas operações utilizando as expressões correspondentes:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Se adicionar manualmente estas expressões à sua aplicação lógica, por exemplo, utilizando o editor de expressão, navegue para longe do Designer de Aplicações Lógicas e regresse ao designer, o designer mostra apenas os valores dos parâmetros. As expressões só são preservadas na visualização de código se não editar os valores dos parâmetros. Caso contrário, as Aplicações Lógicas removem as expressões da visão de código, deixando apenas os valores dos parâmetros. Este comportamento não afeta a codificação ou descodição, apenas se as expressões são mostradas.

<a name="math-functions"></a>

## <a name="math-functions"></a>Funções matemáticas

Para trabalhar com inteiros e carros alegóricos, pode usar estas funções matemáticas.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função matemática | Tarefa |
| ------------- | ---- |
| [adicionar](../logic-apps/workflow-definition-language-functions-reference.md#add) | Devolva o resultado da adição de dois números. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Devolva o resultado da divisão de dois números. |
| [máx](../logic-apps/workflow-definition-language-functions-reference.md#max) | Devolva o valor mais alto de um conjunto de números ou de uma matriz. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Devolva o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Devolva o resto da divisão de dois números. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retire o produto da multiplicação de dois números. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Devolva um inteiro aleatório de um alcance especificado. |
| [gama](../logic-apps/workflow-definition-language-functions-reference.md#range) | Devolva uma matriz de inteiros que começa a partir de um inteiro especificado. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Devolva o resultado da subtração do segundo número do primeiro número. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horários, pode utilizar estas funções de data e hora.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicione alguns dias a uma hora de tempo. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicione algumas horas a um tempotando. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicione alguns minutos a uma hora de tempo. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicione alguns segundos a um tempotando. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adicione um número de unidades de tempo a um relógio de tempo. Ver também [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [converterFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converta um relógio de tempo da Universal Time Coordinated (UTC) para o fuso horário-alvo. |
| [converterTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converta um relógio do fuso horário de origem para o fuso horário alvo. |
| [converterToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converter um tempotando do fuso horário de origem para o tempo universal coordenado (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Devolva o componente do dia do mês a partir de uma hora de tempo. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Devolva o componente do dia da semana a partir de uma estamp de tempo. |
| [diaOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Devolva o componente do dia do ano a partir de um timetamp. |
| [formatoDa hora](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Devolva a data de um tempo. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retornar a temperatura atual mais as unidades de tempo especificadas. Consulte também [o AddToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retornar o tempo de tempo atual menos as unidades de tempo especificadas. Consulte também [subtrairFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Volte o início do dia para uma hora de tempo. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Volte a ligar o início da hora para uma hora marcada. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Devolva o início do mês para uma hora de tempo. |
| [subtraindoFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtraia uma série de unidades de tempo de um relógio. Consulte também [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [carrapatos](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Devolva o `ticks` valor da propriedade para um tempotando especificado. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Devolva a atual estação de tempo como uma corda. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funções de fluxo de trabalho

Estas funções de fluxo de trabalho podem ajudá-lo:

* Obtenha detalhes sobre uma instância de fluxo de trabalho na hora de execução.
* Trabalhe com as entradas utilizadas para instantanear aplicações lógicas ou fluxos.
* Referenciar as saídas de gatilhos e ações.

Por exemplo, pode referenciar as saídas de uma ação e utilizar esses dados numa ação posterior.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de fluxo de trabalho | Tarefa |
| ----------------- | ---- |
| [ação](../logic-apps/workflow-definition-language-functions-reference.md#action) | Devolva a saída da ação atual em tempo de execução, ou valores de outros pares de nome e valor JSON. Consulte também [as ações.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Devolva a saída de uma ação `body` em tempo de execução. Ver também [o corpo.](../logic-apps/workflow-definition-language-functions-reference.md#body) |
| [açãoOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Devolva a saída de uma ação em tempo de execução. Ver [saídas](../logic-apps/workflow-definition-language-functions-reference.md#outputs) e [ações.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Devolva a saída de uma ação em tempo de execução, ou valores de outros pares de nome e valor JSON. Consulte também [a ação.](../logic-apps/workflow-definition-language-functions-reference.md#action)  |
| [corpo](#body) | Devolva a saída de uma ação `body` em tempo de execução. Consulte também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formaDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Crie uma matriz com os valores que correspondam a um *nome-chave* em saídas de ação codificadas por *formulários* ou formulários. |
| [formaDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Devolva um único valor que corresponda a um *nome-chave* nos dados de formulário ou *na saída codificada por formulários de* uma ação . |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando estiver dentro de uma ação repetida sobre uma matriz, volte a colocar o item atual na matriz durante a iteração atual da ação. |
| [itens](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando estiver dentro de um Foreach ou até ao loop, devolva o item atual do laço especificado.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Quando estiver dentro de um loop Until, devolva o valor de índice para a iteração atual. Pode utilizar esta função dentro de aninhada Até que os laços. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Devolva o "URL de retorno" que chama um gatilho ou ação. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Devolva o corpo para uma parte específica na saída de uma ação que tenha várias partes. |
| [saídas](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Devolva a saída de uma ação em tempo de execução. |
| [parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Devolva o valor para um parâmetro descrito na definição de fluxo de trabalho. |
| [resultado](../logic-apps/workflow-definition-language-functions-reference.md#result) | Retornar as entradas e saídas de todas as ações dentro da ação especificada, tais `For_each` `Until` como, e `Scope` . |
| [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Devolva a saída de um gatilho em tempo de execução, ou de outros pares de nome e valor JSON. Consulte também [os gatilhosOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Devolva a saída de um gatilho `body` em tempo de execução. Ver [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Devolva um único valor correspondente a um *nome-chave em saídas de gatilho codificadas* por formulários ou *por formulários.* |
| [gatilhoMultipartCorp](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Devolva o corpo para uma parte específica na saída multipart de um gatilho. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crie uma matriz cujos valores correspondam a um nome chave em *saídas de gatilhos* codificadas por *formulários* ou por formulários. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Devolva a saída de um gatilho em tempo de execução, ou valores de outros pares de nome e valor JSON. Ver [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variáveis](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Devolva o valor para uma variável especificada. |
| [fluxo de trabalho](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Devolva todos os detalhes sobre o fluxo de trabalho em si durante o tempo de funcionaamento. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funções de análise URI

Para trabalhar com identificadores de recursos uniformes (URIs) e obter vários valores de propriedade para estes URIs, você pode usar estas funções de análise URI.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de análise URI | Tarefa |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Devolva o `host` valor de um identificador de recursos uniforme (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Devolva o `path` valor de um identificador de recursos uniforme (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Devolva os `path` valores e `query` valores para um identificador de recursos uniforme (URI). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Devolva o `port` valor de um identificador de recursos uniforme (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Devolva o `query` valor de um identificador de recursos uniforme (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Devolva o `scheme` valor de um identificador de recursos uniforme (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funções de manipulação: JSON & XML

Para trabalhar com objetos JSON e nós XML, pode utilizar estas funções de manipulação.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de manipulação | Tarefa |
| --------------------- | ---- |
| [adicionarProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicione uma propriedade e o seu valor, ou par de valor-nome, a um objeto JSON, e devolva o objeto atualizado. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Devolva o primeiro valor não nulo de um ou mais parâmetros. |
| [removerProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Remova uma propriedade de um objeto JSON e devolva o objeto atualizado. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Desaje o valor da propriedade de um objeto JSON e devolva o objeto atualizado. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verifique se xML se trata de nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Todas as funções - lista alfabética

Esta secção lista todas as funções disponíveis por ordem alfabética.

<a name="action"></a>

### <a name="action"></a>ação

Devolva a saída da ação *atual* em tempo de execução, ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão.
Por predefinição, esta função refere todo o objeto de ação, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Consulte também [as ações()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Só pode utilizar a `action()` função nestes locais:

* A `unsubscribe` propriedade para uma ação webhook para que você possa aceder ao resultado a partir do pedido original `subscribe`
* A `trackedProperties` propriedade para uma ação
* A `do-until` condição do loop para uma ação

```
action()
action().outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*propriedade*> | Não | String | O nome da propriedade do objeto de ação cujo valor pretende: **nome**, **startTime**, **endTime,** **entradas,** **saídas,** **estado,** **código,** **trackingId** e **clientTrackingId**. No portal Azure, você pode encontrar estas propriedades revendo os detalhes de um histórico de execução específico. Para obter mais informações, consulte [REST API - Workflow Run Actions](/rest/api/logic/workflowrunactions/get). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*ação-saída*> | String | A saída da ação ou propriedade atual |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Devolva a saída de uma ação `body` em tempo de execução.
Abreviatura para `actions('<actionName>').outputs.body` .
Ver [corpo()](#body) e [ações()](#actions).

```
actionBody('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome para a saída da ação `body` que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*acção-corpo-saída*> | String | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a `body` saída da ação do `Get user` Twitter:

```
actionBody('Get_user')
```

E devolve este resultado:

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

### <a name="actionoutputs"></a>açãoOutputs

Devolva a saída de uma ação em tempo de execução.  e é abreviatura para `actions('<actionName>').outputs` . Ver [ações()](#actions). A `actionOutputs()` função resolve-se `outputs()` no Logic App Designer, por isso considere usar [saídas()](#outputs)em vez de `actionOutputs()` . Embora ambas as funções funcionem da mesma forma, `outputs()` é preferível.

```
actionOutputs('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome para a saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*saída*> | String | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída da ação do `Get user` Twitter:

```
actionOutputs('Get_user')
```

E devolve este resultado:

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

Devolva a saída de uma ação em tempo de execução, ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão. Por predefinição, a função refere todo o objeto de ação, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Para versões abreviaturas, consulte [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)e [body()](#body).
Para a ação atual, consulte [ação()](#action).

> [!TIP]
> A `actions()` função retorna a saída como uma corda. Se precisar de trabalhar com um valor devolvido como objeto JSON, primeiro tem de converter o valor da corda. Pode transformar o valor da corda num objeto JSON utilizando a ação [Parse JSON](logic-apps-perform-data-operations.md#parse-json-action).

> [!NOTE]
> Anteriormente, pode utilizar a `actions()` função ou o `conditions` elemento ao especificar que uma ação foi operada com base na saída de outra ação. No entanto, para declarar explicitamente dependências entre ações, deve agora usar o bem da ação `runAfter` dependente.
> Para saber mais sobre a `runAfter` propriedade, consulte [Catch e manuseie falhas com a propriedade runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome do objeto de ação cuja saída você quer  |
| <*propriedade*> | Não | String | O nome da propriedade do objeto de ação cujo valor pretende: **nome**, **startTime**, **endTime,** **entradas,** **saídas,** **estado,** **código,** **trackingId** e **clientTrackingId**. No portal Azure, você pode encontrar estas propriedades revendo os detalhes de um histórico de execução específico. Para obter mais informações, consulte [REST API - Workflow Run Actions](/rest/api/logic/workflowrunactions/get). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*ação-saída*> | String | A saída da ação ou propriedade especificada |
||||

*Exemplo*

Este exemplo obtém o `status` valor da propriedade da ação do Twitter em tempo de `Get user` execução:

```
actions('Get_user').outputs.body.status
```

E devolve este resultado: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>adicionar

Devolva o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, *summand_2 <*> | Sim | Inteiro, Flutuante ou Misturado | Os números a adicionar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*resultado-soma*> | Inteiro ou Flutuante | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E devolve este resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione alguns dias a uma hora de tempo.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*Dias*> | Sim | Número inteiro | O número positivo ou negativo de dias a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de dias  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias à hora de tempo especificada:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E devolve este resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias da hora especificada:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E devolve este resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione algumas horas a um tempotando.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas à hora de tempo especificada:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas da hora especificada:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E devolve este resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione alguns minutos a uma hora de tempo.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos à hora de tempo especificada:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E devolve este resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos da hora especificada:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E devolve este resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>adicionarProperty

Adicione uma propriedade e o seu valor, ou par de valor-nome, a um objeto JSON, e devolva o objeto atualizado. Se a propriedade já existir em tempo de execução, a função falha e lança um erro.

```
addProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON onde você quer adicionar uma propriedade |
| <*propriedade*> | Sim | String | O nome para a propriedade a adicionar |
| <*valor*> | Sim | Qualquer | O valor do imóvel |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado com a propriedade especificada |
||||

Para adicionar uma propriedade infantil a uma propriedade existente, use esta sintaxe:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON onde você quer adicionar uma propriedade |
| <*propriedade-mãe*> | Sim | String | O nome para propriedade dos pais onde você quer adicionar a propriedade da criança |
| <*propriedade infantil*> | Sim | String | O nome para a propriedade da criança para adicionar |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo adiciona a `middleName` propriedade a um objeto JSON, que é convertido de uma corda para JSON usando a função [JSON().](#json) O objeto já inclui as `firstName` `surName` propriedades e propriedades. A função atribui o valor especificado à nova propriedade e devolve o objeto atualizado:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Aqui está o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Aqui está o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo adiciona a `middleName` propriedade da criança à propriedade existente num objeto `customerName` JSON, que é convertido de uma corda para JSON utilizando a função [JSON().](#json) A função atribui o valor especificado à nova propriedade e devolve o objeto atualizado:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Aqui está o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Aqui está o objeto JSON atualizado:

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

Adicione alguns segundos a um tempotando.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a adicionar |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de segundos  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos à temperatura prevista:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para a hora especificada:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E devolve este resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicione um número de unidades de tempo a um relógio de tempo.
Consulte também [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A hora da hora mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia à hora de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E devolve este resultado: `"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia à hora de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E devolve o resultado usando o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se todas as expressões são verdadeiras.
Devolva-se quando todas as expressões são verdadeiras, ou retorna falsas quando pelo menos uma expressão é falsa.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Retornar verdadeiro quando todas as expressões são verdadeiras. Devolva-se falso quando pelo menos uma expressão é falsa. |
||||

*Exemplo 1*

Estes exemplos verificam se os valores booleanos especificados são todos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões são verdadeiras, por isso `true` retorna.
* Segundo exemplo: Uma expressão é falsa, por isso `false` retorna.
* Terceiro exemplo: Ambas as expressões são falsas, por isso devoluções `false` .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões são verdadeiras, por isso `true` retorna.
* Segundo exemplo: Uma expressão é falsa, por isso `false` retorna.
* Terceiro exemplo: Ambas as expressões são falsas, por isso devoluções `false` .

<a name="array"></a>

### <a name="array"></a>matriz

Devolva uma matriz a partir de uma única entrada especificada.
Para várias entradas, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para criar uma matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Matriz | Uma matriz que contém a única entrada especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir da cadeia "olá":

```
array('hello')
```

E devolve este resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada base64 para uma cadeia.

> [!NOTE]
> A Azure Logic Apps executa automaticamente a codificação e descodificamento base64, o que significa que não tem de realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

```
base64('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia de entrada |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada de base64 para a cadeia de entrada |
||||

*Exemplo*

Este exemplo converte a cadeia "olá" para uma cadeia codificada base64:

```
base64('hello')
```

E devolve este resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Devolva a versão binária para uma cadeia codificada base64.

> [!NOTE]
> A Azure Logic Apps executa automaticamente a codificação e descodificamento base64, o que significa que não tem de realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

```
base64ToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada base64 para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-base64-corda*> | String | A versão binária para a cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia codificada "aGVsbG8=" para uma cadeia binária:

```
base64ToBinary('aGVsbG8=')
```

E devolve este resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Devolva a versão de corda para uma corda codificada base64, descodificando eficazmente a cadeia base64. Utilize esta função em vez [de descodificar a Base64,,](#decodeBase64)que é depreciada.

> [!NOTE]
> A Azure Logic Apps executa automaticamente a codificação e descodificamento base64, o que significa que não tem de realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

```
base64ToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada de base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada base64-cadeia*> | String | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia codificada "aGVsbG8=" para apenas uma corda:

```
base64ToString('aGVsbG8=')
```

E devolve este resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Devolva a versão binária para uma corda.

```
binary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor binário para a entrada*> | String | A versão binária para a cadeia especificada |
||||

*Exemplo*

Este exemplo converte a cadeia "olá" a uma corda binária:

```
binary('hello')
```

E devolve este resultado:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Devolva a saída de uma ação `body` em tempo de execução.
Abreviatura para `actions('<actionName>').outputs.body` .
Ver [ActionBody()](#actionBody) e [ações()](#actions).

```
body('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome para a saída da ação `body` que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*acção-corpo-saída*> | String | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a `body` saída da ação do `Get user` Twitter:

```
body('Get_user')
```

E devolve este resultado:

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

### <a name="bool"></a>bool

Devolva a versão Booleana de um valor.

```
bool(<value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter para Boolean. |
|||||

Se estiver a usar `bool()` um objeto, o valor do objeto deve ser uma corda ou inteiro que pode ser convertido em Boolean.

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| `true` ou `false` | Booleano | A versão Booleana do valor especificado. |
||||

*Saídas*

Estes exemplos mostram os diferentes tipos de entrada suportados `bool()` para:

| Valor de entrada | Tipo | Valor devolvido |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Número inteiro | `true` |
| `bool(0)` | Número inteiro    | `false` |
| `bool(-1)` | Número inteiro | `true` |
| `bool('true')` | Cadeia | `true` |
| `bool('false')` | Cadeia | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Devolva o primeiro valor não nulo de um ou mais parâmetros.
Cordas vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2 object_2*>... | Sim | Qualquer, pode misturar tipos | Um ou mais itens para verificar se há nulo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro não-náu ponto*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, esta função retorna nulo. |
||||

*Exemplo*

Estes exemplos devolvem o primeiro valor não nulo dos valores especificados, ou nulos quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E devolve estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combine duas ou mais cordas e devolva a corda combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*texto2*>, ... | Sim | String | Pelo menos duas cordas para combinar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | A corda criada a partir das cordas de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cordas "Olá" e "Mundo":

```
concat('Hello', 'World')
```

E devolve este resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Verifique se uma coleção tem um item específico.
Devolva-se quando o item for encontrado, ou devolva falso quando não for encontrado.
Esta função é sensível a casos.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, esta função funciona nestes tipos de coleção:

* Uma *corda* para encontrar um *sub-cordão*
* Uma *matriz* para encontrar um *valor*
* Um *dicionário* para encontrar uma *chave*

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | String, Array ou Dicionário | A coleção para verificar |
| <*valor*> | Sim | String, Array ou Dicionário, respectivamente | O item para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Devolva-se quando o item for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a cadeia "olá mundo" para o substring "mundo" e retorna verdadeiro:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a cadeia "olá mundo" para o substring "universo" e devolve falso:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>converterFromUtc

Converta um relógio de tempo da Universal Time Coordinated (UTC) para o fuso horário-alvo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário do alvo. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte um relógio de tempo para o fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um relógio de tempo para o fuso horário especificado e formato:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>converterTimeZone

Converta um relógio do fuso horário de origem para o fuso horário alvo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário da fonte. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário do alvo. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário-alvo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário especificado e formato:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>converterToUtc

Converter um tempotando do fuso horário de origem para o tempo universal coordenado (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário da fonte. Para obter nomes de fuso horário, consulte [os Valores do Índice do Fuso Horário](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)da Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*tempotamp convertido*> | String | A hora convertida para UTC |
||||

*Exemplo 1*

Este exemplo converte um tempotando para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E devolve este resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte um tempotando para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E devolve este resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>criarArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada únicas, consulte [matriz()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*>, ... | Sim | Qualquer, mas não misturado | Pelo menos dois itens para criar a matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*objeto1*>, <> de *objeto2,* ...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir destas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E devolve este resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Devolva um identificador de recursos uniformes de dados (URI) para uma cadeia.

```
dataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | O dado URI para a cadeia de entrada |
||||

*Exemplo*

Este exemplo cria um dado URI para a cadeia "olá":

```
dataUri('hello')
```

E devolve este resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Utilize esta função em vez [de descodificarDataUri()](#decodeDataUri).
Embora ambas as funções funcionem da mesma forma, `dataUriBinary()` é preferível.

```
dataUriToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | String | A versão binária para o dado URI |
||||

*Exemplo*

Este exemplo cria uma versão binária para este dado URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Devolva a versão de cadeia para um identificador de recursos uniforme de dados (URI).

```
dataUriToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | A versão de cadeia para o dado URI |
||||

*Exemplo*

Este exemplo cria uma cadeia para este dado URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Devolva o dia do mês de um tempotando.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-do-dia*> | Número inteiro | O dia do mês a partir da hora de tempo especificada |
||||

*Exemplo*

Este exemplo devolve o número para o dia do mês a partir desta hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E devolve este resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Volte o dia da semana de uma hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-de-semana*> | Número inteiro | O dia da semana a partir do horário especificado, onde domingo é 0, segunda-feira é 1, e assim por diante |
||||

*Exemplo*

Este exemplo devolve o número para o dia da semana a partir desta hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E devolve este resultado: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>diaOfYear

Volte o dia do ano de um timetamp.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia-do-ano*> | Número inteiro | O dia do ano a partir da hora de tempo especificada |
||||

*Exemplo*

Este exemplo devolve o número do dia do ano a partir desta hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E devolve este resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>descodificar Base64 (precotado)

Esta função é depreciada, por isso, por favor, utilize [a base64ToString()](#base64ToString) em vez disso.

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>descodificarDataUri

Devolva a versão binária para um identificador de recursos uniforme de dados (URI). Considere a utilização de [dadosUriToBinary()](#dataUriToBinary)e não `decodeDataUri()` . Embora ambas as funções funcionem da mesma forma, `dataUriToBinary()` é preferível.

> [!NOTE]
> A Azure Logic Apps executa automaticamente a codificação e descodificamento base64, o que significa que não tem de realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

```
decodeDataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia URI de dados para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | String | A versão binária para uma cadeia URI de dados |
||||

*Exemplo*

Este exemplo devolve a versão binária para este dado URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retornar uma corda que substitua os caracteres de fuga por versões descodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda com os personagens de fuga para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | String | A cadeia atualizada com os caracteres de fuga descodificados |
||||

*Exemplo*

Este exemplo substitui os caracteres de fuga nesta cadeia por versões descodificadas:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

E devolve este resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Devolva o resultado da divisão de dois números. Para obter o resultado remanescente, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuante | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuante | O número que divide o *dividendo,* mas não pode ser 0 |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do quociente*> | Inteiro ou Flutuante | O resultado da divisão do primeiro número pelo segundo número. Se o dividendo ou o divisor tiver o tipo Float, o resultado tem o tipo Float. <p><p>**Nota:** Para converter o resultado do flutuador num inteiro, tente [criar e chamar uma função em Azure](../logic-apps/logic-apps-azure-functions.md) a partir da sua aplicação lógica. |
||||

*Exemplo 1*

Ambos os exemplos devolvem este valor com o tipo Inteiro: `2`

```
div(10,5)
div(11,5)
```

*Exemplo 2*

Ambos os exemplos devolvem este valor com o tipo Float: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retornar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros por URL por caracteres de fuga. Considere usar [uriComponent()](#uriComponent), em vez de `encodeUriComponent()` . Embora ambas as funções funcionem da mesma forma, `uriComponent()` é preferível.

> [!NOTE]
> A Azure Logic Apps executa automaticamente a codificação e descodificamento base64, o que significa que não tem de realizar manualmente estas conversões. No entanto, se o fizer, poderá experimentar comportamentos inesperados de exibição, que não afetam as conversões reais, apenas como são mostradas. Para obter mais informações, consulte [conversões implícitas do tipo de dados](#implicit-data-conversions).

```
encodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter para formato codificado URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A cadeia codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada uri para esta cadeia:

```
encodeUriComponent('https://contoso.com')
```

E devolve este resultado: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Devolva-se quando a coleção estiver vazia, ou devolva falsa quando não estiver vazia.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | String, Array ou Objeto | A coleção para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdadeiro quando a coleção estiver vazia. Devolva-se falso quando não estiver vazio. |
||||

*Exemplo*

Estes exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E devolve estes resultados:

* Primeiro exemplo: Passa uma corda vazia, para que a função retorne `true` .
* Segundo exemplo: Passa a cadeia "abc", para que a função retorne `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma corda termina com um sublamamento específico.
Devolva-se quando o sub-adcção for encontrado, ou devolva falso quando não for encontrado.
Esta função não é sensível a casos.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para verificar |
| <*searchText*> | Sim | String | O final substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdadeiro quando o sub-final for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "Hello world" termina com a cadeia "mundo":

```
endsWith('hello world', 'world')
```

E devolve este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a corda "Hello world" termina com a cadeia "universo":

```
endsWith('hello world', 'universe')
```

E devolve este resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifique se ambos os valores, expressões ou objetos são equivalentes.
Devolva-se quando ambos são equivalentes, ou devolva falso quando não são equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*> | Sim | Vários | Os valores, expressões ou objetos para comparar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando ambos são equivalentes. Devolva falso quando não é equivalente. |
||||

*Exemplo*

Estes exemplos verificam se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E devolve estes resultados:

* Primeiro exemplo: Ambos os valores são equivalentes, pelo que a função retorna `true` .
* Segundo exemplo: Ambos os valores não são equivalentes, pelo que a função retorna `false` .

<a name="first"></a>

### <a name="first"></a>primeiro

Devolva o primeiro item de uma corda ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro artigo de recolha*> | Qualquer | O primeiro item da coleção |
||||

*Exemplo*

Estes exemplos encontram o primeiro item nestas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E devolva estes resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de corda para um número de ponto flutuante para um número de ponto flutuante real.
Só pode utilizar esta função quando passar parâmetros personalizados para uma aplicação, por exemplo, uma aplicação lógica ou fluxo.

```
float('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia que tem um número de ponto flutuante válido para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor flutuante*> | Float | O número do ponto flutuante para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão de corda para este número de ponto flutuante:

```
float('10.333')
```

E devolve este resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatoDa hora

Devolva uma estada de tempo no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*reformatizado-timestamp*> | String | A datatampada atualizada no formato especificado |
||||

*Exemplo*

Este exemplo converte um tempotamp ao formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E devolve este resultado: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formaDataMultiValues

Devolva um conjunto com valores que correspondam a um *nome-chave* nos dados de formulário ou na saída *codificada por formulários de* uma ação.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | A ação cuja saída tem o valor-chave que você quer |
| <*chave*> | Sim | String | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> de *valores-chave com valores-chave]* | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir do valor da tecla "Assunto" nos dados de formulário ou na saída codificada da ação especificada:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E devolve o texto do assunto numa matriz, por exemplo: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formaDataValue

Devolva um único valor que corresponda a um *nome-chave* nos dados de formulário ou na saída *codificada por formulários de* uma ação.
Se a função encontrar mais de uma correspondência, a função lança um erro.

```
formDataValue('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | A ação cuja saída tem o valor-chave que você quer |
| <*chave*> | Sim | String | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor-chave*> | String | O valor na chave especificada  |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir do valor da tecla "Assunto" nos dados de formulário ou na saída codificada da ação especificada:

```
formDataValue('Send_an_email', 'Subject')
```

E devolve o texto do sujeito como uma corda, por exemplo: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatoNumbre

Devolva um número como uma corda que é baseada no formato especificado.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número*> | Sim | Inteiro ou Duplo | O valor que quer formatar. |
| <*formato*> | Sim | String | Uma cadeia de formato composto que especifica o formato que pretende utilizar. Para as cordas de formato numérico suportados, consulte [as cordas de formato numérico Standard](/dotnet/standard/base-types/standard-numeric-format-strings), que são suportadas por `number.ToString(<format>, <locale>)` . |
| <*local*> | Não | String | O local para usar como suportado por `number.ToString(<format>, <locale>)` . Se não for especificado, o valor predefinido é `en-us` . |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*número formatado*> | String | O número especificado como uma cadeia no formato especificado. Pode lançar este valor de retorno para `int` um ou `float` . |
||||

*Exemplo 1*

Suponha que queira formatar o `1234567890` número. Este exemplo forma esse número como a cadeia "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Exemplo 2"

Suponha que queira formatar o `1234567890` número. Este exemplo forma o número da cadeia "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Exemplo 3*

Suponha que queira formatar o `17.35` número. Este exemplo forma o número para a cadeia "$17.35".

```
formatNumber(17.35, 'C2')
```

*Exemplo 4*

Suponha que queira formatar o `17.35` número. Este exemplo forma o número para a cadeia "17,35 kr".

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar a temperatura atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A datatamp atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que a atual hora é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias a essa hora de fixação:

```
getFutureTime(5, 'Day')
```

E devolve este resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que a atual hora é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias e converte o resultado em formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E devolve este resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retornar o tempo de tempo atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | O tempotando atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que a atual hora é "2018-02-01T00:00:00.0000000Z".
Este exemplo subtrai cinco dias a partir dessa hora:

```
getPastTime(5, 'Day')
```

E devolve este resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que a atual hora é "2018-02-01T00:00:00.0000000Z".
Este exemplo subtrai cinco dias e converte o resultado em formato "D":

```
getPastTime(5, 'Day', 'D')
```

E devolve este resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifique se o primeiro valor é maior do que o segundo valor.
Devolva-se quando o primeiro valor for mais, ou devolva falso quando menos.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se é maior do que o segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é maior do que o segundo valor. Devolução falsa quando o primeiro valor é igual ou inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior do que o segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Devolva-se quando o primeiro valor for maior ou igual, ou devolva falso quando o primeiro valor é menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se é maior ou igual ao segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é maior ou igual ao segundo valor. Devolução falsa quando o primeiro valor é inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Gerar um identificador globalmente único (GUID) como uma corda, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, pode especificar um formato diferente para o GUID que não seja o formato padrão, "D", que é de 32 dígitos separados por hífenes.

```
guid('<format>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Um único [especificador de formato](/dotnet/api/system.guid.tostring?view=netcore-3.1#system_guid_tostring_system_string_) para o GUID devolvido. Por predefinição, o formato é "D", mas pode usar "N", "D", "B", "P" ou "X". |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Valor GUID*> | String | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hífenes, e fechados em parênteses:

```
guid('P')
```

E devolve este resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. Os parâmetros são avaliados da esquerda para a direita.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
| <*valorIfTrue*> | Sim | Qualquer | O valor para voltar quando a expressão é verdadeira |
| <*valorIfFalse*> | Sim | Qualquer | O valor a devolver quando a expressão é falsa |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de devolução especificado*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo retorna `"yes"` porque a expressão especificada retorna verdadeiramente.
Caso contrário, o exemplo `"no"` regressa:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Devolva a posição inicial ou o valor do índice para um sub-adcção.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-adc de ser encontrado |
| <*searchText*> | Sim | String | O sub-cordão para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*índice-valor*>| Número inteiro | A posição inicial ou o valor do índice para o sublpeso especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor inicial do índice para o substring "mundo" na cadeia "hello world":

```
indexOf('hello world', 'world')
```

E devolve este resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Devolva a versão inteiro por uma corda.

```
int('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado inteiro*> | Número inteiro | A versão inteiro para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão inteiro para a cadeia "10":

```
int('10')
```

E devolve este resultado: `10`

<a name="item"></a>

### <a name="item"></a>item

Quando utilizado no interior de uma ação de repetição sobre uma matriz, volte a colocar o item atual na matriz durante a iteração atual da ação.
Também pode obter os valores das propriedades desse item.

```
item()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*corrente-array-item*> | Qualquer | O item atual na matriz para a iteração atual da ação |
||||

*Exemplo*

Este exemplo obtém `body` o elemento da mensagem atual para a ação "Send_an_email" dentro da iteração atual de um loop:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>itens

Volte a colocar o item atual de cada ciclo num laço para cada ciclo.
Utilize esta função dentro do laço para cada loop.

```
items('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sim | String | O nome para cada loop |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*item*> | Qualquer | O item do ciclo atual no ciclo especificado para cada ciclo |
||||

*Exemplo*

Este exemplo obtém o item atual a partir do ciclo especificado para cada ciclo:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Devolva o valor de índice para a iteração atual dentro de um loop Until. Pode utilizar esta função dentro de aninhada Até que os laços. 

```
iterationIndexes('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sim | String | O nome para o loop Until | 
||||| 

| Valor devolvido | Tipo | Descrição | 
| ------------ | ---- | ----------- | 
| <*índice*> | Número inteiro | O valor do índice para a iteração atual dentro do referido até loop | 
|||| 

*Exemplo* 

Este exemplo cria uma variável de contador e incrementa essa variável por um durante cada iteração num loop Até que o valor de contador atinja cinco. O exemplo também cria uma variável que acompanha o índice atual para cada iteração. No loop Until, durante cada iteração, o exemplo incrementa o contador e, em seguida, atribui o valor de contador ao valor de índice atual e, em seguida, incrementa o contador. Enquanto no loop, este exemplo refere o índice de iteração atual utilizando a `iterationIndexes` função:

`iterationIndexes('Until_Max_Increment')`

```json
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
      "Until_Max_Increment": {
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
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
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
         "expression": "@equals(variables('myCounter'), 5)",
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

Retornar o valor ou objeto do tipo JavaScript (JSON) para uma cadeia ou XML.

```
json('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String ou XML | A corda ou XML para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Resultado do JSON*> | Tipo ou objeto nativo JSON | O valor ou objeto de tipo nativo JSON para a cadeia especificada ou XML. Se a corda for nula, a função devolve um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte esta cadeia ao valor JSON:

```
json('[1, 2, 3]')
```

E devolve este resultado: `[1, 2, 3]`

*Exemplo 2*

Este exemplo converte esta cadeia para JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E devolve este resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 3*

Este exemplo converte este XML para JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E devolve este resultado:

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

### <a name="intersection"></a>intersecção

Devolva uma coleção que tenha *apenas* os itens comuns em todas as coleções especificadas.
Para aparecer no resultado, deve aparecer um item em todas as coleções passadas a esta função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, <*coleção2*>, ... | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *apenas* os itens comuns |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*itens comuns*> | Matriz ou Objeto, respectivamente | Uma coleção que tem apenas os itens comuns em todas as coleções especificadas |
||||

*Exemplo*

Este exemplo encontra os itens comuns através destas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E devolve uma matriz *apenas* com estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Devolva uma corda que tenha todos os itens de uma matriz e tenha cada personagem separado por um *delimiter*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A matriz que tem os itens para se juntar |
| <*delimiter*> | Sim | String | O separador que aparece entre cada personagem na cadeia resultante |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*char1* >< *delimiter* >< *char2* ><> *delimitador...* | String | A cadeia resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir de todos os itens desta matriz com o carácter especificado como olimiter:

```
join(createArray('a', 'b', 'c'), '.')
```

E devolve este resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>último

Devolva o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o último item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*último artigo de recolha*> | String ou Array, respectivamente | O último item da coleção |
||||

*Exemplo*

Estes exemplos encontram o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E devolve estes resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou o valor do índice para a última ocorrência de um sub-adcção. Esta função não é sensível a casos, e os índices começam com o número 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-adc de ser encontrado |
| <*searchText*> | Sim | String | O sub-cordão para encontrar |
|||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de índice final*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência do sublpesto especificado. |
|||

Se o valor da cadeia ou do sub-adiscão estiver vazio, ocorre o seguinte comportamento:

* Se o valor da corda estiver vazio, a função retorna `-1` .

* Se os valores de cadeia e sub-adstring estiverem vazios, a função retorna `0` .

* Se apenas o valor de sub-adiscagem estiver vazio, a função devolve o comprimento da corda menos 1.

*Exemplos*

Este exemplo encontra o valor inicial do índice para a última ocorrência do `world` sub-adstring na cadeia `hello world hello world` . O resultado devolvido `18` é:

```json
lastIndexOf('hello world hello world', 'world')
```

Este exemplo falta o parâmetro de sub-adamento e devolve um valor `22` de porque o valor da cadeia de entrada `23` () menos 1 é superior a 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>length

Devolva o número de artigos numa coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção com os itens a contar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*comprimento ou contagem*> | Número inteiro | O número de itens na coleção |
||||

*Exemplo*

Estes exemplos contam o número de itens nestas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E devolva este resultado: `4`

<a name="less"></a>

### <a name="less"></a>less

Verifique se o primeiro valor é inferior ao segundo valor.
Devolva-se quando o primeiro valor é menor, ou devolva falso quando o primeiro valor é mais.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se menos do que o segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retorno verdadeiro quando o primeiro valor é inferior ao segundo valor. Devolução falsa quando o primeiro valor é igual ou superior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ao segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é inferior ou igual ao segundo valor.
Devolva-se quando o primeiro valor for inferior ou igual, ou devolva falso quando o primeiro valor for mais.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuante ou String | O primeiro valor para verificar se menos ou igual ao segundo valor |
| <*compararTo*> | Sim | Inteiro, Float ou String, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Retorno verdadeiro quando o primeiro valor é inferior ou igual ao segundo valor. Devolução falsa quando o primeiro valor é maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é menor ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E devolva estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Devolva o "URL de retorno" que chama um gatilho ou ação.
Esta função funciona apenas com gatilhos e ações para os tipos de conector **HttpWebhook** e **ApiConnectionWebhook,** mas não com os tipos **manual**, **Recorrência,** **HTTP** e **APIConnection.**

```
listCallbackUrl()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | O URL de retorno para um gatilho ou ação |
||||

*Exemplo*

Este exemplo mostra um URL de retorno de amostra que esta função pode voltar:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Devolva o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir dos quais você quer o valor mais alto |
| [<número *1*>, <> *número2,* ...] | Sim | Matriz - Inteiro, Float, ou ambos | A variedade de números a partir dos quais você quer o valor mais alto |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor máximo*> | Inteiro ou Flutuante | O valor mais elevado na matriz especificada ou conjunto de números |
||||

*Exemplo*

Estes exemplos obtêm o valor mais alto a partir do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E devolva este resultado: `3`

<a name="min"></a>

### <a name="min"></a>min

Devolva o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir dos quais se pretende o valor mais baixo |
| [<número *1*>, <> *número2,* ...] | Sim | Matriz - Inteiro, Float, ou ambos | A variedade de números a partir dos quais se quer o valor mais baixo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*min-valor*> | Inteiro ou Flutuante | O valor mais baixo no conjunto especificado de números ou matriz especificado |
||||

*Exemplo*

Estes exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E devolva este resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Devolva o resto da divisão de dois números.
Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuante | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuante | O número que divide o *dividendo,* mas não pode ser 0. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-resultado*> | Inteiro ou Flutuante | O restante de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E devolva este resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retire o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Inteiro ou Flutuante | O número a multiplicar por *multiplicand2* |
| <*multiplicand2*> | Sim | Inteiro ou Flutuante | O número que múltiplos *multiplicand1* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do produto*> | Inteiro ou Flutuante | O produto de multiplicar o primeiro número pelo segundo número |
||||

*Exemplo*

Estes exemplos múltiplos o primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E devolva estes resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Devolva o corpo para uma parte específica na saída de uma ação que tenha várias partes.

```
multipartBody('<actionName>', <index>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome para a ação que tem saída com várias partes |
| <*índice*> | Sim | Número inteiro | O valor do índice para a parte que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*corpo*> | String | O corpo para a parte especificada |
||||

<a name="not"></a>

### <a name="not"></a>not

Verifique se uma expressão é falsa.
Devolva-se quando a expressão é falsa, ou devolva falsa quando verdadeira.

```json
not(<expression>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Retornar verdadeiro quando a expressão é falsa. Devolva-se falsa quando a expressão é verdadeira. |
||||

*Exemplo 1*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E devolva estes resultados:

* Primeiro exemplo: A expressão é falsa, por isso a função retorna `true` .
* Segundo exemplo: A expressão é verdadeira, por isso a função retorna `false` .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E devolva estes resultados:

* Primeiro exemplo: A expressão é falsa, por isso a função retorna `true` .
* Segundo exemplo: A expressão é verdadeira, por isso a função retorna `false` .

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Devolva-se quando pelo menos uma expressão é verdadeira, ou devolva falsa quando todas são falsas.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Voltar verdadeiro quando pelo menos uma expressão é verdadeira. Devolva-se falsa quando todas as expressões são falsas. |
||||

*Exemplo 1*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E devolva estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é verdadeira, por isso a função retorna `true` .
* Segundo exemplo: Ambas as expressões são falsas, pelo que a função retorna `false` .

*Exemplo 2*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E devolva estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é verdadeira, por isso a função retorna `true` .
* Segundo exemplo: Ambas as expressões são falsas, pelo que a função retorna `false` .

<a name="outputs"></a>

### <a name="outputs"></a>saídas

Devolva as saídas de uma ação em tempo de execução. Utilize esta função, em vez de `actionOutputs()` , que se resolve no Logic App `outputs()` Designer. Embora ambas as funções funcionem da mesma forma, `outputs()` é preferível.

```
outputs('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | String | O nome para a saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*saída*> | String | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a saída da ação do `Get user` Twitter:

```
outputs('Get_user')
```

E devolve este resultado:

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

Devolva o valor para um parâmetro descrito na definição de fluxo de trabalho.

```
parameters('<parameterName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de parâmetroName*> | Sim | String | O nome do parâmetro cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*parâmetro-valor*> | Qualquer | O valor para o parâmetro especificado |
||||

*Exemplo*

Suponha que tem este valor JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Este exemplo obtém o valor para o parâmetro especificado:

```
parameters('fullName')
```

E devolve este resultado: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Devolva um inteiro aleatório de uma gama especificada, que é inclusiva apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O número mais baixo de inteiros na gama |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o mais alto inteiro na gama que a função pode devolver |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado aleatório*> | Número inteiro | O número inteiro aleatório devolvido da gama especificada |
||||

*Exemplo*

Este exemplo obtém um número inteiro aleatório da gama especificada, excluindo o valor máximo:

```
rand(1, 5)
```

E devolve um destes números como resultado: `1` `2` , ou `3``4`

<a name="range"></a>

### <a name="range"></a>gama

Devolva uma matriz de inteiros que começa a partir de um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| <*contar*> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de resultados de gama]* | Matriz | A matriz com inteiros a partir do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que começa a partir do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E devolve este resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substitua um sub-adc de sublagem com a corda especificada e devolva a cadeia de resultados. Esta função é sensível a casos.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o sub-cordão para substituir |
| <*oldText*> | Sim | String | O sub-cordão para substituir |
| <*newText*> | Sim | String | A cadeia de substituição |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | String | A cadeia atualizada após a substituição do sub-cordão <p>Se o sub-adc de sublagem não for encontrado, devolva a corda original. |
||||

*Exemplo*

Este exemplo encontra o sublpesante "velho" na "velha corda" e substitui "velho" por "novo":

```
replace('the old string', 'old', 'new')
```

E devolve este resultado: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removerProperty

Remova uma propriedade de um objeto e devolva o objeto atualizado. Se a propriedade que tenta remover não existir, a função devolve o objeto original.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON de onde você quer remover uma propriedade |
| <*propriedade*> | Sim | String | O nome para a propriedade para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado sem a propriedade especificada |
||||

Para remover uma propriedade infantil de uma propriedade existente, utilize esta sintaxe:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade você quer remover |
| <*propriedade-mãe*> | Sim | String | O nome para propriedade parental com a propriedade infantil que você quer remover |
| <*propriedade infantil*> | Sim | String | O nome da propriedade da criança para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade infantil que removeu |
||||

*Exemplo 1*

Este exemplo remove a `middleName` propriedade de um objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON,e](#json) devolve o objeto atualizado:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Aqui está o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Aqui está o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo remove a `middleName` propriedade da criança de uma `customerName` propriedade-mãe num objeto JSON, que é convertido de uma cadeia para JSON utilizando a função [JSON()](#json) e devolve o objeto atualizado:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Aqui está o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Aqui está o objeto JSON atualizado:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Devolva as entradas e saídas de todas as ações que estão dentro da ação especificada, como, `For_each` `Until` por exemplo, ou `Scope` ação. Esta função é útil devolvendo os resultados de uma ação falhada para que possa diagnosticar e lidar com exceções. Para obter mais informações, consulte [o contexto e os resultados para falhas.](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)

```
result('<scopedActionName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de aplicação de âmbito*> | Sim | String | O nome da ação a partir da qual devolver as entradas e saídas de todas as ações internas |
||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*conjunto de objeto*> | Objeto de matriz | Uma matriz que contém matrizes de entradas e saídas de cada ação que aparece dentro da ação especificada |
||||

*Exemplo*

Este exemplo devolve as entradas e saídas de cada iteração de uma ação HTTP no interior que está dentro de um `For_each` loop utilizando a `result()` função na `Compose` ação:

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

Aqui está como o conjunto de exemplo devolvido pode olhar onde o objeto exterior `outputs` contém as entradas e saídas de cada iteração das ações dentro da `For_each` ação.

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

Desaje o valor da propriedade do objeto JSON e devolva o objeto atualizado. Se a propriedade que você tenta definir não existe, a propriedade é adicionada ao objeto. Para adicionar um novo imóvel, utilize a função [addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade você quer definir |
| <*propriedade*> | Sim | String | O nome para a propriedade existente ou nova para definir |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

Para colocar a propriedade da criança num objeto infantil, utilize uma `setProperty()` chamada aninhada. Caso contrário, a função devolve apenas o objeto da criança como saída.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade você quer definir |
| <*propriedade-mãe*> | Sim | String | O nome para propriedade parental com a propriedade infantil que você quer definir |
| <*propriedade infantil*> | Sim | String | O nome para a propriedade da criança para definir |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo define a `surName` propriedade num objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON().](#json) A função atribui o valor especificado à propriedade e devolve o objeto atualizado:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Aqui está o objeto JSON atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Aqui está o objeto JSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exemplo 2*

Este exemplo define a `surName` propriedade da criança para a propriedade principal num objeto `customerName` JSON, que é convertido de uma corda para JSON utilizando a função [JSON().](#json) A função atribui o valor especificado à propriedade e devolve o objeto atualizado:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Aqui está o objeto JSON atual:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Aqui está o objeto JSON atualizado:

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

Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A coleção cujos itens você quer remover |
| <*contar*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens a remover na frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de recolha atualizada]* | Matriz | A recolha atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da parte frontal da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E devolve esta matriz com os restantes itens: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Devolva uma matriz que contenha sublutos, separadas por vírgulas, com base no carácter delimiter especificado na cadeia original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para separar em sublamentos com base no limonado especificado na cadeia original |
| <*delimiter*> | Sim | String | O personagem na corda original para usar como o delimiter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*><*substring2*>,...] | Matriz | Uma matriz que contém sublutos da corda original, separada por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com sublípedos a partir da cadeia especificada com base no carácter especificado como olimiter:

```
split('a_b_c', '_')
```

E devolve esta matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Volte o início do dia para uma hora de tempo.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir da marca de zero horas para o dia |
||||

*Exemplo*

Este exemplo encontra o início do dia para esta hora:

```
startOfDay('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Volte a ligar o início da hora para uma hora marcada.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir da marca de zero minutos para a hora |
||||

*Exemplo*

Este exemplo encontra o início da hora para esta hora:

```
startOfHour('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Devolva o início do mês para uma hora de tempo.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | A marca de tempo especificada, mas a partir do primeiro dia do mês na marca de zero horas |
||||

*Exemplo 1*

Este exemplo devolve o início do mês para esta hora:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E devolve este resultado: `"2018-03-01T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo devolve o início do mês no formato especificado para esta hora:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

E devolve este resultado: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma corda começa com um sub-adcção específico.
Devolva-se quando o sub-adcção for encontrado, ou devolva falso quando não for encontrado.
Esta função não é sensível a casos.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para verificar |
| <*searchText*> | Sim | String | A cadeia inicial para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdadeiro quando o sub-ataque inicial for encontrado. Devolva-se falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia "Hello world" começa com o sublpesamento "olá":

```
startsWith('hello world', 'hello')
```

E devolve este resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia "Hello world" começa com o sub-cordão das "saudações":

```
startsWith('hello world', 'greetings')
```

E devolve este resultado: `false`

<a name="string"></a>

### <a name="string"></a>string

Devolva a versão de corda por um valor.

```
string(<value>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter. Se este valor for nulo ou avaliar a nulo, o valor é convertido para um valor de cadeia vazia `""` ( ) <p><p>Por exemplo, se atribuir uma variável de corda a uma propriedade inexistente, à qual pode aceder com o `?` operador, o valor nulo é convertido numa cadeia vazia. No entanto, comparar um valor nulo não é o mesmo que comparar uma corda vazia. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de cordas*> | String | A versão de corda para o valor especificado. Se o parâmetro de *valor* for nulo ou avaliar a nulo, este valor é devolvido como um valor de cadeia vazia `""` ( ) |
||||





*Exemplo 1*

Este exemplo cria a versão de corda para este número:

```
string(10)
```

E devolve este resultado: `"10"`

*Exemplo 2*

Este exemplo cria uma cadeia para o objeto JSON especificado e utiliza o carácter de backslash \\ () como um personagem de fuga para a marca de dupla citação (").

```
string( { "name": "Sophie Owen" } )
```

E devolve este resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Devolva o resultado da subtração do segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Inteiro ou Flutuante | O número a partir do qual subtrair o *subtraído* |
| <*subtrahend*> | Sim | Inteiro ou Flutuante | O número para subtrair do *minuend* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou Flutuante | O resultado da subtração do segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E devolve este resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Devolva os caracteres de uma cadeia, a partir da posição especificada, ou índice. Os valores do índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda cujos personagens quer |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou superior a 0 que pretende utilizar como posição inicial ou valor de índice |
| <*comprimento*> | Não | Número inteiro | Um número positivo de caracteres que você quer no substring |
|||||

> [!NOTE]
> Certifique-se de que a soma da adição dos valores dos parâmetros *startIndex* e *comprimento* é inferior ao comprimento da cadeia que fornece para o parâmetro de *texto.*
> Caso contrário, obtém-se um erro, ao contrário de funções semelhantes noutras línguas onde o resultado é o sublamamento do *startIndex* até ao fim da cadeia. O parâmetro *de comprimento* é opcional e, se não for fornecido, a função **sub-atrof leva** todos os caracteres a partir do início do *Index* até à extremidade da cadeia.

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subdiscípe-resultado*> | String | Um sub-cordão com o número especificado de caracteres, a partir da posição de índice especificado na cadeia de origem |
||||

*Exemplo*

Este exemplo cria um sublamamento de cinco caracteres a partir da cadeia especificada, a partir do valor do índice 6:

```
substring('hello world', 6, 5)
```

E devolve este resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtraindoFromTime

Subtraia uma série de unidades de tempo de um relógio.
Consulte também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda que contém a hora da hora |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo a utilizar com *intervalo:*"Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*hora atualizada*> | String | O tempotamp menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia a partir desta hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E devolve este resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia a partir desta hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E devolve este resultado usando o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Devolva os artigos da parte da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção cujos itens você quer |
| <*contar*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens que você quer da frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*subconjunto*> ou [<*subconjunto*>] | String ou Array, respectivamente | Uma cadeia ou matriz que tem o número especificado de itens retirados da parte da frente da coleção original |
||||

*Exemplo*

Estes exemplos obtêm o número especificado de itens da parte da frente destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E devolva estes resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>carrapatos

Devolve o número de tiques, que são intervalos de 100-nanosegundos, desde 1 de janeiro de 001 12:00:00 meia-noite (ou DateTime.Ticks em C#) até à marca de tempo especificada. Para mais informações, consulte este tópico: [DateTime.Ticks Property (Sistema)](/dotnet/api/system.datetime.ticks?view=netframework-4.7.2#remarks).

```
ticks('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timetamp*> | Sim | String | A corda para uma hora de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carrapatos-número*> | Número inteiro | O número de carrapatos desde a marca de tempo especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Devolva uma corda em formato minúsculo. Se um personagem na corda não tiver uma versão minúscula, esse personagem permanece inalterado na corda devolvida.

```
toLower('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para voltar em formato minúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto minúsculo*> | String | A corda original em formato minúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em minúsculas:

```
toLower('Hello World')
```

E devolve este resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toupper

Devolva uma corda em formato maiúscula. Se um personagem na corda não tiver uma versão maiúscula, esse personagem permanece inalterado na corda devolvida.

```
toUpper('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda para voltar em formato maiúscula |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto maiúscula*> | String | A corda original em formato maiúscula |
||||

*Exemplo*

Este exemplo converte esta cadeia em maiúsculas:

```
toUpper('Hello World')
```

E devolve este resultado: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>gatilho

Devolva a saída de um gatilho em tempo de execução, ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão.

* Dentro das entradas de um gatilho, esta função retorna a saída da execução anterior.

* Dentro da condição de um gatilho, esta função retorna a saída da execução atual.

Por predefinição, a função refere todo o objeto do gatilho, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Além disso, esta função tem versões de abreviaturas disponíveis, ver [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody).

```
trigger()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*gatilho-saída*> | String | A saída de um gatilho no tempo de execução |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Devolva a saída de um gatilho `body` em tempo de execução.
Abreviatura para `trigger().outputs.body` .
Ver [gatilho()](#trigger).

```
triggerBody()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*gatilho-saída do corpo*> | String | A `body` saída do gatilho |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Devolva um conjunto com valores que correspondam a um nome chave nos *dados* de formulário ou na saída *codificada por formulários* do gatilho.

```
triggerFormDataMultiValues('<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*chave*> | Sim | String | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> de *valores-chave com valores-chave]* | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir do valor chave "feedUrl" nos dados de formulário ou saída codificada por formulários de um gatilho RSS:

```
triggerFormDataMultiValues('feedUrl')
```

E devolve esta matriz como um resultado exemplo: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Devolva uma cadeia com um único valor que corresponda a um nome chave nos *dados* de formulário ou na saída *codificada por formulários* do gatilho.
Se a função encontrar mais de uma correspondência, a função lança um erro.

```
triggerFormDataValue('<key>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*chave*> | Sim | String | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor-chave*> | String | O valor na chave especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir do valor chave "feedUrl" nos dados de formulário ou saída codificada por formulários de um gatilho RSS:

```
triggerFormDataValue('feedUrl')
```

E devolve esta corda como um resultado exemplo: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>gatilhoMultipartCorp

Devolva o corpo para uma parte específica na saída de um gatilho que tenha várias partes.

```
triggerMultipartBody(<index>)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*índice*> | Sim | Número inteiro | O valor do índice para a parte que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*corpo*> | String | O corpo para a parte especificada na saída multipart de um gatilho |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Devolva a saída de um gatilho em tempo de execução, ou valores de outros pares de nome e valor JSON.
Abreviatura para `trigger().outputs` .
Ver [gatilho()](#trigger).

```
triggerOutputs()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*gatilho-saída*> | String | A saída de um gatilho no tempo de execução  |
||||

<a name="trim"></a>

### <a name="trim"></a>guarnição

Remova o espaço branco de uma corda e devolva a corda atualizada.

```
trim('<text>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Texto*> | Sim | String | A corda que tem o espaço branco líder e trailing para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | String | Uma versão atualizada para a cadeia original sem liderar ou seguir espaço em branco |
||||

*Exemplo*

Este exemplo remove o espaço branco líder e trailing da cadeia " Hello World ":

```
trim(' Hello World  ')
```

E devolve este resultado: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>união

Devolva uma coleção que tenha *todos os* itens das coleções especificadas.
Para aparecer no resultado, um item pode aparecer em qualquer coleção passada para esta função. Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, <*coleção2*>, ...  | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *todos os* itens |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*actualizaçãoCollection*> | Matriz ou Objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas - sem duplicados |
||||

*Exemplo*

Este exemplo obtém *todos os* itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E devolve este resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponente

Retornar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros por URL por caracteres de fuga.
Utilize esta função em vez de [codificar oUriComponent()](#encodeUriComponent).
Embora ambas as funções funcionem da mesma forma, `uriComponent()` é preferível.

```
uriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda para converter para formato codificado URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | String | A cadeia codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada uri para esta cadeia:

```
uriComponent('https://contoso.com')
```

E devolve este resultado: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Devolva a versão binária para um componente de identificação de recursos uniforme (URI).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada uri para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-codificado-uri*> | String | A versão binária para a cadeia codificada uri. O conteúdo binário é codificado e representado por `$content` . |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia codificada uri:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

E devolve este resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retorna a versão de corda para uma cadeia codificada de identificação de recursos uniforme (URI), descodificando eficazmente a cadeia codificada uri.

```
uriComponentToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A cadeia codificada uri para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | String | A versão descodificada para a cadeia codificada uri |
||||

*Exemplo*

Este exemplo cria a versão de corda descodificada para esta cadeia codificada uri:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

E devolve este resultado: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Devolva o `host` valor de um identificador de recursos uniforme (URI).

```
uriHost('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `host` valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do hospedeiro*> | String | O `host` valor para o URI especificado |
||||

*Exemplo*

Este exemplo encontra o `host` valor para este URI:

```
uriHost('https://www.localhost.com:8080')
```

E devolve este resultado: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Devolva o `path` valor de um identificador de recursos uniforme (URI).

```
uriPath('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `path` valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do caminho*> | String | O `path` valor para o URI especificado. Se `path` não tiver um valor, devolva o caráter "/". |
||||

*Exemplo*

Este exemplo encontra o `path` valor para este URI:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Devolva os `path` valores e `query` valores para um identificador de recursos uniforme (URI).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `path` valores e `query` valores você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*caminho-consulta-valor*> | String | Os `path` `query` valores e valores para o URI especificado. Se `path` não especificar um valor, devolva o caractere "/". |
||||

*Exemplo*

Este exemplo encontra os `path` valores e `query` valores para este URI:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Devolva o `port` valor de um identificador de recursos uniforme (URI).

```
uriPort('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `port` valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor portuário*> | Número inteiro | O `port` valor para o URI especificado. Se `port` não especificar um valor, devolva a porta predefinido para o protocolo. |
||||

*Exemplo*

Este exemplo devolve o `port` valor deste URI:

```
uriPort('https://www.localhost:8080')
```

E devolve este resultado: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Devolva o `query` valor de um identificador de recursos uniforme (URI).

```
uriQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `query` valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*consulta-valor*> | String | O `query` valor para o URI especificado |
||||

*Exemplo*

Este exemplo devolve o `query` valor deste URI:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Devolva o `scheme` valor de um identificador de recursos uniforme (URI).

```
uriScheme('<uri>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujo `scheme` valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do esquema*> | String | O `scheme` valor para o URI especificado |
||||

*Exemplo*

Este exemplo devolve o `scheme` valor deste URI:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Devolva a atual estação de tempo.

```
utcNow('<format>')
```

Opcionalmente, pode especificar um formato diferente com o *<formato*> parâmetro.


| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | String | Ou um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato predefinido para a timetamp é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yy-MM-ddTHH:mm:ss.fffffK), que está em conformidade com a [NORMA 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva a informação do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*actual-timetamp*> | String | A data e hora atuais |
||||

*Exemplo 1*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a atual hora:

```
utcNow()
```

E devolve este resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a atual timetamp utilizando o formato "D" opcional:

```
utcNow('D')
```

E devolve este resultado: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variáveis

Devolva o valor para uma variável especificada.

```
variables('<variableName>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome variável*> | Sim | String | O nome da variável cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor variável*> | Qualquer | O valor para a variável especificada |
||||

*Exemplo*

Suponha que o valor atual para uma variável "numItems" é de 20.
Este exemplo obtém o valor inteiro desta variável:

```
variables('numItems')
```

E devolve este resultado: `20`

<a name="workflow"></a>

### <a name="workflow"></a>fluxo de trabalho

Devolva todos os detalhes sobre o fluxo de trabalho em si durante o tempo de funcionaamento.

```
workflow().<property>
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*propriedade*> | Não | String | O nome da propriedade workflow cujo valor você quer <p>Um objeto de fluxo de trabalho tem estas propriedades: **nome,** **tipo,** **id,** **localização,** e **execução.** O valor da propriedade **de execução** é também um objeto que tem estas propriedades: **nome,** **tipo,** e **id.** |
|||||

*Exemplo*

Este exemplo devolve o nome para a execução atual de um fluxo de trabalho:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Retornar a versão XML para uma cadeia que contenha um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | String | A corda com o objeto JSON para converter <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Utilize o carácter de backslash \\ () como um personagem de fuga para a marca de dupla citação ("). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*versão xml*> | Objeto | O XML codificado para o string especificado ou objeto JSON |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E devolve este resultado XML:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que tem este objeto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Este exemplo cria XML para uma cadeia que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E devolve este resultado XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verifique se xML se trata de nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda-o a navegar numa estrutura de documento XML para que possa selecionar nós ou valores de cálculo no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Obrigatório | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia XML para procurar nóles ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para encontrar nosdes ou valores XML correspondentes |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-nó*> | XML | Um nó XML quando apenas um nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um valor corresponde à expressão XPath especificada |
| [<> *xml-node1,* <*> xml-node2,* ...] </br>-ou- </br>[<*valor1*>, <*valor2*>, ...] | Matriz | Uma matriz com nódoas xml ou valores que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Suponha que tem esta `'items'` cadeia XML: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Este exemplo passa na expressão XPath, `'/produce/item/name'` para encontrar os nó que combinam com o nó na cadeia `<name></name>` `'items'` XML, e devolve uma matriz com esses valores de nó:

`xpath(xml(parameters('items')), '/produce/item/name')`

O exemplo também utiliza a função [parâmetros()](#parameters) para obter a cadeia XML `'items'` e converter a corda para o formato XML utilizando a função [xml().](#xml)

Aqui está o conjunto de resultados com os nóns que `<name></name` combinam:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemplo 2*

Seguindo o Exemplo 1, este exemplo passa na expressão XPath, `'/produce/item/name[1]'` para encontrar o primeiro elemento que é a criança do `name` `item` elemento.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Aqui está o resultado: `Gala`

*Exemplo 3*

Seguindo o Exemplo 1, este exemplo passa na expressão XPath, `'/produce/item/name[last()]'` para encontrar o último elemento que é a criança do `name` `item` elemento.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Aqui está o resultado: `Honeycrisp`

*Exemplo 4*

Neste exemplo, suponha que a sua `items` cadeia XML também contém os atributos, `expired='true'` `expired='false'` e:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Este exemplo passa na expressão XPath, `'//name[@expired]'` para encontrar todos os `name` elementos que têm o `expired` atributo:

`xpath(xml(parameters('items')), '//name[@expired]')`

Aqui está o resultado: `[ Gala, Honeycrisp ]`

*Exemplo 5*

Neste exemplo, suponha que a sua `items` cadeia XML contém apenas este atributo, `expired = 'true'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Este exemplo passa na expressão XPath, `'//name[@expired = 'true']'` para encontrar todos os `name` elementos que têm o atributo, `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Aqui está o resultado: `[ Gala ]`

*Exemplo 6*

Neste exemplo, suponha que a sua `items` cadeia XML também contém estes atributos: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Este exemplo passa na expressão XPath, `'//name[price>35]'` para encontrar todos os `name` elementos que `price > 35` têm:

`xpath(xml(parameters('items')), '//name[price>35]')`

Aqui está o resultado: `Honeycrisp`

*Exemplo 7*

Neste exemplo, suponha que a sua `items` cadeia XML é a mesma que no Exemplo 1:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Este exemplo encontra nóleiros que combinam com o `<count></count>` nó e adiciona esses valores de nó com a `sum()` função:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Aqui está o resultado: `30`

*Exemplo 8*

Neste exemplo, suponha que você tem esta cadeia XML, que inclui o espaço de nome de documento XML, `xmlns="https://contoso.com"` :

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Estas expressões usam a expressão `/*[name()="file"]/*[name()="location"]` `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` XPath, ou, para encontrar nós que correspondam ao `<location></location>` nó. Estes exemplos mostram a sintaxe que utiliza no Logic App Designer ou no editor de expressão:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Aqui está o nó de resultado que combina com o `<location></location>` nó: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Se trabalhar na visualização de código, escape à marca de dupla citação (") utilizando o carácter de backslash \\ (). 
> Por exemplo, tens de usar caracteres de fuga quando serializas uma expressão como uma corda JSON. 
> No entanto, se estiver a trabalhar no Logic App Designer ou no editor de expressão, não precisa de escapar à marca de dupla cotação porque o carácter backslash é adicionado automaticamente à definição subjacente, por exemplo:
> 
> * Vista de código: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor de expressão: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Exemplo 9*

Seguindo no Exemplo 8, este exemplo utiliza a expressão `'string(/*[name()="file"]/*[name()="location"])'` XPath, para encontrar o valor no `<location></location>` nó:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Aqui está o resultado: `Paris`

## <a name="next-steps"></a>Passos seguintes

Conheça a [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
