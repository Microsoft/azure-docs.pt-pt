---
title: As funções de referência de linguagem de definição de fluxo de trabalho - Azure Logic Apps | Documentos da Microsoft
description: Saiba mais sobre as funções de linguagem de definição de fluxo de trabalho para o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 08/15/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e58d534811fc6d6ed2bb24486c149f217a7a28a3
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189908"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de funções para a linguagem de definição de fluxo de trabalho no Azure Logic Apps

Algumas [expressões](../logic-apps/logic-apps-workflow-definition-language.md#expressions) na [Azure Logic Apps](../logic-apps/logic-apps-overview.md) obter os valores de ações de tempo de execução que ainda poderão não existir quando a definição de fluxo de trabalho de aplicação lógica começa a ser executada.
Para referenciar ou trabalhar com esses valores em expressões, pode usar *funções* fornecido pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md).
Por exemplo, pode usar funções matemáticas para cálculos, como o [Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add) função, que devolve a soma de números inteiros ou floats. Seguem-se algumas tarefas de exemplo mais que pode realizar com as funções:

| Tarefa | Sintaxe da função | Resultado |
| ---- | --------------- | ------ |
| Retorne uma cadeia de caracteres no formato em minúsculas. | toLower('<*text*>') <p>Por exemplo: toLower('Hello') | "hello" |
| Devolve um identificador exclusivo global (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Este artigo descreve as funções que pode utilizar ao criar a lógica de definições de aplicação.
Para encontrar funções [com base em suas finalidades gerais](#ordered-by-purpose), prossiga com as tabelas seguintes. Ou, para obter informações detalhadas sobre cada função, consulte a [lista alfabética](#alphabetical-list).

> [!NOTE]
> A sintaxe para definições de parâmetros, um ponto de interrogação (?) que aparece depois de um parâmetro significa que o parâmetro é opcional.
> Por exemplo, veja [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funções em expressões

Para mostrar como utilizar uma função numa expressão, este exemplo mostra como pode obter o valor do `customerName` parâmetro e atribuir esse valor para o `accountName` propriedade utilizando o [parameters()](#parameters) função numa expressão:

```json
"accountName": "@parameters('customerName')"
```

Aqui estão algumas outras formas gerais que pode utilizar funções em expressões de:

| Tarefa | Sintaxe da função numa expressão |
| ---- | -------------------------------- |
| Realizar o trabalho com um item passando esse item para uma função. | "\@<*functionName*> (<*item*>)" |
| 1. Obter o *parameterName*do valor utilizando aninhada `parameters()` função. </br>2. Executar o trabalho com o resultado ao passar esse valor para *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Obter o resultado da função interna aninhada *functionName*. </br>2. Passar o resultado para a função externa *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. Obter o resultado da *functionName*. </br>2. Uma vez que o resultado é um objeto com a propriedade *propertyName*, obter o valor dessa propriedade. | "\@<*functionName*>(<*item*>). <*propertyName*>" |
|||

Por exemplo, o `concat()` função pode assumir dois ou mais valores de cadeia de caracteres como parâmetros. Esta função combina essas cadeias de caracteres numa cadeia de caracteres.
Pode passar em literais de cadeia de caracteres, por exemplo, "Sophia" e "Owen", para que obtém uma cadeia de caracteres combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Em alternativa, pode obter valores de cadeia de caracteres de parâmetros. Este exemplo utiliza a `parameters()` função em cada `concat()` parâmetro e o `firstName` e `lastName` parâmetros. Então, passa as cadeias de caracteres resultantes para o `concat()` funcionar para que obtém uma cadeia de caracteres combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, os dois exemplos atribuir o resultado para o `customerName` propriedade.

Aqui estão as funções disponíveis, ordenadas por suas finalidades gerais ou pode procurar as funções com base na [ordem alfabética](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funções de cadeia

Para trabalhar com cadeias de caracteres, pode usar essas funções de cadeia de caracteres e também alguns [funções de coleção](#collection-functions).
Funções de cadeia só funcionam em cadeias de caracteres.

| Função de cadeia de caracteres | Tarefa |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combinar dois ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifique se uma cadeia de caracteres termina com a subcadeia especificada. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gere um identificador exclusivo global (GUID) como uma cadeia de caracteres. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Devolve a posição inicial para uma subcadeia. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Devolve a posição inicial para a última ocorrência de uma subcadeia. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substituir uma subcadeia com a cadeia especificada e retornar a cadeia de caracteres atualizada. |
| [dividir](../logic-apps/workflow-definition-language-functions-reference.md#split) | Devolva uma matriz que contém subcadeias de caracteres, separadas por vírgulas, a partir de uma cadeia de caracteres maior com base num caractere delimitador especificado na cadeia de caracteres original. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verificar se uma cadeia de caracteres começa com uma subcadeia específica. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Devolva carateres de uma cadeia de caracteres, a partir da posição especificada. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retorne uma cadeia de caracteres no formato em minúsculas. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retorne uma cadeia de caracteres no formato em maiúsculas. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, em geral, matrizes, cadeias de caracteres e, às vezes, dicionários, pode utilizar estas funções de coleção.

| Função de coleção | Tarefa |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifique se uma coleção tem um item específico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Devolva o primeiro item a partir de uma coleção. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retornar uma coleção que tenha *apenas* os itens comuns em coleções especificadas. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando dentro de uma ação de repetição numa matriz, retorna o item atual na matriz durante a iteração da ação de atual. |
| [aderir](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retornar uma cadeia de caracteres que tenha *todos os* os itens de uma matriz, separados pelo caractere especificado. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Devolva o último item a partir de uma coleção. |
| [Comprimento](../logic-apps/workflow-definition-language-functions-reference.md#length) | Devolva o número de itens numa cadeia ou matriz. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Remover itens da frente de uma coleção e devolver *todas as outras* itens. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Itens de retorno da frente de uma coleção. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retornar uma coleção que tenha *todos os* os itens de coleções especificadas. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funções de comparação lógica

Para trabalhar com condições, comparar valores e resultados de expressão ou avaliar os vários tipos de lógica, pode utilizar estas funções de comparação lógica.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifique se todas as expressões forem verdadeiras. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifique se os dois valores são equivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifique se o primeiro valor é maior do que o segundo valor. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifique se o primeiro valor é maior que ou igual ao segundo valor. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifique se uma expressão é verdadeira ou falsa. Com base no resultado, devolva um valor especificado. |
| [menos](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifique se o primeiro valor é menor que o segundo valor. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifique se o primeiro valor é menor ou igual ao segundo valor. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifique se uma expressão é falsa. |
| [ou](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifique se, pelo menos, uma expressão é verdadeira. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou formato de um valor, pode usar essas funções de conversão.
Por exemplo, pode alterar um valor de um valor booleano para um número inteiro.
Para saber como as aplicações lógicas processam os tipos de conteúdo durante a conversão, veja [lidar com tipos de conteúdo](../logic-apps/logic-apps-content-type.md).
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Devolva uma matriz de um único especificado de entrada. Para várias entradas, consulte [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Devolva a versão codificada em base64 para uma cadeia de caracteres. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Devolva a versão binária para uma cadeia de caracteres codificada em base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Devolva a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Devolva a versão binária para um valor de entrada. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Devolva a versão booleana para um valor de entrada. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Devolve o URI de dados para um valor de entrada. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Devolva a versão binária para um URI de dados. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Devolva a versão de cadeia de caracteres para um URI de dados. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Devolva a versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Devolva a versão binária para um URI de dados. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Devolver uma cadeia de caracteres que substitui o escape de caracteres com versões decodificadas. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retorne uma cadeia de caracteres que substitui os carateres não seguros de URL com carateres de escape. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Devolver a flutuante ponto número para um valor de entrada. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Devolva a versão de número inteiro para uma cadeia de caracteres. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Devolve o valor de tipo JavaScript Object Notation (JSON) ou o objeto para uma cadeia ou XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Devolva a versão de cadeia de caracteres para um valor de entrada. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Devolve a versão codificada de URI para um valor de entrada ao substituir carateres não seguros de URL com carateres de escape. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Devolva a versão binária para uma cadeia codificada de URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Devolva a versão de cadeia de caracteres para uma cadeia codificada de URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Devolva a versão XML para uma cadeia de caracteres. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funções matemáticas

Para trabalhar com números inteiros e floats, pode usar essas funções matemáticas.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de matemática | Tarefa |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Devolva o resultado da adição de dois números. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retornar o resultado da divisão de dois números. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Devolva o valor mais alto de um conjunto de números ou uma matriz. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Devolva o valor mais baixo de um conjunto de números ou uma matriz. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Devolva o resto da divisão de dois números. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Devolva o produto da multiplicação de dois números. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Devolva um número inteiro aleatório de um intervalo especificado. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Devolva uma matriz de inteiros que começa a partir de um número inteiro especificado. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Devolva o resultado da subtração o segundo número do primeiro número. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horas, pode usar essas funções de data e hora.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicione um número de dias a um carimbo. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicione um número de horas para um carimbo. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicione um número de minutos para um carimbo. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicione um número de segundos a um carimbo. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adiciona um número de unidades de tempo para um carimbo. Consulte também [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converta um carimbo de Universal tempo coordenada (UTC) para o fuso horário de destino. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converta um carimbo de fuso horário de origem para o fuso horário de destino. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converta um carimbo de fuso horário de origem para Universal tempo coordenada (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Devolva o dia do componente de mês a partir de um carimbo. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Devolva o dia do componente da semana de um carimbo. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Devolva o dia do componente de ano a partir de um carimbo. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Devolva a data de um carimbo. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Devolva o timestamp atual, bem como as unidades de tempo especificado. Consulte também [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Devolva o carimbo de hora atual menos as unidades de tempo especificado. Consulte também [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Devolva o início do dia para um carimbo. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Devolva o início da hora para um carimbo. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Devolva o início do mês para um carimbo. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrai um número de unidades de tempo de um carimbo. Consulte também [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Devolver o `ticks` valor da propriedade para um carimbo especificado. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Devolva o timestamp atual como uma cadeia de caracteres. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funções de fluxo de trabalho

Estas funções de fluxo de trabalho podem ajudá-lo a:

* Obtenha detalhes sobre uma instância de fluxo de trabalho em tempo de execução.
* Trabalhar com as entradas utilizadas para criar uma instância de aplicações lógicas.
* Referenciam as saídas de acionadores e ações.

Por exemplo, pode referenciar as saídas da ação de um e utilizar esses dados numa ação posterior.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de fluxo de trabalho | Tarefa |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Devolva a saída da ação atual em tempo de execução ou valores de outros pares de nome e valor JSON. Consulte também [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Devolver uma ação `body` de saída em tempo de execução. Consulte também [corpo](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Devolva o resultado de uma ação em tempo de execução. Ver [ações](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Ações](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Devolva o resultado de uma ação em tempo de execução ou valores de outros pares de nome e valor JSON. Consulte também [ação](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Devolver uma ação `body` de saída em tempo de execução. Consulte também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Criar uma matriz com os valores que correspondem ao nome da chave no *dados de formulário* ou *formulário codificado* saídas da ação. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Devolver um valor único que corresponde ao nome da chave numa ação *dados de formulário* ou *saída de formulário codificado*. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando dentro de uma ação de repetição numa matriz, retorna o item atual na matriz durante a iteração da ação de atual. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando dentro de um para cada ou fazer até o loop, retorno o item atual do ciclo especificado.|
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Devolva o "URL de retorno de chamada" que chama um acionador ou ação. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Devolve o corpo de uma parte específica na saída de uma ação que tem várias partes. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Devolva o valor para um parâmetro que é descrito na sua definição da aplicação lógica. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Devolva o resultado de um acionador no tempo de execução ou a partir de outros pares de nome e valor JSON. Consulte também [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Devolver um acionador `body` de saída em tempo de execução. Ver [acionador](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Devolver um valor único que corresponda um nome de chave na *dados de formulário* ou *formulário codificado* acionar saídas. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Devolve o corpo de uma parte específica na saída de várias partes de um acionador. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Criar uma matriz cujos valores corresponde ao nome da chave no *dados de formulário* ou *formulário codificado* acionar saídas. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Devolva o resultado de um acionador no tempo de execução ou valores de outros pares de nome e valor JSON. Ver [acionador](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Variáveis](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Devolva o valor para uma variável especificada. |
| [Fluxo de trabalho](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Devolva todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funções de análise do URI

Para trabalhar com os identificadores de recurso uniforme (URIs) e obter vários valores de propriedade para estes URIs, pode utilizar estas funções de análise do URI.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de análise do URI | Tarefa |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Devolver o `host` valor para um identificador de recurso uniforme (URI). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Devolver o `path` valor para um identificador de recurso uniforme (URI). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Devolver o `path` e `query` valores para um identificador de recurso uniforme (URI). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Devolver o `port` valor para um identificador de recurso uniforme (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Devolver o `query` valor para um identificador de recurso uniforme (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Devolver o `scheme` valor para um identificador de recurso uniforme (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funções de manipulação: JSON & XML

Para trabalhar com objetos JSON e nós XML, pode usar essas funções de manipulação.
Para obter a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de manipulação | Tarefa |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicionar uma propriedade e seu valor ou o par nome / valor, a um objeto JSON e devolver do objeto atualizado. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Devolva o primeiro valor não nulo de um ou mais parâmetros. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Remover uma propriedade de um objeto JSON e retornar do objeto atualizado. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Defina o valor de propriedade de um objeto JSON e retornar do objeto atualizado. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verificar o XML de nós ou valores que correspondem a uma expressão XPath (linguagem de caminho de XML) e devolver os valores ou nós correspondentes. |
|||

<a name="alphabetical-list"></a>
<a name="action"></a>

### <a name="action"></a>action

Devolver o *atual* saída em tempo de execução, ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão de ação.
Por predefinição, esta função referencia o objeto de ação inteiro, mas, opcionalmente, pode especificar uma propriedade de valor que pretende.
Consulte também [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Pode utilizar o `action()` função apenas desses lugares:

* O `unsubscribe` propriedade para uma ação de webhook para que possa acessar o resultado em relação ao original `subscribe` pedido
* O `trackedProperties` propriedade para uma ação
* O `do-until` loop condição para uma ação

```
action()
action().outputs.body.<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*property*> | Não | String | O nome para a ação propriedade do objeto valor que pretende: **name**, **startTime**, **endTime**, **entradas**,  **produz**, **status**, **código**, **trackingId**, e **clientTrackingId**. No portal do Azure, pode encontrar essas propriedades, revendo os detalhes de um específico histórico de execuções. Para obter mais informações, consulte [API REST - ações de execução do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*action-output*> | String | A saída da ação atual ou propriedade |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Devolver uma ação `body` de saída em tempo de execução.
Um atalho para `actions('<actionName>').outputs.body`.
Ver [body()](#body) e [actions()](#actions).

```
actionBody('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome para a ação `body` saída que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | O `body` de saída da ação especificada |
||||

*Exemplo*

Neste exemplo obtém o `body` saída da ação de Twitter `Get user`:

```
actionBody('Get_user')
```

E devolve o resultado:

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

Devolva o resultado de uma ação em tempo de execução.
Um atalho para `actions('<actionName>').outputs`.
Ver [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome da ação de saída de que pretende que o |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*output*> | String | A saída da ação especificada |
||||

*Exemplo*

Neste exemplo obtém o resultado da ação de Twitter `Get user`:

```
actionOutputs('Get_user')
```

E devolve o resultado:

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

### <a name="actions"></a>Ações

Devolva o resultado de uma ação em tempo de execução ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão. Por predefinição, a função referencia o objeto de ação inteiro, mas, opcionalmente, pode especificar uma propriedade cujo valor que pretende.
Para versões de forma abreviada, consulte [actionBody()](#actionBody), [actionOutputs()](#actionOutputs), e [body()](#body).
Para a ação atual, consulte [action()](#action).

> [!NOTE]
> Anteriormente, poderia usar o `actions()` função ou o `conditions` elemento ao especificar que uma ação foi executada com base no resultado de outra ação. No entanto, para declarar explicitamente dependências entre ações, deverá utilizar a ação dependente `runAfter` propriedade.
> Para saber mais sobre o `runAfter` propriedade, veja [detetar e processar falhas com a propriedade runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome para o objeto de ação cuja saída desejar  |
| <*property*> | Não | String | O nome para a ação propriedade do objeto valor que pretende: **name**, **startTime**, **endTime**, **entradas**,  **produz**, **status**, **código**, **trackingId**, e **clientTrackingId**. No portal do Azure, pode encontrar essas propriedades, revendo os detalhes de um específico histórico de execuções. Para obter mais informações, consulte [API REST - ações de execução do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*action-output*> | String | A saída da ação especificada ou propriedade |
||||

*Exemplo*

Neste exemplo obtém o `status` valor da propriedade da ação de Twitter `Get user` em tempo de execução:

```
actions('Get_user').outputs.body.status
```

E devolve o resultado: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>adicionar

Devolva o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sim | Número inteiro, vírgula flutuante, ou uma combinação | Os números a adicionar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*result-sum*> | Número inteiro ou flutuante | O resultado da adição os números especificados |
||||

*Exemplo*

Este exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E devolve o resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicione um número de dias a um carimbo.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*dias*> | Sim | Número inteiro | O número positivo ou negativo de dias a adicionar |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora mais o número especificado de dias  |
||||

*Exemplo 1*

Este exemplo adiciona 10 dias para o período de tempo especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E devolve o resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Neste exemplo subtrai cinco dias do período de tempo especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E devolve o resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicione um número de horas para um carimbo.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*Horas*> | Sim | Número inteiro | O número positivo ou negativo de horas a adicionar |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora mais o número especificado de horas  |
||||

*Exemplo 1*

Este exemplo adiciona 10 horas para o período de tempo especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E devolve o resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Neste exemplo subtrai cinco horas a partir do período de tempo especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E devolve o resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicione um número de minutos para um carimbo.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*minutes*> | Sim | Número inteiro | O número positivo ou negativo de minutos a adicionar |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora mais o número especificado de minutos |
||||

*Exemplo 1*

Este exemplo adiciona 10 minutos para o período de tempo especificado:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E devolve o resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Neste exemplo subtrai cinco minutos do período de tempo especificado:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E devolve o resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adicionar uma propriedade e seu valor ou o par nome / valor, a um objeto JSON e devolver do objeto atualizado. Se o objeto já existe no tempo de execução, a função emite um erro.

```
addProperty(<object>, '<property>', <value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Object | O objeto JSON em que pretende adicionar uma propriedade |
| <*property*> | Sim | String | O nome da propriedade adicionar |
| <*Valor*> | Sim | Qualquer | O valor da propriedade |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | O objeto JSON atualizado com a propriedade especificada |
||||

*Exemplo*

Este exemplo adiciona a `accountNumber` propriedade para o `customerProfile` objeto, que é convertido em JSON com o [JSON()](#json) função.
A função atribui um valor que é gerado pelos [guid()](#guid) funcionar e retorna o objeto atualizado:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicione um número de segundos a um carimbo.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*seconds*> | Sim | Número inteiro | O número positivo ou negativo de segundos a adicionar |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora e o número de segundos especificado  |
||||

*Exemplo 1*

Este exemplo adiciona 10 segundos para o período de tempo especificado:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E devolve o resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Neste exemplo subtrai cinco segundos, para o período de tempo especificado:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E devolve o resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adiciona um número de unidades de tempo para um carimbo.
Consulte também [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificado para adicionar |
| <*timeUnit*> | Sim | String | A unidade de tempo para utilizar com o *intervalo*: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês", "Ano" |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora e o número de unidades de tempo especificado  |
||||

*Exemplo 1*

Este exemplo adiciona um dia para o período de tempo especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E devolve o resultado: `"2018-01-02T00:00:00:0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia para o período de tempo especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E devolve o resultado com o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifique se todas as expressões forem verdadeiras.
Devolve verdadeiro quando todas as expressões forem verdadeiras, ou retornar false quando, pelo menos, uma expressão é falsa.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Expression1*>, <*expression2*>,... | Sim | Booleano | As expressões para verificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando todas as expressões forem verdadeiras. Retornar false quando pelo menos uma expressão é falsa. |
||||

*Exemplo 1*

Estes exemplos, verifique se os valores booleanos especificados são todas verdadeiros:

```
and(true, true)
and(false, true)
and(false, false)
```

E retorna esses resultados:

* Primeiro exemplo: Ambas as expressões forem verdadeiras, portanto, retorna `true`.
* Segundo exemplo: Uma expressão é falsa, portanto, retorna `false`.
* Terceiro exemplo: Ambas as expressões forem falsos, portanto, retorna `false`.

*Exemplo 2*

Estes exemplos, verifique se as expressões especificadas são todas verdadeiras:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E retorna esses resultados:

* Primeiro exemplo: Ambas as expressões forem verdadeiras, portanto, retorna `true`.
* Segundo exemplo: Uma expressão é falsa, portanto, retorna `false`.
* Terceiro exemplo: Ambas as expressões forem falsos, portanto, retorna `false`.

<a name="array"></a>

### <a name="array"></a>array

Devolva uma matriz de um único especificado de entrada.
Para várias entradas, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia de caracteres para a criação de uma matriz |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*valor*>] | Array | Uma matriz que contém a única entrada especificada |
||||

*Exemplo*

Este exemplo cria uma matriz da cadeia de "Olá":

```
array('hello')
```

E devolve o resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Devolva a versão codificada em base64 para uma cadeia de caracteres.

```
base64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia de entrada |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada em base64 para a cadeia de entrada |
||||

*Exemplo*

Neste exemplo converte a cadeia "Olá" numa cadeia codificada em base64:

```
base64('hello')
```

E devolve o resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Devolva a versão binária para uma cadeia de caracteres codificada em base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia codificada em base64 a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | A versão binária para a cadeia codificada em base64 |
||||

*Exemplo*

Neste exemplo converte o "aGVsbG8 =" cadeia de caracteres codificada em base64 para uma cadeia de caracteres binária:

```
base64ToBinary('aGVsbG8=')
```

E devolve o resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Devolva a versão de cadeia de caracteres para uma cadeia codificada em base64, efetivamente descodificar a cadeia base64.
Utilize esta função vez [decodeBase64()](#decodeBase64).
Embora ambas as funções funcionam da mesma maneira, `base64ToString()` é preferencial.

```
base64ToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia codificada em base64 a descodificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Neste exemplo converte o "aGVsbG8 =" cadeia de caracteres codificada em base64 para apenas uma cadeia de caracteres:

```
base64ToString('aGVsbG8=')
```

E devolve o resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binário

Devolva a versão binária para uma cadeia de caracteres.

```
binary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | A versão binária para a cadeia especificada |
||||

*Exemplo*

Neste exemplo converte a cadeia "Olá" para uma cadeia de caracteres binária:

```
binary('hello')
```

E devolve o resultado:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>corpo

Devolver uma ação `body` de saída em tempo de execução.
Um atalho para `actions('<actionName>').outputs.body`.
Ver [actionBody()](#actionBody) e [actions()](#actions).

```
body('<actionName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome para a ação `body` saída que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | O `body` de saída da ação especificada |
||||

*Exemplo*

Neste exemplo obtém o `body` saída do `Get user` Twitter ação:

```
body('Get_user')
```

E devolve o resultado:

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

Devolva a versão de um valor booleana.

```
bool(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Qualquer | O valor a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | A versão booleana para o valor especificado |
||||

*Exemplo*

Estes exemplos convertam os valores especificados para valores booleanos:

```
bool(1)
bool(0)
```

E retorna esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Coalesce

Devolva o primeiro valor não nulo de um ou mais parâmetros.
Objetos vazios, prázdná pole e as cadeias vazias não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Sim | Qualquer, pode misturar tipos | Um ou mais itens para verificar se há nulo |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Qualquer | O primeiro item ou valor que não seja null. Se todos os parâmetros são nulos, esta função devolve um valor nula. |
||||

*Exemplo*

Estes exemplos devolvem o primeiro valor não nulo dos valores especificados, ou null quando todos os valores são nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E retorna esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combinar dois ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sim | String | Pelo menos duas cadeias de caracteres para combinar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | A cadeia de caracteres criada a partir as cadeias de caracteres de entrada combinadas |
||||

*Exemplo*

Este exemplo combina as cadeias de caracteres "Hello" e "World":

```
concat('Hello', 'World')
```

E devolve o resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contém

Verifique se uma coleção tem um item específico.
Devolve verdadeiro quando o item for encontrado, ou retornar falso quando não encontrado.
Esta função diferencia maiúsculas de minúsculas.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, essa função funciona nestes tipos de coleção:

* R *cadeia de caracteres* para localizar um *subcadeia*
* Uma *matriz* para localizar um *valor*
* R *dicionário* para localizar um *chave*

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres, matriz ou dicionário | A coleção para verificar |
| <*Valor*> | Sim | Cadeia de caracteres, matriz ou dicionário, respetivamente | O item para localizar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando o item é encontrado. Retornar falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica a cadeia de caracteres "hello world" para a subcadeia "world" e devolve verdadeiro:

```
contains('hello world', 'world')
```

*Exemplo 2*

Este exemplo verifica a cadeia de caracteres "hello world" para a subcadeia "universe" e devolve FALSO:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converta um carimbo de Universal tempo coordenada (UTC) para o fuso horário de destino.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*destinationTimeZone*> | Sim | String | O nome para o fuso horário de destino. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de hora é convertida para o fuso horário de destino |
||||

*Exemplo 1*

Neste exemplo converte um carimbo para o fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E devolve o resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Neste exemplo converte um carimbo para o fuso horário especificado e o formato:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E devolve o resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converta um carimbo de fuso horário de origem para o fuso horário de destino.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*sourceTimeZone*> | Sim | String | O nome para o fuso de horário de origem. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*destinationTimeZone*> | Sim | String | O nome para o fuso horário de destino. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de hora é convertida para o fuso horário de destino |
||||

*Exemplo 1*

Neste exemplo converte o fuso horário de origem para o fuso horário de destino:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E devolve o resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Neste exemplo converte um fuso horário para o fuso horário especificado e o formato:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E devolve o resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converta um carimbo de fuso horário de origem para Universal tempo coordenada (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*sourceTimeZone*> | Sim | String | O nome para o fuso de horário de origem. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de hora convertido para UTC |
||||

*Exemplo 1*

Neste exemplo converte um carimbo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E devolve o resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Neste exemplo converte um carimbo para UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E devolve o resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada únicas, consulte [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*objeto1*>, <*object2*>,... | Sim | Qualquer, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | A matriz criada a partir de todos os itens de entrada |
||||

*Exemplo*

Este exemplo cria uma matriz destas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E devolve o resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Devolve um identificador de recurso uniforme (URI) de dados para uma cadeia de caracteres.

```
dataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | O URI de dados para a cadeia de entrada |
||||

*Exemplo*

Este exemplo cria um URI de dados para a cadeia "Olá":

```
dataUri('hello')
```

E devolve o resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Devolva a versão binária de um identificador de recurso uniforme (URI) de dados.
Utilize esta função vez [decodeDataUri()](#decodeDataUri).
Embora ambas as funções funcionam da mesma maneira, `decodeDataUri()` é preferencial.

```
dataUriToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | O URI de dados a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma versão binária para este URI de dados:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve o resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Devolva a versão de cadeia de caracteres de um identificador de recurso uniforme (URI) de dados.

```
dataUriToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | O URI de dados a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | A versão de cadeia de caracteres para o URI de dados |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para este URI de dados:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve o resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Devolva o dia do mês de um carimbo.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Número inteiro | O dia do mês do período de tempo especificado |
||||

*Exemplo*

Neste exemplo devolve o número para o dia do mês deste timestamp:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E devolve o resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Devolva o dia da semana de um carimbo.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Número inteiro | O dia da semana do período de tempo especificado em que domingo é 0, segunda-feira é 1 e assim por diante |
||||

*Exemplo*

Neste exemplo devolve o número para o dia da semana deste timestamp:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E devolve o resultado: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Devolva o dia do ano a partir de um carimbo.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Número inteiro | O dia do ano do período de tempo especificado |
||||

*Exemplo*

Neste exemplo devolve o número do dia do ano a partir deste timestamp:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E devolve o resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Devolva a versão de cadeia de caracteres para uma cadeia codificada em base64, efetivamente descodificar a cadeia base64.
Considere a utilização [base64ToString()](#base64ToString) vez `decodeBase64()`.
Embora ambas as funções funcionam da mesma maneira, `base64ToString()` é preferencial.

```
decodeBase64('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia codificada em base64 a descodificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | A versão de cadeia de caracteres para uma cadeia de caracteres codificada em base64 |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres para uma cadeia de caracteres codificada em base64:

```
decodeBase64('aGVsbG8=')
```

E devolve o resultado: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Devolva a versão binária de um identificador de recurso uniforme (URI) de dados.
Considere a utilização [dataUriToBinary()](#dataUriToBinary), em vez `decodeDataUri()`.
Embora ambas as funções funcionam da mesma maneira, `dataUriToBinary()` é preferencial.

```
decodeDataUri('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | Os dados de cadeia URI a descodificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária para uma cadeia URI de dados |
||||

*Exemplo*

Este exemplo retorna a versão binária para este URI de dados:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E devolve o resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Devolver uma cadeia de caracteres que substitui o escape de caracteres com versões decodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia de caracteres com os carateres de escape a descodificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A cadeia de caracteres atualizada com os carateres de escape descodificada |
||||

*Exemplo*

Neste exemplo substitui os carateres de escape nessa cadeia descodificadas versões:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E devolve o resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Devolva o resultado de número inteiro de divisão de dois números.
Para obter o resultado de resto, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Número inteiro ou flutuante | O número pelo qual dividir o *divisor* |
| <*divisor*> | Sim | Número inteiro ou flutuante | O número que divida o *dividendo*, mas não pode ser 0 |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Número inteiro | O resultado de número inteiro da divisão o primeiro número, o segundo número |
||||

*Exemplo*

Os dois exemplos dividem o primeiro número, o segundo número:

```
div(10, 5)
div(11, 5)
```

E retornar esse resultado: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Devolve uma versão de identifier (URI) com codificação de recurso uniforme para uma cadeia de caracteres ao substituir carateres não seguros de URL com carateres de escape.
Considere a utilização [uriComponent()](#uriComponent), em vez `encodeUriComponent()`.
Embora ambas as funções funcionam da mesma maneira, `uriComponent()` é preferencial.

```
encodeUriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia a converter para formato codificado por URI |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | A cadeia codificada de URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada de URI para essa cadeia de caracteres:

```
encodeUriComponent('https://contoso.com')
```

E devolve o resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>Vazio

Verifique se uma coleção está vazia.
Devolve verdadeiro quando a coleção está vazia ou retornar falso quando não está vazio.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres, matriz ou objeto | A coleção para verificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando a coleção está vazia. Retornar falso quando não está vazio. |
||||

*Exemplo*

Estes exemplos, verifique se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E retorna esses resultados:

* Primeiro exemplo: Transmite uma cadeia vazia, pelo que a função devolve `true`.
* Segundo exemplo: Passa a cadeia de caracteres "abc", para que a função devolve `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifique se uma cadeia de caracteres termina com uma subcadeia específica.
Devolve verdadeiro quando a subcadeia for encontrada, ou retornar falso quando não encontrado.
Esta função não diferencia maiúsculas de minúsculas.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres para verificar |
| <*searchText*> | Sim | String | A subcadeia final para localizar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO  | Booleano | Devolve verdadeiro quando a subcadeia final é encontrada. Retornar falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia "Olá mundo" termina com a cadeia de caracteres "world":

```
endsWith('hello world', 'world')
```

E devolve o resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia "Olá mundo" termina com a cadeia de caracteres "universe":

```
endsWith('hello world', 'universe')
```

E devolve o resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>é igual a

Verifique se os valores, expressões ou objetos são equivalentes.
Devolve verdadeiro quando ambos são equivalentes ou retornam falsas quando eles não são equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | Vários | Os valores, expressões ou os objetos para comparar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando ambos são equivalentes. Retornar falso quando não é equivalente. |
||||

*Exemplo*

Estes exemplos, verifique se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E retorna esses resultados:

* Primeiro exemplo: Ambos os valores são equivalentes, para que a função devolve `true`.
* Segundo exemplo: Ambos os valores não são equivalentes, para que a função devolve `false`.

<a name="first"></a>

### <a name="first"></a>primeiro

Devolva o primeiro item a partir de uma cadeia ou matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o primeiro item |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Qualquer | O primeiro item na coleção |
||||

*Exemplo*

Estes exemplos encontrar o primeiro item estas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E retornar esses resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converta uma versão de cadeia de caracteres para um número de vírgula flutuante para um número real de ponto flutuante.
Pode utilizar esta função apenas quando passar parâmetros personalizados para uma aplicação, por exemplo, uma aplicação lógica.

```
float('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia de caracteres que tem um número de vírgula flutuante válido para converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*float-value*> | Flutuante | O número de vírgula flutuante para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão de cadeia de caracteres para este número de vírgula flutuante:

```
float('10.333')
```

E devolve o resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Devolve um carimbo no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | O carimbo de hora atualizado no formato especificado |
||||

*Exemplo*

Neste exemplo converte um carimbo para o formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E devolve o resultado: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retornar uma matriz com valores que correspondem ao nome da chave numa ação *dados de formulário* ou *formulário codificado* saída.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | A ação cuja saída tem o valor da chave que pretende |
| <*key*> | Sim | String | O nome da chave de valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Uma matriz com todos os valores que correspondem a chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz de valor da chave "Assunto" em dados do formulário ou a saída de formulário codificado a ação especificada:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E retorna o texto do assunto numa matriz, por exemplo: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Devolver um valor único que corresponde ao nome da chave numa ação *dados de formulário* ou *formulário codificado* saída.
Se a função encontrar mais do que uma correspondência, a função emite um erro.

```
formDataValue('<actionName>', '<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | A ação cuja saída tem o valor da chave que pretende |
| <*key*> | Sim | String | O nome da chave de valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*key-value*> | String | O valor na chave especificada  |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres do valor da chave "Assunto" em dados do formulário ou a saída de formulário codificado a ação especificada:

```
formDataValue('Send_an_email', 'Subject')
```

E retorna o texto do assunto como uma cadeia, por exemplo: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Devolva o timestamp atual, bem como as unidades de tempo especificado.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificada para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo para utilizar com o *intervalo*: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês", "Ano" |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora atual e o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o timestamp atual é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias para esse timestamp:

```
getFutureTime(5, 'Day')
```

E devolve o resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o timestamp atual é "2018-03-01T00:00:00.0000000Z".
Este exemplo adiciona cinco dias e converte o resultado em formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E devolve o resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Devolva o carimbo de hora atual menos as unidades de tempo especificado.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificada para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo para utilizar com o *intervalo*: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês", "Ano" |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora atual menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o timestamp atual é "2018-02-01T00:00:00.0000000Z".
Neste exemplo subtrai cinco dias a partir desse timestamp:

```
getPastTime(5, 'Day')
```

E devolve o resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o timestamp atual é "2018-02-01T00:00:00.0000000Z".
Este exemplo subtrai cinco dias e converte o resultado em formato "D":

```
getPastTime(5, 'Day', 'D')
```

E devolve o resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>maior

Verifique se o primeiro valor é maior do que o segundo valor.
Devolve verdadeiro quando o primeiro valor é mais ou retornar false quando menos.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres | O primeiro valor para verificar se maior que o segundo valor |
| <*compareTo*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres, respetivamente | O valor de comparação |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando o primeiro valor é maior do que o segundo valor. Retornar falso quando o primeiro valor é igual ou menor que o segundo valor. |
||||

*Exemplo*

Estes exemplos, verifique se o primeiro valor é maior do que o segundo valor:

```
greater(10, 5)
greater('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifique se o primeiro valor é maior que ou igual ao segundo valor.
Devolve verdadeiro quando o primeiro valor é maior ou igual ou retornar falso quando o primeiro valor é menor.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres | O primeiro valor para verificar se o maior que ou igual ao segundo valor |
| <*compareTo*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres, respetivamente | O valor de comparação |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando o primeiro valor é maior que ou igual ao segundo valor. Retornar falso quando o primeiro valor é menor do que o segundo valor. |
||||

*Exemplo*

Estes exemplos, verifique se o primeiro valor é maior ou igual que o segundo valor:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Gere um identificador exclusivo global (GUID) como uma cadeia, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, pode especificar um formato diferente para o GUID que não seja o formato de padrão, "D", que é 32 dígitos separados por hífens.

```
guid('<format>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | Uma única [especificador de formato](https://msdn.microsoft.com/library/97af8hh4) para o GUID devolvido. Por predefinição, o formato é "D", mas pode usar "N", "D", "B", "P" ou "X". |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Este exemplo gera o mesmo GUID, mas como 32 dígitos, separados por hífenes e entre parênteses:

```
guid('P')
```

E devolve o resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>IF

Verifique se uma expressão é verdadeira ou falsa.
Com base no resultado, devolva um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Booleano | A expressão para verificar |
| <*valueIfTrue*> | Sim | Qualquer | O valor a devolver quando a expressão for verdadeira |
| <*valueIfFalse*> | Sim | Qualquer | O valor a devolver quando a expressão é falsa |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Qualquer | O valor especificado, que retorna com base em se a expressão é true ou false |
||||

*Exemplo*

Este exemplo retorna `"yes"` porque a expressão especificada retorna true.
Caso contrário, o exemplo retorna `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Devolve a posição inicial ou o valor de índice para uma subcadeia.
Esta função não diferencia maiúsculas de minúsculas e índices começam com o número de 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tenha a subcadeia a localizar |
| <*searchText*> | Sim | String | A subcadeia a localizar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*index-value*>| Número inteiro | O valor de índice ou a posição inicial para a subcadeia especificada. <p>Se a cadeia de caracteres não for encontrada, devolva o número -1. |
||||

*Exemplo*

Neste exemplo localiza o valor de índice inicial para a subcadeia do "mundo" na cadeia de caracteres "hello world":

```
indexOf('hello world', 'world')
```

E devolve o resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Devolva a versão de número inteiro para uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*integer-result*> | Número inteiro | A versão de número inteiro para a cadeia especificada |
||||

*Exemplo*

Este exemplo cria uma versão de número inteiro para a cadeia de caracteres "10":

```
int('10')
```

E devolve o resultado: `10`

<a name="item"></a>

### <a name="item"></a>item

Quando utilizado dentro de uma ação de repetição numa matriz, retornar o item atual na matriz durante a iteração da ação de atual.
Também pode obter os valores de propriedades desse item.

```
item()
```

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Qualquer | O item atual da matriz para iteração atual da ação |
||||

*Exemplo*

Neste exemplo obtém o `body` elemento da mensagem atual para a ação de "Send_an_email" dentro de iteração atual de cada ciclo:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>itens

Devolva o item atual de cada ciclo num loop para cada.
Utilize esta função dentro de cada ciclo.

```
items('<loopName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sim | String | O nome de cada ciclo |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*item*> | Qualquer | O item do ciclo de atual no loop para cada especificado |
||||

*Exemplo*

Neste exemplo obtém o item atual a partir de cada ciclo especificado:

```
items('myForEachLoopName')
```

<a name="json"></a>

### <a name="json"></a>json

Devolve o valor de tipo JavaScript Object Notation (JSON) ou o objeto para uma cadeia ou XML.

```
json('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Cadeia de caracteres ou XML | A cadeia ou o XML a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Tipo JSON nativo ou objeto | O valor de tipo nativo de JSON ou o objeto para a cadeia especificada ou XML. Se a cadeia de caracteres for nula, a função devolve um objeto vazio. |
||||

*Exemplo 1*

Neste exemplo converte essa cadeia de caracteres para o valor JSON:

```
json('[1, 2, 3]')
```

E devolve o resultado: `[1, 2, 3]`

*Exemplo 2*

Neste exemplo converte essa cadeia de caracteres JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E devolve o resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 3*

Neste exemplo converte esse XML para JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E devolve o resultado:

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

Retornar uma coleção que tenha *apenas* os itens comuns em coleções especificadas.
A aparecer no resultado, um item tem de aparecer em todas as coleções transmitidas para esta função.
Se um ou mais itens têm o mesmo nome, o último item com esse nome aparece no resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, <*coleção2*>,... | Sim | Matriz ou objeto, mas não ambos | As coleções do qual *apenas* os itens comuns |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*common-items*> | Matriz ou um objeto, respetivamente | Uma coleção que tenha apenas os itens comuns em coleções especificadas |
||||

*Exemplo*

Neste exemplo localiza os itens comuns entre essas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E retorna uma matriz com *apenas* estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>aderir

Devolver uma cadeia de caracteres que tem todos os itens de uma matriz tem cada caractere separada por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Array | A matriz que tenha os itens para aderir |
| <*delimiter*> | Sim | String | O separador que aparece entre cada caractere na cadeia resultante |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | A cadeia resultante criada a partir de todos os itens na matriz especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres de todos os itens nessa matriz pelo caractere especificado como o delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E devolve o resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>última

Devolva o último item a partir de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres ou matriz | A coleção onde encontrar o último item |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Cadeia ou matriz, respetivamente | O último item da coleção |
||||

*Exemplo*

Estes exemplos encontrar o último item estas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E retorna esses resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Devolve a posição inicial ou o valor de índice para a última ocorrência de uma subcadeia.
Esta função não diferencia maiúsculas de minúsculas e índices começam com o número de 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tenha a subcadeia a localizar |
| <*searchText*> | Sim | String | A subcadeia a localizar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Número inteiro | O valor de índice ou a posição inicial para a última ocorrência da subcadeia especificada. <p>Se a cadeia de caracteres não for encontrada, devolva o número -1. |
||||

*Exemplo*

Neste exemplo localiza o valor de índice inicial para a última ocorrência da subcadeia do "mundo" na cadeia de caracteres "hello world":

```
lastIndexOf('hello world', 'world')
```

E devolve o resultado: `6`

<a name="length"></a>

### <a name="length"></a>Comprimento

Devolva o número de itens numa coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres ou matriz | A coleção com os itens para contar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Número inteiro | O número de itens na coleção |
||||

*Exemplo*

Estes exemplos contam o número de itens nestas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E retornar esse resultado: `4`

<a name="less"></a>

### <a name="less"></a>menos

Verifique se o primeiro valor é menor que o segundo valor.
Devolve verdadeiro quando o primeiro valor é menor ou retornar falso quando o primeiro valor é mais.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres | O primeiro valor para verificar se menor do que o segundo valor |
| <*compareTo*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres, respetivamente | O item de comparação |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando o primeiro valor é menor do que o segundo valor. Retornar falso quando o primeiro valor é igual ou maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos Verifique se o primeiro valor é menor que o segundo valor.

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
Devolve verdadeiro quando o primeiro valor é menor ou igual ou retornar falso quando o primeiro valor é mais.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres | O primeiro valor para verificar se menor que ou igual ao segundo valor |
| <*compareTo*> | Sim | Número inteiro, vírgula flutuante ou de cadeia de caracteres, respetivamente | O item de comparação |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO  | Booleano | Devolve verdadeiro quando o primeiro valor é menor ou igual ao segundo valor. Retornar falso quando o primeiro valor é maior do que o segundo valor. |
||||

*Exemplo*

Estes exemplos, verifique se o primeiro valor é menor ou igual que o segundo valor.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E retornar esses resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Devolva o "URL de retorno de chamada" que chama um acionador ou ação.
Esta função só funciona com acionadores e ações para o **HttpWebhook** e **ApiConnectionWebhook** tipos de conector, mas não o **Manual**,  **Periodicidade**, **HTTP**, e **APIConnection** tipos.

```
listCallbackUrl()
```

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | O URL de retorno de chamada para um acionador ou ação |
||||

*Exemplo*

Este exemplo mostra um URL de retorno de chamada de exemplo que essa função pode retornar:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>máx.

Devolva o valor mais alto de uma lista ou uma matriz com números que é, inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Número inteiro, vírgula flutuante ou ambos | O conjunto de números a partir do qual pretende que o valor mais alto |
| [<*number1*>, <*number2*>, ...] | Sim | Matriz - número inteiro, vírgula flutuante ou ambos | A matriz de números a partir do qual pretende que o valor mais alto |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*max-value*> | Número inteiro ou flutuante | O valor mais alto na matriz especificada ou conjunto de números |
||||

*Exemplo*

Estes exemplos obtém o valor mais alto do conjunto de números e a matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E retornar esse resultado: `3`

<a name="min"></a>

### <a name="min"></a>min.

Devolva o valor mais baixo de um conjunto de números ou uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Número inteiro, vírgula flutuante ou ambos | O conjunto de números a partir do qual pretende que o valor mais baixo |
| [<*number1*>, <*number2*>, ...] | Sim | Matriz - número inteiro, vírgula flutuante ou ambos | A matriz de números a partir do qual pretende que o valor mais baixo |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*min-value*> | Número inteiro ou flutuante | O valor mais baixo no conjunto especificado de números ou matriz especificada |
||||

*Exemplo*

Estes exemplos obtém o valor mais baixo no conjunto de números e a matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E retornar esse resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Devolva o resto da divisão de dois números.
Para obter o resultado de número inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Número inteiro ou flutuante | O número pelo qual dividir o *divisor* |
| <*divisor*> | Sim | Número inteiro ou flutuante | O número que divida o *dividendo*, mas não pode ser 0. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Número inteiro ou flutuante | O resto da divisão o primeiro número, o segundo número |
||||

*Exemplo*

Neste exemplo divide o primeiro número, o segundo número:

```
mod(3, 2)
```

E retornar esse resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Devolva o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Número inteiro ou flutuante | O número a multiplicar por *multiplicand2* |
| <*multiplicand2*> | Sim | Número inteiro ou flutuante | O número que múltiplos *multiplicand1* |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*product-result*> | Número inteiro ou flutuante | O produto da multiplicação o primeiro número, o segundo número |
||||

*Exemplo*

Estes exemplos vários o primeiro número, o segundo número:

```
mul(1, 2)
mul(1.5, 2)
```

E retornar esses resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Devolve o corpo de uma parte específica na saída de uma ação que tem várias partes.

```
multipartBody('<actionName>', <index>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | String | O nome para a ação que tem de saída com várias partes |
| <*index*> | Sim | Número inteiro | O valor de índice para a parte que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*body*> | String | O corpo da parte especificada |
||||

<a name="not"></a>

### <a name="not"></a>não

Verifique se uma expressão é falsa.
Devolve verdadeiro quando a expressão é falsa ou retornar falso quando verdadeiro.

```
not(<expression>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Booleano | A expressão para verificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando a expressão é falsa. Retornar falso quando a expressão for verdadeira. |
||||

*Exemplo 1*

Estes exemplos, verifique se as expressões especificadas são Falso:

```
not(false)
not(true)
```

E retornar esses resultados:

* Primeiro exemplo: A expressão é falsa, para que a função devolve `true`.
* Segundo exemplo: A expressão for verdadeira, portanto, a função devolve `false`.

*Exemplo 2*

Estes exemplos, verifique se as expressões especificadas são Falso:

```
not(equals(1, 2))
not(equals(1, 1))
```

E retornar esses resultados:

* Primeiro exemplo: A expressão é falsa, para que a função devolve `true`.
* Segundo exemplo: A expressão for verdadeira, portanto, a função devolve `false`.

<a name="or"></a>

### <a name="or"></a>ou

Verifique se, pelo menos, uma expressão é verdadeira.
Devolve verdadeiro quando pelo menos uma expressão é verdadeira ou retornar false quando todas forem falsos.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Expression1*>, <*expression2*>,... | Sim | Booleano | As expressões para verificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO | Booleano | Devolve verdadeiro quando pelo menos uma expressão é verdadeira. Retorne false quando todas as expressões forem falsos. |
||||

*Exemplo 1*

Estes exemplos, verifique se, pelo menos, uma expressão é verdadeira:

```
or(true, false)
or(false, false)
```

E retornar esses resultados:

* Primeiro exemplo: Pelo menos uma expressão for verdadeira, para que a função devolve `true`.
* Segundo exemplo: Ambas as expressões forem falsos, para que a função devolve `false`.

*Exemplo 2*

Estes exemplos, verifique se, pelo menos, uma expressão é verdadeira:

```
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E retornar esses resultados:

* Primeiro exemplo: Pelo menos uma expressão for verdadeira, para que a função devolve `true`.
* Segundo exemplo: Ambas as expressões forem falsos, para que a função devolve `false`.

<a name="parameters"></a>

### <a name="parameters"></a>parâmetros

Devolva o valor para um parâmetro que é descrito na sua definição da aplicação lógica.

```
parameters('<parameterName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Sim | String | O nome para o parâmetro de valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Qualquer | O valor para o parâmetro especificado |
||||

*Exemplo*

Suponha que tenha este valor JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Neste exemplo obtém o valor para o parâmetro especificado:

```
parameters('fullName')
```

E devolve o resultado: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Devolva um número inteiro aleatório de um intervalo especificado, o que é, inclusive apenas no final de partida.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Número inteiro | O menor número inteiro no intervalo |
| <*maxValue*> | Sim | Número inteiro | O número inteiro que se segue do maior número inteiro no intervalo de que a função pode retornar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*random-result*> | Número inteiro | O número de inteiro aleatório devolvido do intervalo especificado |
||||

*Exemplo*

Neste exemplo obtém um número inteiro aleatório no intervalo especificado, excluindo o valor máximo de:

```
rand(1, 5)
```

E devolve um desses números como resultado: `1`, `2`, `3`, ou `4`

<a name="range"></a>

### <a name="range"></a>Intervalo

Devolva uma matriz de inteiros que começa a partir de um número inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Número inteiro | Um valor inteiro que é iniciada a matriz, como o primeiro item |
| <*count*> | Sim | Número inteiro | O número de números inteiros na matriz |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | A matriz com números inteiros a partir do índice especificado |
||||

*Exemplo*

Este exemplo cria uma matriz de inteiros que começa a partir do índice especificado e tem o número especificado de números inteiros:

```
range(1, 4)
```

E devolve o resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substituir uma subcadeia com a cadeia especificada e retornar a cadeia de caracteres do resultado. Esta função diferencia maiúsculas de minúsculas.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tenha a subcadeia substituir |
| <*oldText*> | Sim | String | A subcadeia substituir |
| <*newText*> | Sim | String | A cadeia de substituição |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | A cadeia de caracteres atualizada depois de substituir a subcadeia <p>Se não for encontrada a subcadeia, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Neste exemplo encontra a subcadeia "antiga" no "old string" e substitui "antigo" com "new":

```
replace('the old string', 'old', 'new')
```

E devolve o resultado: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Remover uma propriedade de um objeto e retornar do objeto atualizado.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Object | O objeto JSON a partir do qual pretende remover uma propriedade |
| <*property*> | Sim | String | O nome de propriedade a remover |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | O objeto JSON atualizado sem a propriedade especificada |
||||

*Exemplo*

Neste exemplo remove a `"accountLocation"` propriedade de um `"customerProfile"` objeto, que é convertido em JSON com o [JSON()](#json) funcionar e retorna o objeto atualizado:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Defina o valor de propriedade de um objeto e retornar do objeto atualizado.
Para adicionar uma nova propriedade, pode usar esta função ou o [addProperty()](#addProperty) função.

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Object | O objeto JSON cuja propriedade pretende definir |
| <*property*> | Sim | String | O nome da propriedade nova ou existente definir |
| <*Valor*> | Sim | Qualquer | O valor a definir para a propriedade especificada |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | O objeto JSON atualizado cuja propriedade definir |
||||

*Exemplo*

Este exemplo define a `"accountNumber"` propriedade num `"customerProfile"` objeto, que é convertido em JSON com o [JSON()](#json) função.
A função atribui um valor gerado pelo [guid()](#guid) funcionar e retorna o objeto JSON atualizado:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>ignorar

Remover itens da frente de uma coleção e devolver *todas as outras* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Array | A coleção cujos itens que pretende remover |
| <*count*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens a remover à frente |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | A coleção atualizada depois de remover os itens especificados |
||||

*Exemplo*

Neste exemplo remove um item, o número de 0, da frente da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E retornará essa matriz com os itens restantes: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>dividir

Devolva uma matriz que contém subcadeias de caracteres, separadas por vírgulas, com base no caráter delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres para separar em subcadeias de carateres com base no delimitador especificado na cadeia de caracteres original |
| <*delimiter*> | Sim | String | O caractere da cadeia original para utilizar como o delimitador |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Uma matriz que contém subcadeias de carateres da cadeia de caracteres original, separados por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcadeias de carateres da com base no caráter especificado como o delimitador de cadeia especificada:

```
split('a_b_c', '_')
```

E retornará essa matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Devolva o início do dia para um carimbo.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O período de tempo especificado, mas começando na marca de hora zero para o dia |
||||

*Exemplo*

Neste exemplo localiza o início do dia para este timestamp:

```
startOfDay('2018-03-15T13:30:30Z')
```

E devolve o resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Devolva o início da hora para um carimbo.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O período de tempo especificado, mas começando na marca de zero minutos para a hora |
||||

*Exemplo*

Neste exemplo localiza o início da hora para este timestamp:

```
startOfHour('2018-03-15T13:30:30Z')
```

E devolve o resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Devolva o início do mês para um carimbo.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O período de tempo especificado, mas começando no primeiro dia do mês na marca de hora zero |
||||

*Exemplo*

Neste exemplo devolve o início do mês para este timestamp:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E devolve o resultado: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verificar se uma cadeia de caracteres começa com uma subcadeia específica.
Devolve verdadeiro quando a subcadeia for encontrada, ou retornar falso quando não encontrado.
Esta função não diferencia maiúsculas de minúsculas.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres para verificar |
| <*searchText*> | Sim | String | A cadeia de caracteres de partida para localizar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| VERDADEIRO ou FALSO  | Booleano | Devolve verdadeiro quando a subcadeia inicial é encontrada. Retornar falso quando não encontrado. |
||||

*Exemplo 1*

Este exemplo verifica se a cadeia "Olá mundo" começa com a subcadeia "Olá":

```
startsWith('hello world', 'hello')
```

E devolve o resultado: `true`

*Exemplo 2*

Este exemplo verifica se a cadeia "Olá mundo" começa com a subcadeia "saudações":

```
startsWith('hello world', 'greetings')
```

E devolve o resultado: `false`

<a name="string"></a>

### <a name="string"></a>string

Devolva a versão de cadeia de caracteres para um valor.

```
string(<value>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | Qualquer | O valor a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*string-value*> | String | A versão de cadeia de caracteres para o valor especificado |
||||

*Exemplo 1*

Este exemplo cria a versão de cadeia de caracteres para este número:

```
string(10)
```

E devolve o resultado: `"10"`

*Exemplo 2*

Este exemplo cria uma cadeia de caracteres para o objeto JSON especificado e utiliza o caráter de barra invertida (\\) como um caráter de escape para o double-aspas (").

```
string( { "name": "Sophie Owen" } )
```

E devolve o resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Devolva o resultado da subtração o segundo número do primeiro número.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Número inteiro ou flutuante | O número a partir do qual subtrair o *subtraendo* |
| <*subtrahend*> | Sim | Número inteiro ou flutuante | O número a subtrair do *minuendo* |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*result*> | Número inteiro ou flutuante | O resultado da subtração o segundo número do primeiro número dos |
||||

*Exemplo*

Neste exemplo subtrai o segundo número do primeiro número:

```
sub(10.3, .3)
```

E devolve o resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>subcadeia

Devolva carateres de uma cadeia de caracteres, a partir da posição especificada, ou o índice.
Início de valores de índice com o número de 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia cujos carateres que pretende |
| <*startIndex*> | Sim | Número inteiro | Um número positivo igual ou superior a 0 que pretende utilizar como o valor de índice ou a posição inicial |
| <*length*> | Sim | Número inteiro | Um número positivo de carateres que pretende na subcadeia |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | Uma subcadeia com o número especificado de carateres, a partir da posição de índice especificado na cadeia de origem |
||||

*Exemplo*

Este exemplo cria uma subcadeia de cinco caracteres da cadeia especificada, a partir do valor de índice 6:

```
substring('hello world', 6, 5)
```

E devolve o resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrai um número de unidades de tempo de um carimbo.
Consulte também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia que contém o carimbo de hora |
| <*interval*> | Sim | Número inteiro | O número de unidades de tempo especificada para subtrair |
| <*timeUnit*> | Sim | String | A unidade de tempo para utilizar com o *intervalo*: "Segunda", "Minuto", "Hour", "Day", "Week", "Mês", "Ano" |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de hora menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Neste exemplo subtrai um dia deste timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E devolve o resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Neste exemplo subtrai um dia deste timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E retorna esse resultado com o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>tirar

Itens de retorno da frente de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Coleção*> | Sim | Cadeia de caracteres ou matriz | A coleção cujos itens que pretende |
| <*count*> | Sim | Número inteiro | Um número inteiro positivo para o número de itens que pretende da frente |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*subconjunto*> ou [<*subconjunto*>] | Cadeia ou matriz, respetivamente | Uma cadeia ou matriz com o número especificado de itens obtidas a partir de frente da coleção original |
||||

*Exemplo*

Estes exemplos obtém o número especificado de itens da frente destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E retornar esses resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>tiques

Devolver o `ticks` valor da propriedade para um carimbo especificado.
R *escala* é um intervalo de 100 nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres para um carimbo |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Número inteiro | O número de tiques desde o período de tempo especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retorne uma cadeia de caracteres no formato em minúsculas. Se um caráter na cadeia não tiver uma versão em minúsculas, esse caractere permanece inalterado na cadeia devolvida.

```
toLower('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia a devolver no formato em minúsculas |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | A cadeia de caracteres original no formato em minúsculas |
||||

*Exemplo*

Neste exemplo converte essa cadeia de caracteres em minúsculas:

```
toLower('Hello World')
```

E devolve o resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retorne uma cadeia de caracteres no formato em maiúsculas. Se um caráter na cadeia não tiver uma versão em maiúsculas, esse caractere permanece inalterado na cadeia devolvida.

```
toUpper('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia a devolver no formato em maiúsculas |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | A cadeia de caracteres original no formato em maiúsculas |
||||

*Exemplo*

Neste exemplo converte essa cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E devolve o resultado: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Acionador

Devolva o resultado de um acionador no tempo de execução ou valores de outros pares de nome e valor JSON, que pode atribuir a uma expressão.

* Dentro das entradas de um acionador, esta função devolve o resultado da execução anterior.

* Dentro da condição de um acionador, esta função devolve o resultado da execução atual.

Por predefinição, a função referencia o objeto acionador inteiro, mas, opcionalmente, pode especificar uma propriedade cujo valor que pretende.
Além disso, esta função tem versões de forma abreviada disponíveis, consulte [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody).

```
trigger()
```

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | A saída a partir de um acionador no tempo de execução |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Devolver um acionador `body` de saída em tempo de execução.
Um atalho para `trigger().outputs.body`.
Ver [trigger()](#trigger).

```
triggerBody()
```

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | O `body` saída a partir do acionador |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retornar uma matriz com valores que correspondem ao nome da chave num acionador *dados de formulário* ou *formulário codificado* saída.

```
triggerFormDataMultiValues('<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sim | String | O nome da chave de valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Uma matriz com todos os valores que correspondem a chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz do valor da chave "feedUrl" nos dados de formulário ou a saída de formulário codificado de um acionador RSS:

```
triggerFormDataMultiValues('feedUrl')
```

E retornará essa matriz como um resultado de exemplo: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retornar uma cadeia de caracteres com um valor único que corresponde ao nome da chave num acionador *dados de formulário* ou *formulário codificado* saída.
Se a função encontrar mais do que uma correspondência, a função emite um erro.

```
triggerFormDataValue('<key>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sim | String | O nome da chave de valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*key-value*> | String | O valor na chave especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres do valor de chave de "feedUrl" nos dados de formulário ou a saída de formulário codificado de um acionador RSS:

```
triggerFormDataValue('feedUrl')
```

E retorna essa cadeia de caracteres como um resultado de exemplo: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Devolve o corpo de uma parte específica na saída de um acionador que tem várias partes.

```
triggerMultipartBody(<index>)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*index*> | Sim | Número inteiro | O valor de índice para a parte que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*body*> | String | O corpo para a parte especificada na saída de várias partes de um acionador |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Devolva o resultado de um acionador no tempo de execução ou valores de outros pares de nome e valor JSON.
Um atalho para `trigger().outputs`.
Ver [trigger()](#trigger).

```
triggerOutputs()
```

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | A saída a partir de um acionador no tempo de execução  |
||||

<a name="trim"></a>

### <a name="trim"></a>Cortar

Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tenha o espaço em branco de esquerda e à direita para remover |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | Uma versão atualizada para a cadeia de caracteres original sem espaço em branco à esquerda nem à direita |
||||

*Exemplo*

Neste exemplo remove o espaço em branco à esquerda e à direita da cadeia de caracteres "Hello World":

```
trim(' Hello World  ')
```

E devolve o resultado: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>União

Retornar uma coleção que tenha *todos os* os itens de coleções especificadas.
A aparecer no resultado, um item pode aparecer em qualquer coleção transmitida para esta função. Se um ou mais itens têm o mesmo nome, o último item com esse nome aparece no resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*coleção1*>, <*coleção2*>,...  | Sim | Matriz ou objeto, mas não ambos | As coleções do qual *todos os* os itens |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Matriz ou um objeto, respetivamente | Uma coleção com todos os itens de coleções especificadas - não contém duplicados |
||||

*Exemplo*

Neste exemplo obtém *todos os* os itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E devolve o resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Devolve uma versão de identifier (URI) com codificação de recurso uniforme para uma cadeia de caracteres ao substituir carateres não seguros de URL com carateres de escape.
Utilize esta função vez [encodeUriComponent()](#encodeUriComponent).
Embora ambas as funções funcionam da mesma maneira, `uriComponent()` é preferencial.

```
uriComponent('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia a converter para formato codificado por URI |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | A cadeia codificada de URI com caracteres de escape |
||||

*Exemplo*

Este exemplo cria uma versão codificada de URI para essa cadeia de caracteres:

```
uriComponent('https://contoso.com')
```

E devolve o resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Devolva a versão binária de um componente do uniform resource identifier (URI).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia codificada de URI a converter |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | A versão binária para a cadeia codificada de URI. O conteúdo do binário é codificada em base64 e representado pelo `$content`. |
||||

*Exemplo*

Este exemplo cria a versão binária para essa cadeia de caracteres codificada do URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E devolve o resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Devolva a versão de cadeia de caracteres de um identificador de recurso uniforme (URI) codificado em cadeia de caracteres, com eficiência descodificar a cadeia codificada de URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia codificada de URI a descodificar |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A versão decodificada para a cadeia codificada de URI |
||||

*Exemplo*

Este exemplo cria a versão de cadeia descodificada para essa cadeia de caracteres codificada do URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E devolve o resultado: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Devolver o `host` valor para um identificador de recurso uniforme (URI).

```
uriHost('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `host` valor desejado |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*host-value*> | String | O `host` valor para o URI especificado |
||||

*Exemplo*

Neste exemplo localiza o `host` valor para este URI:

```
uriHost('https://www.localhost.com:8080')
```

E devolve o resultado: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Devolver o `path` valor para um identificador de recurso uniforme (URI).

```
uriPath('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `path` valor desejado |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*path-value*> | String | O `path` valor para o URI especificado. Se `path` não ter um valor, devolve o caráter "/". |
||||

*Exemplo*

Neste exemplo localiza o `path` valor para este URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve o resultado: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Devolver o `path` e `query` valores para um identificador de recurso uniforme (URI).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `path` e `query` valores pretendidos |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | O `path` e `query` valores para o URI especificado. Se `path` não especificar um valor, devolve o caráter "/". |
||||

*Exemplo*

Neste exemplo localiza os `path` e `query` valores para este URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve o resultado: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Devolver o `port` valor para um identificador de recurso uniforme (URI).

```
uriPort('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `port` valor desejado |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*port-value*> | Número inteiro | O `port` valor para o URI especificado. Se `port` não especificar um valor, devolve a porta predefinida para o protocolo. |
||||

*Exemplo*

Este exemplo retorna o `port` valor para este URI:

```
uriPort('http://www.localhost:8080')
```

E devolve o resultado: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Devolver o `query` valor para um identificador de recurso uniforme (URI).

```
uriQuery('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `query` valor desejado |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*query-value*> | String | O `query` valor para o URI especificado |
||||

*Exemplo*

Este exemplo retorna o `query` valor para este URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve o resultado: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Devolver o `scheme` valor para um identificador de recurso uniforme (URI).

```
uriScheme('<uri>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | String | O URI cujos `scheme` valor desejado |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | O `scheme` valor para o URI especificado |
||||

*Exemplo*

Este exemplo retorna o `scheme` valor para este URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E devolve o resultado: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Devolva o timestamp atual.

```
utcNow('<format>')
```

Opcionalmente, pode especificar um formato diferente com a <*formato*> parâmetro.


| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | De qualquer um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou uma [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). É o formato padrão para o período de tempo ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (AAAA-MM-ddTHH:mm:ss:fffffffK), que está em conformidade com [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e preserva as informações de fuso horário. |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | A data e hora atuais |
||||

*Exemplo 1*

Suponha que hoje é dia 15 de Abril de 2018 em 1: das 12:00:00.
Neste exemplo obtém o timestamp atual:

```
utcNow()
```

E devolve o resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é dia 15 de Abril de 2018 em 1: das 12:00:00.
Neste exemplo obtém o timestamp atual usando o formato "D" opcional:

```
utcNow('D')
```

E devolve o resultado: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Variáveis

Devolva o valor para uma variável especificada.

```
variables('<variableName>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Sim | String | O nome para a variável valor que pretende |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*variable-value*> | Qualquer | O valor para a variável especificada |
||||

*Exemplo*

Suponha que o valor atual para uma variável de "numItems" é 20.
Neste exemplo obtém o valor de número inteiro para esta variável:

```
variables('numItems')
```

E devolve o resultado: `20`

<a name="workflow"></a>

### <a name="workflow"></a>fluxo de trabalho

Devolva todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução.

```
workflow().<property>
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*property*> | Não | String | O nome para a propriedade de fluxo de trabalho valor que pretende <p>Um objeto de fluxo de trabalho tem estas propriedades: **name**, **tipo**, **id**, **localização**, e **executar**. O **execute** valor da propriedade também é um objeto com estas propriedades: **nome**, **tipo**, e **id**. |
|||||

*Exemplo*

Neste exemplo devolve o nome para a execução de um fluxo de trabalho atual:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Devolva a versão XML para uma cadeia que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*Valor*> | Sim | String | A cadeia de caracteres com o objeto JSON para converter <p>O objeto JSON tem de ter propriedade de apenas uma raiz, que não pode ser uma matriz. <br>Utilizar o caráter de barra invertida (\\) como um caráter de escape aspas ("). |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | O XML codificado para a cadeia especificada ou o objeto JSON |
||||

*Exemplo 1*

Este exemplo cria a versão XML para essa cadeia de caracteres, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E devolve o resultado XML:

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

Este exemplo cria o XML para uma cadeia que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E devolve o resultado XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verificar o XML de nós ou valores que correspondem a uma expressão XPath (linguagem de caminho de XML) e devolver os valores ou nós correspondentes. Uma expressão XPath, ou apenas "XPath", ajuda-o a percorrer uma estrutura de documento XML para que possa selecionar nós ou valores de computação no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Necessário | Tipo | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia de caracteres XML para procurar nós ou valores que correspondem a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para encontrar valores ou nós XML correspondentes |
|||||

| Valor de retorno | Type | Descrição |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Um nó XML quando apenas um único nó corresponde a expressão XPath especificada |
| <*Valor*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-ou- </br>[<*value1*>, <*value2*>, ...] | Array | Uma matriz connosco XML ou valores que correspondem à expressão XPath especificada |
||||

*Exemplo 1*

Neste exemplo encontra nós que correspondem a `<name></name>` nó os argumentos especificados e retorna uma matriz com esses valores de nó:

`xpath(xml(parameters('items')), '/produce/item/name')`

Seguem-se os argumentos:

* A cadeia de "items", que contém este XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  O exemplo utiliza a [parameters()](#parameters) funcionar para obter a cadeia de caracteres XML no argumento de "items", mas tem também de converter a cadeia de formato XML, utilizando o [xml()](#xml) função.

* Esta expressão XPath, que é transmitido como uma cadeia de caracteres:

  `"/produce/item/name"`

Segue-se a matriz de resultado com os nós que correspondem ao `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemplo 2*

A seguir no exemplo 1, neste exemplo encontra-se nós que correspondem a `<count></count>` nó e adiciona esses valores de nó com o `sum()` função:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E devolve o resultado: `30`

*Exemplo 3*

Neste exemplo, ambas as expressões de localizar nós que correspondem a `<location></location>` nó, os argumentos especificados, que incluem o XML com um espaço de nomes. As expressões de utilizam o caráter de barra invertida (\\) como um caráter de escape aspas (").

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]')`

Seguem-se os argumentos:

* Este XML, que inclui o espaço de nomes de documento XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* A expressão aqui de XPath:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name=()=\"file\"] and namespace-uri()=\"http://contoso.com\"/*[local-name()]=\"location\" and namespace-uri()=\"\"]`

Aqui está o nó de resultado que corresponda a `<location></location` nó:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 4*

A seguir no exemplo 3, neste exemplo encontra-se o valor no `<location></location>` nó:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E devolve o resultado: `"Paris"`

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
