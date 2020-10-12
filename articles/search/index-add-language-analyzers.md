---
title: Adicione analisadores de linguagem a campos de cordas
titleSuffix: Azure Cognitive Search
description: Análise lexical multilingue para consultas e índices não ingleses na Pesquisa Cognitiva Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: bda186f6bb45250763e439b77b4d3af988574401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935889"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Adicione analisadores de linguagem a campos de cordas num índice de pesquisa cognitiva Azure

Um *analisador de linguagem* é um tipo específico de [analisador](search-analyzers.md) de texto que realiza análise lexical usando as regras linguísticas da linguagem-alvo. Cada campo pesmável tem uma propriedade **de analisador.** Se o seu conteúdo consistir em cordas traduzidas, tais como campos separados para texto inglês e chinês, você pode especificar analisadores de línguas em cada campo para aceder às ricas capacidades linguísticas desses analisadores.

## <a name="when-to-use-a-language-analyzer"></a>Quando usar um analisador de linguagem

Deve considerar um analisador de linguagem quando a consciência da palavra ou da estrutura da frase acrescenta valor à análise de texto. Um exemplo comum é a associação de formas verbos irregulares ("bring" e "brought) ou substantivos plural ("ratos" e "rato"). Sem consciência linguística, estas cordas são analisadas apenas sobre características físicas, o que não consegue captar a ligação. Uma vez que grandes pedaços de texto são mais propensos a ter este conteúdo, os campos compostos por descrições, revisões ou resumos são bons candidatos a um analisador de línguas.

Você também deve considerar os analisadores de linguagem quando o conteúdo consiste em cordas de linguagem não ocidentais. Embora o [analisador padrão](search-analyzers.md#default-analyzer) seja agnóstico linguístico, o conceito de usar espaços e caracteres especiais (hífenes e cortes) para separar cordas tende a ser mais aplicável às línguas ocidentais do que às não ocidentais. 

Por exemplo, em chinês, japonês, coreano (CJK), e outras línguas asiáticas, um espaço não é necessariamente uma palavra delimiter. Considere a seguinte corda japonesa. Como não tem espaços, um analisador agnóstico linguístico provavelmente analisaria toda a corda como um símbolo, quando na verdade a corda é na verdade uma frase.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Para o exemplo acima, uma consulta bem sucedida teria de incluir o token completo, ou um símbolo parcial usando um sfixix wildcard, resultando numa experiência de pesquisa não natural e limitante.

Uma experiência melhor é procurar palavras individuais: 明るい (Bright), 私たちの (Our), 銀河系 (Galaxy). Usar um dos analisadores japoneses disponíveis na Pesquisa Cognitiva é mais propenso a desbloquear este comportamento porque esses analisadores estão mais bem equipados para dividir o pedaço de texto em palavras significativas na linguagem-alvo.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Comparar Lucene e Microsoft Analyzers

A Azure Cognitive Search suporta 35 analisadores linguísticos apoiados por Lucene, e 50 analisadores linguísticos apoiados pela tecnologia proprietária de processamento de linguagem natural da Microsoft usada no Office e Bing.

Alguns desenvolvedores podem preferir a solução mais familiar, simples e aberta da Lucene. Os analisadores de língua lucene são mais rápidos, mas os analisadores da Microsoft têm capacidades avançadas, tais como lematização, descompoção de palavras (em línguas como alemão, dinamarquês, holandês, sueco, norueguês, estónio, finish, húngaro, eslovaco) e reconhecimento de entidades (URLs, e-mails, datas, números). Se possível, deve executar comparações tanto dos analisadores microsoft como lucene para decidir qual é o melhor encaixe. 

Indexar com os analisadores da Microsoft é, em média, duas a três vezes mais lento do que os seus equivalentes Lucene, dependendo do idioma. O desempenho da pesquisa não deve ser significativamente afetado para consultas de tamanho médio. 

### <a name="english-analyzers"></a>Analisadores ingleses

O analisador padrão é Standard Lucene, que funciona bem para inglês, mas talvez não tão bem como o analisador inglês de Lucene ou o analisador inglês da Microsoft. 
 
+ O analisador inglês de Lucene estende o analisador padrão. Remove possessivos (rastos de palavras), aplica-se como algoritmo de Porter Stemming, e remove palavras de paragem inglesas.  

+ O analisador inglês da Microsoft executa a lematização em vez de se conter. Isto significa que pode lidar com formas de palavras inflexidas e irregulares muito melhor, o que resulta em resultados de pesquisa mais relevantes 

## <a name="configuring-analyzers"></a>Configurar analisadores

Os analisadores linguísticos são usados como está. Para cada campo na definição de índice, pode definir a propriedade do **analisador** para um nome de analisador que especifica a pilha de idioma e linguística (Microsoft ou Lucene). O mesmo analisador será aplicado ao indexar e procurar por esse campo. Por exemplo, você pode ter campos separados para descrições de hotéis ingleses, franceses e espanhóis que existem lado a lado no mesmo índice.

> [!NOTE]
> Não é possível utilizar um analisador de linguagem diferente no tempo de indexação do que no tempo de consulta para um campo. Essa capacidade é reservada para [analisadores personalizados.](index-add-custom-analyzers.md) Por esta razão, se tentar definir as propriedades **de PesquisaNallyzer** ou **IndexAnalyzer** para o nome de um analisador de idiomas, a API REST devolverá uma resposta de erro. Em vez disso, deve utilizar a propriedade **do analisador.**

Utilize o parâmetro de consulta **searchFields** para especificar qual o campo específico da linguagem a procurar nas suas consultas. Pode rever exemplos de consulta que incluem a propriedade do analisador em [Documentos de Busca.](/rest/api/searchservice/search-documents) 

Para obter mais informações sobre propriedades de índice, consulte [Create Index &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index). Para obter mais informações sobre a análise na Pesquisa Cognitiva Azure, consulte [Os Analisadores em Pesquisa Cognitiva Azure.](./search-analyzers.md)

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista de analisadores de idiomas 
 Abaixo está a lista de línguas suportadas juntamente com nomes de analisadores lucene e Microsoft.  

|Idioma|Nome do analisador da Microsoft|Nome do analisador lucene|  
|--------------|-----------------------------|--------------------------|  
|Árabe|ar.microsoft|ar.lucene|  
|Arménio||hy.lucene|  
|Bangla|bn.microsoft||  
|Basco||eu.lucene|  
|Búlgaro|bg.microsoft|bg.lucene|  
|Catalão|ca.microsoft|ca.lucene|  
|Chinês simplificado|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinês tradicional|zh-Hant.microsoft|zh-Hant.lucene|  
|Croata|hr.microsoft||  
|Checo|cs.microsoft|cs.lucene|  
|Dinamarquês|da.microsoft|da.lucene|  
|Neerlandês|nl.microsoft|nl.lucene|  
|Inglês|en.microsoft|en.lucene|  
|Estónio|et.microsoft||  
|Finlandês|fi.microsoft|fi.lucene|  
|Francês|fr.microsoft|fr.lucene|  
|Galego||gl.lucene|  
|Alemão|de.microsoft|de.lucene|  
|Grego|el.microsoft|el.lucene|  
|Guzerate|gu.microsoft||  
|Hebraico|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Húngaro|hu.microsoft|hu.lucene|  
|Islandês|is.microsoft||  
|Indonésio (Bahasa)|id.microsoft|id.lucene|  
|Irlandês||ga.lucene|  
|Italiano|it.microsoft|it.lucene|  
|Japonês|ja.microsoft|ja.lucene|  
|Canarês|kn.microsoft||  
|Coreano|ko.microsoft|ko.lucene|  
|Letão|lv.microsoft|lv.lucene|  
|Lituano|lt.microsoft||  
|Malaiala|ml.microsoft||  
|Malaio (latim)|ms.microsoft||  
|Marata|mr.microsoft||  
|Norueguês|nb.microsoft|no.lucene|  
|Persa||fa.lucene|  
|Polaco|pl.microsoft|pl.lucene|  
|Português (Brasil)|pt-Br.microsoft|pt-Br.lucene|  
|Português (Portugal)|pt-Pt.microsoft|pt-pt.lucene|  
|Punjabi|pa.microsoft||  
|Romeno|ro.microsoft|ro.lucene|  
|Russo|ru.microsoft|ru.lucene|  
|Sérvio (Cirílico)|sr-cirílico.microsoft||  
|Sérvio (Latim)|sr-latin.microsoft||  
|Eslovaco|sk.microsoft||  
|Esloveno|sl.microsoft||  
|Espanhol|es.microsoft|es.lucene|  
|Sueco|sv.microsoft|sv.lucene|  
|Tâmil|ta.microsoft||  
|Telugu|te.microsoft||  
|Tailandês|th.microsoft|th.lucene|  
|Turco|tr.microsoft|tr.lucene|  
|Ucraniano|uk.microsoft||  
|Urdu|seu.microsoft||  
|Vietnamita|vi.microsoft||  

 Todos os analisadores com nomes anotados com **Lucene** são alimentados pelos [analisadores linguísticos de Apache Lucene.](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )

## <a name="see-also"></a>Consulte também  

+ [Criar índice &#40;Azure Cognitive Search REST API&#41;](/rest/api/searchservice/create-index)  

+ [Aula de Nome Analisador](/dotnet/api/microsoft.azure.search.models.analyzername)