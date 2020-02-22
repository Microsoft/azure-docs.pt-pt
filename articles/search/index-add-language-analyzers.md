---
title: Adicione analisadores de linguagem aos campos de cordas
titleSuffix: Azure Cognitive Search
description: Análise de texto lexical multilingual para consultas e índices não ingleses em Pesquisa Cognitiva Azure.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539404"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Adicione analisadores de linguagem aos campos de cordas num índice de pesquisa cognitiva azure

Um *analisador* de linguagem é um tipo específico de [analisador](search-analyzers.md) de texto que realiza análises lexicais usando as regras linguísticas da linguagem-alvo. Cada campo pesquisável tem uma propriedade **de analisador.** Se o seu índice contiver cordas traduzidas, tais como campos separados para texto inglês e chinês, poderá especificar os analisadores de línguas em cada campo para aceder às ricas capacidades linguísticas desses analisadores.  

A Azure Cognitive Search suporta 35 analisadores apoiados por Lucene, e 50 analisadores apoiados pela tecnologia de processamento de linguagem natural da Microsoft, utilizada no Office e bing.

## <a name="comparing-analyzers"></a>Comparar analisadores

Alguns desenvolvedores podem preferir a solução mais familiar, simples e de código aberto da Lucene. Os analisadores de línguas lucene são mais rápidos, mas os analisadores da Microsoft têm capacidades avançadas, como a lemtização, o descomposto de palavras (em línguas como alemão, dinamarquês, holandês, sueco, norueguês, estónio, acabamento, húngaro, eslovaco) e entidade reconhecimento (URLs, e-mails, datas, números). Se possível, deverá fazer comparações tanto dos analisadores microsoft como lucene para decidir qual é o melhor encaixe. 

A indexação com os analisadores da Microsoft é, em média, duas a três vezes mais lenta do que os seus equivalentes Lucene, dependendo do idioma. O desempenho da pesquisa não deve ser significativamente afetado para consultas de tamanho médio. 

### <a name="english-analyzers"></a>Analisadores ingleses

O analisador padrão é o Standard Lucene, que funciona bem para inglês, mas talvez não tão bem como o analisador inglês da Lucene ou o analisador inglês da Microsoft. 
 
+ O analisador inglês de Lucene estende o analisador padrão. Remove possessivos (trailing's) das palavras, aplica-se como por algoritmo de Porter Stemming, e remove as palavras de paragem inglesas.  

+ O analisador inglês da Microsoft realiza lemmatização em vez de conter. Isto significa que pode lidar com formas de palavras inflexidas e irregulares muito melhor o que resulta em resultados de pesquisa mais relevantes 

## <a name="configuring-analyzers"></a>Configurar analisadores

Os analisadores de linguagem são usados como está. Para cada campo na definição de índice, pode definir a propriedade **do analisador** para um nome analisador que especifica a pilha de idiomas e linguísticas (Microsoft ou Lucene). O mesmo analisador será aplicado quando indexar e procurar por esse campo. Por exemplo, você pode ter campos separados para descrições de hotéis ingleses, franceses e espanhóis que existem lado a lado no mesmo índice.

> [!NOTE]
> Não é possível utilizar um analisador de linguagem diferente no tempo de indexação do que no momento da consulta para um campo. Essa capacidade é reservada para [analisadores personalizados.](index-add-custom-analyzers.md) Por esta razão, se tentar definir as propriedades **do SearchAnalyzer** ou **indexAnalyzer** para o nome de um analisador de idiomas, a API REST devolverá uma resposta de erro. Em vez disso, deve usar a propriedade **do analisador.**

Utilize o parâmetro de consulta **searchFields** para especificar qual o campo específico da linguagem para procurar nas suas consultas. Pode rever exemplos de consulta que incluem a propriedade de analisador em [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)de Pesquisa . 

Para obter mais informações sobre propriedades indexadas, consulte [Create Index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Para mais informações sobre análises em Pesquisa Cognitiva Azure, consulte [Analisadores em Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista de analisadores de linguagem 
 Abaixo está a lista de idiomas suportados juntamente com nomes de lucene e analisadores da Microsoft.  

|Idioma|Nome do Analisador da Microsoft|Nome do Analisador Lucene|  
|--------------|-----------------------------|--------------------------|  
|Árabe|ar.microsoft|ar.lucene|  
|Arménio||hy.lucene|  
|Bangla|bn.microsoft||  
|Basco (Basco)||eu.lucene|  
|Búlgaro|bg.microsoft|bg.lucene|  
|Catalão|ca.microsoft|ca.lucene|  
|Chinês simplificado|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinês tradicional|zh-Hant.microsoft|zh-Hant.lucene|  
|Croata|hr.microsoft||  
|Checo|cs.microsoft|cs.lucene|  
|Dinamarquês|da.microsoft|da.lucene|  
|Neerlandês|nl.microsoft|nl.lucene|  
|Português|en.microsoft|en.lucene|  
|Estónio|et.microsoft||  
|Finlandês|fi.microsoft|fi.lucene|  
|Francês|fr.microsoft|fr.lucene|  
|Galego||gl.lucene|  
|Alemão|de.microsoft|de.lucene|  
|Grego|el.microsoft|el.lucene|  
|Guzarate|gu.microsoft||  
|Hebraico|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Húngaro|hu.microsoft|hu.lucene|  
|Islandês|is.microsoft||  
|Indonésio (Bahasa)|id.microsoft|id.lucene|  
|Irlandês||ga.lucene|  
|Italiano|it.microsoft|it.lucene|  
|Japonês|ja.microsoft|ja.lucene|  
|Canarim|kn.microsoft||  
|Coreano|ko.microsoft|ko.lucene|  
|Letão|lv.microsoft|lv.lucene|  
|Lituano|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malaio (latim)|ms.microsoft||  
|Marata|mr.microsoft||  
|Norueguês|nb.microsoft|no.lucene|  
|Persa||fa.lucene|  
|Polaco|pl.microsoft|pl.lucene|  
|Português (Brasil)|pt-Br.microsoft|pt-Br.lucene|  
|Português (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Punjabi|pa.microsoft||  
|Romeno|ro.microsoft|ro.lucene|  
|Russo|ru.microsoft|ru.lucene|  
|Sérvio (Cirílico)|sr-cyrillic.microsoft||  
|Sérvio (Latim)|sr-latin.microsoft||  
|Eslovaco|sk.microsoft||  
|Esloveno|sl.microsoft||  
|Espanhol|es.microsoft|es.lucene|  
|Sueco|sv.microsoft|sv.lucene|  
|Tamil|ta.microsoft||  
|Télego|te.microsoft||  
|Tailandês|th.microsoft|th.lucene|  
|Turco|tr.microsoft|tr.lucene|  
|Ucraniano|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamita|vi.microsoft||  

 Todos os analisadores com nomes anotados com **Lucene** são alimentados pelos [analisadores linguísticos de Apache Lucene.](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )

## <a name="see-also"></a>Veja também  

+ [Criar &#40;a API de pesquisa cognitiva do Index Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

