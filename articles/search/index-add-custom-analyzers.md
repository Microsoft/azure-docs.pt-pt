---
title: Adicione analisadores personalizados a campos de cordas
titleSuffix: Azure Cognitive Search
description: Configure os tokenizers de texto e os filtros de caracteres para realizar a análise de texto em cadeias durante a indexação e consultas.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 831e57a68c79c245b96baec0fc3d062c4c9112c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604445"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Adicione analisadores personalizados a campos de cordas num índice de pesquisa cognitiva Azure

Um *analisador personalizado* é uma combinação de tokenizer, um ou mais filtros simbólicos, e um ou mais filtros de caracteres que você define no índice de pesquisa, e, em seguida, referência em definições de campo que requerem análise personalizada. O tokenizer é responsável por quebrar texto em fichas, e os filtros simbólicos para modificar fichas emitidas pelo tokenizer. Os filtros de caracteres preparam o texto de entrada antes de ser processado pelo tokenizer. 

Um analisador personalizado dá-lhe controlo sobre o processo de conversão do texto em fichas indexáveis e pes pespáveis, permitindo-lhe escolher quais tipos de análise ou filtragem para invocar, e a ordem em que ocorrem. Se quiser utilizar um analisador incorporado com opções personalizadas, como alterar o maxTokenLength na Standard, criaria um analisador personalizado, com um nome definido pelo utilizador, para definir essas opções.

As situações em que os analisadores personalizados podem ser úteis incluem:

- Utilizando filtros de caracteres para remover a marcação HTML antes de as entradas de texto serem tokenizadas, ou substituir certos caracteres ou símbolos.

- Procura fonética. Adicione um filtro fonético para permitir a pesquisa com base no som de uma palavra, não como é escrita.  

- Desative a análise lexical. Utilize o analisador de palavras-chave para criar campos pes pesjáveis que não sejam analisados.  

- Pesquisa rápida de prefixo/sufixo. Adicione o filtro de token Edge N-gram a prefixos de palavras de índice para permitir uma rápida correspondência de prefixos. Combine-o com o filtro de token inverso para fazer a correspondência de sufixo.  

- Tokenização personalizada. Por exemplo, use o tokenizer Whitespace para quebrar frases em fichas usando o espaço branco como um delimitador  

- ASCII dobrável. Adicione o filtro de dobragem STANDARD ASCII para normalizar diacríticos como ö ou ê em termos de pesquisa.  

Para criar um analisador personalizado, especifique-o na secção "analisadores" de um índice no momento do design e, em seguida, refira-o em campos pesquisáveis, Edm.String usando a propriedade "analisador" ou o par "indexAnalyzer" e "searchAnalyzer".

> [!NOTE]  
> Os analisadores personalizados que cria não estão expostos no portal Azure. A única maneira de adicionar um analisador personalizado é através de código que define um índice. 

## <a name="create-a-custom-analyzer"></a>Criar um analisador personalizado

Uma definição de analisador inclui um nome, tipo, um ou mais filtros de caracteres, um máximo de um tokenizer, e um ou mais filtros simbólicos para o processamento pós-tokenização. Os filtros de caracteres são aplicados antes da tokenização. Filtros de token e filtros de caracteres são aplicados da esquerda para a direita.

- Os nomes de um analisador personalizado devem ser únicos e não podem ser os mesmos que qualquer um dos analisadores incorporados, tokenizers, filtros de fichas ou filtros de caracteres. Só deve conter letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos, e está limitado a 128 caracteres. 

- O tipo deve ser #Microsoft.Azure.Search.CustomAnalyzer.

- "charFiltros" pode ser um ou mais filtros de Filtros de [Caracteres, processados](#CharFilter)antes da tokenização, na ordem fornecida. Alguns filtros de caracteres têm opções, que podem ser definidas numa secção "charFilter" Os filtros de caracteres são opcionais.

- "Tokenizer" é exatamente um [Tokenizer.](#tokenizers) É necessário um valor. Se precisar de mais de um tokenizer, pode criar vários analisadores personalizados e atribuí-los numa base de campo a campo no seu esquema de índice.

- Os "tokenFilters" podem ser um ou mais filtros de [Filtros Token, processados](#TokenFilters)após a tokenização, na ordem fornecida. Para filtros simbólicos que tenham opções, adicione uma secção "tokenFilter" para especificar a configuração. Os filtros token são opcionais.

Os analisadores não devem produzir fichas com mais de 300 caracteres, ou a indexação falhará. Para aparar um token longo ou para os excluir, utilize o **TruncateTokenFilter** e o **LengthTokenFilter,** respectivamente. Consulte [**os filtros Token**](#TokenFilters) para obter referência.

```json
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

Dentro de uma definição de índice, você pode colocar esta secção em qualquer lugar do corpo de um pedido de índice de criação, mas geralmente vai no final:  

```json
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

A definição de analisador é uma parte do índice maior. Definições para filtros de carvão, tokenizers e filtros de fichas só são adicionadas ao índice se estiver a definir opções personalizadas. Para utilizar um filtro ou tokenizer existente como-is, especifique-o pelo nome na definição do analisador. Para obter mais informações, consulte [Criar Índice (REST)](/rest/api/searchservice/create-index). Para mais exemplos, consulte [adicionar analisadores em Azure Cognitive Search](search-analyzers.md#examples).

## <a name="test-custom-analyzers"></a>Testar analisadores personalizados

Pode utilizar o [Analisador de Teste (REST)](/rest/api/searchservice/test-analyzer) para ver como um analisador quebra o texto dado em fichas.

**Pedir**

```http
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```

**Response**

```http
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

Uma vez definido um analisador, um tokenizer, um filtro simbólico ou um filtro de caracteres, não pode ser modificado. Os novos só podem ser adicionados a um índice existente se a `allowIndexDowntime` bandeira for definida como verdadeira no pedido de atualização do índice:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Esta operação retira o seu índice offline durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. A disponibilidade de desempenho e de escrita do índice pode ser prejudicada por vários minutos após a atualização do índice, ou mais tempo para índices muito grandes, mas estes efeitos são temporários e eventualmente resolvem-se por si próprios.

<a name="built-in-analyzers"></a>

## <a name="built-in-analyzers"></a>Analisadores incorporados

Se quiser utilizar um analisador incorporado com opções personalizadas, criar um analisador personalizado é o mecanismo pelo qual especifica essas opções. Em contraste, para usar um analisador incorporado como está, basta [fazê-lo referenciar pelo nome](search-analyzers.md#how-to-specify-analyzers) na definição de campo.

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrição e Opções**|  
|-----------------|-------------------------------|---------------------------|  
|[palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (o tipo só se aplica quando as opções estão disponíveis) |Trata todo o conteúdo de um campo como um único símbolo. Isto é útil para dados como códigos postais, IDs e alguns nomes de produtos.|  
|[padrão](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PadrãoAnalyzer|Flexivelmente separa o texto em termos através de um padrão de expressão regular. </br></br>**Opções** </br></br>minúscula (tipo: bool) - Determina se os termos são mais baixos. O padrão é verdadeiro. </br></br>[padrão](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (tipo: corda) - Um padrão de expressão regular para combinar com separadores simbólicos. O padrão é `\W+` , que corresponde a caracteres não-palavra. </br></br>[bandeiras](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: corda) - Bandeiras de expressão regulares. O padrão é uma corda vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMENTÁRIOS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>palavras de ordem (tipo: matriz de cordas) - Uma lista de palavras de ordem. O padrão é uma lista vazia.|  
|[simples](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(o tipo só se aplica quando as opções estão disponíveis) |Divide o texto em não-letras e converte-os em minúsculas. |  
|[padrão](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) </br>(Também referido como standard.lucene)|StandardAnalyzer|Analisador padrão Lucene, composto pelo tokenizer padrão, filtro minúsculo e filtro stop. </br></br>**Opções** </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token. O padrão é 255. As fichas mais compridas do que o comprimento máximo são divididas. O comprimento máximo do token que pode ser usado é de 300 caracteres. </br></br>palavras de ordem (tipo: matriz de cordas) - Uma lista de palavras de ordem. O padrão é uma lista vazia.|  
|standardasciifolding.lucene|(o tipo só se aplica quando as opções estão disponíveis) |Analisador padrão com filtro dobrável Ascii. |  
|[parar](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Divide o texto em não-letras, aplica os filtros de token minúsculos e de stopword. </br></br>**Opções** </br></br>palavras de ordem (tipo: matriz de cordas) - Uma lista de palavras de ordem. O padrão é uma lista predefinida para inglês. |  
|[espaço branco](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(o tipo só se aplica quando as opções estão disponíveis) |Um analisador que usa o tokenizer whitespace. Tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de analisador são sempre prefixados em código com "#Microsoft.Azure.Search" de modo a que "PatternAnalyzer" seja especificado como "#Microsoft.Azure.Search.PatternAnalyzer". Removemos o prefixo para a brevidade, mas o prefixo é necessário no seu código.

O analyzer_type é fornecido apenas para analisadores que podem ser personalizados. Se não houver opções, como é o caso do analisador de palavras-chave, não existe #Microsoft.Azure.Search type.

<a name="CharFilter"></a>

## <a name="character-filters"></a>Filtros de caracteres

Na tabela abaixo, os filtros de caracteres que são implementados usando Apache Lucene estão ligados à documentação da API Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrição e Opções**|  
|--------------------|---------------------------------|---------------------------|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Um filtro de carvão que tenta desmontar as construções HTML.|  
|[mapeamento](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Um filtro de carvão que aplica mapeamentos definidos com a opção de mapeamento. A correspondência é gananciosa (o padrão mais longo que combina num determinado ponto ganha). A substituição pode ser a corda vazia.  </br></br>**Opções**  </br></br> mapeamentos (tipo: matriz de cordas) - Uma lista de mapeamentos do seguinte formato: "a=>b" (todas as ocorrências do personagem "a" são substituídas pelo caractere "b"). Obrigatório.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PadrõesReplaceCharFilter|Um filtro de carvão que substitui os caracteres na cadeia de entrada. Usa uma expressão regular para identificar sequências de caracteres para preservar e um padrão de substituição para identificar caracteres para substituir. Por exemplo, texto de entrada = "aa bb aa bb", pattern="(aa) \\ \s+(bb)" replacement="$1#$2", resultado = "aa#bb aa#bb".  </br></br>**Opções**  </br></br>padrão (tipo: corda) - Obrigatório.  </br></br>substituição (tipo: cadeia) - Obrigatório.|  

 <sup>1</sup> Os tipos de filtro de char estão sempre pré-fixados em código com "#Microsoft.Azure.Search" de modo a que "MappingCharFilter" seja especificado como "#Microsoft.Azure.Search.MappingCharFilter. Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo no seu código. Note que char_filter_type só é fornecido para filtros que podem ser personalizados. Se não houver opções, como é o caso html_strip, não existe #Microsoft.Azure.Search type.

<a name="tokenizers"></a>

## <a name="tokenizers"></a>Tokenizers

Um tokenizer divide o texto contínuo numa sequência de fichas, como quebrar uma frase em palavras. Na tabela abaixo, os tokenizers que são implementados usando Apache Lucene estão ligados à documentação da API lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrição e Opções**|  
|------------------|-------------------------------|---------------------------|  
|[clássica](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Tokenizer baseado em gramática que é adequado para o processamento da maioria dos documentos de língua europeia.  </br></br>**Opções**  </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token. Padrão: 255, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes a entrada de uma borda em n-gramas de tamanho determinado( s).  </br></br> **Opções**  </br></br>minGram (tipo: int) - Padrão: 1, máximo: 300.  </br></br>maxGram (tipo: int) - Padrão: 2, máximo: 300. Deve ser maior que o minGram.  </br></br>tokenChars (tipo: matriz de cordas) - Classes de caracteres para manter nos tokens. Valores permitidos: </br>"letra", "dígito", "espaço em branco", "pontuação", "símbolo". Predefinições para uma matriz vazia - mantém todos os caracteres. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|Palavras-chaveTokenizerV2|Emite toda a entrada como um único símbolo.  </br></br>**Opções**  </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token. Padrão: 256, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas.|  
|[carta](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Divide texto em não-letras. Tokens com mais de 255 caracteres são divididos.|  
|[minúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Divide o texto em não-letras e converte-os em minúsculas. Tokens com mais de 255 caracteres são divididos.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide o texto usando regras específicas da linguagem.  </br></br>**Opções**  </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token, padrão: 255, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas. Tokens com mais de 300 caracteres são divididos pela primeira vez em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto maxTokenLength.  </br></br>isSearchTokenizer (tipo: bool) - Definido como verdadeiro se usado como tokenizer de pesquisa, definido como falso se usado como o tokenizer de indexação. </br></br>idioma (tipo: cadeia) - Idioma a utilizar, padrão "inglês". Os valores permitidos incluem: </br>"bangla", "búlgaro", "catalão", "chinêss", "chinês", "chinês", "croata", "checo", "dinamarquês", "holandês", "francês", "alemão", "grego", "gujarati", "hindi", "islandês", "indonésio", "italiano", "japonês", "kannada", "coreano", "malaio", "malaio"alam", "marathi", "norueguêsBokmaal", "polaco", "português", "português", "portuguêsBrazil", "punjabi", "romeno", "russo", "sérvioCyrillic", "sérvio", "sérvio", "esloveno", "espanhol", "sueco", "tamil", "telugu", "thai", "ucraniano", "urdu", "vietnamita". |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide texto usando regras específicas da linguagem e reduz as palavras às suas formas de base </br></br>**Opções** </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token, padrão: 255, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas. Tokens com mais de 300 caracteres são divididos pela primeira vez em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto maxTokenLength. </br></br> isSearchTokenizer (tipo: bool) - Definido como verdadeiro se usado como tokenizer de pesquisa, definido como falso se usado como o tokenizer de indexação. </br></br>idioma (tipo: cadeia) - Idioma a utilizar, padrão "inglês". Os valores permitidos incluem: </br>"árabe", "bangla", "búlgaro", "catalão", "croata", "checo", "dinamarquês", "holandês", "holandês", "estónio", "finlandês", "francês", "grego", "gujarati", "hebraico", "hindi", "húngaro", "islandês", "indonésio", "italiano", "kannada", "letão", "lituano", "malaio", "malaia", "marathi", "norueguêsBokmaal", "polaco", "português", "português", "punjabi", "romeno", "russo", "sérvio", "sérvio", "eslovaco", "esloveno", "espanhol", "sueco", "tamil", "telugu", "turco", "ucraniano", "urdu". |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes a entrada em n-gramas do tamanho ou tamanho dado. </br></br>**Opções** </br></br>minGram (tipo: int) - Padrão: 1, máximo: 300. </br></br>maxGram (tipo: int) - Padrão: 2, máximo: 300. Deve ser maior que o minGram. </br></br>tokenChars (tipo: matriz de cordas) - Classes de caracteres para manter nos tokens. Valores permitidos: "letra", "dígito", "espaço em branco", "pontuação", "símbolo". Predefinições para uma matriz vazia - mantém todos os caracteres. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer para hierarquias semelhantes a caminhos. **Opções** </br></br>delimiter (tipo: cadeia) - Padrão: '/. </br></br>substituição (tipo: cadeia) - Se for definido, substitui o carácter delimiter. Padrão igual ao valor do delimiter. </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token. Padrão: 300, máximo: 300. Os caminhos mais longos do que o maxTokenLength são ignorados. </br></br>reverso (tipo: bool) - Se for verdade, gera token em ordem inversa. Predefinição: false. </br></br>skip (tipo: bool) - Fichas iniciais a saltar. A predefinição é 0.|  
|[padrão](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PadrãoTokenizer|Este tokenizer usa padrão regex combinando para construir fichas distintas. </br></br>**Opções** </br></br> [padrão](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (tipo: corda) - Padrão de expressão regular para combinar com separadores simbólicos. O padrão é `\W+` , que corresponde a caracteres não-palavra. </br></br>[bandeiras](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: corda) - Bandeiras de expressão regulares. O padrão é uma corda vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, COMENTÁRIOS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES </br></br>grupo (tipo: int) - Que grupo extrair em fichas. O padrão é -1 (divisão).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Quebra o texto seguindo as [regras de Segmentação de Texto do Unicode](https://unicode.org/reports/tr29/). </br></br>**Opções** </br></br>maxTokenLength (tipo: int) - O comprimento máximo do token. Padrão: 255, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes urls e e-mails como um símbolo. </br></br>**Opções** </br></br> maxTokenLength (tipo: int) - O comprimento máximo do token. Padrão: 255, máximo: 300. As fichas mais compridas do que o comprimento máximo são divididas.|  
|[espaço branco](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(o tipo só se aplica quando as opções estão disponíveis) |Divide texto no espaço branco. Tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> Os tipos de tokenizer são sempre prefixados em código com "#Microsoft.Azure.Search" de tal forma que "ClassicTokenizer" seria realmente especificado como "#Microsoft.Azure.Search.ClassicTokenizer". Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo no seu código. Note que tokenizer_type é fornecido apenas para tokenizers que podem ser personalizados. Se não houver opções, como é o caso do tokenizer de carta, não existe #Microsoft.Azure.Search type.

<a name="TokenFilters"></a>

## <a name="token-filters"></a>Filtros token

Um filtro de ficha é utilizado para filtrar ou modificar os tokens gerados por um tokenizer. Por exemplo, pode especificar um filtro minúsculo que converte todos os caracteres em minúsculas. Pode ter vários filtros simbólicos num analisador personalizado. Os filtros token funcionam na ordem em que estão listados. 

Na tabela abaixo, os filtros simbólicos que são implementados usando o Apache Lucene estão ligados à documentação da API lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrição e Opções**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Um filtro simbólico que aplica o normalizador árabe para normalizar a ortografia.|  
|[apóstrofo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Tira todos os caracteres após um apóstrofo (incluindo o próprio apóstrofe). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converte caracteres unicócnicos alfabéticos, numéricos e simbólicos que não estão nos primeiros 127 caracteres ASCII (o bloco Unicode "Basic Latin" nos seus equivalentes ASCII, se existirem.<br /><br /> **Opções**<br /><br /> preservar aOriginal (tipo: bool) - Se for verdade, o token original é mantido. O padrão é falso.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Forma bigrams de termos CJK que são gerados a partir de StandardTokenizer.<br /><br /> **Opções**<br /><br /> ignoreScripts (tipo: matriz de cordas) - Scripts a ignorar. Os valores permitidos incluem: "han", "hiragana", "katakana", "hangul". O padrão é uma lista vazia.<br /><br /> outputUnigrams (tipo: bool) - Definido como verdadeiro se sempre quiser fazer produção tanto de unigramas como de bigrams. O padrão é falso.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza as diferenças de largura do CJK. Dobra as variantes ASCII de largura total nas variantes de Katakana em latim e meia largura equivalentes para o kana equivalente. |  
|[clássica](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Remove os possessivos ingleses e pontos das siglas. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Construa bigrams para termos que ocorrem frequentemente durante a indexação. Os termos únicos ainda estão indexados também, com os bigrams sobrepostos.<br /><br /> **Opções**<br /><br /> commonWords (tipo: matriz de cordas) - O conjunto de palavras comuns. O padrão é uma lista vazia. Obrigatório.<br /><br /> ignoreCase (tipo: bool) - Se for verdade, combinar é caso insensível. O padrão é falso.<br /><br /> queryMode (tipo: bool) - Gera bigrams em seguida, remove palavras comuns e termos únicos seguidos por uma palavra comum. O padrão é falso.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|Decompõe palavras compostas encontradas em muitas línguas germânicas.<br /><br /> **Opções**<br /><br /> wordList (tipo: matriz de cordas) - A lista de palavras a combinar. O padrão é uma lista vazia. Obrigatório.<br /><br /> minWordSize (tipo: int) - Apenas palavras mais longas do que esta ser processada. O padrão é 5.<br /><br /> minSubwordSize (tipo: int) - Apenas as sub-palavras mais compridas do que esta são outputadas. O padrão é 2.<br /><br /> maxSubwordSize (tipo: int) - Apenas as sub-palavras mais curtas do que esta são outputadas. A predefinição é 15.<br /><br /> onlyLongestMatch (tipo: bool) - Adicione apenas a sub-palavra de correspondência mais longa à saída. O padrão é falso.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgenGramTokenFilterV2|Gera n-gramas do tamanho(s) dado a partir da frente ou na parte de trás de um token de entrada.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Padrão: 2, máximo 300. Deve ser maior que o minGram.<br /><br /> lado (tipo: cadeia) - Especifica de que lado da entrada o n-grama deve ser gerado. Valores permitidos: "frontal", "back" |  
|[elisão](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Remove elisões. Por exemplo, "l'avion" (o avião) é convertido em "avion" (avião).<br /><br /> **Opções**<br /><br /> artigos (tipo: matriz de cordas) - Um conjunto de artigos a remover. O padrão é uma lista vazia. Se não houver uma lista de artigos definidos, por defeito todos os artigos franceses são removidos.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza os caracteres alemães de acordo com a heurística do [algoritmo de bola de neve alemão2.](https://snowballstem.org/algorithms/german2/stemmer.html)|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza o texto em hindi para remover algumas diferenças nas variações ortográficas. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliza a representação do texto unico em línguas indianas.
|[manter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Um filtro simbólico que só guarda fichas com texto contido na lista de palavras especificada.<br /><br /> **Opções**<br /><br /> keepWords (tipo: matriz de cordas) - Uma lista de palavras a manter. O padrão é uma lista vazia. Obrigatório.<br /><br /> keepWordsCase (tipo: bool) - Se for verdade, minúsculas todas as palavras primeiro. O padrão é falso.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|Palavra-chaveMarkrTokenFiltro|Marca termos como palavras-chave.<br /><br /> **Opções**<br /><br /> palavras-chave (tipo: matriz de cordas) - Uma lista de palavras para marcar como palavras-chave. O padrão é uma lista vazia. Obrigatório.<br /><br /> ignoreCase (tipo: bool) - Se for verdade, minúscula todas as palavras primeiro. O padrão é falso.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Emite cada token de entrada duas vezes como palavra-chave e uma vez como palavra-chave. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Um filtro kstem de alto desempenho para inglês. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|ComprimentoTokenFilter|Remove palavras demasiado longas ou demasiado curtas.<br /><br /> **Opções**<br /><br /> min (tipo: int) - O número mínimo. Predefinição: 0, máximo: 300.<br /><br /> max (tipo: int) - O número máximo. Padrão: 300, máximo: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita o número de fichas durante a indexação.<br /><br /> **Opções**<br /><br /> maxTokenCount (tipo: int) - Número máximo de fichas para produzir. A predefinição é 1.<br /><br /> consumirAllTokens (tipo: bool) - Se todos os tokens da entrada devem ser consumidos mesmo que o maxTokenCount seja atingido. O padrão é falso.|  
|[minúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza o texto simbólico para o caso mais baixo. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Gera n-gramas do tamanho(s) dado.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) - Padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) - Padrão: 2, máximo 300. Deve ser maior que o minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PadrõesCaptureTokenFilter|Usa os regexes de Java para emitir vários tokens, um para cada grupo de captura em um ou mais padrões.<br /><br /> **Opções**<br /><br /> padrões (tipo: matriz de cordas) - Uma lista de padrões que combinam com cada token. Obrigatório.<br /><br /> preserveOriginal (tipo: bool) - Definido para ser verdadeiro para devolver o token original mesmo que um dos padrões corresponda, padrão: verdadeiro |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PadrõesReplaceTokenFilter|Um filtro de ficha que aplica um padrão a cada símbolo no fluxo, substituindo as ocorrências de correspondência com a cadeia de substituição especificada.<br /><br /> **Opções**<br /><br /> padrão (tipo: corda) - Obrigatório.<br /><br /> substituição (tipo: cadeia) - Obrigatório.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(o tipo só se aplica quando as opções estão disponíveis) |Aplica normalização para persa. |  
|[fonética](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|FonéticoTokenFilter|Crie fichas para fósforos fonéticos.<br /><br /> **Opções**<br /><br /> codificadora (tipo: cadeia) - Codificadora fonética para usar. Os valores permitidos incluem: "metafone", "doubleMetaphone", "soundex", "refinadoSoundex", "caverphone1", "caverphone2", "colónia", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Predefinição: "metafone". O padrão é o metafone.<br /><br /> Consulte [o codificar](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) para mais informações.<br /><br /> substituir (tipo: bool) - Verdade se as fichas codificadas devem substituir as fichas originais, falsas se forem adicionadas como sinónimos. O padrão é verdadeiro.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Transforma o fluxo simbólico de acordo com o [algoritmo de cauling do Porter.](https://tartarus.org/~martin/PorterStemmer/) |  
|[inverter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Inverte a corda simbólica. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza o uso dos caracteres escandinavos intercambiáveis. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Dobra os caracteres escandinavos åÅäæÄÆ->a e ööøØ->o. Também discrimina o uso de vogais duplas aa, ae, ao, oe e oo, deixando apenas a primeira. |  
|[telha](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Cria combinações de fichas como um único símbolo.<br /><br /> **Opções**<br /><br /> maxShingleSize (tipo: int) - Predefinição a 2.<br /><br /> minShingleSize (tipo: int) - Predefinição a 2.<br /><br /> outputUnigrams (tipo: bool) - se for verdade, o fluxo de saída contém os tokens de entrada (unigramas) e telhas. O padrão é verdadeiro.<br /><br /> outputUnigramsIfNoShingles (tipo: bool) - Se for verdade, sobreponha o comportamento do outputUnigrams==falso para os tempos em que não há telhas disponíveis. O padrão é falso.<br /><br /> tokenSeparator (tipo: corda) - A corda a utilizar ao unir fichas adjacentes para formar uma telha. O padrão é " ".<br /><br /> filterToken (tipo: corda) - A corda a inserir para cada posição em que não há símbolo. O padrão é "_".|  
|[bola de neve](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtro de ficha de bola de neve.<br /><br /> **Opções**<br /><br /> língua (tipo: corda) - Valores permitidos incluem: "arménio", "basco", "catalão", "dinamarquês", "holandês", "finlandês", "alemão", "alemão2", "húngaro", "italiano", "kp", "lovins", "norueguês", "porteiro", "português", "romeno", "russo", "espanhol", "sueco", "turco".|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliza a representação unicódes do texto Sorani.<br /><br /> **Opções**<br /><br /> Nenhum.|  
|stemmer|Filtro StemmerToken|Filtro de caule específico da linguagem.<br /><br /> **Opções**<br /><br /> linguagem (tipo: cadeia) - Os valores permitidos incluem: <br /> -   ["árabe"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["arménio"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basco"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brasileiro"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "búlgaro"<br />-   ["catalão"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["checo"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["dinamarquês"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["holandês"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["DutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["inglês"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["LightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["PossessivoEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porteiro2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["Amores"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finlandês"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   ["francês"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["LightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["Mínimo francês"](https://dl.acm.org/citation.cfm?id=318984)<br />- "galego"<br />- "minimalÍtico"<br />-   ["alemão"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["LightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "Mínimo Alemão"<br />-   ["grego"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["húngaro"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["LightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonésio"](https://eprints.illc.uva.nl/741/2/MoL-2003-03.text.pdf)<br />-   ["irlandês"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italiano"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["letão"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norueguês"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["LightNorgian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorgian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["LightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["MinimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["Português"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["mínimos de Porto"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["PortuguêsR"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romeno"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russo"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussiano"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["espanhol"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["LightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sueco"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["turco"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|Filtro StemmerOverrideToken|Quaisquer termos de stemmed dicionário são marcados como palavras-chave, o que impede que a corrente desça. Deve ser colocado antes de quaisquer filtros de ensoamento.<br /><br /> **Opções**<br /><br /> regras (tipo: matriz de cordas) - Regras de ícorar no seguinte formato "palavra => caule" por exemplo "ran => run". O padrão é uma lista vazia.  Obrigatório.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Remove as palavras de um fluxo simbólico. Por predefinição, o filtro utiliza uma lista de palavras de paragem predefinida para inglês.<br /><br /> **Opções**<br /><br /> palavras de ordem (tipo: matriz de cordas) - Uma lista de palavras de ordem. Não é possível especificar se for especificado um stopwordsList.<br /><br /> stopwordsList (tipo: cadeia) - Uma lista predefinida de palavras de ordem. Não é possível especificar se as palavras de ordem forem especificadas. Os valores permitidos incluem: "árabe", "arménio", "basco", "brasileiro", "búlgaro", "catalão", "checo", "holandês", "inglês", "finlandês", "francês", "galego", "alemão", "hindi", "húngaro", "indonésio", "irlandês", "italiano", "letão", "norueguês", "persa", "romeno", "russo", "sorani", " Não é possível especificar se as palavras de ordem forem especificadas. <br /><br /> ignoreCase (tipo: bool) - Se for verdade, todas as palavras são maiúsculas primeiro. O padrão é falso.<br /><br /> remover OTrailing (tipo: bool) - Se for verdade, ignore o último termo de pesquisa se for uma palavra de paragem. O padrão é verdadeiro.
|[sinónimo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Corresponde a sinónimos de palavras únicas ou multi-palavras num fluxo simbólico.<br /><br /> **Opções**<br /><br /> sinónimos (tipo: matriz de cordas) - Obrigatório. Lista de sinónimos num dos dois formatos seguintes:<br /><br /> -incrível, inacreditável, fabuloso => incrível - todos os termos no lado esquerdo de => símbolo são substituídos por todos os termos do seu lado direito.<br /><br /> - incrível, inacreditável, fabuloso, incrível - Uma lista separada de vírgulas de palavras equivalentes. Desa estada a opção de expansão para alterar a forma como esta lista é interpretada.<br /><br /> ignoreCase (tipo: bool) - Entrada de dobras de caso para combinar. O padrão é falso.<br /><br /> expandir (tipo: bool) - Se for verdade, todas as palavras na lista de sinónimos (se = notação> não é usada) mapeiam entre si. <br />A seguinte lista: incrível, inacreditável, fabuloso, incrível é equivalente a: incrível, inacreditável, fabuloso, incrível => incrível, inacreditável, fabuloso, incrível, incrível<br /><br />- Se falso, a seguinte lista: incrível, inacreditável, fabuloso, incrível são equivalentes a: incrível, inacreditável, fabuloso, incrível => incrível.|  
|[guarnição](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Aparas liderando e seguindo o espaço branco de tokens. |  
|[truncato](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunca os termos num comprimento específico.<br /><br /> **Opções**<br /><br /> comprimento (tipo: int) - Padrão: 300, máximo: 300. Obrigatório.|  
|[único](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|ExclusivoTokenFilter|Filtra fichas com o mesmo texto que o token anterior.<br /><br /> **Opções**<br /><br /> onlyOnSamePosition (tipo: bool) - Se estiver definido, remova os duplicados apenas na mesma posição. O padrão é verdadeiro.|  
|[maiúscula](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(o tipo só se aplica quando as opções estão disponíveis)  |Normaliza o texto simbólico para a caixa superior. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Divide as palavras em sub-palavras e executa transformações opcionais em grupos de sub-palavras.<br /><br /> **Opções**<br /><br /> generateWordParts (tipo: bool) - Provoca a geração de partes de palavras, por exemplo "AzureSearch" torna-se "Azure" "Search". O padrão é verdadeiro.<br /><br /> gerar Númerospartais (tipo: bool) - Provoca a geração de sub-palavras de número. O padrão é verdadeiro.<br /><br /> catenateWords (tipo: bool) - Faz com que as folhas máximas das partes de palavras sejam catenadas, por exemplo "Azure-Search" torna-se "AzureSearch". O padrão é falso.<br /><br /> catenateNumbers (tipo: bool) - Provoca a catenação máxima das peças numeradas, por exemplo "1-2" torna-se "12". O padrão é falso.<br /><br /> catenateAll (tipo: bool) - Faz com que todas as partes do subfástico sejam catenadas, por exemplo "Azure-Search-1" torna-se "AzureSearch1". O padrão é falso.<br /><br /> splitOnCaseChange (tipo: bool) - Se for verdade, divide palavras no casoChange, por exemplo "AzureSearch" torna-se "Azure" "Search". O padrão é verdadeiro.<br /><br /> preservarOriginal - Faz com que as palavras originais sejam preservadas e adicionadas à lista de sub-palavras. O padrão é falso.<br /><br /> splitOnNumerics (tipo: bool) - Se for verdade, divide-se nos números, por exemplo "Azure1Search" torna-se "Azure" "1" "Search". O padrão é verdadeiro.<br /><br /> stemEnglishPossessive (tipo: bool) - Faz com que os "'s" de rasto sejam removidos para cada subclínea. O padrão é verdadeiro.<br /><br /> ProtectedWords (tipo: matriz de cordas) - Tokens para proteger de ser delimitado. O padrão é uma lista vazia.|  

 <sup>1</sup> Os tipos de filtro token são sempre pré-fixados em código com "#Microsoft.Azure.Search" de modo a que "ArabicNormalizationTokenFilter" seja especificado como "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo no seu código.  

## <a name="see-also"></a>Ver também

- [APIs de pesquisa cognitiva Azure](/rest/api/searchservice/)
- [Analisadores em Pesquisa Cognitiva Azure (Exemplos)](search-analyzers.md#examples)
- [Criar Índice (REST)](/rest/api/searchservice/create-index)