---
title: Sintaxe de consulta de pesquisa do Graph
titleSuffix: Azure Machine Learning
description: Aprenda a usar a sintaxe de consulta de pesquisa para procurar nós em gráfico de pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 166b87159b5ac2434c3ce7a7c66529a4b61ee565
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290505"
---
# <a name="graph-search-query-syntax"></a>Sintaxe de consulta de pesquisa do Graph

Neste artigo, você aprende sobre a sintaxe de pesquisa de gráficos em Azure Machine Learning. O recurso de pesquisa de gráficos permite-lhe procurar um nó pelo seu nome e propriedades. 

 ![Screenshot animado mostrando uma experiência de pesquisa de gráfico de exemplo](media/search/graph-search.gif)

A pesquisa por gráficos suporta a pesquisa de palavras-chave de texto completo no nome do nó e nos comentários. Também pode filtrar na propriedade do nó como runStatus, duração, computeTarget. A pesquisa de palavras-chave baseia-se na consulta de Lucene. Uma consulta completa de pesquisa é assim:  

**[consulta lucene ] [consulta de filtro]** 

Pode utilizar consulta de Lucene ou consulta de filtro. Para utilizar ambos, utilize o **|** separador. A sintaxe da consulta do filtro é mais rigorosa do que a consulta de Lucene. Assim, se a entrada do cliente puder ser analisada como ambas, a consulta do filtro será aplicada.

 

## <a name="lucene-query"></a>Consulta de Lucene

A pesquisa de gráficos utiliza a consulta simples de Lucene como sintaxe de pesquisa de texto completo no nó "nome" e "comentário". São apoiados os seguintes operadores lucene:

 
- E/OU
- Wildcard combinando **com?** e **\*** operadores.

### <a name="examples"></a>Exemplos

- Pesquisa simples: `JSON Data`

- E/OU: `JSON AND Validation`

- Múltiplos E/OU: `(JSON AND Validation) OR (TSV AND Training)`

 
- Correspondência wildcard: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Não se pode começar uma consulta de Lucene com um caráter "*".

##  <a name="filter-query"></a>Consulta de filtro

 
As consultas de filtro utilizam o seguinte padrão:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Pode utilizar as seguintes propriedades de nó como chaves:

- runStatus
- computação
- duration
- reutilizar

E utilizar os seguintes operadores:

- Maior ou igual: `>=`
- Menos ou igual: `<=`
- Maior: `>`
- Menos: `<`
- Igual: `==`
- Conter: `=`
- NotEqual: `!=`
- Em: `in`

 
 

### <a name="example"></a>Exemplo

- duração > 100;
- estado em { Falhado,NãoStarted}
- computação em {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>Notas técnicas

- A relação entre vários filtros é "E"
- Se `>=,  >,  <, or  <=` forem escolhidos, os valores serão automaticamente convertidos para o tipo de número. Caso contrário, os tipos de cordas são usados para comparação.
- Para todos os valores do tipo de corda, o caso é insensível em comparação.
- Operador "In" espera uma coleção como valor, sintaxe de coleção é `{name1, name2, name3}`
- O espaço será ignorado entre palavras-chave