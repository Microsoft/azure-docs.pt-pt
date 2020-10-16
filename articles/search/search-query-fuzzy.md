---
title: Pesquisa difusa
titleSuffix: Azure Cognitive Search
description: Implemente uma experiência de pesquisa "quer dizer" para corrigir automaticamente um termo ou erro escrito.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: faa98f1c52cfe2dd0e19f085f4d33dedb6f01851
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934893"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Pesquisa fuzzy para corrigir erros ortográficos e erros de tipografia

A Azure Cognitive Search suporta a pesquisa fuzzy, um tipo de consulta que compensa os erros de tipografia e termos mal escritos na cadeia de entrada. Fá-lo digitalizando termos com uma composição semelhante. A pesquisa de expansão para cobrir quase-fósforos tem o efeito de corrigir automaticamente um erro quando a discrepância é apenas alguns caracteres deslocados. 

## <a name="what-is-fuzzy-search"></a>O que é procura duvidosa?

É um exercício de expansão que produz uma correspondência em termos com uma composição semelhante. Quando uma pesquisa difusa é especificada, o motor constrói um gráfico (baseado na [teoria determinista finita autótona](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) de termos igualmente compostos, para todos os termos na consulta. Por exemplo, se a sua consulta inclui três termos "Universidade de Washington", é criado um gráfico para cada termo na consulta `search=university~ of~ washington~` (não há remoção de palavras de stop na pesquisa fuzzy, então "of" obtém um gráfico).

O gráfico consiste em até 50 expansões, ou permutações, de cada termo, capturando variantes corretas e incorretas no processo. Em seguida, o motor devolve os jogos mais importantes na resposta. 

Para um termo como "universidade", o gráfico pode ter "inversidade, universidade, universidade, universo, inverso". Quaisquer documentos que correspondam aos do gráfico estão incluídos nos resultados. Em contraste com outras consultas que analisam o texto para lidar com diferentes formas da mesma palavra ("ratos" e "rato"), as comparações numa consulta difusa são tomadas pelo valor facial sem qualquer análise linguística no texto. "Universo" e "inverso", que são semânticamente diferentes, vão corresponder porque as discrepâncias sintáticas são pequenas.

Uma correspondência tem sucesso se as discrepâncias forem limitadas a duas ou menos edições, quando uma edição é um personagem inserido, eliminado, substituído ou transposto. O algoritmo de correção de cordas que especifica o diferencial é a métrica de [distância Damerau-Levenshtein,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) descrita como o "número mínimo de operações (inserções, supressões, substituições ou transposições de dois caracteres adjacentes) necessárias para alterar uma palavra na outra". 

Em Azure Cognitive Search:

+ A consulta fuzzy aplica-se a termos inteiros, mas você pode apoiar frases através de construções E. Por exemplo, "Unviersty~ de~ "Wshington~" corresponderia à "Universidade de Washington".

+ A distância padrão de uma edição é 2. Um valor de `~0` significa não expansão (apenas o termo exato é considerado um fósforo), mas você pode especificar `~1` para um grau de diferença, ou uma edição. 

+ Uma consulta difusa pode expandir um termo até 50 permutações adicionais. Este limite não é configurável, mas pode reduzir efetivamente o número de expansões diminuindo a distância de edição para 1.

+ As respostas consistem em documentos que contenham uma correspondência relevante (até 50).

Coletivamente, os gráficos são apresentados como critérios de correspondência com fichas no índice. Como pode imaginar, a procura difusa é inerentemente mais lenta do que outras formas de consulta. O tamanho e complexidade do seu índice podem determinar se os benefícios são suficientes para compensar a latência da resposta.

> [!NOTE]
> Como a pesquisa difusa tende a ser lenta, pode valer a pena investigar alternativas como a indexação n-gram, com a sua progressão de sequências de caracteres curtos (sequências de dois e três caracteres para tokens de bigram e trigramas). Dependendo da sua língua e superfície de consulta, n-gram pode dar-lhe um melhor desempenho. O trade off é que a indexação n-gram é muito intensiva de armazenamento e gera índices muito maiores.
>
> Outra alternativa, que poderia considerar se quisesse lidar com apenas os casos mais flagrantes, seria um [mapa de sinónimos.](search-synonyms.md) Por exemplo, mapear "search" para "serach, serch, sarch" ou "recuperar" para "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexação para pesquisa difusa

Os analisadores não são usados durante o processamento de consultas para criar um gráfico de expansão, mas isso não significa que os analisadores devam ser ignorados em cenários de pesquisa confusos. Afinal, os analisadores são usados durante a indexação para criar fichas contra as quais a correspondência é feita, se a consulta é de forma livre, pesquisa filtrada ou uma pesquisa difusa com um gráfico como entrada. 

Geralmente, ao atribuir analisadores por campo, a decisão de afinar a cadeia de análise baseia-se no caso de utilização primária (um filtro ou pesquisa completa de texto) em vez de formas de consulta especializadas como pesquisa duvidosa. Por esta razão, não existe uma recomendação específica de analisador para pesquisa duvidosa. 

No entanto, se as consultas de teste não estiverem a produzir os fósforos que espera, poderá tentar variar o analisador de indexação, definindo-o para um [analisador de idiomas](index-add-language-analyzers.md), para ver se obtém melhores resultados. Algumas línguas, particularmente as que têm mutações vogais, podem beneficiar da inflexão e formas de palavras irregulares geradas pelos processadores de linguagem natural da Microsoft. Em alguns casos, a utilização do analisador de linguagem certo pode fazer a diferença se um termo é tokenizado de uma forma compatível com o valor fornecido pelo utilizador.

## <a name="how-to-use-fuzzy-search"></a>Como usar a pesquisa difusa

As consultas fuzzy são construídas usando a sintaxe de consulta lucene completa, invocando o [parser de consulta lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Coloque o parser Lucene completo na consulta `queryType=full` ().

1. Opcionalmente, coloque o pedido em campos específicos, utilizando este parâmetro `searchFields=<field1,field2>` (). 

1. Aped o operador de `~` azulejos no final de todo o período `search=<string>~` ().

   Inclua um parâmetro opcional, um número entre 0 e 2 (predefinição), se quiser especificar a distância de edição `~1` (). Por exemplo, "blue~" ou "blue~1" devolveria "azul", "blues" e "cola".

Na Pesquisa Cognitiva Azure, além do termo e distância (máximo de 2), não existem parâmetros adicionais para definir na consulta.

> [!NOTE]
> Durante o processamento de consultas, as consultas fuzzy não são submetidas a [análises lexicais](search-lucene-query-architecture.md#stage-2-lexical-analysis). A entrada de consulta é adicionada diretamente à árvore de consulta e expandida para criar um gráfico de termos. A única transformação realizada é o invólucro mais baixo.

## <a name="testing-fuzzy-search"></a>Testar pesquisa duvidosa

Para testes simples, recomendamos [pesquisar explorador](search-explorer.md) ou [carteiro](search-get-started-postman.md) para iterar sobre uma expressão de consulta. Ambas as ferramentas são interativas, o que significa que você pode passar rapidamente através de várias variantes de um termo e avaliar as respostas que voltam.

Quando os resultados são ambíguos, [o destaque de sucesso](search-pagination-page-layout.md#hit-highlighting) pode ajudá-lo a identificar a partida na resposta. 

> [!Note]
> O uso de destaques de sucesso para identificar fósforos difusos tem limitações e só funciona para pesquisa básica e difusa. Se o seu índice tiver perfis de pontuação, ou se colocar a consulta com sintaxe adicional, o destaque de impacto pode não conseguir identificar a correspondência. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exemplo 1: pesquisa confusa com o termo exato

Assuma que o seguinte fio existe num `"Description"` campo num documento de pesquisa: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Comece com uma pesquisa difusa em "especial" e adicione o ponto de destaque ao campo Descrição:

```console
search=special~&highlight=Description
```

Na resposta, porque adicionou o destaque do sucesso, a formatação é aplicada a "especial" como termo correspondente.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Tente novamente o pedido, soletrando mal "especial" ao retirar várias letras ("pe"):

```console
search=scial~&highlight=Description
```

Até agora, nenhuma alteração na resposta. Utilizando o padrão de distância de 2 graus, remover dois caracteres "pe" de "especial" ainda permite uma correspondência bem sucedida nesse termo.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Tentando mais um pedido, modificar ainda mais o termo de pesquisa, retirando um último carácter para um total de três supressões (de "especial" a "escala"):

```console
search=scal~&highlight=Description
```

Note que a mesma resposta é devolvida, mas agora em vez de combinar em "especial", o jogo fuzzy está em "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

O objetivo deste exemplo alargado é ilustrar a clareza que o destaque pode trazer a resultados ambíguos. Em todos os casos, o mesmo documento é devolvido. Se tivesse confiado em documentos de identificação para verificar uma correspondência, poderia ter perdido a mudança de "especial" para "SQL".

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa completa de texto na Azure Cognitive Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](./search-get-started-dotnet.md)
+ [Como consultar em REST](./search-get-started-powershell.md)