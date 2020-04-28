---
title: Trabalho com javaScript consulta integrada API em Procedimentos e Gatilhos Azure Cosmos DB Armazenados
description: Este artigo introduz os conceitos de API de consulta integrada em língua JavaScript para criar procedimentos e gatilhos armazenados em Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76901832"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript consulta API em Azure Cosmos DB

Além de emitir consultas utilizando o SQL API em Azure Cosmos DB, o [SDK](https://azure.github.io/azure-cosmosdb-js-server/) do lado do servidor Cosmos DB fornece uma interface JavaScript para realizar consultas otimizadas em Procedimentos e Gatilhos Da Cosmos DB Stored. Não é preciso estar atento à linguagem SQL para utilizar esta interface JavaScript. A Consulta JavaScript API permite-lhe construir consultas programáticas, passando funções predicadas em sequência de chamadas de função, com uma sintaxe familiar à matriz incorporada do ECMAScript5 e bibliotecas javaScript populares como Lodash. As consultas são analisadas pelo tempo de execução do JavaScript e executadas de forma eficiente usando índices De DB Do Azure Cosmos.

## <a name="supported-javascript-functions"></a>Funções JavaScript suportadas

| **Função** | **Descrição** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Inicia uma chamada acorrentada que deve ser terminada com valor().|
|`filter(predicateFunction [, options] [, callback])`|Filtra a entrada utilizando uma função predicada que devolve documentos verdadeiros/falsos para filtrar os documentos de entrada/saída no conjunto resultante. Esta função comporta-se semelhante a uma cláusula WHERE no SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e achata as matrizes de cada item de entrada numa única matriz. Esta função comporta-se semelhante à SelectMany no LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplica uma projeção dada uma função de transformação que mapeia cada item de entrada para um objeto ou valor JavaScript. Esta função comporta-se semelhante a uma cláusula SELECT no SQL.|
|`pluck([propertyName] [, options] [, callback])`|Esta função é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.|
|`sortBy([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos, separando os documentos no fluxo de documentos de entrada por ordem ascendente, utilizando o predicado dado. Esta função comporta-se semelhante a uma cláusula ORDER BY no SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos, separando os documentos no fluxo de documentos de entrada por ordem descendente utilizando o predicado dado. Esta função comporta-se semelhante a uma cláusula ORDER BY x DESC no SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Realiza uma auto-união com a matriz interna e adiciona resultados de ambos os lados como tuples à projeção do resultado. Por exemplo, juntar-se a um documento de pessoa com pessoa.animais de estimação produziria tuples [pessoa, animal de estimação]. Isto é semelhante ao SelectMany em .NET LINK.|

Quando incluídas dentro das funções predicadas e/ou seletoras, as seguintes construções JavaScript são automaticamente otimizadas para funcionar diretamente nos índices De Db do Azure Cosmos:

- Operadores `=` `+` `-` `*` `/` `%` simples: `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literals, incluindo o objeto literal:{}
- var, retorno

As seguintes construções JavaScript não são otimizadas para índices De DB Do Azure Cosmos:

- Fluxo de controlo (por exemplo, se, durante, enquanto)
- Chamadas de função

Para mais informações, consulte a [Documentação JavaScript do Lado](https://azure.github.io/azure-cosmosdb-js-server/)do Servidor DB Cosmos .

## <a name="sql-to-javascript-cheat-sheet"></a>Folha de batota SQL para JavaScript

A tabela seguinte apresenta várias consultas SQL e as correspondentes consultas JavaScript. Tal como acontece com as consultas SQL, as propriedades (por exemplo, item.id) são sensíveis aos casos.

> [!NOTE]
> `__`(sublinhado duplo) é um `getContext().getCollection()` pseudónimo para quando se utiliza a API de consulta JavaScript.

|**SQL**|**JavaScript Consulta API**|**Descrição**|
|---|---|---|
|SELECIONE *<br>De docs| __.mapa (função(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;devolução do c;<br>});|Resulta em todos os documentos (paginados com token de continuação) como é.|
|SELECIONAR <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.ações <br>De docs|__.mapa (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolução {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ações:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projeta o id, mensagem (pseudónimo a msg) e ação de todos os documentos.|
|SELECIONE *<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolução doc.id =="X998_Y998";<br>});|Consultas para documentos com o predicado: id = "X998_Y998".|
|SELECIONE *<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs). Etiquetas, 123)|__.filter (função(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retorno x.Tags && x.Tags.index(123) > -1;<br>});|Consultas para documentos que têm uma propriedade Tags e Tags é uma matriz que contém o valor 123.|
|SELECIONAR<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.mensagem AS msg<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolução doc.id =="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.mapa (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolução {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.valor();|Consultas para documentos com predicado, id = "X998_Y998", e depois projeta o id e a mensagem (pseudónimo a msg).|
|Etiqueta DE VALOR SELECIONADO<br>De docs<br>Junte-se à etiqueta NOS médicos. Etiquetas<br>ORDEM POR docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver doc. Tags && Array.isArray (doc. Etiquetas);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy (função (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolução do c._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.valor()|Filtros para documentos que tenham uma propriedade de matriz, Tags, e classifica os documentos resultantes pela propriedade do sistema de carimbo sustem o tempo _ts, e depois projetos + achata a matriz de Tags.|

## <a name="next-steps"></a>Passos seguintes

Saiba mais conceitos e como escrever e utilizar procedimentos, gatilhos e funções definidas pelo utilizador no Azure Cosmos DB:

- [Como escrever procedimentos e gatilhos armazenados usando a API de Consulta Javascript](how-to-write-javascript-query-api.md)
- [Trabalhar com procedimentos, gatilhos e funções definidas pela Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Como utilizar procedimentos armazenados, gatilhos, funções definidas pelo utilizador em Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Referência a API do lado do servidor Do Lado do Servidor Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
