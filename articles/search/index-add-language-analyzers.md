---
title: Adicionar analisadores de idioma-Azure Search
description: Análise de texto léxico multilíngue para consultas e índices que não estão em inglês no Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
manager: nitinme
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
ms.openlocfilehash: e54fa449e0ed7f3208d9924b69946c6598a00444
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648815"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Adicionar analisadores de idioma a um índice de Azure Search

Um *analisador de linguagem* é um tipo específico de analisador de [texto](search-analyzers.md) que executa uma análise lexical usando as regras linguísticas do idioma de destino. Cada campo pesquisável tem uma Propriedade do analisador. Se o índice contiver cadeias de caracteres traduzidas, como campos separados para texto em inglês e chinês, você poderá especificar analisadores de idioma em cada campo para acessar os recursos lingüísticos avançados desses analisadores.  

O Azure Search dá suporte a analisadores 35 apoiados por Lucene e 50 analisadores apoiados por tecnologia proprietária de processamento de linguagem natural da Microsoft usada no Office e no Bing.

## <a name="comparing-analyzers"></a>Comparando analisadores

Alguns desenvolvedores podem preferir a solução mais familiar, simples e de software livre do Lucene. Os analisadores de linguagem Lucene são mais rápidos, mas os analisadores da Microsoft têm recursos avançados, como lematização, descomposto de palavras (em linguagens como alemão, dinamarquês, holandês, Sueco, norueguês, estoniano, término, húngaro, eslovaco) e entidade reconhecimento (URLs, emails, datas, números). Se possível, você deve executar comparações dos analisadores da Microsoft e do Lucene para decidir qual deles é mais adequado. 

A indexação com os analisadores da Microsoft é, em média, de duas a três vezes mais lenta do que seus equivalentes do Lucene, dependendo do idioma. O desempenho da pesquisa não deve ser significativamente afetado para consultas de tamanho médio. 

### <a name="english-analyzers"></a>Analisadores em inglês

O analisador padrão é o Lucene padrão, que funciona bem para o inglês, mas talvez não tão bem quanto o analisador em inglês do Lucene ou o analisador em inglês da Microsoft. 
 
+ O analisador em inglês do Lucene estende o analisador padrão. Ele remove possessivos (à direita) de palavras, aplica a lematização de acordo com o algoritmo de lematização de carregador e remove palavras de parada em inglês.  

+ O analisador em inglês da Microsoft executa lematização em vez de derivar. Isso significa que ele pode lidar com formulários formas flexionadas e irregulares de palavras, o que resulta em resultados de pesquisa mais relevantes 

## <a name="configuring-analyzers"></a>Configurando analisadores

Os analisadores de linguagem são usados no estado em que se encontram. Para cada campo na definição de índice, você pode definir a Propriedade do analisador como um nome do analisador que especifica a pilha de idiomas e linguísticas (Microsoft ou Lucene). O mesmo analisador será aplicado durante a indexação e a pesquisa desse campo. Por exemplo, você pode ter campos separados para descrições do Hotel em inglês, francês e espanhol que existem lado a lado no mesmo índice. Como alternativa, em vezde analisador, você pode usar **indexAnalyzer** e **searchAnalyzer** para ter regras de análise diferentes no tempo de indexação e no tempo de consulta. 

Use o parâmetro de consulta **searchFields** para especificar o campo específico do idioma a ser pesquisado em suas consultas. Você pode examinar os exemplos de consulta que incluem a propriedade do analisador em [documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Para obter mais informações sobre propriedades de índice, consulte [criar índice &#40;Azure Search API&#41;REST do serviço](https://docs.microsoft.com/rest/api/searchservice/create-index). Para obter mais informações sobre a análise em Azure Search, consulte [analisadores no Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista do analisador de idioma 
 Abaixo está a lista de idiomas com suporte junto com os nomes do Lucene e do Microsoft Analyzer.  

|Idioma|Nome do Microsoft Analyzer|Nome do analisador Lucene|  
|--------------|-----------------------------|--------------------------|  
|Árabe|ar.microsoft|ar. Lucene|  
|Arménio||hipótese. Lucene|  
|Bangla|bn.microsoft||  
|Basco (Basco)||UE. Lucene|  
|Búlgaro|bg.microsoft|bg.lucene|  
|Catalão|ca.microsoft|ca. Lucene|  
|Chinês simplificado|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinês tradicional|zh-Hant.microsoft|zh-Hant.lucene|  
|Croata|hr.microsoft||  
|Checo|cs.microsoft|cs. Lucene|  
|Dinamarquês|da.microsoft|da. Lucene|  
|Neerlandês|nl.microsoft|nl.lucene|  
|Português|en.microsoft|en. Lucene|  
|Estónio|et.microsoft||  
|Finlandês|fi.microsoft|o fi. Lucene|  
|Francês|fr.microsoft|fr. Lucene|  
|Galego||gl.lucene|  
|Alemão|de.microsoft|de. Lucene|  
|Grego|el.microsoft|El. Lucene|  
|Guzarate|gu.microsoft||  
|Hebraico|he.microsoft||  
|Hindi|hi.microsoft|Oi. Lucene|  
|Húngaro|hu.microsoft|Hu. Lucene|  
|Islandês|is.microsoft||  
|Indonésio (Bahasa)|id.microsoft|id.lucene|  
|Irlandês||ga. Lucene|  
|Italiano|it.microsoft|ti. Lucene|  
|Japonês|ja.microsoft|ja. Lucene|  
|Canarim|kn.microsoft||  
|Coreano|ko.microsoft|Ko. Lucene|  
|Letão|lv.microsoft|LV. Lucene|  
|Lituano|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malaio (latino)|ms.microsoft||  
|Marata|mr.microsoft||  
|Norueguês|nb.microsoft|Não. Lucene|  
|Persa||FA. Lucene|  
|Polaco|pl.microsoft|pl. Lucene|  
|Português (Brasil)|pt-Br.microsoft|pt-br. Lucene|  
|Português (Portugal)|pt-Pt.microsoft|pt pt. Lucene|  
|Punjabi|pa.microsoft||  
|Romeno|ro.microsoft|ro. Lucene|  
|Russo|ru.microsoft|ru. Lucene|  
|Sérvio (Cirílico)|sr-cyrillic.microsoft||  
|Sérvio (Latim)|sr-latin.microsoft||  
|Eslovaco|sk.microsoft||  
|Esloveno|sl.microsoft||  
|Espanhol|es.microsoft|es. Lucene|  
|Sueco|sv.microsoft|VA. Lucene|  
|Tamil|ta.microsoft||  
|Télego|te.microsoft||  
|Tailandês|th.microsoft|th. Lucene|  
|Turco|tr.microsoft|TR. Lucene|  
|Ucraniano|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamita|vi.microsoft||  

 Todos os analisadores com nomes anotados com o **Lucene** são compatíveis com os [analisadores de idioma do Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Consulte também  
 [Criar índice &#40;Azure Search API REST do serviço&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [Classe analyzername](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Vídeo: módulo 7 da apresentação Azure Search MVA](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

