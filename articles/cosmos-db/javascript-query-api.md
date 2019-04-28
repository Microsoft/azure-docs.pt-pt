---
title: Trabalhar com JavaScript consulta integrada à linguagem API no Azure Cosmos DB
description: Este artigo apresenta os conceitos de JavaScript consulta integrada à linguagem API criar procedimentos armazenados e acionadores no Azure Cosmos DB.
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 12/08/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043243"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Consulta de JavaScript API no Azure Cosmos DB

Para além de emitir consultas com a API de SQL no Azure Cosmos DB, o [SDK do lado do servidor do Cosmos DB](https://azure.github.io/azure-cosmosdb-js-server/) permite-lhe efetuar consultas otimizadas através de uma interface de JavaScript. Não tem de conhecer a linguagem SQL para usar essa interface de JavaScript. A consulta de JavaScript que API permite-lhe criar programaticamente consultas ao transmitir as funções de predicado em seqüência da função chama, com uma sintaxe familiar do ECMAScript5 built-ins de matriz e bibliotecas JavaScript populares, como Lodash. Consultas são analisadas pelo tempo de execução de JavaScript e executadas com eficiência usando os índices do Azure Cosmos DB.

## <a name="supported-javascript-functions"></a>Funções de JavaScript suportadas

| **Função** | **Descrição** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Inicia uma chamada em cadeia que tem de ser terminada com Value ().|
|`filter(predicateFunction [, options] [, callback])`|Filtra a entrada usando uma função de predicado que retorna verdadeiro/falso para filtrar documentos entrados entrada/saída para o conjunto resultante. Esta função tem um comportamento semelhante a uma cláusula WHERE em SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combina e nivela as matrizes de cada item de entrada numa única matriz. Esta função tem um comportamento semelhante para SelectMany no LINQ.|
|`map(transformationFunction [, options] [, callback])`|Aplica-se uma projeção dada uma função de transformação que mapeia cada item de entrada para um valor ou o objeto JavaScript. Esta função tem um comportamento semelhante a uma cláusula SELECT em SQL.|
|`pluck([propertyName] [, options] [, callback])`|Esta função é um atalho para um mapa que extrai o valor de uma única propriedade de cada item de entrada.|
|`sortBy([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando-os documentos no fluxo de documento de entrada em ordem crescente, utilizando o predicado especificado. Esta função tem um comportamento semelhante a uma cláusula ORDER BY em SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produz um novo conjunto de documentos classificando-os documentos no fluxo de documento de entrada por ordem descendente utilizando o predicado especificado. Esta função tem um comportamento semelhante a uma cláusula ORDER BY x DESC em SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Executa uma associação automática com uma matriz interna e adiciona os resultados de ambos os lados como cadeias de identificação para a projeção de resultado. Por exemplo, associar um documento de pessoa com person.pets produziria tuplas [pessoa, pet]. Isso é semelhante ao SelectMany na ligação do .NET.|

Quando incluídos no interior do predicado de e/ou o Seletor de funções, as construções de JavaScript seguintes serão automaticamente otimizadas para ser executado diretamente no Azure Cosmos DB índices:

- Operadores simples: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literais, incluindo o literal de objeto: {}
- var, retorno

As seguintes construções de JavaScript não serão otimizadas para índices do Azure Cosmos DB:

- Controlar o fluxo (por exemplo, se, para, embora)
- Chamadas de função

Para obter mais informações, consulte a [documentação de JavaScript do lado do Cosmos DB Server](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL para JavaScript e dicas

A tabela seguinte apresenta várias consultas SQL e as consultas de JavaScript correspondentes. Tal como acontece com consultas SQL, as propriedades (por exemplo, item.id) diferenciam maiúsculas de minúsculas.

> [!NOTE]
> `__` (valor de duplo sublinhado) é um alias para `getContext().getCollection()` ao utilizar a API de consulta do JavaScript.

|**SQL**|**Consulta de JavaScript API**|**Descrição**|
|---|---|---|
|SELECIONE *<br>ATRAVÉS da documentação| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;devolver o documento;<br>});|Resultados em todos os documentos (paginados com o token de continuação), como é.|
|SELECIONAR <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Docs.Message como msg,<br>&nbsp;&nbsp;&nbsp;Docs.Actions <br>ATRAVÉS da documentação|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|O id, a mensagem (um alias para msg) e a ação a partir de todos os documentos de projetos.|
|SELECIONE *<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Consultas para documentos com o predicado: id = "X998_Y998".|
|SELECIONE *<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;devolver x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Consultas para documentos que têm uma propriedade de etiquetas e marcas é uma matriz que contém o valor ' 123.|
|SELECIONAR<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;Docs.Message como msg<br>ATRAVÉS da documentação<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Consultas para documentos com um predicado, id = "X998_Y998" e, em seguida, projeta o id e a mensagem (um alias para msg).|
|Etiqueta de SELECT VALUE<br>ATRAVÉS da documentação<br>Junte-se docs IN de marca. Etiquetas<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolva o documento. As etiquetas & & Array.isArray (documento. Etiquetas);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;devolver doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtros para documentos que tem uma propriedade de matriz, etiquetas e ordena os documentos resultantes pela propriedade de sistema TS timestamp e, em seguida, projetos + nivela a matriz de etiquetas.|

## <a name="next-steps"></a>Passos Seguintes

Saber mais, conceitos e procedimento escrita e utilize os procedimentos armazenados, acionadores e funções definidas pelo utilizador no Azure Cosmos DB:

- [Como escrever procedimentos armazenados e acionadores com a API de consulta de Javascript](how-to-write-javascript-query-api.md)
- [Trabalhar com o Azure Cosmos DB procedimentos armazenados, acionadores e funções definidas pelo utilizador](stored-procedures-triggers-udfs.md)
- [Como utilizar os procedimentos armazenados, disparadores, funções definidas pelo utilizador no Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Referência da API do Cosmos DB JavaScript do lado do servidor do Azure](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

<!-- Update_Description: update meta properties -->