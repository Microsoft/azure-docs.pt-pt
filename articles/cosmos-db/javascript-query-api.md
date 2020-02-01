---
title: Trabalho com javaScript consulta integrada API em Procedimentos e Gatilhos Azure Cosmos DB Armazenados
description: Este artigo apresenta os conceitos da API de consulta integrada à linguagem JavaScript para criar procedimentos armazenados e gatilhos no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901832"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>API de consulta JavaScript no Azure Cosmos DB

Além de emitir consultas utilizando o SQL API em Azure Cosmos DB, o [SDK](https://azure.github.io/azure-cosmosdb-js-server/) do lado do servidor Cosmos DB fornece uma interface JavaScript para realizar consultas otimizadas em Procedimentos e Gatilhos Da Cosmos DB Stored. Você não precisa estar ciente da linguagem SQL para usar essa interface JavaScript. A API de consulta JavaScript permite que você crie consultas programaticamente passando funções de predicado em sequência de chamadas de função, com uma sintaxe familiar a ECMAScript5'ss internas de matriz e bibliotecas JavaScript populares como Lodash. As consultas são analisadas pelo tempo de execução do JavaScript e executadas com eficiência usando índices Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Funções JavaScript com suporte

| **Função** | **Descrição** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Inicia uma chamada em cadeia que tem de ser terminada com Value ().|
|`filter(predicateFunction [, options] [, callback])`|Filtra a entrada usando uma função de predicado que retorna verdadeiro/falso para filtrar documentos entrados entrada/saída para o conjunto resultante. Esta função tem um comportamento semelhante a uma cláusula WHERE em SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e mescla matrizes de cada item de entrada em uma única matriz. Esta função tem um comportamento semelhante para SelectMany no LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplica-se uma projeção dada uma função de transformação que mapeia cada item de entrada para um valor ou o objeto JavaScript. Esta função tem um comportamento semelhante a uma cláusula SELECT em SQL.|
|`pluck([propertyName] [, options] [, callback])`|Esta função é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.|
|`sortBy([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo do documento de entrada em ordem crescente usando o predicado fornecido. Esta função tem um comportamento semelhante a uma cláusula ORDER BY em SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando os documentos no fluxo do documento de entrada em ordem decrescente usando o predicado fornecido. Esta função tem um comportamento semelhante a uma cláusula ORDER BY x DESC em SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Executa uma autojunção com matriz interna e adiciona resultados de ambos os lados como tuplas à projeção de resultado. Por exemplo, juntar-se a um documento de pessoa com pessoa.animais de estimação produziria tuples [pessoa, animal de estimação]. Isso é semelhante ao SelectMany no LINK do .NET.|

Quando incluídos no interior do predicado de e/ou o Seletor de funções, as construções de JavaScript seguintes serão automaticamente otimizadas para ser executado diretamente no Azure Cosmos DB índices:

- Operadores simples: `=` `+` `>>>!` `>>` `<<` `||` `>=` `<=` `>` `<` `!===` `!=` `==` `&` `%` `*` `-`  `/`  `|` `^`    `===` `&&`       `~`
- Literais, incluindo o literal de objeto: {}
- var, retorno

As seguintes construções de JavaScript não serão otimizadas para índices do Azure Cosmos DB:

- Controlar o fluxo (por exemplo, se, para, embora)
- Chamadas de função

Para obter mais informações, consulte a [documentação de JavaScript do lado do servidor Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Roteiro de SQL para JavaScript

A tabela seguinte apresenta várias consultas SQL e as consultas de JavaScript correspondentes. Assim como acontece com as consultas SQL, as propriedades (por exemplo, item.id) diferenciam maiúsculas de minúsculas.

> [!NOTE]
> `__` (double-underscore) é um pseudónimo para `getContext().getCollection()` ao utilizar a API de consulta JavaScript.

|**SQL**|**API de consulta JavaScript**|**Descrição**|
|---|---|---|
|SELECIONE *<br>ATRAVÉS da documentação| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;devolver o documento;<br>});|Resultados em todos os documentos (paginados com o token de continuação), como é.|
|SELECIONAR <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message como MSG,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>ATRAVÉS da documentação|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|O id, a mensagem (um alias para msg) e a ação a partir de todos os documentos de projetos.|
|SELECIONE *<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Consultas para documentos com o predicado: id = "X998_Y998".|
|SELECIONE *<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Consultas para documentos que têm uma propriedade de etiquetas e marcas é uma matriz que contém o valor ' 123.|
|SELECIONAR<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message como msg<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Consultas para documentos com um predicado, id = "X998_Y998" e, em seguida, projeta o id e a mensagem (um alias para msg).|
|Etiqueta de SELECT VALUE<br>ATRAVÉS da documentação<br>Junte-se docs IN de marca. Etiquetas<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolva o documento. As etiquetas & & Array.isArray (documento. Etiquetas);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtros para documentos que tem uma propriedade de matriz, etiquetas e ordena os documentos resultantes pela propriedade de sistema TS timestamp e, em seguida, projetos + nivela a matriz de etiquetas.|

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre conceitos e como escrever e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

- [Como escrever procedimentos armazenados e gatilhos usando a API de consulta JavaScript](how-to-write-javascript-query-api.md)
- [Trabalhando com Azure Cosmos DB procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md)
- [Como usar procedimentos armazenados, gatilhos, funções definidas pelo usuário no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Referência da API do Cosmos DB JavaScript do lado do servidor do Azure](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
