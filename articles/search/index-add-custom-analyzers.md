---
title: Adicione analisadores personalizados aos campos de cordas
titleSuffix: Azure Cognitive Search
description: Configure tokenizers de texto e filtros de caracteres utilizados em consultas completas de pesquisa de texto da Pesquisa Cognitiva Azure.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
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
ms.openlocfilehash: f0d9576f5275bcfc061ce29740f8d85aff4ccfff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231089"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Adicione analisadores personalizados a campos de cordas num índice de pesquisa cognitiva Azure

Um *analisador personalizado* é um tipo específico de [analisador](search-analyzers.md) de texto que consiste numa combinação definida pelo utilizador de tokenizer existente e filtros opcionais. Ao combinar tokenizers e filtros de novas formas, pode personalizar o processamento de texto no motor de busca para obter resultados específicos. Por exemplo, pode criar um analisador personalizado com um *filtro de carvão* para remover a marcação HTML antes de as inputs de texto serem tokenizadas.

 Pode definir vários analisadores personalizados para variar a combinação de filtros, mas cada campo só pode usar um analisador para análise de indexação e um para análise de pesquisa. Para uma ilustração de como é um analisador de clientes, consulte o exemplo do [analisador personalizado.](search-analyzers.md#Custom-analyzer-example)

## <a name="overview"></a>Descrição geral

 O papel de um motor de [pesquisa de texto completo,](search-lucene-query-architecture.md)em termos simples, é processar e armazenar documentos de uma forma que permita uma consulta e recuperação eficientes. A um nível elevado, tudo se resume a extrair palavras importantes de documentos, colocando-as num índice, e, em seguida, usando o índice para encontrar documentos que correspondam às palavras de uma determinada consulta. O processo de extração de palavras de documentos e consultas de pesquisa chama-se *análise lexical.* Os componentes que realizam análises lexical são chamados *analisadores*.

 Na Pesquisa Cognitiva Azure, pode escolher entre um conjunto de analisadores agnósticos predefinidos na tabela [Analisadores](#AnalyzerTable) ou analisadores específicos da linguagem listados em analisadores de idiomas &#40;serviço de [pesquisa cognitiva Azure REST API&#41;](index-add-language-analyzers.md). Também tem uma opção para definir os seus próprios analisadores personalizados.  

 Um analisador personalizado permite-lhe assumir o controlo sobre o processo de conversão do texto em fichas indexáveis e pesquisáveis. É uma configuração definida pelo utilizador que consiste num único tokenizer predefinido, um ou mais filtros simbólicos e um ou mais filtros de carvão. O tokenizer é responsável por quebrar o texto em fichas, e os filtros de fichas para modificar tokens emitidos pelo tokenizer. Os filtros de carvão são aplicados para preparar texto de entrada antes de ser processado pelo tokenizer. Por exemplo, o filtro de carvão pode substituir certos caracteres ou símbolos.

 Os cenários populares habilitados por analisadores personalizados incluem:  

- Procura fonética. Adicione um filtro fonético para permitir a pesquisa com base no som de uma palavra, não como é escrito.  

- Desativar a análise lexical. Utilize o analisador de palavras-chave para criar campos pesquisáveis que não sejam analisados.  

- Procura rápida de prefixo/sufixo. Adicione o filtro de ficha edge N-grama aos prefixos de palavras para permitir a correspondência de prefixo rápido. Combine-o com o filtro de token invertido para fazer a correspondência do sufixo.  

- Tokenização personalizada. Por exemplo, use o tokenizer Whitespace para quebrar frases em tokens usando o espaço branco como um delimitador  

- ASCII dobrável. Adicione o filtro dobrável Standard ASCII para normalizar os diacríticos como ö ou ê em termos de pesquisa.  

  Esta página fornece uma lista de analisadores, tokenizers, filtros de token e filtros de carvão. Também pode encontrar uma descrição das alterações à definição do índice com um exemplo de utilização. Para obter mais informações sobre a tecnologia subjacente alavancada na implementação da Pesquisa Cognitiva Azure, consulte o resumo do [pacote de análise (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html). Por exemplo, de configurações de analisadores, consulte [Adicionar analisadores em Pesquisa Cognitiva Azure](search-analyzers.md#examples).

## <a name="validation-rules"></a>Regras de validação  
 Os nomes de analisadores, tokenizers, filtros de token e filtros de carvão têm de ser únicos e não podem ser os mesmos que qualquer um dos analisadores predefinidos, tokenizers, filtros de token ou filtros de carvão. Consulte a [Referência de Propriedade](#PropertyReference) para os nomes já utilizados.

## <a name="create-custom-analyzers"></a>Criar analisadores personalizados
 Pode definir analisadores personalizados no tempo de criação de índices. A sintaxe para especificar um analisador personalizado é descrita nesta secção. Também pode familiarizar-se com a sintaxe revendo definições de amostra em [Add analisadores em Pesquisa Cognitiva Azure](search-analyzers.md#examples).  

 Uma definição de analisador inclui um nome, um tipo, um ou mais filtros de carvão, um máximo de um tokenizer, e um ou mais filtros simbólicos para processamento pós-tokenização. Os filetes de carvão são aplicados antes da tokenização. Filtros de token e filtros de carvão são aplicados da esquerda para a direita.

 O `tokenizer_name` nome é o nome `token_filter_name_1` de `token_filter_name_2` um tokenizer, e `char_filter_name_1` são `char_filter_name_2` os nomes dos filtros simbólicos, e são os nomes dos filtros de carvão (ver os [Tokenizers,](#Tokenizers) [filtros token](#TokenFilters) e tabelas de filtros Char para valores válidos).

A definição de analisador é uma parte do índice maior. Consulte [a Create Index API](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter informações sobre o resto do índice.

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
>  Os analisadores personalizados que cria não estão expostos no portal Azure. A única maneira de adicionar um analisador personalizado é através de um código que faz chamadas para a API ao definir um índice.  

 Dentro de uma definição de índice, você pode colocar esta secção em qualquer lugar do corpo de um pedido de índice de criação, mas geralmente vai no final:  

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

As definições para filtros de carvão, tokenizers e filtros de fichas são adicionadas ao índice apenas se estiver a definir opções personalizadas. Para utilizar um filtro ou tokenizer existente como está, especifique-o pelo nome na definição de analisador.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testar analisadores personalizados

Pode utilizar a **operação Test Analyzer** na [API REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para ver como um analisador quebra o texto em fichas.

**Pedir**
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

## <a name="update-custom-analyzers"></a>Atualizar analisadores personalizados

Uma vez definido um analisador, um tokenizer, um filtro de fichas ou um filtro de carvão, não pode ser modificado. Os novos só podem ser adicionados a `allowIndexDowntime` um índice existente se a bandeira for definida como verdadeira no pedido de atualização do índice:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Esta operação desativa o seu índice durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. A disponibilidade de desempenho e escrita do índice pode ser prejudicada durante vários minutos após a atualização do índice, ou mais tempo para índices muito grandes, mas estes efeitos são temporários e eventualmente resolvem-se por si próprios.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Referência do analisador

As tabelas abaixo listam as propriedades de configuração para os analisadores, tokenizers, filtros de fichas e secção de filtro de carvão de uma definição de índice. A estrutura de um analisador, tokenizer ou filtro no seu índice é composta por estes atributos. Para obter informações sobre atribuição de valor, consulte a [Referência imobiliária](#PropertyReference).

### <a name="analyzers"></a>Analisadores

Para os analisadores, os atributos do índice variam dependendo do facto de estar a usar analisadores pré-definidos ou personalizados.

#### <a name="predefined-analyzers"></a>Analisadores predefinidos

|||  
|-|-|  
|Nome|Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres.|  
|Tipo|Tipo de analisador da lista de analisadores suportados. Consulte a coluna **analyzer_type** na tabela [Analisadores](#AnalyzerTable) abaixo.|  
|Opções|Devem ser opções válidas de um analisador predefinido listado na tabela [Analisadores](#AnalyzerTable) abaixo.|  

#### <a name="custom-analyzers"></a>Analisadores personalizados

|||  
|-|-|  
|Nome|Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres.|  
|Tipo|Deve ser "#Microsoft.Azure.Search.CustomAnalyzer".|  
|Filtros de CharFilters|Definir para um dos filtros de carvão predefinidos listados na tabela [Char Filters](#char-filters-reference) ou um filtro de carvão personalizado especificado na definição de índice.|  
|Tokenizer|Necessário. Definir para um dos tokenizers predefinidos listados na tabela [Tokenizers](#Tokenizers) abaixo ou um tokenizer personalizado especificado na definição de índice.|  
|Filtros de token|Definir para um dos filtros de token predefinidos listados na tabela de [filtros Token](#TokenFilters) ou um filtro de ficha personalizado especificado na definição de índice.|  

> [!NOTE]
> É necessário configurar o seu analisador personalizado para não produzir tokens com mais de 300 caracteres. A indexação falha em documentos com tais fichas. Para aparar ou ignorar, utilize o **Filtro TruncateToken** e o **LengthTokenFilter** respectivamente.  Verifique se os [**filtros Token**](#TokenFilters) têm referência.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtros char

 Um filtro de carvão é utilizado para preparar texto de entrada antes de ser processado pelo tokenizer. Por exemplo, podem substituir certos caracteres ou símbolos. Você pode ter vários filtros de carvão em um analisador personalizado. Os filtros de carvão são executados na ordem em que estão listados.  

|||  
|-|-|  
|Nome|Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres.|  
|Tipo|Tipo de filtro de carvão da lista de filtros de carvão suportados. Consulte **char_filter_type** coluna na tabela char [filters](#char-filters-reference) abaixo.|  
|Opções|Devem ser opções válidas de um dado tipo [filtros char.](#char-filters-reference)|  

### <a name="tokenizers"></a>Tokenizers

 Um tokenizer divide o texto contínuo numa sequência de tokens, como quebrar uma frase em palavras.  

 Pode especificar exatamente um tokenizer por analisador personalizado. Se precisar de mais do que um tokenizer, pode criar vários analisadores personalizados e atribuí-los numa base de campo a campo no seu esquema de índice.  
Um analisador personalizado pode usar um tokenizer predefinido com opções padrão ou personalizadas.  

|||  
|-|-|  
|Nome|Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres.|  
|Tipo|Nome tokenizer da lista de tokenizers suportados. Consulte **tokenizer_type** coluna na tabela [tokenizers](#Tokenizers) abaixo.|  
|Opções|Devem ser opções válidas de um dado tipo de tokenizer listado na tabela [Tokenizers](#Tokenizers) abaixo.|  

### <a name="token-filters"></a>Filtros de fichas

 Um filtro de fichas é usado para filtrar ou modificar as fichas geradas por um tokenizer. Por exemplo, pode especificar um filtro minúsculo que converte todos os caracteres em minúsculas.   
Pode ter vários filtros de fichanum analisador personalizado. Os filtros de token são executados na ordem em que estão listados.  

|||  
|-|-|  
|Nome|Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres.|  
|Tipo|Nome do filtro token da lista de filtros de fichas suportados. Consulte **token_filter_type** coluna na tabela dos [filtros Token](#TokenFilters) abaixo.|  
|Opções|Devem ser [filtros token](#TokenFilters) de um dado tipo de filtro de ficha.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Referência imobiliária

Esta secção fornece os valores válidos para atributos especificados na definição de um analisador personalizado, tokenizer, filtro de carvão ou filtro de fichas no seu índice. Os analisadores, os tokenizers e os filtros que são implementados usando o Apache Lucene têm ligações à documentação lucene API.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Referência de Analisadores Predefinidos

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrição e Opções**|  
|-|-|-|  
|[palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (o tipo só se aplica quando existem opções disponíveis) |Trata todo o conteúdo de um campo como um único símbolo. Isto é útil para dados como códigos postais, iDs e alguns nomes de produtos.|  
|[padrão](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|Analisador de Padrões|Flexivelmente separa o texto em termos através de um padrão de expressão regular.<br /><br /> **Opções**<br /><br /> minúscula (tipo: bool) - Determina se os termos são minúsculos. O padrão é verdade.<br /><br /> [padrão](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tipo: cadeia) - Um padrão de expressão regular para combinar separadores simbólicos. O padrão `\W+`é , que corresponde a caracteres não-palavra.<br /><br /> [bandeiras](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: corda) - Bandeiras de expressão regular. O padrão é uma corda vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMENTÁRIOS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> cronografias (tipo: string array) - Uma lista de palavras-stop. O padrão é uma lista vazia.|  
|[simples](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(o tipo só se aplica quando existem opções disponíveis) |Divide o texto em não-letras e converte-os em minúsculas. |  
|[padrão](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Também referido como standard.lucene)|StandardAnalyzer|Analisador Lucene padrão, composto pelo tokenizer padrão, filtro de minúsculas e filtro de paragem.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. O padrão é 255. Os tokens mais compridos do que o comprimento máximo são divididos. O comprimento máximo do símbolo que pode ser usado é de 300 caracteres.<br /><br /> cronografias (tipo: string array) - Uma lista de palavras-stop. O padrão é uma lista vazia.|  
|standardasciifolding.lucene|(o tipo só se aplica quando existem opções disponíveis) |Analisador padrão com filtro dobrável Ascii. |  
|[parar](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Divide o texto em não-letras, aplica os filtros de token minúsculoe e de palavra seleções.<br /><br /> **Opções**<br /><br /> cronografias (tipo: string array) - Uma lista de palavras-stop. O padrão é uma lista predefinida para inglês. |  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(o tipo só se aplica quando existem opções disponíveis) |Um analisador que usa o tokenizer whitespace. Tokens que têm mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de analisador são sempre pré-fixados em código com "#Microsoft.Azure.Search" de tal forma que "PatternAnalyzer" seria especificado como "#Microsoft.Azure.Search.PatternAnalyzer". Removemos o prefixo para a brevidade, mas o prefixo é exigido no seu código. 
 
O analyzer_type só é fornecido para analisadores que podem ser personalizados. Se não houver opções, como é o caso do analisador de palavras-chave, não existe um tipo associado #Microsoft.Azure.Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referência de filtros char

Na tabela abaixo, os filtros de caracteres que são implementados usando Apache Lucene estão ligados à documentação lucene API.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrição e Opções**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Um filtro de carvão que tenta despir as construções HTML.|  
|[mapeamento](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MapeadoFiltro Carcarchar|Um filtro de carvão que aplica mapeamentos definidos com a opção mapeamento. Matching é ganancioso (o padrão mais longo combinando num determinado ponto ganha). A substituição pode ser a corda vazia.<br /><br /> **Opções**<br /><br /> mapeamentos (tipo: string array) - Uma lista de mapeamentos do seguinte formato: "a=>b" (todas as ocorrências do personagem "a" são substituídas pelo personagem "b"). Necessário.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|Filtro PatternReplaceChar|Um filtro de carvão que substitui caracteres na cadeia de entrada. Usa uma expressão regular para identificar sequências de caracteres para preservar e um padrão de substituição para identificar caracteres para substituir. Por exemplo, texto de entrada = "aa bb aa\\bb", pattern="(aa) \s+(bb)" substituição="$1#$2", resultado = "aa#bb aa#bb".<br /><br /> **Opções**<br /><br /> padrão (tipo: corda) - Necessário.<br /><br /> substituição (tipo: corda) - Necessário.|  

 <sup>1</sup> Os tipos de filtro de char são sempre pré-fixados em código com "#Microsoft.Azure.Search" de tal forma que "MappingCharFilter" seria especificado como "#Microsoft.Azure.Search.MappingCharFilter. Removemos o prefixo para reduzir a largura da mesa, mas lembre-se de incluí-lo no seu código. Note que char_filter_type só está prevista para filtros que possam ser personalizados. Se não houver opções, como é o caso do html_strip, não existe um tipo de pesquisa associado #Microsoft.Azure.Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Referência de tokenizers

Na tabela abaixo, os tokenizers que são implementados usando Apache Lucene estão ligados à documentação lucene API.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrição e Opções**|  
|-|-|-|  
|[clássico](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizer baseado em gramática que é adequado para processar a maioria dos documentos de língua europeia.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. Predefinição: 255, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenize a entrada de uma borda em n-gramas de tamanho ou tamanho dado).<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Predefinição: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Predefinição: 2, máximo: 300. Deve ser maior que minGram.<br /><br /> tokenChars (tipo: string array) - Aulas de caracteres para manter nas fichas. Valores permitidos: <br />"carta", "dígito", "espaço branco", "pontuação", "símbolo". Incumprimentos a uma matriz vazia - mantém todos os caracteres. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|Palavra-chaveTokenizerV2|Emite toda a entrada como um único símbolo.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. Predefinição: 256, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos.|  
|[carta](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(o tipo só se aplica quando existem opções disponíveis)  |Divide texto em não-letras. Tokens que têm mais de 255 caracteres são divididos.|  
|[minúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(o tipo só se aplica quando existem opções disponíveis)  |Divide o texto em não-letras e converte-os em minúsculas. Tokens que têm mais de 255 caracteres são divididos.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide o texto usando regras específicas da linguagem.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token, predefinido: 255, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos. Tokens com mais de 300 caracteres são divididos pela primeira vez em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto maxTokenLength.<br /><br />isSearchTokenizer (tipo: bool) - Definido como verdadeiro se utilizado como tokenizer de pesquisa, definido como falso se utilizado como tokenizer indexante. <br /><br /> linguagem (tipo: cadeia) - Linguagem a utilizar, padrão "inglês". Os valores permitidos incluem:<br />"bangla", "búlgaro", "catalã", "chinêsSimplificado", "chinêsTradicional", "croata", "checo", "dinamarquês", "holandês", "inglês", "francês", "alemão", "grego", "gujarati", "hindi", "islandês", "indonésio", "italiano", "japonês", "kannada", "coreano", "malaio", "malaia" Alalam", "marathi", "norueguês Bokmaal", "polaco", "português", "portuguêsbrasileiro", "punjabi", "romeno", "russo", "sérvio", "sérvio", "eslovaco", "espanhol", "sueco", "tamil", "telugu", "tailandês", "ucraniano", "urdu", "vietnamita". |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide o texto usando regras específicas da linguagem e reduz as palavras às suas formas base<br /><br /> **Opções**<br /><br />maxTokenLength (tipo: int) - O comprimento máximo do token, predefinido: 255, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos. Tokens com mais de 300 caracteres são divididos pela primeira vez em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto maxTokenLength.<br /><br /> isSearchTokenizer (tipo: bool) - Definido como verdadeiro se utilizado como tokenizer de pesquisa, definido como falso se utilizado como tokenizer indexante.<br /><br /> linguagem (tipo: cadeia) - Linguagem a utilizar, padrão "inglês". Os valores permitidos incluem:<br />"árabe", "bangla", "búlgaro", "catalão", "croata", "checo", "dinamarquês", "holandês", "inglês", "estónio", "finlandês", "alemão", "grego", "gujarati", "hebraico", "hindi", "húngaro", "islandês", "indonésio", "italiano", "kannada", "letão", "lituano", "mala" Yy", "malayalam", "marathi", "norueguês Bokmaal", "polaco", "português", "portuguêsbrasileiro", "punjabi", "romeno", "russo", "sérvio", "sérvioLatino", "eslovaco", "esloveno", "espanhol", "sueco", "tamil", "telugu", "turco", "ucraniano", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenize a entrada em n-gramas do tamanho dado.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Predefinição: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Predefinição: 2, máximo: 300. Deve ser maior que minGram. <br /><br /> tokenChars (tipo: string array) - Aulas de caracteres para manter nas fichas. Valores permitidos: "letra", "dígito", "espaço branco", "pontuação", "símbolo". Incumprimentos a uma matriz vazia - mantém todos os caracteres. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer para hierarquias semelhantes a caminhos.<br /><br /> **Opções**<br /><br /> delimitador (tipo: cadeia) - Predefinição: '/.<br /><br /> substituição (tipo: cadeia) - Se estiver definido, substitui o carácter delimitador. Padrão o mesmo que o valor do delimitador.<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. Predefinição: 300, máximo: 300. Caminhos mais longos do que maxTokenLength são ignorados.<br /><br /> inverter (tipo: bool) - Se for verdade, gera token em ordem inversa. Predefinição: false.<br /><br /> skip (tipo: bool) - Fichas iniciais para saltar. A predefinição é 0.|  
|[padrão](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PadrãoTokenizer|Este tokenizer usa padrão regex combinando para construir fichas distintas.<br /><br /> **Opções**<br /><br /> [padrão](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (tipo: cadeia) - Padrão de expressão regular para combinar separadores simbólicos. O padrão `\W+`é , que corresponde a caracteres não-palavra. <br /><br /> [bandeiras](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: corda) - Bandeiras de expressão regular. O padrão é uma corda vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMENTÁRIOS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> grupo (tipo: int) - Que grupo extrair em fichas. O predefinido é -1 (split).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Quebra texto seguindo as regras de segmentação de [texto unicódigo](https://unicode.org/reports/tr29/).<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. Predefinição: 255, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokeniza urls e e-mails como um símbolo.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int) - O comprimento máximo do token. Predefinição: 255, máximo: 300. Os tokens mais compridos do que o comprimento máximo são divididos.|  
|[whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(o tipo só se aplica quando existem opções disponíveis) |Divide texto no espaço branco. Tokens que têm mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de tokenizer são sempre pré-fixados em código com "#Microsoft.Azure.Search" de tal forma que "ClassicTokenizer" seria especificado como "#Microsoft.Azure.Search.ClassicTokenizer". Removemos o prefixo para reduzir a largura da mesa, mas lembre-se de incluí-lo no seu código. Note que tokenizer_type só está prevista para tokenizers que podem ser personalizados. Se não houver opções, como é o caso do tokenizer da carta, não existe um tipo associado #Microsoft.Azure.Search.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referência de filtros de token

Na tabela abaixo, os filtros de fichas que são implementados usando Apache Lucene estão ligados à documentação lucene API.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrição e Opções**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Um filtro simbólico que aplica o normalizador árabe para normalizar a ortografia.|  
|[apóstrofo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Tira todos os caracteres após um apóstrofo (incluindo o próprio apóstrofo). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|Filtro AsciiFoldingToken|Converte caracteres unicódigo alfabéticos, numéricos e simbólicos que não estão nos primeiros 127 caracteres ASCII (o bloco Unicode "Básico" Latino) nos seus equivalentes ASCII, se existir.<br /><br /> **Opções**<br /><br /> preservarOriginal (tipo: bool) - Se for verdade, o símbolo original é mantido. O padrão é falso.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|Filtro CjkBigramToken|Forma bigrams de termos CJK que são gerados a partir de StandardTokenizer.<br /><br /> **Opções**<br /><br /> ignorarScripts (tipo: string array) - Scripts para ignorar. Os valores permitidos incluem: "han", "hiragana", "katakana", "hangul". O padrão é uma lista vazia.<br /><br /> outputUnigramas (tipo: bool) - Definir para verdade se quiser sempre obter unigramas e bigrams. O padrão é falso.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza as diferenças de largura do CJK. Dobra variantes ASCII de largura total nas variantes básicas equivalentes de Katakana em equivalente sacana. |  
|[clássico](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Remove os possuídores ingleses e pontos das siglas. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|Filtro CommonGramToken|Construa bigrams para termos frequentemente ocorridos durante a indexação. Os termos individuais também estão indexados, com os bigrams sobrepostos.<br /><br /> **Opções**<br /><br /> palavras comuns (tipo: string array) - O conjunto de palavras comuns. O padrão é uma lista vazia. Necessário.<br /><br /> ignorandoCaso (tipo: bool) - Se for verdade, combinar é caso insensível. O padrão é falso.<br /><br /> consultaMode (tipo: bool) - Gera bigrams e depois remove palavras comuns e termos individuais seguidos por uma palavra comum. O padrão é falso.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Decompõe palavras compostas encontradas em muitas línguas germânicas.<br /><br /> **Opções**<br /><br /> wordList (tipo: string array) - A lista de palavras para combinar. O padrão é uma lista vazia. Necessário.<br /><br /> minWordSize (tipo: int) - Apenas palavras mais longas do que esta são processadas. O padrão é 5.<br /><br /> minSubwordSize (tipo: int) - Apenas subpalavras mais compridas do que esta são saídas. O padrão é 2.<br /><br /> maxSubwordSize (tipo: int) - Apenas sub-palavras mais curtas do que esta são saídas. A predefinição é 15.<br /><br /> apenasLongestMatch (tipo: bool) - Adicione apenas a subpalavra mais longa correspondente à saída. O padrão é falso.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Gera n-gramas do tamanho(s) dado a partir da frente ou da parte de trás de um token de entrada.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Predefinição: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Predefinição: 2, máximo 300. Deve ser maior que minGram.<br /><br /> lado (tipo: cadeia) - Especifica de que lado da entrada o n-grama deve ser gerado. Valores permitidos: "frente", "costas" |  
|[elissão](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|Filtro ElisionToken|Remove elisões. Por exemplo, "l'avion" (o avião) é convertido em "avião" (avião).<br /><br /> **Opções**<br /><br /> artigos (tipo: string array) - Um conjunto de artigos a remover. O padrão é uma lista vazia. Se não houver uma lista de artigos definidos, por padrão todos os artigos franceses são removidos.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza os caracteres alemães de acordo com a heurística do algoritmo de [bola de neve germano2.](https://snowballstem.org/algorithms/german2/stemmer.html)|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza o texto em hindi para remover algumas diferenças nas variações ortográficas. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizaçãoTokenFilter|Normaliza a representação do texto unicode em línguas indianas.
|[manter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Um filtro simbólico que apenas mantém fichas com texto contido na lista de palavras especificada.<br /><br /> **Opções**<br /><br /> manterWords (tipo: string array) - Uma lista de palavras a manter. O padrão é uma lista vazia. Necessário.<br /><br /> manterWordsCase (tipo: bool) - Se for verdade, menos todas as palavras primeiro. O padrão é falso.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|PalavraMarkerTokenFilter|Marca termos como palavras-chave.<br /><br /> **Opções**<br /><br /> palavras-chave (tipo: string array) - Uma lista de palavras para marcar como palavras-chave. O padrão é uma lista vazia. Necessário.<br /><br /> ignoreCase (tipo: bool) - Se for verdade, abaixe todas as palavras primeiro. O padrão é falso.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Emite cada token de entrada duas vezes como palavra-chave e uma vez como palavra-chave. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Um filtro kstem de alto desempenho para inglês. |  
|[comprimento](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|Filtro LengthToken|Remove palavras demasiado longas ou curtas.<br /><br /> **Opções**<br /><br /> min (tipo: int) - O número mínimo. Padrão: 0, máximo: 300.<br /><br /> máx (tipo: int) - O número máximo. Predefinição: 300, máximo: 300.|  
|[limite](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita o número de fichas durante a indexação.<br /><br /> **Opções**<br /><br /> maxTokenCount (tipo: int) - Número máximo de fichas para produzir. A predefinição é 1.<br /><br /> consumirAllTokens (tipo: bool) - Se todas as fichas da entrada devem ser consumidas mesmo que o MaxTokenCount seja atingido. O padrão é falso.|  
|[minúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza o texto simbólico para minúscula. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Gera n-gramas do tamanho dado.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Predefinição: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Predefinição: 2, máximo 300. Deve ser maior que minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|Filtro PatternCaptureToken|Usa regexes java para emitir várias fichas, uma para cada grupo de captura em um ou mais padrões.<br /><br /> **Opções**<br /><br /> padrões (tipo: string array) - Uma lista de padrões que combinam com cada token. Necessário.<br /><br /> preservarOriginal (tipo: bool) - Definido para o verdadeiro retorno do token original, mesmo que um dos padrões corresponda, padrão: verdadeiro |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|Filtro PatternReplaceToken|Um filtro de ficha que aplica um padrão a cada token no fluxo, substituindo as ocorrências de correspondência com a corda de substituição especificada.<br /><br /> **Opções**<br /><br /> padrão (tipo: corda) - Necessário.<br /><br /> substituição (tipo: corda) - Necessário.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(o tipo só se aplica quando existem opções disponíveis) |Aplica normalização para persa. |  
|[fonética](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|Filtro Fonerkenken|Crie fichas para fósforos fonéticos.<br /><br /> **Opções**<br /><br /> codificador (tipo: cadeia) - Codificador fonético a utilizar. Os valores permitidos incluem: "metafone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "colónia", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Padrão: "metafone". Padrão é metafone.<br /><br /> Consulte o [codificador](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) para mais informações.<br /><br /> substituir (tipo: bool) - Verdadeiro se as fichas codificadas devem substituir as fichas originais, falsas se devem ser adicionadas como sinónimos. O padrão é verdade.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(o tipo só se aplica quando existem opções disponíveis)  |Transforma o fluxo simbólico de acordo com o [algoritmo de conagem porter.](https://tartarus.org/~martin/PorterStemmer/) |  
|[reverter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Inverte a corda simbólica. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza o uso dos caracteres escandinavos intercambiáveis. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Dobra personagens escandinavos åÅäææ->a e öÖøØ->o. Também discrimina o uso de vogais duplas aa, ae, ao, oe e oo, deixando apenas a primeira. |  
|[telha](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Cria combinações de fichas como um único símbolo.<br /><br /> **Opções**<br /><br /> maxShingleSize (tipo: int) - Predefinições para 2.<br /><br /> minShingleSize (tipo: int) - Predefinições para 2.<br /><br /> outputUnigramas (tipo: bool) - se for verdade, o fluxo de saída contém as fichas de entrada (unigramas) bem como as telhas. O padrão é verdade.<br /><br /> outputUnigramsIfNoShingles (tipo: bool) - Se for verdade, sobrepor-se ao comportamento da saídaUnigrams==falso para aqueles tempos em que não há telhas disponíveis. O padrão é falso.<br /><br /> tokenSeparator (tipo: corda) - A corda a utilizar ao juntar fichas adjacentes para formar uma telha. O padrão é " ".<br /><br /> filtroToken (tipo: corda) - A corda a inserir para cada posição em que não há símbolo. O padrão é "_".|  
|[bola de neve](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|Filtro SnowballToken|Filtro de token bola de neve.<br /><br /> **Opções**<br /><br /> língua (tipo: corda) - Valores permitidos incluem: "arménio", "basco", "catalão", "dinamarquês", "holandês", "inglês", "finlandês", "alemão", "alemão2", "húngaro", "italiano", "kp", "lovins", "norueguês", "porteiro", "português", "romeno", "russo", "espanhol", "sueco", "turco"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|Filtro SoraniNormalizationToken|Normaliza a representação do texto do Unicode do Texto Sorani.<br /><br /> **Opções**<br /><br /> Nenhum.|  
|stemmer|Filtro StemmerToken|Filtro de contudo específico da linguagem.<br /><br /> **Opções**<br /><br /> linguagem (tipo: cadeia) - Os valores permitidos incluem: <br /> -   ["árabe"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["arménio"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basco"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brasileiro"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "búlgaro"<br />-   ["Catalão"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["Checo"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["dinamarquês"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["holandês"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["DutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["inglês"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["Mínimo inglês"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["Inglês possessivo"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porteiro2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["Lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finlandês"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "ligeiro finlandês"<br />-   ["francês"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["francês claro"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["Mínimofrancês"](https://dl.acm.org/citation.cfm?id=318984)<br />- "galego"<br />- "mínimo galego"<br />-   ["alemão"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "mínimo alemão"<br />-   ["grego"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["húngaro"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["LightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonésio"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irlandês"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italiano"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["letão"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norueguês"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["LightNorueguês"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["MínimoNorueguês"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["LightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["MínimoNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["português"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["mínimoportuguês"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["PortugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romeno"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russo"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["LightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["espanhol"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["luz espanhola"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sueco"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["turco"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|Filtro StemmerOverrideToken|Quaisquer termos dicionário-Stemmed são marcados como palavras-chave, o que impede a desafogada na cadeia. Deve ser colocado antes de quaisquer filtros de desengato.<br /><br /> **Opções**<br /><br /> regras (tipo: string array) - Regras de detenção no seguinte formato "palavra => caule" por exemplo "correu => executar". O padrão é uma lista vazia.  Necessário.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Remove as palavras de paragem de um fluxo de fichas. Por padrão, o filtro utiliza uma lista de palavras de paragem predefinida para inglês.<br /><br /> **Opções**<br /><br /> cronografias (tipo: string array) - Uma lista de palavras-stop. Não pode ser especificado se for especificado um stopwordsList.<br /><br /> stopwordsList (tipo: cadeia) - Uma lista predefinida de cronografias. Não é possível especificar se as palavras-stop forem especificadas. Valores permitidos incluem: "árabe", "arménio", "basco", "brasileiro", "búlgaro", "catalão", "checo", "dinamarquês", "holandês", "inglês", "finlandês", "francês", "galego", "alemão", "grego", "hindi", "húngaro", "indonésio", "irlandês", "italiano", "letão", "norueguês", "persa", "português", "romeno", "russo", "sorani", "espanhol", "sueco", "tailandês", "turco", "inglês". Não é possível especificar se as palavras-stop forem especificadas. <br /><br /> ignorandoCaso (tipo: bool) - Se for verdade, todas as palavras são minúsculas primeiro. O padrão é falso.<br /><br /> removerTrailing (tipo: bool) - Se for verdade, ignore o último termo de pesquisa se for uma palavra de paragem. O padrão é verdade.
|[sinónimo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|Filtro SinonymToken|Corresponde a sinónimos de palavras simples ou multi num fluxo simbólico.<br /><br /> **Opções**<br /><br /> sinónimos (tipo: string array) - Necessário. Lista de sinónimos num dos dois formatos seguintes:<br /><br /> -incrível, inacreditável, fabuloso => incrível - todos os termos do lado esquerdo de => símbolo são substituídos por todos os termos do seu lado direito.<br /><br /> - incrível, inacreditável, fabuloso, incrível - Uma lista separada de vírinas de palavras equivalentes. Detete a opção de expansão para alterar a forma como esta lista é interpretada.<br /><br /> ignorandoCase (tipo: bool) - Entrada de dobras de caso para correspondência. O padrão é falso.<br /><br /> expandir (tipo: bool) - Se for verdade, todas as palavras na lista de sinónimos (se = = notação> não forem usadas) mapa uns aos outros. <br />A seguinte lista: incrível, inacreditável, fabuloso, incrível equivale a: incrível, inacreditável, fabuloso, incrível => incrível, inacreditável, fabuloso, incrível<br /><br />- Se for falso, a seguinte lista: incrível, inacreditável, fabulosa, incrível, incrível equivale a: incrível, inacreditável, fabuloso, incrível => incrível.|  
|[aparar](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Aparas que conduzem e seguem o espaço branco a partir de fichas. |  
|[truncate](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|Filtro TruncateToken|Trunca os termos num comprimento específico.<br /><br /> **Opções**<br /><br /> comprimento (tipo: int) - Padrão: 300, máximo: 300. Necessário.|  
|[único](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|Filtro ExclusivoToken|Filtra fichas com o mesmo texto que o token anterior.<br /><br /> **Opções**<br /><br /> apenasOnSamePosition (tipo: bool) - Se definido, remova os duplicados apenas na mesma posição. O padrão é verdade.|  
|[maiúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(o tipo só se aplica quando existem opções disponíveis)  |Normaliza o texto simbólico para o caso superior. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|Filtro WordDelimiterToken|Divide as palavras em subpalavras e executa transformações opcionais em grupos de sub-palavras.<br /><br /> **Opções**<br /><br /> geraWordParts (tipo: bool) - Faz com que partes das palavras sejam geradas, por exemplo "AzureSearch" torna-se "Azure" "Search". O padrão é verdade.<br /><br /> gerarNumberParts (tipo: bool) - Faz com que as sub-palavras de número sejam geradas. O padrão é verdade.<br /><br /> catenatoWords (tipo: bool) - Faz com que as máximas correções de peças de palavra sejam categorizadas, por exemplo "Azure-Search" torna-se "AzureSearch". O padrão é falso.<br /><br /> catenatoSNúmeros (tipo: bool) - Faz com que as corridas máximas das peças numéricas sejam categorizadas, por exemplo "1-2" torna-se "12". O padrão é falso.<br /><br /> catenatoAll (tipo: bool) - Faz com que todas as peças de sub-palavra sejam categorizadas, por exemplo"Azure-Search-1" torna-se "AzureSearch1". O padrão é falso.<br /><br /> splitOnCaseChange (tipo: bool) - Se for verdade, divide palavras no casoChange, por exemplo "AzureSearch" torna-se "Azure" "Search". O padrão é verdade.<br /><br /> preservarOriginal - Faz com que as palavras originais sejam preservadas e adicionadas à lista de sub-palavras. O padrão é falso.<br /><br /> splitOnNumerics (tipo: bool) - Se for verdade, divide-se em números, por exemplo "Azure1Search" torna-se "Azure" "1" "Search". O padrão é verdade.<br /><br /> stemEnglishPossessive (tipo: bool) - Faz com que os "s" de trailing sejam removidos para cada subpalavra. O padrão é verdade.<br /><br /> palavras protegidas (tipo: string array) - Tokens para proteger de ser delimitado. O padrão é uma lista vazia.|  

 <sup>1</sup> Os tipos de filtros de token são sempre pré-fixados em código com "#Microsoft.Azure.Search" de tal forma que o "Filtro DeNormalização Token" seria especificado como "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Removemos o prefixo para reduzir a largura da mesa, mas lembre-se de incluí-lo no seu código.  


## <a name="see-also"></a>Consulte também  
 [APIs de pesquisa cognitiva azure](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analisadores em Pesquisa Cognitiva Azure > Exemplos](search-analyzers.md#examples)    
 [Criar índice &#40;&#41;de API de pesquisa cognitiva de pesquisa cognitiva](https://docs.microsoft.com/rest/api/searchservice/create-index)  
