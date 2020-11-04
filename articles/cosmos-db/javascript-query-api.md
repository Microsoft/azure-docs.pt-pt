---
title: Trabalhar com a API de consulta integrada JavaScript em Procedimentos e Gatilhos armazenados Azure Cosmos
description: Este artigo introduz os conceitos de consulta integrada em linguagem JavaScript API para criar procedimentos e gatilhos armazenados em Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340979"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Consulta JavaScript API em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Além de emitir consultas utilizando o SQL API em Azure Cosmos DB, o [Cosmos DB server-side SDK](https://github.com/Azure/azure-cosmosdb-js-server/) fornece uma interface JavaScript para realizar consultas otimizadas em Procedimentos e Gatilhos Armazenados cosmos. Não é preciso estar atento ao idioma SQL para utilizar esta interface JavaScript. A consulta JavaScript API permite-lhe construir programáticamente consultas, passando funções predicados em sequência de chamadas de função, com uma sintaxe familiar à matriz incorporada do ECMAScript5 e às populares bibliotecas JavaScript como Lodash. As consultas são analisadas pelo tempo de execução javaScript e executadas de forma eficiente usando índices DB Azure Cosmos.

## <a name="supported-javascript-functions"></a>Funções JavaScript suportadas

| **Função** | **Descrição** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Inicia uma chamada acorrentada que deve ser terminada com valor().|
|`filter(predicateFunction [, options] [, callback])`|Filtra a entrada utilizando uma função predicado que devolve verdadeiro/falso para filtrar os documentos de entrada dentro/fora no conjunto resultante. Esta função comporta-se com um comportamento semelhante a uma cláusula WHERE em SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e achata as matrizes de cada item de entrada numa única matriz. Esta função comporta-se semelhante à SelectMany em LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplica uma projeção dada uma função de transformação que mapeia cada item de entrada para um objeto ou valor JavaScript. Esta função comporta-se com um comportamento semelhante a uma cláusula SELECT em SQL.|
|`pluck([propertyName] [, options] [, callback])`|Esta função é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.|
|`sortBy([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo de documentos de entrada por ordem ascendente, utilizando o predicado dado. Esta função comporta-se com um comportamento semelhante a uma cláusula ORDER BY em SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo de documentos de entrada em ordem descendente usando o predicado dado. Esta função comporta-se semelhante a uma cláusula ORDER BY x DESC em SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Executa uma união auto-união com a matriz interna e adiciona resultados de ambos os lados como tuples à projeção do resultado. Por exemplo, juntar um documento de pessoa com pessoa.animais de estimação produziria tuples [pessoa, animal de estimação]. Isto é semelhante ao SelectMany em .NET LINK.|

Quando incluídos dentro das funções predicados e/ou seletores, as seguintes construções JavaScript ficam automaticamente otimizadas para funcionar diretamente nos índices DB do Azure Cosmos:

- Operadores `=` `+` `-` `*` `/` `%` `|` simples: `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literal, incluindo o objeto literal: {}
- var, retorno

As seguintes construções JavaScript não são otimizadas para índices DB Azure Cosmos:

- Fluxo de controlo (por exemplo, se, por enquanto)
- Chamadas de função

Para obter mais informações, consulte a [documentação JavaScript side do cosmos DB Server.](https://github.com/Azure/azure-cosmosdb-js-server/)

## <a name="sql-to-javascript-cheat-sheet"></a>Folha de batota SQL para JavaScript

A tabela seguinte apresenta várias consultas SQL e as consultas javaScript correspondentes. Tal como acontece com as consultas SQL, as propriedades (por exemplo, item.id) são sensíveis a casos.

> [!NOTE]
> `__` (duplo-sublinhado) é um pseudónimo para `getContext().getCollection()` quando se utiliza a API de consulta JavaScript.

|**SQL**|**Consulta JavaScript API**|**Descrição**|
|---|---|---|
|SELECIONE *<br>De docs| __.map(função(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;devolução doc;<br>});|Resulta em todos os documentos (paginados com token de continuação) como está.|
|SELECIONAR <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS MSG,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>De docs|__.map(função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retorno {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ações:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projeta o id, mensagem (aliased to MSG), e ação de todos os documentos.|
|SELECIONE *<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;doc.id de regresso ==="X998_Y998";<br>});|Consultas para documentos com o predicado: id = "X998_Y998".|
|SELECIONE *<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs). Etiquetas, 123)|__.filter(função(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retorno x.Tags && x.Tags.indexOf(123) > -1;<br>});|Consultas para documentos que tenham uma propriedade tags e Tags é uma matriz contendo o valor 123.|
|SELECIONAR<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>De docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc.id de regresso ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retorno {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Consultas para documentos com predicado, id = "X998_Y998", e depois projeta o id e a mensagem (aliased to msg).|
|ETIQUETA DE VALOR SELECIONADO<br>De docs<br>Junte a etiqueta em docs. Etiquetas<br>ENCOMENDA POR DOCS._TS|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolução doc. Tags && Array.isArray (doc. Etiquetas);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy (função(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc._ts de regresso;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtros para documentos que tenham uma propriedade de matriz, Tags, e classifica os documentos resultantes pela propriedade do sistema de marcação de tempo _ts, e depois projetos + achata a matriz tags.|

## <a name="next-steps"></a>Passos seguintes

Saiba mais conceitos e como escrever e utilizar procedimentos armazenados, gatilhos e funções definidas pelo utilizador em Azure Cosmos DB:

- [Como escrever procedimentos e gatilhos armazenados usando a API de Consulta Javascript](how-to-write-javascript-query-api.md)
- [Trabalhar com a Azure Cosmos DB armazena procedimentos, gatilhos e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md)
- [Como utilizar procedimentos armazenados, gatilhos, funções definidas pelo utilizador em Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript referência API do lado do servidor](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
