---
title: Adicionar analisadores de idioma a campos de cadeia de caracteres em um índice
titleSuffix: Azure Cognitive Search
description: Análise de texto léxico multilíngue para consultas e índices que não estão em inglês no Azure Pesquisa Cognitiva.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: f5833da5b15c893499b0d786972eff61c7391137
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790147"
---
# <a name="add-language-analyzers-to-an-azure-cognitive-search-index"></a>Adicionar analisadores de idioma a um índice de Pesquisa Cognitiva do Azure

Um *analisador de linguagem* é um tipo específico de [analisador de texto](search-analyzers.md) que executa uma análise lexical usando as regras linguísticas do idioma de destino. Cada campo pesquisável tem uma propriedade do **analisador** . Se o índice contiver cadeias de caracteres traduzidas, como campos separados para texto em inglês e chinês, você poderá especificar analisadores de idioma em cada campo para acessar os recursos lingüísticos avançados desses analisadores.  

O Azure Pesquisa Cognitiva dá suporte a analisadores 35 apoiados por Lucene e 50 analisadores apoiados por tecnologia proprietária de processamento de linguagem natural da Microsoft usada no Office e no Bing.

## <a name="comparing-analyzers"></a>Comparando analisadores

Alguns desenvolvedores podem preferir a solução mais familiar, simples e de software livre do Lucene. Os analisadores de linguagem Lucene são mais rápidos, mas os analisadores da Microsoft têm recursos avançados, como lematização, descomposto de palavras (em linguagens como alemão, dinamarquês, holandês, Sueco, norueguês, estoniano, término, húngaro, eslovaco) e entidade reconhecimento (URLs, emails, datas, números). Se possível, você deve executar comparações dos analisadores da Microsoft e do Lucene para decidir qual deles é mais adequado. 

A indexação com os analisadores da Microsoft é, em média, de duas a três vezes mais lenta do que seus equivalentes do Lucene, dependendo do idioma. O desempenho da pesquisa não deve ser significativamente afetado para consultas de tamanho médio. 

### <a name="english-analyzers"></a>Analisadores em inglês

O analisador padrão é o Lucene padrão, que funciona bem para o inglês, mas talvez não tão bem quanto o analisador em inglês do Lucene ou o analisador em inglês da Microsoft. 
 
+ O analisador em inglês do Lucene estende o analisador padrão. Ele remove possessivos (à direita) de palavras, aplica a lematização de acordo com o algoritmo de lematização de carregador e remove palavras de parada em inglês.  

+ O analisador em inglês da Microsoft executa lematização em vez de derivar. Isso significa que ele pode lidar com formulários formas flexionadas e irregulares de palavras, o que resulta em resultados de pesquisa mais relevantes 

## <a name="configuring-analyzers"></a>Configurando analisadores

Os analisadores de linguagem são usados no estado em que se encontram. Para cada campo na definição de índice, você pode definir a propriedade do **analisador** como um nome do analisador que especifica a pilha de idiomas e linguísticas (Microsoft ou Lucene). O mesmo analisador será aplicado durante a indexação e a pesquisa desse campo. Por exemplo, você pode ter campos separados para descrições do Hotel em inglês, francês e espanhol que existem lado a lado no mesmo índice. Como alternativa, em vez de **analisador**, você pode usar **indexAnalyzer** e **searchAnalyzer** para ter regras de análise diferentes no tempo de indexação e no tempo de consulta. 

Use o parâmetro de consulta **searchFields** para especificar o campo específico do idioma a ser pesquisado em suas consultas. Você pode examinar os exemplos de consulta que incluem a propriedade do analisador em [documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Para obter mais informações sobre propriedades de índice, consulte [criar índice &#40;Azure pesquisa cognitiva&#41;API REST](https://docs.microsoft.com/rest/api/searchservice/create-index). Para obter mais informações sobre a análise no Azure Pesquisa Cognitiva, consulte [analisadores no azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista do analisador de idioma 
 Abaixo está a lista de idiomas com suporte junto com os nomes do Lucene e do Microsoft Analyzer.  

|Linguagem|Nome do Microsoft Analyzer|Nome do analisador Lucene|  
|--------------|-----------------------------|--------------------------|  
|Árabe|ar. Microsoft|ar. Lucene|  
|Armênio||hipótese. Lucene|  
|Bengali|bilhão. Microsoft||  
|Basco||UE. Lucene|  
|Búlgaro|BG. Microsoft|BG. Lucene|  
|Catalão|ca. Microsoft|ca. Lucene|  
|Chinês simplificado|zh-Hans. Microsoft|zh-Hans. Lucene|  
|Chinês tradicional|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Croata|HR. Microsoft||  
|Tcheco|cs. Microsoft|cs. Lucene|  
|Dinamarquês|da. Microsoft|da. Lucene|  
|Holandês|nl. Microsoft|nl. Lucene|  
|Português|en. Microsoft|en. Lucene|  
|Estoniano|et. Microsoft||  
|Finlandês|Fi. Microsoft|o fi. Lucene|  
|Francês|fr. Microsoft|fr. Lucene|  
|Galego||GL. Lucene|  
|Alemão|de. Microsoft|de. Lucene|  
|Grego|El. Microsoft|El. Lucene|  
|Guzerate|Gu. Microsoft||  
|Hebraico|Ele. Microsoft||  
|Hindi|Olá. Microsoft|Oi. Lucene|  
|Húngaro|Hu. Microsoft|Hu. Lucene|  
|Islandês|é. Microsoft||  
|Indonésio (Bahasa)|ID. Microsoft|ID. Lucene|  
|Irlandês||ga. Lucene|  
|Italiano|ti. Microsoft|ti. Lucene|  
|Japonês|ja. Microsoft|ja. Lucene|  
|Kannada|kN. Microsoft||  
|Coreano|Ko. Microsoft|Ko. Lucene|  
|Letão|LV. Microsoft|LV. Lucene|  
|Lituano|lt. Microsoft||  
|Malaiala|ml. Microsoft||  
|Malaio (latino)|MS. Microsoft||  
|Marata|Sr. Microsoft||  
|Norueguês|NB. Microsoft|Não. Lucene|  
|Persa||FA. Lucene|  
|Polaco|pl. Microsoft|pl. Lucene|  
|Português (Brasil)|pt-br. Microsoft|pt-br. Lucene|  
|Português (Portugal)|pt pt. Microsoft|pt pt. Lucene|  
|Punjabi|PA. Microsoft||  
|Romeno|ro. Microsoft|ro. Lucene|  
|Russo|ru. Microsoft|ru. Lucene|  
|Sérvio (Cirílico)|Sr-cirílico. Microsoft||  
|Sérvio (latino)|Sr-latino. Microsoft||  
|Eslovaco|SK. Microsoft||  
|Esloveno|SL. Microsoft||  
|Espanhol|es. Microsoft|es. Lucene|  
|Sueco|VA. Microsoft|VA. Lucene|  
|Tâmil|ta. Microsoft||  
|Telugu|te. Microsoft||  
|Tailandês|th. Microsoft|th. Lucene|  
|Turco|TR. Microsoft|TR. Lucene|  
|Ucraniano|Reino Unido. Microsoft||  
|Urdu|seu. Microsoft||  
|Vietnamita|vi. Microsoft||  

 Todos os analisadores com nomes anotados com o **Lucene** são compatíveis com os [analisadores de idioma do Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Ver também  

+ [Criar índice &#40;API REST do Azure pesquisa cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Classe analyzername](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

