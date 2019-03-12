---
title: Adicionar os analisadores personalizados - Azure Search
description: Modificar tokenizers de texto e os filtros de caráter utilizados em consultas de pesquisa de texto completo do Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: dd3b4743edf8bcd7f03a89b71a8c3a6cc3fe9de9
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570883"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Adicionar os analisadores personalizados para um índice da Azure Search

R *analisador personalizado* é um tipo específico de [analisador de texto](search-analyzers.md) que consiste numa combinação de definidas pelo utilizador de mensagens em fila de atomizador existente e filtros opcionais. Ao combinar tokenizers e filtros de novas formas, pode personalizar o processamento no mecanismo de pesquisa para alcançar resultados específicos de texto. Por exemplo, pode criar um analisador personalizado com um *filtro de char* para remover a marcação HTML antes de entradas de texto são indexadas.

 Pode definir vários analisadores personalizados para variar a combinação de filtros, mas cada campo só pode utilizar um analisador para indexação de análise e outro para análise de pesquisa. Para obter uma ilustração o aspeto de um analisador de cliente, consulte [exemplo de analisador personalizado](search-analyzers.md#Example1).

## <a name="overview"></a>Descrição geral

 A função de um [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md), em termos simples, é processar e armazenar documentos de forma que permite e obtenção de uma consulta eficiente. Num alto nível, tudo se resume extrair palavras importantes de documentos, colocá-las num índice e, em seguida, utilizar o índice para localizar documentos que correspondem palavras de uma determinada consulta. O processo de extração de palavras de documentos e consultas de pesquisa é chamado *análise lexical*. São chamados de componentes que executam a análise lexical *analisadores*.

 No Azure Search, pode escolher entre um conjunto de analisadores de idioma agnóstico predefinidos no [analisadores](#AnalyzerTable) tabela ou analisadores de idioma específico listados na [analisadores de idiomas &#40;API de REST do serviço do Azure Search&#41;](index-add-language-analyzers.md). Tem também uma opção para definir suas próprias analisadores personalizados.  

 Um analisador personalizado permite-lhe assumir o controlo sobre o processo de conversão de texto em tokens indexáveis e pesquisáveis. É uma configuração definida pelo utilizador compostas por um único atomizador predefinido, um ou mais filtros de token e um ou mais filtros de char. O atomizador é responsável pela divisão de texto em tokens e os filtros de token para modificar tokens emitidos pelo atomizador. Filtros de char são aplicados para preparar o texto de entrada antes de é processado pelo atomizador. Por exemplo, o filtro de char pode substituir determinados caracteres ou símbolos.

 Populares cenários ativados pelo analisadores personalizados incluem:  

-   Pesquisa fonética. Adicione um filtro fonético para ativar a pesquisa com base na forma como pode parecer uma palavra, não como ele está escrito.  

-   Desative a análise lexical. Utilize o analisador de palavra-chave para criar campos pesquisáveis, que não estão a ser analisados.  

-   Pesquisa rápida de prefixo/sufixo. Adicione o filtro de token Edge N-grama aos prefixos de índice de palavras para ativar a correspondência de prefixos rápido. Combiná-la com o filtro de token inverso para o sufixo correspondente.  

-   Atomização personalizada. Por exemplo, utilize o atomizador de espaço em branco para dividir as frases em tokens com espaços em branco como delimitador  

-   Dobragem ASCII. Adicione o ASCII padrão subconjuntos de validação de filtro para normalizar diacríticos como ö ou ê em termos de pesquisa.  

 Esta página fornece uma lista de filtros de char, filtros de token, tokenizers e analisadores suportados. Também pode encontrar uma descrição das alterações para a definição do índice com um exemplo de utilização. Para obter mais informações sobre a tecnologia subjacente utilizada na implementação do Azure Search, veja [resumo com pacote de análise (Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html). Para obter exemplos de configurações do analisador de mensagens em fila, consulte [adicionar analisadores no Azure Search](search-analyzers.md#examples).

## <a name="validation-rules"></a>Regras de validação  
 Nomes dos analisadores, tokenizers, filtros de token e filtros de char tem de ser exclusivo e não podem ser o mesmo que qualquer um dos analisadores predefinidos, tokenizers, filtros de token ou filtros de char. Consulte a [referência de propriedade](#PropertyReference) para nomes já em utilização.

## <a name="create-custom-analyzers"></a>Criar analisadores personalizados
 Pode definir os analisadores personalizados no momento de criação do índice. A sintaxe para especificar um analisador personalizado é descrita nesta secção. Pode também se familiarizar com a sintaxe por rever as definições de exemplo na [adicionar analisadores no Azure Search](search-analyzers.md#examples).  

 Uma definição de analisador inclui um nome, um tipo, um ou mais filtros de char, um máximo de um atomizador e um ou mais filtros de token para o processamento de pós atomização. Se filtram de char é aplicadas antes de atomização. Filtros de token e filtros de char são aplicados, da esquerda para a direita.

 O `tokenizer_name` é o nome de um atomizador `token_filter_name_1` e `token_filter_name_2` são os nomes de filtros de token, e `char_filter_name_1` e `char_filter_name_2` são os nomes de char filtros (consulte a [Tokenizers](#Tokenizers), [ Filtros de token](#TokenFilters) e tabelas para valores válidos de filtros de Char).

A definição de analyzer é uma parte do índice maior. Ver [criar o índice API](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter informações sobre o restante do índice.

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  Analisadores personalizados que criar não são expostos no portal do Azure. É a única forma de adicionar um analisador personalizado por meio de código que faz chamadas à API, ao definir um índice.  

 Dentro de uma definição de índice, pode colocar esta secção em qualquer lugar no corpo de uma solicitação de índice de criação, mas normalmente vai no final:  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Definições de filtros de char, tokenizers e token filtros são adicionadas ao índice apenas se estiver a definir opções personalizadas. Para utilizar um filtro existente ou atomizador como-é, especifique-o por nome na definição do analyzer.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Analisadores personalizados de teste

Pode utilizar o **operação do analisador de teste** no [REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para ver como um analisador desmembra fornecido texto em tokens.

**Pedido**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Resposta**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Atualize os analisadores personalizados

Depois de um analisador, um tokenizer, um filtro de token ou um filtro de char é definido, não pode ser modificado. Novos podem ser adicionados a um índice existente, apenas se o `allowIndexDowntime` sinalizador estiver definido como verdadeiro no pedido de atualização de índice:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Esta operação retira o seu índice offline para, pelo menos, alguns segundos, fazendo com que os pedidos de indexação e consulta a falhar. Disponibilidade de desempenho e de escrita do índice pode ser afetada durante vários minutos, depois do índice é mais longo para índices muito grandes ou atualizados, mas esses efeitos são temporários e, eventualmente, resolver por conta própria.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Referência de analisador

As tabelas a seguir listam as propriedades de configuração para analisadores, tokenizers, filtros de token e char secção filtro de uma definição de índice. A estrutura de um analisador, tokenizer ou filtro no seu índice é composta por estes atributos. Para informações de atribuição de valor, consulte a [referência de propriedade](#PropertyReference).

### <a name="analyzers"></a>Analisadores

Para analisadores, atributos de índice variam consoante o se estiver a utilizar analisadores predefinidos ou personalizados.

#### <a name="predefined-analyzers"></a>Analisadores predefinidos

|||  
|-|-|  
|Name|Só pode conter letras, dígitos, espaços, traços ou carateres de sublinhado, só pode começar e terminar com carateres alfanuméricos e está limitado a 128 carateres.|  
|Type|Tipo de analisador da lista de analisadores suportados. Consulte a **analyzer_type** coluna na [analisadores](#AnalyzerTable) tabela abaixo.|  
|Opções|Tem de ser as opções válidas de um analisador predefinido listados na [analisadores](#AnalyzerTable) tabela abaixo.|  

#### <a name="custom-analyzers"></a>Analisadores personalizados

|||  
|-|-|  
|Name|Só pode conter letras, dígitos, espaços, traços ou carateres de sublinhado, só pode começar e terminar com carateres alfanuméricos e está limitado a 128 carateres.|  
|Type|Tem de ser "#Microsoft.Azure.Search.CustomAnalyzer".|  
|CharFilters|Definido como um dos filtros de char predefinidos listados na [filtros de Char](#CharFilter) tabela ou um filtro de char personalizado especificado na definição do índice.|  
|Tokenizer|Necessário. Definido como uma destas tokenizers predefinidos listados na [Tokenizers](#Tokenizers) abaixo da tabela ou um atomizador personalizado especificado na definição do índice.|  
|TokenFilters|Definido como um dos filtros de token predefinidos listados na [filtros do Token](#TokenFilters) tabela ou um filtro personalizado de token especificado na definição do índice.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtros de char

 Um filtro de char é usado para preparar o texto de entrada antes de é processado pelo atomizador. Por exemplo, elas substituem certos caracteres ou símbolos. Pode ter vários filtros de char num analisador personalizado. Filtros de char são executados pela ordem em que estão listados.  

|||  
|-|-|  
|Name|Só pode conter letras, dígitos, espaços, traços ou carateres de sublinhado, só pode começar e terminar com carateres alfanuméricos e está limitado a 128 carateres.|  
|Type|Tipo da lista de filtros de char suportadas de filtro de char. Ver **char_filter_type** coluna na [Char filtros](#CharFilter) tabela abaixo.|  
|Opções|Tem de ser as opções válidas de uma determinada [Char filtros](#CharFilter) tipo.|  

### <a name="tokenizers"></a>Tokenizers

 Um atomizador divide texto contínuo numa seqüência de tokens, como quebrar uma frase em palavras.  

 Pode especificar exatamente um atomizador por analisador personalizado. Se precisar de mais do que um tokenizer, pode criar vários analisadores personalizados e atribuí-los numa base de campo por campo no seu esquema de índice.  
Um analisador personalizado pode utilizar um atomizador predefinido com opções personalizadas ou padrão.  

|||  
|-|-|  
|Name|Só pode conter letras, dígitos, espaços, traços ou carateres de sublinhado, só pode começar e terminar com carateres alfanuméricos e está limitado a 128 carateres.|  
|Type|Nome de atomizador na lista de tokenizers suportados. Ver **tokenizer_type** coluna na [Tokenizers](#Tokenizers) tabela abaixo.|  
|Opções|Tem de ser as opções válidas de um tipo de determinado atomizador listados na [Tokenizers](#Tokenizers) tabela abaixo.|  

### <a name="token-filters"></a>Filtros de token

 Um filtro de token é utilizado para filtrar ou modificar os tokens gerados por um atomizador. Por exemplo, pode especificar um filtro em minúsculas, que converte todos os carateres em minúsculas.   
Pode ter vários filtros de token num analisador personalizado. Filtros de token são executados pela ordem em que estão listados.  

|||  
|-|-|  
|Name|Só pode conter letras, dígitos, espaços, traços ou carateres de sublinhado, só pode começar e terminar com carateres alfanuméricos e está limitado a 128 carateres.|  
|Type|Nome do filtro de token na lista de filtros de token suportados. Ver **token_filter_type** coluna na [filtros do Token](#TokenFilters) tabela abaixo.|  
|Opções|Tem de ser [filtros do Token](#TokenFilters) de um tipo de filtro de token específico.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Referência de propriedade

Esta secção fornece os valores válidos para atributos especificados na definição de um analisador personalizado, tokenizer, filtro de char ou um filtro de token no seu índice. Analisadores e filtros que são implementados com o Apache Lucene tokenizers tem ligações para a documentação da API de Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Referência de analisadores predefinidos

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrição e as opções**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (tipo aplica-se apenas quando estão disponíveis opções) |Processa todo o conteúdo de um campo como um único token. Isto é útil para dados, como códigos postais, IDs e alguns nomes de produtos.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Forma flexível separa o texto em termos por meio de um padrão de expressão regular.<br /><br /> **Opções**<br /><br /> em minúsculas (tipo: bool)-determina se termos estão em minúsculas. A predefinição é verdadeira.<br /><br /> [padrão de](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tipo: cadeia de caracteres)-um padrão de expressão regular para corresponder ao token separadores. A predefinição é \w+.<br /><br /> [sinalizadores de](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres)-os sinalizadores de expressão Regular. A predefinição é uma cadeia vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> palavras de paragem (tipo: matriz de cadeia de caracteres)-uma lista de palavras de paragem. A predefinição é uma lista vazia.|  
|[simple](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(tipo aplica-se apenas quando estão disponíveis opções) |Divide texto na não letras e converte-os em minúsculas. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Também referido como standard.lucene)|StandardAnalyzer|Analisador de Lucene padrão, é composto pelo atomizador padrão, o filtro em minúsculas e o filtro de paragem.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. A predefinição é 255. Mais do que o comprimento máximo de tokens são divididos. Comprimento máximo de token que pode ser usado é 300 carateres.<br /><br /> palavras de paragem (tipo: matriz de cadeia de caracteres)-uma lista de palavras de paragem. A predefinição é uma lista vazia.|  
|standardasciifolding.lucene|(tipo aplica-se apenas quando estão disponíveis opções) |Analisador de padrão com Ascii subconjuntos de validação de filtro. |  
|[stop](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Divide texto na não letras, aplica-se os filtros de token em minúsculas e a palavra de paragem.<br /><br /> **Opções**<br /><br /> palavras de paragem (tipo: matriz de cadeia de caracteres)-uma lista de palavras de paragem. A predefinição é uma lista predefinida para inglês. |  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(tipo aplica-se apenas quando estão disponíveis opções) |Um analisador que utiliza o atomizador de espaço em branco. São divididas tokens que são mais de 255 carateres.|  

 <sup>1</sup> analisador de tipos são sempre o prefixo no código com "#Microsoft.Azure.Search", de modo a que o "PatternAnalyzer", na verdade, teria de ser especificado como "#Microsoft.Azure.Search.PatternAnalyzer". Removemos o prefixo para fins de brevidade, mas o prefixo é necessária no seu código. 
 
O analyzer_type só é fornecido para analisadores que podem ser personalizados. Se não há opções, como é o caso com o analisador de palavra-chave, não há nenhum tipo de #Microsoft.Azure.Search associados.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referência de filtros de char

Na tabela abaixo, os filtros de caracteres que são implementados com o Apache Lucene estão ligados para a documentação da API de Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrição e as opções**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Constrói um filtro de char que tenta de retirar HTML.|  
|[mapping](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Um filtro de char aplica-se definida com a opção de mapeamentos de mapeamentos. A correspondência é greedy (mais longo correspondência de padrão num determinado ponto wins). Substituição tem permissão para ser a cadeia vazia.<br /><br /> **Opções**<br /><br /> mapeamentos (tipo: matriz de cadeia de caracteres)-uma lista de mapeamentos no seguinte formato: "a = > b" (todas as ocorrências do caractere "a" é substituída por carateres "b"). Necessário.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Um filtro de char que substitui caracteres na cadeia de entrada. Utiliza uma expressão regular para identificar as seqüências de caracteres para preservar e um padrão de substituição para identificar os carateres a substituir. Por exemplo, texto de entrada = "aa bb aa bb", pattern="(aa)\\\s+(bb)" substituição = "$1 n. º 2$", resultado = "aa #bb aa #bb".<br /><br /> **Opções**<br /><br /> padrão (tipo: cadeia de caracteres) – necessário.<br /><br /> substituição (tipo: cadeia de caracteres) – necessário.|  

 <sup>1</sup> tipos de filtro de char é sempre adicionado como prefixo no código com "#Microsoft.Azure.Search", de modo a que o "MappingCharFilter", na verdade, teria de ser especificado como "#Microsoft.Azure.Search.MappingCharFilter. Removemos o prefixo para reduzir a largura da tabela, mas não se esqueça de incluí-lo em seu código. Tenha em atenção que char_filter_type só é fornecido para filtros que podem ser personalizados. Se não há opções, como é o caso com html_strip, não há nenhum tipo de #Microsoft.Azure.Search associados.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Referência de tokenizers

Na tabela abaixo, os que são implementadas com o Apache Lucene tokenizers estão ligados para a documentação da API de Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrição e as opções**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Atomizador de gramática, com base em que é adequado para processar a maioria dos documentos de idiomas da Europa.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. Predefinição: 255, máximo: 300. Mais do que o comprimento máximo de tokens são divididos.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Divide a entrada de uma borda em n-grams de dado size(s).<br /><br /> **Opções**<br /><br /> minGram (tipo: int)-predefinido: 1, máximo: 300.<br /><br /> maxGram (tipo: int)-predefinido: 2, máximo: 300. Tem de ser maior que minGram.<br /><br /> tokenChars (tipo: matriz de cadeia de caracteres)-classes para manter nos tokens de caracteres. Valores permitidos: <br />"letter", "digit", "whitespace", "punctuation", "symbol". O padrão é uma matriz vazia - mantém todos os caracteres. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emite a toda entrada como um único token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. Predefinição: 256, máximo: 300. Mais do que o comprimento máximo de tokens são divididos.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Divide texto na não são letras. São divididas tokens que são mais de 255 carateres.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Divide texto na não letras e converte-os em minúsculas. São divididas tokens que são mais de 255 carateres.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide texto com regras específicas de idioma.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token, o padrão: 255, máximo: 300. Mais do que o comprimento máximo de tokens são divididos. Tokens de mais do que 300 carateres primeiro são divididas em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividida com base no conjunto maxTokenLength.<br /><br />isSearchTokenizer (tipo: bool) – defina como VERDADEIRO se for utilizado como o atomizador de pesquisa, definido como FALSO se for utilizado como a indexação atomizador. <br /><br /> idioma (tipo: cadeia de caracteres)-idioma a utilizar, predefinido "inglês". Valores permitidos incluem:<br />"bangla", "Búlgaro", "catalan", "chineseSimplified", "chineseTraditional", "Croata", "czech", "dinamarquês", "holandês", "inglês", "francesa", "Alemanha", "Grego", "guzerate", "híndi", "króna", "Rupia", "italiano", "japonês", "kannada" " Coreano","malay","Malaiala","Marati","norwegianBokmaal","polonês","português","portugueseBrazilian","punjabi","Romeno","russo","serbianCyrillic","serbianLatin","Esloveno","espanhol","sueco","tamil","Télugo","tailandês"" ucraniano","urdu","vietnamita" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide texto com regras específicas de idioma e reduz as palavras para suas formas de bases<br /><br /> **Opções**<br /><br />maxTokenLength (tipo: int)-o comprimento máximo de token, o padrão: 255, máximo: 300. Mais do que o comprimento máximo de tokens são divididos. Tokens de mais do que 300 carateres primeiro são divididas em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividida com base no conjunto maxTokenLength.<br /><br /> isSearchTokenizer (tipo: bool) – defina como VERDADEIRO se for utilizado como o atomizador de pesquisa, definido como FALSO se for utilizado como a indexação atomizador.<br /><br /> idioma (tipo: cadeia de caracteres)-idioma a utilizar, predefinido "inglês". Valores permitidos incluem:<br />"Árabe", "bangla", "Búlgaro", "catalan", "Croata", "czech", "dinamarquês", "holandês", "inglês", "Estónio", "finlandês", "francesa", "Alemanha", "Grego", "guzerate", "Hebraico", "híndi", "húngara", "króna", "Rupia", "italiano", "kannada" " Letão","lithuanian","malay","Malaiala","Marati","norwegianBokmaal","polonês","português","portugueseBrazilian","punjabi","Romeno","russo","serbianCyrillic","serbianLatin","Eslovaco","Esloveno","espanhol","sueco"" tamil","Télugo","turco","ucraniano","urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Divide a entrada n-grams do size(s) determinado.<br /><br /> **Opções**<br /><br /> minGram (tipo: int)-predefinido: 1, máximo: 300.<br /><br /> maxGram (tipo: int)-predefinido: 2, máximo: 300. Tem de ser maior que minGram. <br /><br /> tokenChars (tipo: matriz de cadeia de caracteres)-classes para manter nos tokens de caracteres. Valores permitidos: "letra", "dígito", "espaços em branco", "pontuação", "símbolo". O padrão é uma matriz vazia - mantém todos os caracteres. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Atomizador para hierarquias de tipo de caminho.<br /><br /> **Opções**<br /><br /> delimitador (tipo: cadeia de caracteres)-predefinição: "/.<br /><br /> substituição (tipo: cadeia de caracteres) – se estiver definido, substitui o caráter delimitador. Padrão mesmo como o valor do delimitador.<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. Predefinição: 300, máximo: 300. Caminhos com mais de maxTokenLength são ignorados.<br /><br /> inversa (tipo: bool) – se for VERDADEIRO, gera o token na ordem inversa. Predefinição: FALSO.<br /><br /> Ignorar (tipo: bool)-inicial tokens para ignorar. A predefinição é 0.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Este atomizador utiliza a correspondência de padrões de regex para construir tokens distintos.<br /><br /> **Opções**<br /><br /> padrão (tipo: cadeia de caracteres)-padrão de expressão Regular. A predefinição é \w+.<br /><br /> [sinalizadores de](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres)-os sinalizadores de expressão Regular. A predefinição é uma cadeia vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> grupo (tipo: int)-qual o grupo para extrair em tokens. A predefinição é de -1 (divisão).|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Interrompe o seguinte texto de [regras de segmentação de texto Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. Predefinição: 255, máximo: 300. Mais do que o comprimento máximo de tokens são divididos.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Divide a urls e e-mails como um token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo de token. Predefinição: 255, máximo: 300. Mais do que o comprimento máximo de tokens são divididos.|  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(tipo aplica-se apenas quando estão disponíveis opções) |Divide texto no espaço em branco. São divididas tokens que são mais de 255 carateres.|  

 <sup>1</sup> atomizador tipos são sempre o prefixo no código com "#Microsoft.Azure.Search", de modo a que o "ClassicTokenizer", na verdade, teria de ser especificado como "#Microsoft.Azure.Search.ClassicTokenizer". Removemos o prefixo para reduzir a largura da tabela, mas não se esqueça de incluí-lo em seu código. Tenha em atenção que tokenizer_type só é fornecido para tokenizers que podem ser personalizados. Se não há opções, como é o caso com o atomizador de letra, não há nenhum tipo de #Microsoft.Azure.Search associados.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referência de token de filtros

Na tabela abaixo, os filtros de token que são implementados com o Apache Lucene estão ligados para a documentação da API de Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrição e as opções**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Um filtro de token que se aplica a normalizer Árabe normalizar o orthography.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Remove todos os carateres após um apóstrofo (incluindo o apóstrofe propriamente dito). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converte os caracteres de Unicode alfabéticos, numéricos e de símbolos que não estejam nos primeiro 127 carateres ASCII (o bloco de Unicode "Latim básico") em seus equivalentes em ASCII, se existir.<br /><br /> **Opções**<br /><br /> preserveOriginal (tipo: bool) – se VERDADEIRO, o token original é mantido. A predefinição é falso.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Formulários bigrams dos termos CJK gerados a partir StandardTokenizer.<br /><br /> **Opções**<br /><br /> ignoreScripts (tipo: matriz de cadeia de caracteres)-Scripts para ignorar. Permissão de valores incluem: "han", "hiragana", "katakana", "hangul". A predefinição é uma lista vazia.<br /><br /> outputUnigrams (tipo: bool) – defina como VERDADEIRO se pretender sempre unigrams e bigrams de saída. A predefinição é falso.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza diferenças de largura CJK. Subconjuntos completo variantes de ASCII de largura para o Latim básico equivalentes e variantes de Katakana de meia largura para o kana equivalente. |  
|[classic](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Remove os possessives em inglês e pontos de acrônimos. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Construa bigrams para a frequência de ocorrência de termos durante a indexação. Termos únicos ainda são indexados, com bigrams sobrepostas.<br /><br /> **Opções**<br /><br /> commonWords (tipo: matriz de cadeia de caracteres)-o conjunto de palavras comuns. A predefinição é uma lista vazia. Necessário.<br /><br /> ignoreCase (tipo: bool) – se VERDADEIRO, a correspondência é sensível. A predefinição é falso.<br /><br /> queryMode (tipo: bool) – gera bigrams, em seguida, remove palavras comuns e termos simples, seguidos por uma palavra em comum. A predefinição é falso.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Decomposes palavras compostas encontradas em muitas linguagens Germanic.<br /><br /> **Opções**<br /><br /> wordList (tipo: matriz de cadeia de caracteres)-a lista de palavras para correspondência. A predefinição é uma lista vazia. Necessário.<br /><br /> minWordSize (tipo: int)-apenas as palavras maiores do que isto são processadas. A predefinição é 5.<br /><br /> minSubwordSize (tipo: int)-apenas subwords maiores do que isto são produzidos. A predefinição é 2.<br /><br /> maxSubwordSize (tipo: int)-apenas subwords menores do que isso são produzidos. A predefinição é 15.<br /><br /> onlyLongestMatch (tipo: bool)-adicionar apenas o subword correspondente mais longo para a saída. A predefinição é falso.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Gera n-grams do size(s) determinado a partir de começar a frente ou parte de trás de um token de entrada.<br /><br /> **Opções**<br /><br /> minGram (tipo: int)-predefinido: 1, máximo: 300.<br /><br /> maxGram (tipo: int)-predefinido: 2, 300 máximo. Tem de ser maior que minGram.<br /><br /> lado (tipo: cadeia de caracteres) – Especifica qual lado da entrada n-grama deve ser gerado de. Valores permitidos: "front", "voltar" |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Remove elisions. Por exemplo, "l'avion" ("o plano") é convertido em "avion" (plano).<br /><br /> **Opções**<br /><br /> artigos (tipo: matriz de cadeia de caracteres)-um conjunto de artigos para remover. A predefinição é uma lista vazia. Se não houver nenhuma lista de conjunto de artigos, por predefinição, todos os artigos francês são removidos.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza alemães caracteres, de acordo com a heurística do [algoritmo de snowball German2](http://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza o texto em Híndi para remover algumas diferenças na variações de ortografia. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliza a representação Unicode de texto em idiomas indianos.
|[keep](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Um filtro de token que mantém apenas os tokens com o texto contido na lista especificada de palavras.<br /><br /> **Opções**<br /><br /> keepWords (tipo: matriz de cadeia de caracteres)-uma lista de palavras para manter. A predefinição é uma lista vazia. Necessário.<br /><br /> keepWordsCase (tipo: bool) – se VERDADEIRO, o menor caso todas as palavras em primeiro lugar. A predefinição é falso.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Termos de marcas como palavras-chave.<br /><br /> **Opções**<br /><br /> palavras-chave (tipo: matriz de cadeia de caracteres)-uma lista de palavras para marcar como palavras-chave. A predefinição é uma lista vazia. Necessário.<br /><br /> ignoreCase (tipo: bool) – se VERDADEIRO, o menor caso todas as palavras em primeiro lugar. A predefinição é falso.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Emite o token cada entrada de duas vezes uma vez como palavra-chave e uma vez como não palavra-chave. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Um filtro de alto desempenho kstem para inglês. |  
|[Comprimento](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Remove palavras que são demasiado longo ou demasiado curtas.<br /><br /> **Opções**<br /><br /> min (tipo: int)-o número mínimo. Predefinição: 0, máximo: 300.<br /><br /> Máx. (tipo: int)-o número máximo. Predefinição: 300, máximo: 300.|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita o número de tokens durante a indexação.<br /><br /> **Opções**<br /><br /> maxTokenCount (tipo: int)-número máx. de tokens para produzir. A predefinição é 1.<br /><br /> consumeAllTokens (tipo: bool) - se todos os tokens de entrada têm de ser utilizados, mesmo se maxTokenCount for atingido. A predefinição é falso.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza o texto de token em minúsculas. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Gera n-grams do size(s) determinado.<br /><br /> **Opções**<br /><br /> minGram (tipo: int)-predefinido: 1, máximo: 300.<br /><br /> maxGram (tipo: int)-predefinido: 2, 300 máximo. Tem de ser maior que minGram.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Utiliza as regexes Java para emitir tokens de vários, um para cada grupo de captura em padrões de um ou mais.<br /><br /> **Opções**<br /><br /> padrões (tipo: matriz de cadeia de caracteres)-uma lista de padrões para correspondência com cada token. Necessário.<br /><br /> preserveOriginal (tipo: bool) – defina como verdadeiro para devolver o token original, mesmo se um dos padrões corresponder, predefinição: VERDADEIRO |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Um filtro de token que aplica-se de um padrão para cada token no fluxo, substituindo as ocorrências de correspondência com a cadeia de caracteres substituta especificada.<br /><br /> **Opções**<br /><br /> padrão (tipo: cadeia de caracteres) – necessário.<br /><br /> substituição (tipo: cadeia de caracteres) – necessário.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções) |Aplica-se de normalização para Persa. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Crie tokens para correspondências fonéticas.<br /><br /> **Opções**<br /><br /> codificador (tipo: cadeia de caracteres)-fonético codificador para utilizar. Permissão de valores incluem: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Predefinição: "metaphone". A predefinição é metaphone.<br /><br /> Ver [codificador](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) para obter mais informações.<br /><br /> Substituir (tipo: bool) – VERDADEIRO se tokens codificadas devem substituir o originais tokens, FALSO se devem ser adicionados como sinónimos. A predefinição é verdadeira.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Transforma o fluxo de token, de acordo a [algoritmo de Lematização Porter](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverse](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Reverte a cadeia de caracteres de token. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza o uso dos caracteres Scandinavian intercambiáveis. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Folds Scandinavian characters åÅäæÄÆ->a and öÖøØ->o. Ele também discriminates contra o uso de vogais duplos aa, ae, pedidos, oe e oo, deixando apenas aquele primeiro. |  
|[shingle](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Cria combinações de tokens como um único token.<br /><br /> **Opções**<br /><br /> maxShingleSize (tipo: int)-o padrão é 2.<br /><br /> minShingleSize (tipo: int)-o padrão é 2.<br /><br /> outputUnigrams (tipo: bool) – se VERDADEIRO, o fluxo de saída contém os tokens de entrada (unigrams), bem como shingles. A predefinição é verdadeira.<br /><br /> outputUnigramsIfNoShingles (tipo: bool) – se for VERDADEIRO, substituir o comportamento de outputUnigrams = = false para aquelas situações quando não existem shingles estão disponíveis. A predefinição é falso.<br /><br /> tokenSeparator (tipo: cadeia de caracteres)-a cadeia de caracteres a utilizar ao associar adjacentes tokens para formar um shingle. A predefinição é "".<br /><br /> filterToken (tipo: cadeia de caracteres)-a cadeia de caracteres para inserir para cada posição em que não existe nenhum token. A predefinição é "_".|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtro de Token da bola.<br /><br /> **Opções**<br /><br /> idioma (tipo: cadeia de caracteres)-permitida de valores incluem: "Arménio", "Basco", "catalan", "dinamarquês", "holandês", "inglês", "finlandês", "francesa", "Alemanha", "german2", "Húngaro", "italiano", "kp", "lovins", "coroa", "porter", "português", "Romeno", " Russo","espanhol","sueco","turco"|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliza a representação de Unicode de texto de Sorani.<br /><br /> **Opções**<br /><br /> Nenhum.|  
|Lematizador|StemmerTokenFilter|Filtro de Lematização de idioma específico.<br /><br /> **Opções**<br /><br /> idioma (tipo: cadeia de caracteres)-permitida de valores incluem: <br /> -   ["arabic"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["Arménio"](http://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](http://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brasileiro"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"Búlgaro"<br />-   ["catalan"](http://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danish"](http://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](http://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](http://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["inglês"](http://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](http://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](http://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finnish"](http://snowballstem.org/algorithms/finnish/stemmer.html)<br />-   "lightFinnish"<br />-   ["french"](http://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-   "galician"<br />-   "minimalGalician"<br />-   ["alemão"](http://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](http://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   "minimalGerman"<br />-   ["greek"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   "hindi"<br />-   ["húngara"](http://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["Rupia"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irlandesa"](http://snowballstem.org/otherapps/oregan/)<br />-   ["italiano"](http://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Letão"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["coroa"](http://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["português"](http://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["Romeno"](http://snowballstem.org/otherapps/romanian/)<br />-   ["russo"](http://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["espanhol"](http://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](http://snowballstem.org/algorithms/swedish/stemmer.html)<br />-   "lightSwedish"<br />-   ["turkish"](http://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Todos os termos surgiu com o dicionário são marcados como palavras-chave, o que impede a lematização pela cadeia. Tem de ser colocado antes de quaisquer filtros stemming.<br /><br /> **Opções**<br /><br /> regras (tipo: matriz de cadeia de caracteres)-Lematização regras no seguinte formato "word = > se originam" por exemplo "executou = > executar". A predefinição é uma lista vazia.  Necessário.|  
|[palavras de paragem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Remove parar palavras de um fluxo de token. Por predefinição, o filtro utiliza uma lista de palavras de paragem predefinidas para inglês.<br /><br /> **Opções**<br /><br /> palavras de paragem (tipo: matriz de cadeia de caracteres)-uma lista de palavras de paragem. Não é possível especificar se um stopwordsList for especificado.<br /><br /> stopwordsList (tipo: cadeia de caracteres)-uma lista de palavras de paragem predefinida. Não é possível especificar se as palavras de paragem for especificada. Permissão de valores incluem: "Árabe", "Arménio", "Basco", "brasileiro", "Búlgaro", "catalan", "czech", "dinamarquês", "holandês", "inglês", "finlandês", "francesa", "galician", "Alemanha", "gregos", "híndi", "húngara", "Rupia", "irlandesa", "italiano", "Letão ","norueguês","persian","português","Romeno","russo","sorani","espanhol","sueco"," tailandês","turco,"predefinido:"inglês". Não é possível especificar se as palavras de paragem for especificada. <br /><br /> ignoreCase (tipo: bool) – se for VERDADEIRO, todas as palavras são inferior primeiro com maiúsculas e minúsculas. A predefinição é falso.<br /><br /> removeTrailing (tipo: bool) – se for VERDADEIRO, ignore o termo de pesquisa de último caso se trate de uma palavra de paragem. A predefinição é verdadeira.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Correspondências única ou múltipla sinónimos de word num fluxo de token.<br /><br /> **Opções**<br /><br /> sinónimos (tipo: matriz de cadeia de caracteres) – necessário. Lista de sinónimos dos seguintes dois formatos:<br /><br /> -incrível, unbelievable, fabuloso = > incrível - todos os termos no lado esquerdo da = > símbolo são substituídos por todos os termos do seu lado direito.<br /><br /> -incrível, unbelievable, fabuloso, incrível - uma lista separada por vírgulas de palavras equivalentes. Defina a opção de expandir para alterar a forma como esta lista é interpretada.<br /><br /> ignoreCase (tipo: bool)-entrada de subconjuntos de caso para correspondência. A predefinição é falso.<br /><br /> Expanda (tipo: bool) – se VERDADEIRO, todas as palavras na lista de sinónimos (se = > notação não for utilizada) mapear para um do outro. <br />A lista seguinte: incrível, unbelievable, fabuloso, incrível é equivalente a: incrível, unbelievable, fabuloso, incrível = > incrível, unbelievable, fabuloso, incrível<br /><br />-Se false, a lista seguinte: incrível incrível, unbelievable, fabuloso, são equivalentes às: incrível, unbelievable, fabuloso, incrível = > incrível.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Corta esquerda e à direita espaços em branco de tokens. |  
|[truncate](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunca os termos num limite específico.<br /><br /> **Opções**<br /><br /> comprimento (tipo: int)-predefinido: 300, máximo: 300. Necessário.|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtra os tokens com o mesmo texto que o token anterior.<br /><br /> **Opções**<br /><br /> onlyOnSamePosition (tipo: bool) – se estiver definido, remover duplicados apenas na mesma posição. A predefinição é verdadeira.|  
|[uppercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(tipo aplica-se apenas quando estão disponíveis opções)  |Normaliza o texto de token em maiúsculas. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Divide palavras em subwords e efetua transformações opcionais em grupos de subword.<br /><br /> **Opções**<br /><br /> generateWordParts (tipo: bool)-faz com que partes da palavras, sejam gerados, por exemplo, "AzureSearch" torna-se "Azure" "Search". A predefinição é verdadeira.<br /><br /> generateNumberParts (tipo: bool)-faz com que o número subwords seja gerado. A predefinição é verdadeira.<br /><br /> catenateWords (tipo: bool)-faz com que o máximas execuções das partes do word para ser catenated, para o exemplo "Azure-pesquisa" torna-se "AzureSearch". A predefinição é falso.<br /><br /> catenateNumbers (tipo: bool)-causas máximo de execução das partes de número para ser catenated, por exemplo "1-2" torna-se "12". A predefinição é falso.<br /><br /> catenateAll (tipo: bool)-causas subword todas as partes para ser catenated, por exemplo, de "Azure-pesquisa-1" torna-se "AzureSearch1". A predefinição é falso.<br /><br /> splitOnCaseChange (tipo: bool) – se VERDADEIRO, divisões de palavras no caseChange, por exemplo "AzureSearch" torna-se "Azure" "Search". A predefinição é verdadeira.<br /><br /> preserveOriginal - causas palavras originais para ser mantidas e adicionado à lista de subword. A predefinição é falso.<br /><br /> splitOnNumerics (tipo: bool) – se VERDADEIRO, divisões números, por exemplo, "Azure1Search" torna-se "Azure", "1" de "pesquisar". A predefinição é verdadeira.<br /><br /> stemEnglishPossessive (tipo: bool) – à direita "do" para ser removido para cada subword as causas. A predefinição é verdadeira.<br /><br /> protectedWords (tipo: matriz de cadeia de caracteres)-Tokens para proteger contra a ser delimitados. A predefinição é uma lista vazia.|  

 <sup>1</sup> tipos de filtro de tokens são sempre o prefixo no código com "#Microsoft.Azure.Search", de modo a que o "ArabicNormalizationTokenFilter", na verdade, teria de ser especificado como "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Removemos o prefixo para reduzir a largura da tabela, mas não se esqueça de incluí-lo em seu código.  

> [!NOTE]
> Ele requer que configure o analisador personalizado não produzir tokens de mais de 300 carateres. Falha de indexação para documentos com esses tokens. Para cortá-los ou ignorá-las, utilize o **TruncateTokenFilter** e o **LengthTokenFilter** , respetivamente.


## <a name="see-also"></a>Consulte também  
 [REST do serviço de pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analisadores no Azure Search > exemplos](search-analyzers.md#examples)    
 [Criar índice &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
