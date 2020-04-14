---
title: Pesquisa difusa
titleSuffix: Azure Cognitive Search
description: Implemente uma experiência de pesquisa "quer dizer" para corrigir automaticamente um termo ou tipografia mal escrito.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262439"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Procura difusa para corrigir erros ortográficos e tipografias

A Pesquisa Cognitiva Azure suporta a pesquisa fuzzy, um tipo de consulta que compensa os tipografias e os termos mal escritos na cadeia de entrada. Fá-lo digitalizando termos com uma composição semelhante. Expandir a procura para cobrir quase-fósforos tem o efeito de corrigir automaticamente um erro quando a discrepância é apenas alguns caracteres deslocados. 

## <a name="what-is-fuzzy-search"></a>O que é procura confusa?

É um exercício de expansão que produz uma correspondência em termos de composição semelhante. Quando uma pesquisa difusa é especificada, o motor constrói um gráfico (baseado na [teoria de autómato finita determinista)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)de termos igualmente compostos, para todos os termos na consulta. Por exemplo, se a sua consulta inclui três termos "Universidade de Washington", `search=university~ of~ washington~` é criado um gráfico para cada período na consulta (não há remoção de palavras-stop na pesquisa difusa, por isso "de" recebe um gráfico).

O gráfico consiste em até 50 expansões, ou permutações, de cada termo, capturando variantes corretas e incorretas no processo. Em seguida, o motor devolve os mais altos jogos relevantes na resposta. 

Para um termo como "universidade", o gráfico pode ter "desversty, universty, universidade, universo, inverso". Quaisquer documentos que correspondam aos do gráfico estão incluídos nos resultados. Em contraste com outras consultas que analisam o texto para lidar com diferentes formas da mesma palavra ("ratos" e "rato"), as comparações numa consulta difusa são tomadas pelo valor facial sem qualquer análise linguística do texto. "Universo" e "inverso", que são semânticamente diferentes, vão coincidir porque as discrepâncias sintáticas são pequenas.

Um jogo tem sucesso se as discrepâncias estiverem limitadas a duas ou menos edificações, quando uma edição é um personagem inserido, apagado, substituído ou transposto. O algoritmo de correção de cordas que especifica o diferencial é a métrica [da distância Damerau-Levenshtein,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) descrita como o "número mínimo de operações (inserções, supressões, substituições ou transposições de dois caracteres adjacentes) necessárias para alterar uma palavra para a outra". 

Em Pesquisa Cognitiva Azure:

+ A consulta fuzzy aplica-se a termos inteiros, mas você pode apoiar frases através de e construções. Por exemplo, "Unviersty~ de ~"Wshington~" corresponderia à "Universidade de Washington".

+ A distância padrão de uma edição é 2. Um valor `~0` de não significa nenhuma expansão (apenas o termo `~1` exato é considerado compatível), mas você poderia especificar para um grau de diferença, ou uma edição. 

+ Uma consulta fuzzy pode expandir um termo até 50 permutações adicionais. Este limite não é configurável, mas pode reduzir efetivamente o número de expansões diminuindo a distância de edição para 1.

+ As respostas consistem em documentos que contenham uma correspondência relevante (até 50).

Colectivamente, os gráficos são apresentados como critérios de correspondência contra fichas no índice. Como pode imaginar, a procura confusa é inerentemente mais lenta do que outras formas de consulta. O tamanho e complexidade do seu índice podem determinar se os benefícios são suficientes para compensar a latência da resposta.

> [!NOTE]
> Como a pesquisa fuzzy tende a ser lenta, pode valer a pena investigar alternativas como a indexação n-gram, com a sua progressão de sequências de caracteres curtos (duas e três sequências de caracteres para bigram e fichas de trigrama). Dependendo da sua linguagem e superfície de consulta, n-gram pode dar-lhe um melhor desempenho. A troca é que o índice n-gram é muito intensivo de armazenamento e gera índices muito maiores.
>
> Outra alternativa, que se poderia considerar se quiser lidar com os casos mais flagrantes, seria um [mapa sinónimo.](search-synonyms.md) Por exemplo, mapear "pesquisa" para "serach, serch, sarch" ou "recuperar" para "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexação para pesquisa difusa

Os analisadores não são usados durante o processamento de consultas para criar um gráfico de expansão, mas isso não significa que os analisadores devem ser ignorados em cenários de pesquisa difusos. Afinal, os analisadores são usados durante o indexante para criar fichas contra as quais a correspondência é feita, se a consulta é forma livre, pesquisa filtrada ou uma pesquisa difusa com um gráfico como entrada. 

Geralmente, ao atribuir analisadores por campo, a decisão de afinar a cadeia de análise baseia-se no caso de utilização primária (um filtro ou pesquisa completa de texto) em vez de formas de consulta especializadas como pesquisa difusa. Por esta razão, não existe uma recomendação específica do analisador para a pesquisa confusa. 

No entanto, se as consultas de teste não produzirem os fósforos que espera, poderá tentar variar o analisador de indexação, definindo-o num analisador de [linguagem,](index-add-language-analyzers.md)para ver se obtém melhores resultados. Algumas línguas, particularmente as que sofrem de mutações vogais, podem beneficiar da inflexão e formas de palavras irregulares geradas pelos processadores de linguagem natural da Microsoft. Em alguns casos, a utilização do analisador de idiomas certo pode fazer a diferença se um termo é tokenizado de uma forma compatível com o valor fornecido pelo utilizador.

## <a name="how-to-use-fuzzy-search"></a>Como usar a pesquisa fuzzy

As consultas fuzzy são construídas usando a sintaxe de consulta lucene completa, invocando o [parser lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)consulta .

1. Coloque o parser Lucene completo`queryType=full`na consulta .

1. Opcionalmente, remeter o pedido para campos`searchFields=<field1,field2>`específicos, utilizando este parâmetro ( ). 

1. Anexar o operador`~`de tilde no final`search=<string>~`de todo o período ( ).

   Inclua um parâmetro opcional, um número entre 0 e 2 (predefinição), se quiser especificar a distância de edição ().`~1` Por exemplo, "blue~" ou "blue~1" devolveriam "azul", "blues" e "cola".

Na Pesquisa Cognitiva Azure, além do termo e distância (máximo de 2), não existem parâmetros adicionais para definir na consulta.

> [!NOTE]
> Durante o processamento de consultas, as consultas difusas não são submetidas a [análises lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis). A entrada de consulta é adicionada diretamente à árvore de consulta e expandida para criar um gráfico de termos. A única transformação realizada é o invólucro mais baixo.

## <a name="testing-fuzzy-search"></a>Testar pesquisa difusa

Para testes simples, recomendamos [o explorador](search-explorer.md) de pesquisa ou [carteiro](search-get-started-postman.md) para iterar sobre uma expressão de consulta. Ambas as ferramentas são interativas, o que significa que você pode rapidamente passar por várias variantes de um termo e avaliar as respostas que voltam.

Quando os resultados são ambíguos, [o destaque](search-pagination-page-layout.md#hit-highlighting) do hit pode ajudá-lo a identificar o jogo na resposta. 

> [!Note]
> O uso de destaque sinuoso para identificar fósforos confusos tem limitações e só funciona para pesquisa básica. Se o seu índice tiver perfis de pontuação, ou se colocar a consulta com sintaxe adicional, o destaque do hit pode não identificar a correspondência. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exemplo 1: pesquisa difusa com o termo exato

Assuma que a `"Description"` seguinte corda existe num campo num documento de pesquisa:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Comece com uma pesquisa fuzzy em "special" e adicione o destaque do hit ao campo Descrição:

    search=special~&highlight=Description

Na resposta, porque adicionou o destaque do sucesso, a formatação é aplicada a "especial" como o termo correspondente.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Tente novamente o pedido, ortografando mal "especial", tirando várias letras ("pe"):

    search=scial~&highlight=Description

Até agora, não há alterações na resposta. Utilizando o padrão de 2 graus de distância, remover dois caracteres "pe" de "especial" ainda permite uma correspondência bem sucedida nesse termo.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Experimentando mais um pedido, modificar ainda mais o termo de pesquisa, retirando um último carácter para um total de três supressões (de "especial" a "escalda"):

    search=scal~&highlight=Description

Note que a mesma resposta é devolvida, mas agora em vez de combinar em "especial", o jogo confuso é em "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

O objetivo deste exemplo alargado é ilustrar a clareza que o destaque do sucesso pode trazer a resultados ambíguos. Em todos os casos, o mesmo documento é devolvido. Se tivesse confiado em iDs de documentos para verificar uma correspondência, poderia ter perdido a mudança de "especial" para "SQL".

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa completa de texto em Azure Cognitive Search (consulta de arquitetura de análise)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
