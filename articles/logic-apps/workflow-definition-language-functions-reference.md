---
title: Guia de referência para funções em expressões
description: Guia de referência para funções em expressões para Aplicações lógicas Azure e Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 87798c93bbc1098daea2f7258a3af3e26bb4bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283917"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Guia de referência para a utilização de funções em expressões para aplicações lógicas azure e automatização de potência

Para definições de fluxo de trabalho em [Aplicações lógicas Azure](../logic-apps/logic-apps-overview.md) e [Power Automate,](https://docs.microsoft.com/flow/getting-started) [algumas expressões](../logic-apps/logic-apps-workflow-definition-language.md#expressions) obtêm os seus valores a partir de ações de tempo de execução que podem ainda não existir quando o seu fluxo de trabalho começa a funcionar. Para fazer referência a estes valores ou processar os valores nestas expressões, pode utilizar *funções* fornecidas pela [Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md)de fluxo de trabalho. 

> [!NOTE]
> Esta página de referência aplica-se tanto às Aplicações Lógicas Azure como ao Power Automate, mas aparece na documentação das Aplicações Lógicas Azure. Embora esta página se refira especificamente a aplicações lógicas, estas funções funcionam tanto para fluxos como para aplicações lógicas. Para obter mais informações sobre funções e expressões no Power Automate, consulte [As expressões de utilização em condições](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Por exemplo, pode calcular valores utilizando funções matemáticas, como a [função add()](../logic-apps/workflow-definition-language-functions-reference.md#add)quando se deseja a soma de inteiros ou carros alegóricos. Aqui estão outros exemplos de tarefas que pode executar com funções:

| Tarefa | Sintaxe de função | Resultado |
| ---- | --------------- | ------ |
| Volte a colocar uma corda em formato minúsculo. | toLower *('<texto*>') <p>Por exemplo: para Lower ('Olá') | "Olá" |
| Devolva um identificador globalmente único (GUID). | guia() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Para encontrar funções [com base no seu propósito geral,](#ordered-by-purpose)reveja as seguintes tabelas. Ou, para informação detalhada sobre cada função, consulte a [lista alfabética.](#alphabetical-list)

> [!NOTE]
> Na sintaxe para definições de parâmetros, um ponto de interrogação (?) que aparece após um parâmetro significa que o parâmetro é opcional.
> Por exemplo, consulte [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funções em expressões

Para mostrar como usar uma função numa expressão, este exemplo `customerName` mostra como pode obter o `accountName` valor do parâmetro e atribuir esse valor à propriedade utilizando a função dos [parâmetros](#parameters) numa expressão:

```json
"accountName": "@parameters('customerName')"
```

Aqui estão outras formas gerais de usar funções em expressões:

| Tarefa | Sintaxe de função em uma expressão |
| ---- | -------------------------------- |
| Execute o trabalho com um item passando esse item para uma função. | \@<*"nome de função*> (> de> de *<)"* |
| 1. Obtenha o valor do *parâmetroNome*utilizando `parameters()` a função aninhada. </br>2. Realizar trabalhocom o resultado passando esse valor para *a funçãoNome*. | "\@<*funçãoNome*> ('<*parâmetrosName*>')" |
| 1. Obtenha o resultado da *função*interna aninhadaNome . </br>2. Passe o resultado para a *função exteriorNome2*. | "\@<*funçãoNome2*> *(<funçãoNome*> *(> do artigo* <)" |
| 1. Obtenha o resultado da *funçãoNome*. </br>2. Dado que o resultado é um objeto com *propriedade name,* obtenha o valor desse imóvel. | \@<*"nome de função*> *(item* <>).<*propertyName*>" |
|||

Por exemplo, `concat()` a função pode tomar dois ou mais valores de cadeia como parâmetros. Esta função combina essas cordas numa corda.
Pode passar em cordas literais, por exemplo, "Sophia" e "Owen" para que consiga uma corda combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ou pode obter valores de cordas a partir de parâmetros. Este exemplo `parameters()` utiliza a `concat()` função `firstName` em `lastName` cada parâmetro e nos e parâmetros. Em seguida, passa as `concat()` cordas resultantes para a função para que obtenha uma corda combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, ambos os exemplos atribuem o resultado à `customerName` propriedade.

Aqui estão as funções disponíveis ordenadas pelo seu propósito geral, ou pode navegar nas funções com base na [ordem alfabética](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funções de cadeia

Para trabalhar com cordas, pode utilizar estas funções de cadeia e também [algumas funções](#collection-functions)de recolha.
As funções das cordas funcionam apenas em cordas.

| Função de corda | Tarefa |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combine duas ou mais cordas e devolva a corda combinada. |
| [terminaCom](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifique se uma corda termina com a subcadeia especificada. |
| [formatoNúmero](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Devolva um número como uma corda com base no formato especificado |
| [guia](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gere um identificador globalmente único (GUID) como uma corda. |
| [indexof](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Volte a colocar a posição inicial para um substring. |
| [últimoIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Volte a colocar a posição inicial na última ocorrência de um substring. |
| [substituir](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substitua uma subcadeia com a corda especificada e devolva a corda atualizada. |
| [dividir](../logic-apps/workflow-definition-language-functions-reference.md#split) | Devolva uma matriz que contenha subcordas, separadas por vírgulas, de uma corda maior baseada num caracteres delimitadores especificados na cadeia original. |
| [começaCom](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verifique se uma corda começa com um substring específico. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Volte a colocar caracteres de uma corda, a partir da posição especificada. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Volte a colocar uma corda em formato minúsculo. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Volte a colocar uma corda em formato superior. |
| [aparar](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remova o espaço branco de liderar e arrastando a partir de uma corda e devolva a corda atualizada. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funções de recolha

Para trabalhar com coleções, geralmente matrizes, cordas e, por vezes, dicionários, pode utilizar estas funções de coleção.

| Função de recolha | Tarefa |
| ------------------- | ---- |
| [contém](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifique se uma coleção tem um item específico. |
| [vazio](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. |
| [primeiro](../logic-apps/workflow-definition-language-functions-reference.md#first) | Devolva o primeiro item de uma coleção. |
| [cruzamento](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Devolva uma coleção que tenha *apenas* os itens comuns nas coleções especificadas. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando estiver dentro de uma ação repetida sobre uma matriz, devolva o item atual na matriz durante a iteração atual da ação. |
| [aderir](../logic-apps/workflow-definition-language-functions-reference.md#join) | Volte a colocar uma corda que tenha *todos os* itens de uma matriz, separadas pelo carácter especificado. |
| [última](../logic-apps/workflow-definition-language-functions-reference.md#last) | Devolva o último item de uma coleção. |
| [comprimento](../logic-apps/workflow-definition-language-functions-reference.md#length) | Volte a devolver o número de itens numa corda ou numa matriz. |
| [saltar](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Retire os itens da parte da frente de uma coleção e devolva *todos os outros* itens. |
| [tomar](../logic-apps/workflow-definition-language-functions-reference.md#take) | Devolver artigos da frente de uma coleção. |
| [união](../logic-apps/workflow-definition-language-functions-reference.md#union) | Devolva uma coleção que tenha *todos os* itens das coleções especificadas. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funções de comparação lógica

Para trabalhar com condições, comparar valores e resultados de expressão, ou avaliar vários tipos de lógica, pode utilizar estas funções de comparação lógica.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [e](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifique se todas as expressões são verdadeiras. |
| [iguais](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifique se ambos os valores são equivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifique se o primeiro valor é superior ao segundo valor. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifique se o primeiro valor é maior ou igual ao segundo valor. |
| [se](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifique se o primeiro valor é inferior ao segundo valor. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifique se o primeiro valor é inferior ou igual ao segundo valor. |
| [não](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifique se uma expressão é falsa. |
| [ou](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifique se pelo menos uma expressão é verdadeira. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou formato de um valor, pode utilizar estas funções de conversão.
Por exemplo, pode mudar um valor de um Boolean para um inteiro.
Para obter mais informações sobre como as Aplicações Lógicas lidam com tipos de conteúdo durante a conversão, consulte os tipos de [conteúdo de Handle](../logic-apps/logic-apps-content-type.md).
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Devolva uma matriz de uma única entrada especificada. Para várias inputs, consulte [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Devolva a versão codificada base64 para uma corda. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Volte a colocar a versão binária numa cadeia codificada com base64. |
| [base64Tostring](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Volte a colocar a versão de corda numa cadeia codificada com base64. |
| [binário](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Devolva a versão binária por um valor de entrada. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Devolva a versão Boolean por um valor de entrada. |
| [criar Array](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Devolva uma matriz de várias inputs. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Devolva os dados URI por um valor de entrada. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Devolva a versão binária para obter um URI de dados. |
| [datauritostring](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Devolva a versão de cadeia para um URI de dados. |
| [descodificarBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Volte a colocar a versão de corda numa cadeia codificada com base64. |
| [descodificarDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Devolva a versão binária para obter um URI de dados. |
| [descodificar Componente Uri](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Volte a colocar uma corda que substitua caracteres de fuga por versões descodificadas. |
| [codificar Componente UriUri](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Volte a colocar uma corda que substitua caracteres inseguros de URL por caracteres de fuga. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Devolva um número de ponto flutuante para obter um valor de entrada. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Devolva a versão inteiro para uma corda. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Devolva o valor ou objeto do tipo JavaScript Object (JSON) para uma corda ou XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Devolva a versão de corda por um valor de entrada. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Devolva a versão codificada por URI para obter um valor de entrada substituindo caracteres url-inseguros por caracteres de fuga. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Devolva a versão binária para uma corda codificada por URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Devolva a versão de corda para uma corda codificada por URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Devolva a versão XML para uma corda. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funções matemáticas

Para trabalhar com inteiros e carros alegóricos, pode usar estas funções matemáticas.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função matemática | Tarefa |
| ------------- | ---- |
| [adicionar](../logic-apps/workflow-definition-language-functions-reference.md#add) | Devolva o resultado da adição de dois números. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Devolva o resultado da divisão de dois números. |
| [máximo](../logic-apps/workflow-definition-language-functions-reference.md#max) | Devolva o valor mais alto de um conjunto de números ou de uma matriz. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Devolva o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Devolva o restante da divisão de dois números. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Devolva o produto de multiplicar dois números. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Devolva um inteiro aleatório de um intervalo especificado. |
| [alcance](../logic-apps/workflow-definition-language-functions-reference.md#range) | Devolva uma matriz inteiro que começa a partir de um inteiro especificado. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Devolva o resultado de subtrair o segundo número do primeiro número. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horários, pode utilizar estas funções de data e hora.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicione alguns dias a uma marca de tempo. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicione algumas horas a um carimbo temporal. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicione alguns minutos a uma marca de tempo. |
| [adicionarSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicione alguns segundos a uma marca de tempo. |
| [adicionarToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adicione várias unidades de tempo a uma marca de tempo. Ver também [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [converterFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converta uma marca de tempo da Universal Time Coordenada (UTC) para o fuso horário-alvo. |
| [converterTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converta uma marca de tempo do fuso horário de origem para o fuso horário alvo. |
| [converteToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converter uma marca de tempo do fuso horário de origem para Universal Time Coordenado (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Volte o componente do dia do mês a partir de uma marca de tempo. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Volte o componente do dia da semana a partir de uma marca de tempo. |
| [dia ofyear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Volte o componente do dia do ano a partir de uma marca de tempo. |
| [formatoDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Devolva a data de uma marca de tempo. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Volte a colocar a marca atual mais as unidades de tempo especificadas. Consulte também [adicionarToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Volte a colocar a marca atual menos as unidades de tempo especificadas. Consulte também [subtrairFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [inícioOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Volte o início do dia para um carimbo temporal. |
| [inícioOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Volte o início da hora por uma marca de tempo. |
| [inícioDo mês](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Devolva o início do mês por um carimbo temporal. |
| [subtratoFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtraia várias unidades temporais de uma marca temporal. Ver também [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [tiques](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Devolva o valor da `ticks` propriedade por um carimbo de tempo especificado. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Devolva o carimbo de tempo atual como uma corda. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funções de fluxo de trabalho

Estas funções de fluxo de trabalho podem ajudá-lo:

* Obtenha detalhes sobre uma instância de fluxo de trabalho no tempo de execução.
* Trabalhe com as entradas usadas para instantâneos aplicações lógicas ou fluxos.
* Referenciar as saídas a partir de gatilhos e ações.

Por exemplo, pode fazer referência às saídas de uma ação e utilizar esses dados numa ação posterior.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de fluxo de trabalho | Tarefa |
| ----------------- | ---- |
| [ação](../logic-apps/workflow-definition-language-functions-reference.md#action) | Devolva a produção da ação atual em tempo de execução, ou valores de outros pares de nome e valor da JSON. Ver também [ações.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Devolva a `body` saída de uma ação no tempo de execução. Ver também [o corpo.](../logic-apps/workflow-definition-language-functions-reference.md#body) |
| [actionSaídas](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Devolva a saída de uma ação no tempo de execução. Ver [saídas](../logic-apps/workflow-definition-language-functions-reference.md#outputs) e [ações.](../logic-apps/workflow-definition-language-functions-reference.md#actions) |
| [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Devolva a saída de uma ação no prazo de execução, ou valores de outros pares de nome e valor da JSON. Ver também [ação](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [corpo](#body) | Devolva a `body` saída de uma ação no tempo de execução. Ver também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Crie uma matriz com os valores que correspondam a um nome chave em *dados de formulário* ou saídas de ação codificadas por *formulários.* |
| [formulárioDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Devolva um único valor que corresponda a um nome-chave nos *dados de formulário* de uma ação ou na saída codificada por *formulários*. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando estiver dentro de uma ação repetida sobre uma matriz, devolva o item atual na matriz durante a iteração atual da ação. |
| [itens](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando estiver dentro de um foreach ou até o loop, devolva o item atual do laço especificado.|
| [iteraçõesIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Quando estiver dentro de um loop Until, devolva o valor do índice para a iteração atual. Pode utilizar esta função dentro de aninhados até os laços. |
| [listaCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Devolva o "URL de chamada" que chama um gatilho ou ação. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Devolva o corpo para uma parte específica da saída de uma ação que tenha várias partes. |
| [saídas](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Devolva a saída de uma ação no tempo de execução. |
| [parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Devolva o valor de um parâmetro descrito na sua definição de fluxo de trabalho. |
| [resultado](../logic-apps/workflow-definition-language-functions-reference.md#result) | Devolva as inputs e saídas de todas as ações `For_each` `Until`no `Scope`interior da ação de âmbito especificado, tais como, e . |
| [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Volte a colocar a saída de um gatilho no prazo de execução, ou de outros pares de nome e valor da JSON. Consulte também [gatilhoS saídas](#triggerOutputs) e [gatilhoS](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Volte a devolver `body` a saída de um gatilho no tempo de execução. Ver [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Devolva um único valor correspondente a um nome-chave em *dados de formulário* ou saídas de gatilho codificadas por *formulários.* |
| [gatilhoMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Devolva o corpo para uma parte específica na saída multiparte de um gatilho. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crie uma matriz cujos valores correspondam a um nome chave em *dados de formulário* ou saídas de gatilho codificadas por *formulários.* |
| [gatilhoSaídas](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Volte a colocar a saída de um gatilho no prazo de execução, ou valores de outros pares de nome e valor da JSON. Ver [gatilho](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variáveis](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Devolva o valor para uma variável especificada. |
| [fluxo de trabalho](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Devolva todos os detalhes sobre o fluxo de trabalho em si durante o tempo de funcionamento. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funções de parsing URI

Para trabalhar com identificadores de recursos uniformes (URIs) e obter vários valores de propriedade para estes URIs, você pode usar estas funções de parsing URI.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de parsing URI | Tarefa |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Devolva o valor de `host` um identificador de recursos uniforme (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Devolva o valor de `path` um identificador de recursos uniforme (URI). |
| [uriPathAndquery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Devolva os `path` valores e valores `query` para um identificador de recursos uniforme (URI). |
| [uriPorto](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Devolva o valor de `port` um identificador de recursos uniforme (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Devolva o valor de `query` um identificador de recursos uniforme (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Devolva o valor de `scheme` um identificador de recursos uniforme (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funções de manipulação: JSON & XML

Para trabalhar com objetos JSON e nós XML, pode utilizar estas funções de manipulação.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de manipulação | Tarefa |
| --------------------- | ---- |
| [adicionarPropriedade](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicione uma propriedade e o seu valor, ou par de valor de nome, a um objeto JSON, e devolva o objeto atualizado. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Devolva o primeiro valor não nulo de um ou mais parâmetros. |
| [removerPropriedade](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Retire uma propriedade de um objeto JSON e devolva o objeto atualizado. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Desloque o valor para a propriedade de um objeto JSON e devolva o objeto atualizado. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verifique o XML para ver se há nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Todas as funções - lista alfabética

Esta secção lista todas as funções disponíveis por ordem alfabética.

<a name="action"></a>

### <a name="action"></a>action

Devolva a produção da ação *atual* no prazo de execução, ou valores de outros pares de nome e valor da JSON, que pode atribuir a uma expressão.
Por predefinição, esta função refere todo o objeto de ação, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Ver também [ações()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Só pode `action()` utilizar a função nestes locais:

* A `unsubscribe` propriedade para uma ação webhook para que `subscribe` você possa aceder ao resultado do pedido original
* A `trackedProperties` propriedade para uma ação
* A `do-until` condição do loop para uma ação

```
action()
action().outputs.body.<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*propriedade*> | Não | Cadeia | O nome para o imóvel do objeto de ação cujo valor deseja: **nome,** **início,** **tempo final,** **inputs,** **saídas,** **estado,** **código,** **trackingId**e **clienteTrackingId**. No portal Azure, pode encontrar estas propriedades revendo detalhes específicos do histórico de execução. Para mais informações, consulte [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*produção de ação*> | Cadeia | A saída da ação ou propriedade em curso |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Devolva a `body` saída de uma ação no tempo de execução.
Abreviação `actions('<actionName>').outputs.body`para.
Ver [corpo()](#body) e [ações()](#actions).

```
actionBody('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome para a `body` saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*ação-corpo-produção*> | Cadeia | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo `body` obtém a `Get user`saída da ação do Twitter:

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

### <a name="actionoutputs"></a>actionSaídas

Devolva a saída de uma ação no tempo de execução.  e é abreviado para `actions('<actionName>').outputs`. Ver [ações()](#actions). A `actionOutputs()` função `outputs()` resolve-se no Logic App Designer, por isso `actionOutputs()`considere usar [saídas() em](#outputs)vez de . Embora ambas as funções `outputs()` funcionem da mesma forma, é preferível.

```
actionOutputs('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome para a saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*saída*> | Cadeia | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a `Get user`saída da ação do Twitter:

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

Devolva a saída de uma ação no prazo de execução, ou valores de outros pares de nome e valor da JSON, que pode atribuir a uma expressão. Por predefinição, a função refere todo o objeto de ação, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Para versões abreviadas, consulte [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)e [body()](#body).
Para a ação em curso, consulte [a ação()](#action).

> [!NOTE]
> Anteriormente, poderia utilizar `actions()` a `conditions` função ou o elemento ao especificar que uma ação decorreu com base na saída de outra ação. No entanto, para declarar explicitamente dependências entre ações, deve agora usar os bens da `runAfter` ação dependente.
> Para saber mais `runAfter` sobre a propriedade, consulte [Catch e lide com falhas com a propriedade runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome do objeto de ação cuja saída quer  |
| <*propriedade*> | Não | Cadeia | O nome para o imóvel do objeto de ação cujo valor deseja: **nome,** **início,** **tempo final,** **inputs,** **saídas,** **estado,** **código,** **trackingId**e **clienteTrackingId**. No portal Azure, pode encontrar estas propriedades revendo detalhes específicos do histórico de execução. Para mais informações, consulte [REST API - Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*produção de ação*> | Cadeia | A saída da ação ou propriedade especificada |
||||

*Exemplo*

Este exemplo `status` obtém o valor `Get user` da propriedade da ação do Twitter em tempo de execução:

```
actions('Get_user').outputs.body.status
```

E devolve este resultado:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>adicionar

Devolva o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, *<summand_2*> | Sim | Inteiro, Flutuador ou Misto | Os números a adicionar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*resultado-soma*> | Inteiro ou Flutuador | O resultado da adição dos números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E devolve este resultado:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione alguns dias a uma marca de tempo.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*dias*> | Sim | Número inteiro | O número positivo ou negativo de dias a adicionar |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo mais o número especificado de dias  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias ao carimbo de tempo especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E devolve este resultado:`"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco dias do carimbo de tempo especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E devolve este resultado:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione algumas horas a um carimbo temporal.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*horas*> | Sim | Número inteiro | O número positivo ou negativo de horas a adicionar |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas ao carimbo de tempo especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado:`"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco horas do carimbo de tempo especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E devolve este resultado:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione alguns minutos a uma marca de tempo.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*minutos*> | Sim | Número inteiro | O número positivo ou negativo de minutos a adicionar |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos à marca ção especificada:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E devolve este resultado:`"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco minutos da marca ção especificada:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E devolve este resultado:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>adicionarPropriedade

Adicione uma propriedade e o seu valor, ou par de valor de nome, a um objeto JSON, e devolva o objeto atualizado. Se a propriedade já existir no tempo de funcionamento, a função falha e lança um erro.

```
addProperty(<object>, '<property>', <value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON onde você quer adicionar uma propriedade |
| <*propriedade*> | Sim | Cadeia | O nome para a propriedade adicionar |
| <*valor*> | Sim | Qualquer | O valor para o imóvel |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado com a propriedade especificada |
||||

Para adicionar uma propriedade infantil a uma propriedade existente, use esta sintaxe:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON onde você quer adicionar uma propriedade |
| <*propriedade-mãe*> | Sim | Cadeia | O nome da propriedade dos pais onde você quer adicionar a propriedade da criança |
| <*child-property*> | Sim | Cadeia | O nome da propriedade da criança para adicionar |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo `middleName` adiciona a propriedade a um objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) O objeto já `firstName` `surName` inclui as propriedades e propriedades. A função atribui o valor especificado à nova propriedade e devolve o objeto atualizado:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Aqui está o objeto json atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Aqui está o objeto jSON atualizado:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo `middleName` adiciona a propriedade `customerName` da criança à propriedade existente num objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) A função atribui o valor especificado à nova propriedade e devolve o objeto atualizado:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Aqui está o objeto json atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Aqui está o objeto jSON atualizado:

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

### <a name="addseconds"></a>adicionarSeconds

Adicione alguns segundos a uma marca de tempo.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*segundos*> | Sim | Número inteiro | O número positivo ou negativo de segundos a adicionar |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo mais o número especificado de segundos  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos à marca ção especificada:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E devolve este resultado:`"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Este exemplo subtrai cinco segundos para a marca de tempo especificada:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E devolve este resultado:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>adicionarToTime

Adicione várias unidades de tempo a uma marca de tempo.
Consulte também [o FutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | Cadeia | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo mais o número especificado de unidades de tempo  |
||||

*Exemplo 1*

Este exemplo adiciona um dia à marca de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E devolve este resultado:`"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia à marca de tempo especificada:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E devolve o resultado utilizando o formato "D" opcional:`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se todas as expressões são verdadeiras.
Volte a ser verdade quando todas as expressões forem verdadeiras, ou volte a ser falsa quando pelo menos uma expressão é falsa.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| true ou false | Booleano | Volte a ser verdade quando todas as expressões forem verdadeiras. Volte falso quando pelo menos uma expressão é falsa. |
||||

*Exemplo 1*

Estes exemplos verificam se os valores booleanos especificados são todos verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões `true`são verdadeiras, por isso os retornos.
* Segundo exemplo: Uma expressão é `false`falsa, por isso devolve.
* Terceiro exemplo: Ambas as expressões `false`são falsas, por isso devoluções .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E devolve estes resultados:

* Primeiro exemplo: Ambas as expressões `true`são verdadeiras, por isso os retornos.
* Segundo exemplo: Uma expressão é `false`falsa, por isso devolve.
* Terceiro exemplo: Ambas as expressões `false`são falsas, por isso devoluções .

<a name="array"></a>

### <a name="array"></a>array

Devolva uma matriz de uma única entrada especificada.
Para várias inputs, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para criar uma matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Matriz | Uma matriz que contém a única entrada especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir da corda "olá":

```
array('hello')
```

E devolve este resultado:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Devolva a versão codificada base64 para uma corda.

```
base64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia de entrada |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | Cadeia | A versão codificada base64 para a cadeia de entrada |
||||

*Exemplo*

Este exemplo converte a corda "olá" para uma cadeia codificada base64:

```
base64('hello')
```

E devolve este resultado:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Volte a colocar a versão binária numa cadeia codificada com base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia codificada base64 para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-base64-string*> | Cadeia | A versão binária para a cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia "aGVsbG8=" base64-codificada numa cadeia binária:

```
base64ToBinary('aGVsbG8=')
```

E devolve este resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64Tostring

Volte a colocar a versão de corda para uma cadeia codificada base64, descodificando eficazmente a cadeia base64.
Utilize esta função em vez [de descodificarBase64()](#decodeBase64).
Embora ambas as funções `base64ToString()` funcionem da mesma forma, é preferível.

```
base64ToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia codificada base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada-base64-string*> | Cadeia | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo converte a cadeia "aGVsbG8=" base64 codificada em apenas uma corda:

```
base64ToString('aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Devolva a versão binária por uma corda.

```
binary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-input-valor*> | Cadeia | A versão binária para a corda especificada |
||||

*Exemplo*

Este exemplo converte a corda "olá" para uma corda binária:

```
binary('hello')
```

E devolve este resultado:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Devolva a `body` saída de uma ação no tempo de execução.
Abreviação `actions('<actionName>').outputs.body`para.
Ver [actionBody()](#actionBody) e [ações()](#actions).

```
body('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome para a `body` saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*ação-corpo-produção*> | Cadeia | A `body` saída da ação especificada |
||||

*Exemplo*

Este exemplo `body` obtém `Get user` a saída da ação do Twitter:

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

Devolva a versão Boolean por um valor.

```
bool(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | A versão Boolean para o valor especificado |
||||

*Exemplo*

Estes exemplos convertem os valores especificados em valores booleanos:

```
bool(1)
bool(0)
```

E devolve estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Devolva o primeiro valor não nulo de um ou mais parâmetros.
Cordas vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>,> *de object_2* <, ... | Sim | Qualquer, pode misturar tipos | Um ou mais itens para verificar se há nulos |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro-não-nulo-item*> | Qualquer | O primeiro item ou valor que não é nulo. Se todos os parâmetros forem nulos, esta função devolve-se a nulas. |
||||

*Exemplo*

Estes exemplos devolvem o primeiro valor não nulo dos valores especificados, ou nulos quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E devolve estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`"hello"`
* Terceiro exemplo:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combine duas ou mais cordas e devolva a corda combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*> de texto1,* <*texto2*>, ... | Sim | Cadeia | Pelo menos duas cordas para combinar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto1texto2...*> | Cadeia | A cadeia criada a partir das cordas combinadas de entrada |
||||

*Exemplo*

Este exemplo combina as cordas "Olá" e "Mundo":

```
concat('Hello', 'World')
```

E devolve este resultado:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contém

Verifique se uma coleção tem um item específico.
Volte a ser verdade quando o artigo for encontrado, ou volte a ser falso quando não for encontrado.
Esta função é sensível ao caso.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, esta função funciona nestes tipos de recolha:

* Uma *corda* para encontrar uma *subcorda*
* Uma *matriz* para encontrar um *valor*
* Um *dicionário* para encontrar uma *chave*

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda, Matriz ou Dicionário | A coleção para verificar |
| <*valor*> | Sim | String, Array ou Dicionário, respectivamente | O item para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o artigo for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a corda "hello world" para o substring "mundo" e retorna verdadeira:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a corda "hello world" para o substring "universo" e devolve falso:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>converterFromUtc

Converta uma marca de tempo da Universal Time Coordenada (UTC) para o fuso horário-alvo.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*destinoTimeZone*> | Sim | Cadeia | O nome do fuso horário alvo. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | Cadeia | O carimbo de tempo convertido para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte uma marca de tempo para o fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte uma marca de tempo para o fuso horário e formato especificados:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>converterTimeZone

Converta uma marca de tempo do fuso horário de origem para o fuso horário alvo.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*fonteTimeZone*> | Sim | Cadeia | O nome do fuso horário de origem. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*destinoTimeZone*> | Sim | Cadeia | O nome do fuso horário alvo. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | Cadeia | O carimbo de tempo convertido para o fuso horário alvo |
||||

*Exemplo 1*

Este exemplo converte o fuso horário de origem para o fuso horário alvo:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Este exemplo converte um fuso horário para o fuso horário e formato especificados:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>converteToUtc

Converter uma marca de tempo do fuso horário de origem para Universal Time Coordenado (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*fonteTimeZone*> | Sim | Cadeia | O nome do fuso horário de origem. Para obter nomes de fusos horários, consulte os valores do Índice do [Fuso Horário](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)do Microsoft, mas poderá ter de remover qualquer pontuação do nome do fuso horário. |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo convertido*> | Cadeia | O carimbo de tempo convertido em UTC |
||||

*Exemplo 1*

Este exemplo converte uma marca de tempo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E devolve este resultado:`"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Este exemplo converte uma marca de tempo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E devolve este resultado:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>criar Array

Devolva uma matriz de várias inputs.
Para uma única dosmina de entrada, consulte [a matriz()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*objeto2*>, ... | Sim | Qualquer, mas não misturado | Pelo menos dois itens para criar a matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*objeto1*>, <*objeto2*>, ...] | Matriz | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir destas inputs:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E devolve este resultado:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Devolva um identificador de recursos uniforme de dados (URI) para uma cadeia.

```
dataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | Cadeia | Os dados URI para a cadeia de entrada |
||||

*Exemplo*

Este exemplo cria um URI de dados para a cadeia "olá":

```
dataUri('hello')
```

E devolve este resultado:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Utilize esta função em vez [de descodificarDataUri()](#decodeDataUri).
Embora ambas as funções `dataUriBinary()` funcionem da mesma forma, é preferível.

```
dataUriToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | Cadeia | A versão binária para os dados URI |
||||

*Exemplo*

Este exemplo cria uma versão binária para estes dados URI:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>datauritostring

Devolva a versão de cadeia para um identificador de recursos uniforme de dados (URI).

```
dataUriToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | Os dados URI para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Cadeia | A versão de cadeia para os dados URI |
||||

*Exemplo*

Este exemplo cria uma cadeia para estes dados URI:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Volte o dia do mês de uma marca de tempo.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do mês*> | Número inteiro | O dia do mês a partir do carimbo de tempo especificado |
||||

*Exemplo*

Este exemplo devolve o número do dia do mês a partir deste carimbo temporal:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E devolve este resultado:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Volte o dia da semana de uma marca de tempo.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia da semana*> | Número inteiro | O dia da semana a partir do carimbo de tempo especificado onde domingo é 0, segunda-feira é 1, e assim por diante |
||||

*Exemplo*

Este exemplo devolve o número para o dia da semana a partir deste carimbo temporal:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E devolve este resultado:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dia ofyear

Volte o dia do ano de uma marca de tempo.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*dia do ano*> | Número inteiro | O dia do ano a partir do carimbo de tempo especificado |
||||

*Exemplo*

Este exemplo devolve o número do dia do ano a partir deste carimbo temporal:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E devolve este resultado:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>descodificarBase64

Volte a colocar a versão de corda para uma cadeia codificada base64, descodificando eficazmente a cadeia base64.
Considere usar [base64ToString()](#base64ToString) em vez de `decodeBase64()`.
Embora ambas as funções `base64ToString()` funcionem da mesma forma, é preferível.

```
decodeBase64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia codificada base64 para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificada-base64-string*> | Cadeia | A versão de corda para uma cadeia codificada base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia para uma cadeia codificada base64:

```
decodeBase64('aGVsbG8=')
```

E devolve este resultado:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>descodificarDataUri

Devolva a versão binária para um identificador de recursos uniforme de dados (URI).
Considere utilizar [dadosUriToBinary() ()](#dataUriToBinary)em vez de `decodeDataUri()`.
Embora ambas as funções `dataUriToBinary()` funcionem da mesma forma, é preferível.

```
decodeDataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia URI de dados para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-dados-uri*> | Cadeia | A versão binária para uma cadeia URI de dados |
||||

*Exemplo*

Este exemplo devolve a versão binária para estes dados URI:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>descodificar Componente Uri

Volte a colocar uma corda que substitua caracteres de fuga por versões descodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda com os personagens de fuga para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | Cadeia | A cadeia atualizada com os caracteres de fuga descodificados |
||||

*Exemplo*

Este exemplo substitui os caracteres de fuga nesta cadeia por versões descodificadas:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Devolva o resultado inteiro da divisão de dois números.
Para obter o resultado remanescente, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuador | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuador | O número que divide o *dividendo,* mas não pode ser 0 |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado quociente*> | Número inteiro | O resultado inteiro de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Ambos os exemplos dividem o primeiro número pelo segundo número:

```
div(10, 5)
div(11, 5)
```

E devolva este resultado:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>codificar Componente UriUri

Volte a colocar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros de URL por caracteres de fuga.
Considere utilizar [uriComponent()](#uriComponent) `encodeUriComponent()`em vez de .
Embora ambas as funções `uriComponent()` funcionem da mesma forma, é preferível.

```
encodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para converter em formato codificado por URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | Cadeia | A corda codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada por URI para esta cadeia:

```
encodeUriComponent('https://contoso.com')
```

E devolve este resultado:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>vazio

Verifique se uma coleção está vazia.
Volte a ser verdade quando a coleção estiver vazia, ou volte a ser falsa quando não estiver vazia.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda, Matriz ou Objeto | A coleção para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando a coleção estiver vazia. Volte falso quando não estiver vazio. |
||||

*Exemplo*

Estes exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E devolve estes resultados:

* Primeiro exemplo: Passa uma corda vazia, `true`para que a função retorne .
* Segundo exemplo: passa a corda "abc", `false`para que a função retorne.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma corda termina com um substring específico.
Volte a ser verdade quando o substring for encontrado, ou volte a ser falso quando não for encontrado.
Esta função não é sensível a casos.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda para verificar |
| <*pesquisarTexto*> | Sim | Cadeia | O substring final para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o substring final for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "hello world" termina com a corda "mundo":

```
endsWith('hello world', 'world')
```

E devolve este resultado:`true`

*Exemplo 2*

Este exemplo verifica se a corda "hello world" termina com a corda "universo":

```
endsWith('hello world', 'universe')
```

E devolve este resultado:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifique se ambos os valores, expressões ou objetos são equivalentes.
Volte a ser verdade quando ambos são equivalentes, ou devolva falsoquando não são equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, objeto *<2*> | Sim | Vários | Os valores, expressões ou objetos para comparar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando ambos são equivalentes. Devolver falso quando não equivalente. |
||||

*Exemplo*

Estes exemplos verificam se as inputs especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E devolve estes resultados:

* Primeiro exemplo: Ambos os valores são `true`equivalentes, pelo que a função retorna .
* Segundo exemplo: Ambos os valores não são `false`equivalentes, pelo que a função retorna .

<a name="first"></a>

### <a name="first"></a>primeiro

Volte a devolver o primeiro item de uma corda ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*primeiro-coleção-item*> | Qualquer | O primeiro item da coleção |
||||

*Exemplo*

Estes exemplos encontram o primeiro item nestas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E devolver estes resultados:

* Primeiro exemplo:`"h"`
* Segundo exemplo:`0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de corda para um número de ponto flutuante para um número de ponto flutuante real.
Só pode utilizar esta função ao passar parâmetros personalizados para uma aplicação, por exemplo, uma aplicação lógica ou fluxo.

```
float('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda que tem um número de ponto flutuante válido para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor flutuante*> | Float | O número de ponto flutuante para a corda especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia para este número de ponto flutuante:

```
float('10.333')
```

E devolve este resultado:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatoDateTime

Devolva uma marca de tempo no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*selo de tempo reformada*> | Cadeia | A marca de tempo atualizada no formato especificado |
||||

*Exemplo*

Este exemplo converte uma marca de tempo para o formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E devolve este resultado:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Devolva uma matriz com valores que correspondam a um nome chave nos *dados de formulário* de uma ação ou na saída codificada pelo *formulário.*

```
formDataMultiValues('<actionName>', '<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | A ação cuja saída tem o valor-chave que deseja |
| <*chave*> | Sim | Cadeia | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> de *conjunto com valores-chave]* | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir do valor da chave "Sujeito" nos dados de formulário da ação especificada ou na saída codificada pelo formulário:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E devolve o texto do assunto numa matriz, por exemplo:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formulárioDataValue

Devolva um único valor que corresponda a um nome-chave nos *dados de formulário* de uma ação ou na saída *codificada pelo formulário.*
Se a função encontrar mais de um fósforo, a função lança um erro.

```
formDataValue('<actionName>', '<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | A ação cuja saída tem o valor-chave que deseja |
| <*chave*> | Sim | Cadeia | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor-chave*> | Cadeia | O valor na chave especificada  |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir do valor da chave "Sujeito" nos dados de formulário da ação especificada ou na saída codificada pelo formulário:

```
formDataValue('Send_an_email', 'Subject')
```

E devolve o texto do assunto como uma corda, por exemplo:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatoNúmero

Volte a devolver um número como uma corda baseada no formato especificado.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número*> | Sim | Inteiro ou Duplo | O valor que quer formatar. |
| <*formato*> | Sim | Cadeia | Uma cadeia de formato composto que especifica o formato que pretende utilizar. Para as cordas de formato numérico suportadas, consulte as `number.ToString(<format>, <locale>)`cordas de [formato numérico Standard,](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings)que são suportadas por . |
| <*local*> | Não | Cadeia | O local a usar como `number.ToString(<format>, <locale>)`apoiado por . Se não especificado, o `en-us`valor predefinido é . |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*número formatado*> | Cadeia | O número especificado como uma cadeia no formato que especificou. Pode lançar este valor `int` de `float`devolução a um ou . |
||||

*Exemplo 1*

Suponha que queira `1234567890`formatar o número. Este exemplo formata esse número como a corda "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*Exemplo 2"

Suponha que queira `1234567890`formatar o número. Este exemplo formata o número para a corda "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*Exemplo 3*

Suponha que queira `17.35`formatar o número. Este exemplo formata o número para a corda "$17.35".

```
formatNumber(17.36, 'C2')
```

*Exemplo 4*

Suponha que queira `17.35`formatar o número. Este exemplo formata o número para a corda "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Volte a colocar a marca atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para adicionar |
| <*timeUnit*> | Sim | Cadeia | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | O carimbo de tempo atual mais o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o atual carimbo temporal é "2018-03-01T00:00.00.00000000Z".
Este exemplo adiciona cinco dias a essa marca de tempo:

```
getFutureTime(5, 'Day')
```

E devolve este resultado:`"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o atual carimbo temporal é "2018-03-01T00:00.00.00000000Z".
Este exemplo adiciona cinco dias e converte o resultado em formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E devolve este resultado:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Volte a colocar a marca atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | Cadeia | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | O carimbo de tempo atual menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Suponha que o atual carimbo temporal é "2018-02-01T00:00.00.00000000Z".
Este exemplo subtrai cinco dias a partir desse carimbo temporal:

```
getPastTime(5, 'Day')
```

E devolve este resultado:`"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o atual carimbo temporal é "2018-02-01T00:00.00.00000000Z".
Este exemplo subtrai cinco dias e converte o resultado em formato "D":

```
getPastTime(5, 'Day', 'D')
```

E devolve este resultado:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifique se o primeiro valor é superior ao segundo valor.
Volte a ser verdade quando o primeiro valor for mais, ou volte a ser falso quando menos.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se maior do que o segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor é maior que o segundo valor. Devolver falso quando o primeiro valor for igual ou inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é superior ao segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior ou igual ao segundo valor.
Volte a ser verdade quando o primeiro valor for maior ou igual, ou volte a ser falso quando o primeiro valor for menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se maior ou igual ao segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O valor de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor for maior ou igual ao segundo valor. Devolver falso quando o primeiro valor for inferior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é maior ou igual ao segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="guid"></a>

### <a name="guid"></a>guia

Gerar um identificador globalmente único (GUID) como uma corda, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, pode especificar um formato diferente para o GUID que não seja o formato predefinido, "D", que é de 32 dígitos separados por hífenes.

```
guid('<format>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | Cadeia | Um [único especificador](https://msdn.microsoft.com/library/97af8hh4) de formato para o GUID devolvido. Por padrão, o formato é "D", mas pode utilizar "N", "D", "B", "P" ou "X". |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Valor-guia*> | Cadeia | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hífenes, e fechados em parênteses:

```
guid('P')
```

E devolve este resultado:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifique se uma expressão é verdadeira ou falsa.
Com base no resultado, devolva um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
| <*valorIfTrue*> | Sim | Qualquer | O valor a devolver quando a expressão é verdadeira |
| <*valorIfFalso*> | Sim | Qualquer | O valor a devolver quando a expressão é falsa |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de retorno especificado*> | Qualquer | O valor especificado que retorna com base em se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Este exemplo `"yes"` retorna porque a expressão especificada retorna verdadeira.
Caso contrário, o `"no"`exemplo retorna:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexof

Volte a colocar a posição inicial ou o valor do índice para um substring.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda que tem o substring para encontrar |
| <*pesquisarTexto*> | Sim | Cadeia | O substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de índice*>| Número inteiro | A posição inicial ou o valor do índice para o substring especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor do índice inicial para o substring "mundo" na cadeia "hello world":

```
indexOf('hello world', 'world')
```

E devolve este resultado:`6`

<a name="int"></a>

### <a name="int"></a>int

Devolva a versão inteiro para uma corda.

```
int('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado inteiro*> | Número inteiro | A versão inteiro para a corda especificada |
||||

*Exemplo*

Este exemplo cria uma versão inteiro para a corda "10":

```
int('10')
```

E devolve este resultado:`10`

<a name="item"></a>

### <a name="item"></a>item

Quando utilizado dentro de uma ação de repetição sobre uma matriz, devolva o item atual na matriz durante a iteração atual da ação.
Também pode obter os valores das propriedades desse item.

```
item()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atual array-item*> | Qualquer | O item atual na matriz para a iteração atual da ação |
||||

*Exemplo*

Este exemplo `body` obtém o elemento da mensagem atual para a ação "Send_an_email" dentro da iteração atual de cada loop:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>itens

Volte a colocar o item atual de cada ciclo num ciclo para cada ciclo.
Utilize esta função dentro do laço para cada um.

```
items('<loopName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sim | Cadeia | O nome para cada loop |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*item*> | Qualquer | O item do ciclo atual no laço especificado para cada |
||||

*Exemplo*

Este exemplo obtém o item atual do circuito especificado para cada loop:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iteraçõesIndexes

Volte a colocar o valor do índice para a iteração atual dentro de um loop Until. Pode utilizar esta função dentro de aninhados até os laços. 

```
iterationIndexes('<loopName>')
```

| Parâmetro | Necessário | Tipo | Descrição | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sim | Cadeia | O nome para o loop Until | 
||||| 

| Valor devolvido | Tipo | Descrição | 
| ------------ | ---- | ----------- | 
| <*índice*> | Número inteiro | O valor do índice para a iteração atual dentro do circuito especificado Até ao loop | 
|||| 

*Exemplo* 

Este exemplo cria uma contra variável e incrementos que variáveis por um durante cada iteração em loop Until até que o valor de contador atinja cinco. O exemplo também cria uma variável que acompanha o índice atual para cada iteração. No lacete Until, durante cada iteração, o exemplo incrementa o contador e atribui o valor de contador ao valor do índice atual e, em seguida, incrementa o contador. A qualquer momento, pode determinar o número atual de iteração recuperando o valor atual do índice.

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

Devolva o valor ou objeto do tipo JavaScript Object (JSON) para uma corda ou XML.

```
json('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Corda ou XML | A corda ou XML para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*Resultado da JSON*> | Tipo ou objeto nativo JSON | O valor ou objeto do tipo nativo JSON para a corda especificada ou XML. Se a corda for nula, a função devolve um objeto vazio. |
||||

*Exemplo 1*

Este exemplo converte esta cadeia para o valor JSON:

```
json('[1, 2, 3]')
```

E devolve este resultado:`[1, 2, 3]`

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

Este exemplo converte este XML em JSON:

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

### <a name="intersection"></a>cruzamento

Devolva uma coleção que tenha *apenas* os itens comuns nas coleções especificadas.
Para aparecer no resultado, deve aparecer um item em todas as coleções passadas para esta função.
Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, *<coleção2*>, ... | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *apenas* os itens comuns |
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

E devolve uma matriz com *apenas* estes itens:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Volte a colocar uma corda que tenha todos os itens de uma matriz e tenha cada personagem separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A matriz que tem os itens para se juntar |
| <*delimitador*> | Sim | Cadeia | O separador que aparece entre cada personagem na corda resultante |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*char1*><*delimitador*><*char2*><*delimitador*>... | Cadeia | A cadeia resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de todos os itens desta matriz com o caráter especificado como delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E devolve este resultado:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>última

Devolva o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção onde encontrar o último item |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*último-colecção-item*> | String ou Array, respectivamente | O último item da coleção |
||||

*Exemplo*

Estes exemplos encontram o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E devolve estes resultados:

* Primeiro exemplo:`"d"`
* Segundo exemplo:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>últimoIndexOf

Volte a colocar a posição inicial ou o valor do índice para a última ocorrência de um substring.
Esta função não é sensível a casos, e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda que tem o substring para encontrar |
| <*pesquisarTexto*> | Sim | Cadeia | O substring para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do índice final*> | Número inteiro | A posição inicial ou o valor do índice para a última ocorrência do substring especificado. <p>Se a corda não for encontrada, devolva o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor do índice inicial para a última ocorrência do substring "mundo" na cadeia "hello world":

```
lastIndexOf('hello world', 'world')
```

E devolve este resultado:`6`

<a name="length"></a>

### <a name="length"></a>length

Devolva o número de artigos numa coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção com os itens para contar |
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

E devolva este resultado:`4`

<a name="less"></a>

### <a name="less"></a>less

Verifique se o primeiro valor é inferior ao segundo valor.
Volte a ser verdade quando o primeiro valor for menor, ou volte a ser falso quando o primeiro valor for mais.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se menos do que o segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando o primeiro valor for inferior ao segundo valor. Devolver falso quando o primeiro valor for igual ou superior ao segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ao segundo valor.

```
less(5, 10)
less('banana', 'apple')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifique se o primeiro valor é inferior ou igual ao segundo valor.
Volte a ser verdade quando o primeiro valor for inferior ou igual, ou volte a ser falso quando o primeiro valor for mais.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Inteiro, Flutuador ou Corda | O primeiro valor para verificar se menos ou igual ao segundo valor |
| <*comparar*> | Sim | Inteiro, Flutuador ou Corda, respectivamente | O item de comparação |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o primeiro valor for inferior ou igual ao segundo valor. Devolver falso quando o primeiro valor for maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos verificam se o primeiro valor é inferior ou igual ao segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E devolver estes resultados:

* Primeiro exemplo:`true`
* Segundo exemplo:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listaCallbackUrl

Devolva o "URL de chamada" que chama um gatilho ou ação.
Esta função funciona apenas com gatilhos e ações para os tipos de conector **HttpWebhook** e **ApiConnectionWebhook,** mas não com os tipos **Manual**, **Recurrence,** **HTTP**e **APIConnection.**

```
listCallbackUrl()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Cadeia | O URL de callback para um gatilho ou ação |
||||

*Exemplo*

Este exemplo mostra um URL de chamada de amostra que esta função pode devolver:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>máximo

Devolva o valor mais alto de uma lista ou matriz com números que são inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir do qual quer o maior valor |
| [<*> número 1,* <*número 2*>, ...] | Sim | Array - Inteiro, Flutuador, ou ambos | A variedade de números a partir dos quais quer o maior valor |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor máximo*> | Inteiro ou Flutuador | O valor mais elevado na matriz ou conjunto de números especificados |
||||

*Exemplo*

Estes exemplos obtêm o maior valor a partir do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E devolva este resultado:`3`

<a name="min"></a>

### <a name="min"></a>min

Devolva o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*número1*>, <*número2*>, ... | Sim | Inteiro, Float, ou ambos | O conjunto de números a partir do qual quer o valor mais baixo |
| [<*> número 1,* <*número 2*>, ...] | Sim | Array - Inteiro, Flutuador, ou ambos | A variedade de números a partir dos quais quer o valor mais baixo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*min-valor*> | Inteiro ou Flutuador | O valor mais baixo no conjunto especificado de números ou matriz especificada |
||||

*Exemplo*

Estes exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E devolva este resultado:`1`

<a name="mod"></a>

### <a name="mod"></a>mod

Devolva o restante da divisão de dois números.
Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividendo*> | Sim | Inteiro ou Flutuador | O número a dividir pelo *divisor* |
| <*divisor*> | Sim | Inteiro ou Flutuador | O número que divide o *dividendo,* mas não pode ser 0. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-resultado*> | Inteiro ou Flutuador | O restante de dividir o primeiro número pelo segundo número |
||||

*Exemplo*

Este exemplo divide o primeiro número pelo segundo número:

```
mod(3, 2)
```

E devolva este resultado:`1`

<a name="mul"></a>

### <a name="mul"></a>mul

Devolva o produto de multiplicar dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicador1*> | Sim | Inteiro ou Flutuador | O número a multiplicar por *multiplicador2* |
| <*multiplicador2*> | Sim | Inteiro ou Flutuador | O número que múltiplos *multiplicam1* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado do produto*> | Inteiro ou Flutuador | O produto de multiplicar o primeiro número pelo segundo número |
||||

*Exemplo*

Estes exemplos múltiplos o primeiro número pelo segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E devolver estes resultados:

* Primeiro exemplo:`2`
* Segundo exemplo`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Devolva o corpo para uma parte específica da saída de uma ação que tenha várias partes.

```
multipartBody('<actionName>', <index>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome para a ação que tem saída com várias partes |
| <*índice*> | Sim | Número inteiro | O valor do índice para a parte que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*corpo*> | Cadeia | O corpo para a parte especificada |
||||

<a name="not"></a>

### <a name="not"></a>not

Verifique se uma expressão é falsa.
Volte a ser verdade quando a expressão é falsa, ou retorne falsa quando for verdadeira.

```json
not(<expression>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão*> | Sim | Booleano | A expressão para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando a expressão é falsa. Volte falso quando a expressão for verdadeira. |
||||

*Exemplo 1*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(false)
not(true)
```

E devolver estes resultados:

* Primeiro exemplo: A expressão é falsa, `true`por isso a função retorna .
* Segundo exemplo: A expressão é verdadeira, `false`por isso a função retorna .

*Exemplo 2*

Estes exemplos verificam se as expressões especificadas são falsas:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E devolver estes resultados:

* Primeiro exemplo: A expressão é falsa, `true`por isso a função retorna .
* Segundo exemplo: A expressão é verdadeira, `false`por isso a função retorna .

<a name="or"></a>

### <a name="or"></a>ou

Verifique se pelo menos uma expressão é verdadeira.
Volte a ser verdade quando pelo menos uma expressão for verdadeira, ou volte a ser falsa quando todas são falsas.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, *expressão <2*>, ... | Sim | Booleano | As expressões para verificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Booleano | Volte a ser verdade quando pelo menos uma expressão é verdadeira. Volte falso quando todas as expressões forem falsas. |
||||

*Exemplo 1*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(true, false)
or(false, false)
```

E devolver estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é `true`verdadeira, pelo que a função retorna .
* Segundo exemplo: Ambas as expressões são `false`falsas, pelo que a função retorna .

*Exemplo 2*

Estes exemplos verificam se pelo menos uma expressão é verdadeira:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E devolver estes resultados:

* Primeiro exemplo: Pelo menos uma expressão é `true`verdadeira, pelo que a função retorna .
* Segundo exemplo: Ambas as expressões são `false`falsas, pelo que a função retorna .

<a name="outputs"></a>

### <a name="outputs"></a>saídas

Devolva as saídas de uma ação no tempo de execução. Utilize esta função, em vez de `actionOutputs()`, que se resolve `outputs()` no Logic App Designer. Embora ambas as funções `outputs()` funcionem da mesma forma, é preferível.

```
outputs('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome de ação*> | Sim | Cadeia | O nome para a saída da ação que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | -----| ----------- |
| <*saída*> | Cadeia | A saída da ação especificada |
||||

*Exemplo*

Este exemplo obtém a `Get user`saída da ação do Twitter:

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

Devolva o valor de um parâmetro descrito na sua definição de fluxo de trabalho.

```
parameters('<parameterName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*parâmetronome*> | Sim | Cadeia | O nome do parâmetro cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor dos parâmetros*> | Qualquer | O valor para o parâmetro especificado |
||||

*Exemplo*

Suponha que tenha este valor JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Este exemplo obtém o valor para o parâmetro especificado:

```
parameters('fullName')
```

E devolve este resultado:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Devolva um inteiro aleatório de uma gama especificada, que só é inclusiva no final inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O inteiro mais baixo da gama |
| <*maxValue*> | Sim | Número inteiro | O inteiro que segue o maior inteiro na gama que a função pode devolver |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado aleatório*> | Número inteiro | O inteiro aleatório voltou da gama especificada |
||||

*Exemplo*

Este exemplo obtém um inteiro aleatório da gama especificada, excluindo o valor máximo:

```
rand(1, 5)
```

E devolve um destes números `2` `3`como resultado: `1`, , ou`4`

<a name="range"></a>

### <a name="range"></a>alcance

Devolva uma matriz inteiro que começa a partir de um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que começa a matriz como o primeiro item |
| <*contar*> | Sim | Número inteiro | O número de inteiros na matriz |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de resultados de alcance]* | Matriz | A matriz com inteiros a partir do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz inteiro que começa a partir do índice especificado e tem o número especificado de inteiros:

```
range(1, 4)
```

E devolve este resultado:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substitua uma subcadeia com a corda especificada e devolva a corda de resultado. Esta função é sensível ao caso.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda que tem a subcadeia para substituir |
| <*oldText*> | Sim | Cadeia | A subcadeia para substituir |
| <*novoTexto*> | Sim | Cadeia | A corda de substituição |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto atualizado*> | Cadeia | A cadeia atualizada após a substituição da subcadeia <p>Se o substring não for encontrado, devolva a corda original. |
||||

*Exemplo*

Este exemplo encontra o substring "velho" na "corda velha" e substitui "velho" por "novo":

```
replace('the old string', 'old', 'new')
```

E devolve este resultado:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removerPropriedade

Retire uma propriedade de um objeto e devolva o objeto atualizado. Se a propriedade que tenta remover não existir, a função devolve o objeto original.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON de onde você quer remover uma propriedade |
| <*propriedade*> | Sim | Cadeia | O nome para a propriedade remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado sem a propriedade especificada |
||||

Para remover uma propriedade infantil de uma propriedade existente, use esta sintaxe:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade pretende remover |
| <*propriedade-mãe*> | Sim | Cadeia | O nome da propriedade dos pais com a propriedade da criança que você quer remover |
| <*child-property*> | Sim | Cadeia | O nome para a propriedade da criança remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade infantil que removeu |
||||

*Exemplo 1*

Este exemplo remove `middleName` a propriedade de um objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) e devolve o objeto atualizado:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Aqui está o objeto json atual:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Aqui está o objeto jSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Exemplo 2*

Este exemplo remove `middleName` a propriedade `customerName` da criança de uma propriedade dos pais num objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) e devolve o objeto atualizado:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Aqui está o objeto json atual:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Aqui está o objeto jSON atualizado:

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

Devolva as inputs e saídas de todas as ações que `For_each`estejam `Until`dentro `Scope` da ação de âmbito especificado, tais como a , ou ação. Esta função é útil devolvendo os resultados de uma ação falhada para que possa diagnosticar e lidar com exceções. Para mais informações, consulte [O contexto e os resultados para falhas](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Sim | Cadeia | O nome da ação de âmbito de aplicação a partir da qual devolver as inputs e saídas de todas as ações internas |
||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*array-object*> | Objeto de matriz | Uma matriz que contém matrizes de inputs e saídas de cada ação que aparece dentro da ação específica |
||||

*Exemplo*

Este exemplo devolve as inputs e saídas de cada iteração de uma ação HTTP dentro de um `For_each` loop usando a `result()` função na `Compose` ação:

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

Aqui está como o exemplo de volta `outputs` array pode olhar onde o objeto exterior contém `For_each` as inputs e saídas de cada iteração das ações dentro da ação.

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

Depreite o valor para a propriedade do objeto JSON e devolva o objeto atualizado. Se a propriedade que você tenta definir não existe, a propriedade é adicionada ao objeto. Para adicionar uma nova propriedade, utilize a função [addProperty()](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade você quer definir |
| <*propriedade*> | Sim | Cadeia | O nome para o imóvel existente ou novo para definir |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

Para colocar a propriedade da criança num `setProperty()` objeto de criança, use uma chamada aninhada. Caso contrário, a função devolve apenas o objeto da criança como saída.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto*> | Sim | Objeto | O objeto JSON cuja propriedade você quer definir |
| <*propriedade-mãe*> | Sim | Cadeia | O nome da propriedade dos pais com a propriedade da criança que você quer definir |
| <*child-property*> | Sim | Cadeia | O nome para a propriedade da criança para definir |
| <*valor*> | Sim | Qualquer | O valor a definir para o imóvel especificado |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*objeto atualizado*> | Objeto | O objeto JSON atualizado cuja propriedade você definiu |
||||

*Exemplo 1*

Este exemplo define `surName` a propriedade num objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) A função atribui o valor especificado à propriedade e devolve o objeto atualizado:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Aqui está o objeto json atual:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Aqui está o objeto jSON atualizado:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Exemplo 2*

Este exemplo define `surName` a propriedade `customerName` da criança para a propriedade dos pais num objeto JSON, que é convertido de uma corda para JSON utilizando a função [JSON()](#json) A função atribui o valor especificado à propriedade e devolve o objeto atualizado:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Aqui está o objeto json atual:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Aqui está o objeto jSON atualizado:

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

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Matriz | A coleção cujos itens pretende remover |
| <*contar*> | Sim | Número inteiro | Um inteiro positivo para o número de itens a remover na frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> *de recolha atualizada]* | Matriz | A recolha atualizada após a remoção dos itens especificados |
||||

*Exemplo*

Este exemplo remove um item, o número 0, da frente da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E devolve esta matriz com os restantes itens:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Volte a colocar uma matriz que contenha subcordas, separadas por vírgulas, com base no carácter delimitador especificado na cadeia original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda para separar em subcordas com base no delimitador especificado na corda original |
| <*delimitador*> | Sim | Cadeia | O personagem na corda original para usar como delimitador |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Matriz | Uma matriz que contém subcordas da corda original, separada por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcordas da corda especificada com base no carácter especificado como delimitador:

```
split('a_b_c', '_')
```

E devolve esta matriz como resultado:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>inícioOfDay

Volte o início do dia para um carimbo temporal.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo especificada, mas começando na marca de zero horas para o dia |
||||

*Exemplo*

Este exemplo encontra o início do dia para este carimbo temporal:

```
startOfDay('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>inícioOfHour

Volte o início da hora por uma marca de tempo.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo especificada, mas começando na marca de zero minutos para a hora |
||||

*Exemplo*

Este exemplo encontra o início da hora para este carimbo temporal:

```
startOfHour('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>inícioDo mês

Devolva o início do mês por um carimbo temporal.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | A marca de tempo especificada, mas a partir do primeiro dia do mês na marca de zero horas |
||||

*Exemplo*

Este exemplo devolve o início do mês para este carimbo temporal:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E devolve este resultado:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifique se uma corda começa com um substring específico.
Volte a ser verdade quando o substring for encontrado, ou volte a ser falso quando não for encontrado.
Esta função não é sensível a casos.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda para verificar |
| <*pesquisarTexto*> | Sim | Cadeia | A corda de partida para encontrar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| true ou false  | Booleano | Volte a ser verdade quando o substring inicial for encontrado. Devolver falso quando não for encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a corda "hello world" começa com o substring "olá":

```
startsWith('hello world', 'hello')
```

E devolve este resultado:`true`

*Exemplo 2*

Este exemplo verifica se a corda "hello world" começa com o substring "saudações":

```
startsWith('hello world', 'greetings')
```

E devolve este resultado:`false`

<a name="string"></a>

### <a name="string"></a>string

Devolva a versão de corda por um valor.

```
string(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Qualquer | O valor para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor de cadeia*> | Cadeia | A versão de corda para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de cadeia para este número:

```
string(10)
```

E devolve este resultado:`"10"`

*Exemplo 2*

Este exemplo cria uma corda para o objeto JSON\\especificado e utiliza o carácter backslash () como um personagem de fuga para a marca de dupla citação (").

```
string( { "name": "Sophie Owen" } )
```

E devolve este resultado:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Devolva o resultado de subtrair o segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Inteiro ou Flutuador | O número a partir do qual subtrair o *subsolo* |
| <*subtrahend*> | Sim | Inteiro ou Flutuador | O número para subtrair do *minúendo* |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado*> | Inteiro ou Flutuador | O resultado de subtrair o segundo número do primeiro número |
||||

*Exemplo*

Este exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E devolve este resultado:`10`

<a name="substring"></a>

### <a name="substring"></a>substring

Volte a colocar caracteres de uma corda, a partir da posição especificada, ou do índice.
Os valores do índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda cujos personagens quer |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou superior a 0 que pretende utilizar como posição inicial ou valor de índice |
| <*comprimento*> | Sim | Número inteiro | Um número positivo de caracteres que você quer no substring |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*resultado substring*> | Cadeia | Uma subcadeia com o número especificado de caracteres, a partir da posição de índice especificada na cadeia de origem |
||||

*Exemplo*

Este exemplo cria uma subcadeia de cinco caracteres a partir da corda especificada, a partir do valor do índice 6:

```
substring('hello world', 6, 5)
```

E devolve este resultado:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtratoFromTime

Subtraia várias unidades temporais de uma marca temporal.
Ver também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda que contém o carimbo de tempo |
| <*intervalo*> | Sim | Número inteiro | O número de unidades de tempo especificadas para subtrair |
| <*timeUnit*> | Sim | Cadeia | A unidade de tempo para usar com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atualizado*> | Cadeia | O carimbo de tempo menos o número especificado de unidades de tempo |
||||

*Exemplo 1*

Este exemplo subtrai um dia a partir desta marca de tempo:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E devolve este resultado:`"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo subtrai um dia a partir desta marca de tempo:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E devolve este resultado utilizando o formato "D" opcional:`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>tomar

Devolver artigos da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção*> | Sim | Corda ou Matriz | A coleção cujos itens você quer |
| <*contar*> | Sim | Número inteiro | Um inteiro positivo para o número de itens que você quer da frente |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <> de *subset* ou [<*> de subconjunto]* | String ou Array, respectivamente | Uma cadeia ou matriz que tenha o número especificado de itens retirados da parte da frente da coleção original |
||||

*Exemplo*

Estes exemplos obtêm o número especificado de itens da parte da frente destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E devolver estes resultados:

* Primeiro exemplo:`"abc"`
* Segundo exemplo:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>tiques

Devolva o valor da `ticks` propriedade por um carimbo de tempo especificado.
Um *tique* é um intervalo de 100 nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*carimbo de tempo*> | Sim | Cadeia | A corda para um carimbo temporal |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carraças-número*> | Número inteiro | O número de carrapatos desde o carimbo de tempo especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Volte a colocar uma corda em formato minúsculo. Se um personagem na corda não tem uma versão minúscula, esse personagem permanece inalterado na corda devolvida.

```
toLower('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda para voltar em formato minúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*minúscula-texto*> | Cadeia | A cadeia original em formato minúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em minúscula:

```
toLower('Hello World')
```

E devolve este resultado:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Volte a colocar uma corda em formato superior. Se um personagem na corda não tem uma versão maiúscula, esse personagem permanece inalterado na corda devolvida.

```
toUpper('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda para voltar em formato maiúsculo |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*texto maiúsculo*> | Cadeia | A cadeia original em formato maiúsculo |
||||

*Exemplo*

Este exemplo converte esta cadeia em maiúsculas:

```
toUpper('Hello World')
```

E devolve este resultado:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>gatilho

Volte a devolva a saída de um gatilho no prazo de execução, ou valores de outros pares de nome e valor da JSON, que pode atribuir a uma expressão.

* Dentro das inputs de um gatilho, esta função devolve a saída da execução anterior.

* Dentro da condição de um gatilho, esta função devolve a saída da execução atual.

Por predefinição, a função refere todo o objeto do gatilho, mas pode especificar opcionalmente uma propriedade cujo valor deseja.
Além disso, esta função tem versões abreviadas disponíveis, ver [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody).

```
trigger()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*saída de gatilho*> | Cadeia | A saída de um gatilho no tempo de execução |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Volte a devolver `body` a saída de um gatilho no tempo de execução.
Abreviação `trigger().outputs.body`para.
Ver [gatilho()](#trigger).

```
triggerBody()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*gatilho-saída corporal*> | Cadeia | A `body` saída do gatilho |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Devolva uma matriz com valores que correspondam a um nome chave nos *dados de formulário* de um gatilho ou na saída codificada pelo *formulário.*

```
triggerFormDataMultiValues('<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*chave*> | Sim | Cadeia | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| [<> de *conjunto com valores-chave]* | Matriz | Uma matriz com todos os valores que correspondem à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz a partir do valor-chave "feedUrl" nos dados de formulário de um gatilho RSS ou na saída codificada por formulários:

```
triggerFormDataMultiValues('feedUrl')
```

E devolve esta matriz como um resultado exemplo:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Volte a colocar uma corda com um único valor que corresponda a um nome-chave nos *dados de formulário* de um gatilho ou na saída codificada pelo *formulário.*
Se a função encontrar mais de um fósforo, a função lança um erro.

```
triggerFormDataValue('<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*chave*> | Sim | Cadeia | O nome da chave cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor-chave*> | Cadeia | O valor na chave especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia a partir do valor-chave "feedUrl" nos dados de formulário de um gatilho RSS ou na saída codificada por formulários:

```
triggerFormDataValue('feedUrl')
```

E devolve esta corda como um resultado exemplo:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>gatilhoMultipartBody

Devolva o corpo para uma parte específica da saída de um gatilho que tenha várias partes.

```
triggerMultipartBody(<index>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*índice*> | Sim | Número inteiro | O valor do índice para a parte que você quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*corpo*> | Cadeia | O corpo para a parte especificada na saída multiparte de um gatilho |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>gatilhoSaídas

Volte a colocar a saída de um gatilho no prazo de execução, ou valores de outros pares de nome e valor da JSON.
Abreviação `trigger().outputs`para.
Ver [gatilho()](#trigger).

```
triggerOutputs()
```

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*saída de gatilho*> | Cadeia | A saída de um gatilho no tempo de execução  |
||||

<a name="trim"></a>

### <a name="trim"></a>aparar

Remova o espaço branco de liderar e arrastando a partir de uma corda e devolva a corda atualizada.

```
trim('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*texto*> | Sim | Cadeia | A corda que tem o espaço branco líder e trailing para remover |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizadoTexto*> | Cadeia | Uma versão atualizada para a cadeia original sem liderar ou seguir o espaço branco |
||||

*Exemplo*

Este exemplo remove o espaço branco líder e trailing da corda " Hello World ":

```
trim(' Hello World  ')
```

E devolve este resultado:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>união

Devolva uma coleção que tenha *todos os* itens das coleções especificadas.
Para aparecer no resultado, um item pode aparecer em qualquer coleção passada para esta função. Se um ou mais itens tiverem o mesmo nome, o último item com esse nome aparece no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, *<coleção2*>, ...  | Sim | Matriz ou Objeto, mas não ambos | As coleções de onde você quer *todos os* itens |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*atualizadoCollection*> | Matriz ou Objeto, respectivamente | Uma coleção com todos os itens das coleções especificadas - sem duplicados |
||||

*Exemplo*

Este exemplo obtém *todos os* itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E devolve este resultado:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Volte a colocar uma versão codificada por um identificador de recursos uniforme (URI) para uma cadeia, substituindo caracteres inseguros de URL por caracteres de fuga.
Utilize esta função em vez [de codificar o Componente Uri()](#encodeUriComponent).
Embora ambas as funções `uriComponent()` funcionem da mesma forma, é preferível.

```
uriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda para converter em formato codificado por URI |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*codificado-uri*> | Cadeia | A corda codificada uri com caracteres de fuga |
||||

*Exemplo*

Este exemplo cria uma versão codificada por URI para esta cadeia:

```
uriComponent('https://contoso.com')
```

E devolve este resultado:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Devolva a versão binária para um componente de identificador de recursos uniforme (URI).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia codificada uri para converter |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*binário-para-codificado-uri*> | Cadeia | A versão binária para a cadeia codificada por URI. O conteúdo binário é codificado por base64 `$content`e representado por . |
||||

*Exemplo*

Este exemplo cria a versão binária para esta cadeia codificada por URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Devolva a versão de cadeia para um fio codificado de um identificador de recursos uniforme (URI), descodificando eficazmente a cadeia codificada pelo URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A cadeia codificada uri para descodificar |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*descodificado-uri*> | Cadeia | A versão descodificada para a cadeia codificada uri |
||||

*Exemplo*

Este exemplo cria a versão de string descodificada para esta cadeia codificada por URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E devolve este resultado:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Devolva o valor de `host` um identificador de recursos uniforme (URI).

```
uriHost('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `host` cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor hospedeiro*> | Cadeia | O `host` valor para o URI especificado |
||||

*Exemplo*

Este exemplo `host` encontra o valor para este URI:

```
uriHost('https://www.localhost.com:8080')
```

E devolve este resultado:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Devolva o valor de `path` um identificador de recursos uniforme (URI).

```
uriPath('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `path` cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor da rota*> | Cadeia | O `path` valor para o URI especificado. Se `path` não tiver um valor, devolva o personagem "/". |
||||

*Exemplo*

Este exemplo `path` encontra o valor para este URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndquery

Devolva os `path` valores e valores `query` para um identificador de recursos uniforme (URI).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `path` `query` cujos e valores quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*path-consulta-valor*> | Cadeia | Os `path` `query` valores e valores para o URI especificado. Se `path` não especificar um valor, devolva o carácter "/". |
||||

*Exemplo*

Este exemplo `path` encontra `query` os valores e valores para este URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPorto

Devolva o valor de `port` um identificador de recursos uniforme (URI).

```
uriPort('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `port` cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor portuário*> | Número inteiro | O `port` valor para o URI especificado. Se `port` não especificar um valor, devolva a porta padrão para o protocolo. |
||||

*Exemplo*

Este exemplo `port` devolve o valor para este URI:

```
uriPort('http://www.localhost:8080')
```

E devolve este resultado:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Devolva o valor de `query` um identificador de recursos uniforme (URI).

```
uriQuery('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `query` cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*consulta-valor*> | Cadeia | O `query` valor para o URI especificado |
||||

*Exemplo*

Este exemplo `query` devolve o valor para este URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Devolva o valor de `scheme` um identificador de recursos uniforme (URI).

```
uriScheme('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia | O URI `scheme` cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor do regime*> | Cadeia | O `scheme` valor para o URI especificado |
||||

*Exemplo*

Este exemplo `scheme` devolve o valor para este URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve este resultado:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Devolva o carimbo de tempo atual.

```
utcNow('<format>')
```

Opcionalmente, pode especificar um formato diferente com o *formato* <> parâmetro.


| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*formato*> | Não | Cadeia | Ou um [único especificador](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) de formato ou um padrão de [formato personalizado.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) O formato predefinido para a marca de tempo é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), que cumpre com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações do fuso horário. |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*carimbo de tempo atual*> | Cadeia | A data e hora atuais |
||||

*Exemplo 1*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém o carimbo de tempo atual:

```
utcNow()
```

E devolve este resultado:`"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é 15 de abril de 2018 às 13:00.
Este exemplo obtém a marca de tempo atual utilizando o formato "D" opcional:

```
utcNow('D')
```

E devolve este resultado:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variáveis

Devolva o valor para uma variável especificada.

```
variables('<variableName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*nome variável*> | Sim | Cadeia | O nome da variável cujo valor quer |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*valor variável*> | Qualquer | O valor da variável especificada |
||||

*Exemplo*

Suponha que o valor atual para uma variável "numItems" é de 20.
Este exemplo obtém o valor inteiro para esta variável:

```
variables('numItems')
```

E devolve este resultado:`20`

<a name="workflow"></a>

### <a name="workflow"></a>fluxo de trabalho

Devolva todos os detalhes sobre o fluxo de trabalho em si durante o tempo de funcionamento.

```
workflow().<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*propriedade*> | Não | Cadeia | O nome da propriedade workflow cujo valor você quer <p>Um objeto de fluxo de trabalho tem estas propriedades: **nome,** **tipo,** **id,** **localização,** e **execução.** O valor da propriedade **executada** é também um objeto que tem estas propriedades: **nome,** **tipo,** e **id**. |
|||||

*Exemplo*

Este exemplo devolve o nome para a execução atual de um fluxo de trabalho:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Devolva a versão XML para uma corda que contenha um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*valor*> | Sim | Cadeia | A corda com o objeto JSON para converter <p>O objeto JSON deve ter apenas uma propriedade de raiz, que não pode ser uma matriz. <br>Use o carácter\\backslash () como um personagem de fuga para a marca de citação dupla ("). |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-versão*> | Objeto | O XML codificado para a corda especificada ou objeto JSON |
||||

*Exemplo 1*

Este exemplo cria a versão XML para esta cadeia, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E devolve este resultado XML:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que tenha este objeto JSON:

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

Verifique o XML para ver se há nós ou valores que correspondam a uma expressão XPath (XML Path Language) e devolva os nós ou valores correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda-o a navegar numa estrutura de documentoXML para que possa selecionar nós ou calcular valores no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia XML para procurar por nóouos ou valores que correspondam a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para encontrar nódosos ou valores XML correspondentes |
|||||

| Valor devolvido | Tipo | Descrição |
| ------------ | ---- | ----------- |
| <*xml-nó*> | XML | Um nó XML quando apenas um nó corresponde à expressão XPath especificada |
| <*valor*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-nóde1*>, <*xml-node2*>, ...] </br>-ou- </br>[<*valor 1*>, <*valor2*>, ...] | Matriz | Uma matriz com nódosos ou valores XML que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Este exemplo encontra nós que `<name></name>` combinam com o nó nos argumentos especificados, e devolve uma matriz com esses valores de nó:

`xpath(xml(parameters('items')), '/produce/item/name')`

Aqui estão os argumentos:

* A corda "itens", que contém este XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  O exemplo utiliza a função [de parâmetros()](#parameters) para obter a cadeia XML a partir do argumento "itens", mas também deve converter a cadeia para o formato XML utilizando a função [xml().](#xml)

* Esta expressão XPath, que é passada como uma corda:

  `"/produce/item/name"`

Aqui está o conjunto de resultados `<name></name`com os nós que combinam:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemplo 2*

Seguindo o Exemplo 1, este exemplo encontra `<count></count>` nós que combinam com o `sum()` nó e adiciona esses valores de nó com a função:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E devolve este resultado:`30`

*Exemplo 3*

Para este exemplo, ambas as expressões `<location></location>` encontram nós que combinam com o nó, nos argumentos especificados, que incluem XML com um espaço de nome. 

> [!NOTE]
>
> Se estiver a trabalhar em vista de código, escape da marca de\\citação dupla (") utilizando o caracteres backslash (). 
> Por exemplo, precisa de usar caracteres de fuga quando serializar uma expressão como uma corda JSON. 
> No entanto, se estiver a trabalhar no Logic App Designer ou editor de expressão, não precisa de escapar à marca de citação dupla porque o carácter backslash é adicionado automaticamente à definição subjacente, por exemplo:
> 
> * Vista de código:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor de expressão:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> Os seguintes exemplos aplicam-se às expressões que introduz no editor de expressão.

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Aqui estão os argumentos:

* Este XML, que inclui o espaço `xmlns="http://contoso.com"`de nome do documento XML,

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Ou expressão XPath aqui:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Aqui está o nó de `<location></location>` resultados que combina com o nó:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 4*

Seguindo o Exemplo 3, este exemplo `<location></location>` encontra o valor no nó:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

E devolve este resultado:`"Paris"`

## <a name="next-steps"></a>Passos seguintes

Conheça a Linguagem de [Definição](../logic-apps/logic-apps-workflow-definition-language.md) de Fluxo de Trabalho
