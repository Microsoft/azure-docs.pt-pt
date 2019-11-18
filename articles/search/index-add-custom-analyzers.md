---
title: Adicionar analisadores personalizados a campos de cadeia de caracteres
titleSuffix: Azure Cognitive Search
description: Configure criadores de texto e filtros de caracteres usados no Azure Pesquisa Cognitiva consultas de pesquisa de texto completo.
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
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113625"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Adicionar analisadores personalizados a campos de cadeia de caracteres em um índice de Pesquisa Cognitiva do Azure

Um *analisador personalizado* é um tipo específico de [analisador de texto](search-analyzers.md) que consiste em uma combinação definida pelo usuário de criador e filtros opcionais existentes. Ao combinar criadores e filtros de novas maneiras, você pode personalizar o processamento de texto no mecanismo de pesquisa para obter resultados específicos. Por exemplo, você pode criar um analisador personalizado com um *filtro de caractere* para remover a marcação HTML antes que as entradas de texto sejam indexadas.

 Você pode definir vários analisadores personalizados para variar a combinação de filtros, mas cada campo pode usar apenas um analisador para análise de indexação e outro para análise de pesquisa. Para obter uma ilustração do que se parece com um analisador de cliente, consulte [exemplo de analisador personalizado](search-analyzers.md#Custom-analyzer-example).

## <a name="overview"></a>Descrição geral

 A função de um [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md), em termos simples, é processar e armazenar documentos de forma a permitir a consulta e a recuperação eficientes. Em um alto nível, tudo se resume a extrair palavras importantes de documentos, colocá-las em um índice e, em seguida, usar o índice para localizar documentos que correspondem a palavras de uma determinada consulta. O processo de extração de palavras de documentos e consultas de pesquisa é chamado de *análise lexical*. Os componentes que executam a análise léxica são chamados de *analisadores*.

 No Pesquisa Cognitiva do Azure, você pode escolher entre um conjunto de analisadores independentes de idioma predefinidos na tabela de [analisadores](#AnalyzerTable) ou analisadores específicos de idioma listados em [analisadores de &#40;idioma Azure&#41;pesquisa cognitiva API REST do serviço](index-add-language-analyzers.md). Você também tem uma opção para definir seus próprios analisadores personalizados.  

 Um analisador personalizado permite que você assuma o controle do processo de conversão de texto em tokens indexáveis e pesquisáveis. É uma configuração definida pelo usuário que consiste em um único criador predefinido, um ou mais filtros de token e um ou mais filtros de caracteres. O criador é responsável por dividir o texto em tokens e os filtros de token para modificar tokens emitidos pelo criador. Os filtros de caracteres são aplicados para preparar o texto de entrada antes que ele seja processado pelo criador. Por exemplo, o filtro de caractere pode substituir determinados caracteres ou símbolos.

 Os cenários populares habilitados por analisadores personalizados incluem:  

- Pesquisa fonética. Adicione um filtro fonético para habilitar a pesquisa com base em como uma palavra soa, e não como ela está grafada.  

- Desabilite a análise léxica. Use o analisador de palavras-chave para criar campos pesquisáveis que não são analisados.  

- Pesquisa rápida de prefixo/sufixo. Adicione o filtro de token Edge N-Gram para indexar os prefixos de palavras para habilitar a correspondência de prefixo rápida. Combine-o com o filtro de token reverso para fazer a correspondência de sufixo.  

- Geração de tokens personalizadas. Por exemplo, use o espaço em branco criador para quebrar frases em tokens usando o espaço em branco como um delimitador  

- Dobramento ASCII. Adicione o filtro de dobra ASCII padrão para normalizar diacríticos como ö ou ê em termos de pesquisa.  

  Esta página fornece uma lista de analisadores com suporte, criadores, filtros de token e filtros de caracteres. Você também pode encontrar uma descrição das alterações na definição do índice com um exemplo de uso. Para obter mais informações sobre a tecnologia subjacente utilizada na implementação de Pesquisa Cognitiva do Azure, consulte [Resumo do pacote de análise (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html). Para obter exemplos de configurações do analisador, consulte [Adicionar analisadores no Azure pesquisa cognitiva](search-analyzers.md#examples).

## <a name="validation-rules"></a>Regras de validação  
 Os nomes de analisadores, criadores, filtros de token e filtros de caracteres devem ser exclusivos e não podem ser iguais a qualquer um dos analisadores predefinidos, criadores, filtros de token ou filtros de caracteres. Consulte a [referência de propriedade](#PropertyReference) para nomes já em uso.

## <a name="create-custom-analyzers"></a>Criar analisadores personalizados
 Você pode definir analisadores personalizados no momento da criação do índice. A sintaxe para especificar um analisador personalizado é descrita nesta seção. Você também pode se familiarizar com a sintaxe examinando definições de exemplo em [Adicionar analisadores no Azure pesquisa cognitiva](search-analyzers.md#examples).  

 Uma definição do analisador inclui um nome, um tipo, um ou mais filtros de caractere, um máximo de um criador e um ou mais filtros de token para o processamento pós-tokenização. Os Filers de Char são aplicados antes da geração de tokens. Filtros de token e filtros de caractere são aplicados da esquerda para a direita.

 O `tokenizer_name` é o nome de um criador, `token_filter_name_1` e `token_filter_name_2` são os nomes dos filtros de token, e `char_filter_name_1` e `char_filter_name_2` são os nomes dos filtros de caracteres (consulte as tabelas [criadores](#Tokenizers), [filtros de token](#TokenFilters) e filtros de caractere para obter valores válidos).

A definição do analisador faz parte do índice maior. Consulte [CREATE INDEX API](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter informações sobre o restante do índice.

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
>  Os analisadores personalizados criados por você não são expostos no portal do Azure. A única maneira de adicionar um analisador personalizado é por meio de código que faz chamadas para a API ao definir um índice.  

 Dentro de uma definição de índice, você pode colocar essa seção em qualquer lugar no corpo de uma solicitação Create index, mas geralmente ela vai ao final:  

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

As definições para filtros de caractere, criadores e filtros de token são adicionadas ao índice somente se você estiver definindo opções personalizadas. Para usar um filtro ou criador existente no estado em que se encontra, especifique-o por nome na definição do analisador.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Testar analisadores personalizados

Você pode usar a **operação do Test Analyzer** na [API REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para ver como um analisador quebra o texto em tokens.

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

## <a name="update-custom-analyzers"></a>Atualizar analisadores personalizados

Depois que um analisador, um criador, um filtro de token ou um filtro de caractere é definido, ele não pode ser modificado. Novos poderão ser adicionados a um índice existente somente se o sinalizador `allowIndexDowntime` estiver definido como true na solicitação de atualização de índice:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Esta operação coloca o índice offline por pelo menos alguns segundos, causando a falha de suas solicitações de indexação e consulta. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado ou mais tempo para índices muito grandes, mas esses efeitos são temporários e eventualmente são resolvidos por conta própria.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Referência do analisador

As tabelas abaixo listam as propriedades de configuração para a seção analisadores, criadores, filtros de token e filtro de caractere de uma definição de índice. A estrutura de um analisador, criador ou filtro no índice é composta por esses atributos. Para obter informações de atribuição de valor, consulte a [referência de propriedade](#PropertyReference).

### <a name="analyzers"></a>Analisadores

Para analisadores, os atributos de índice variam dependendo se você estiver usando analisadores predefinidos ou personalizados.

#### <a name="predefined-analyzers"></a>Analisadores predefinidos

|||  
|-|-|  
|Nome|Ele deve conter apenas letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos e é limitado a 128 caracteres.|  
|Tipo|Tipo de analisador na lista de analisadores com suporte. Consulte a coluna **analyzer_type** na tabela de [analisadores](#AnalyzerTable) abaixo.|  
|Opções|Deve ser uma opção válida de um analisador predefinido listado na tabela de [analisadores](#AnalyzerTable) abaixo.|  

#### <a name="custom-analyzers"></a>Analisadores personalizados

|||  
|-|-|  
|Nome|Ele deve conter apenas letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos e é limitado a 128 caracteres.|  
|Tipo|Deve ser "#Microsoft. Azure. Search. CustomAnalyzer".|  
|CharFilters|Defina como um dos filtros de caracteres predefinidos listados na tabela [filtros de caracteres](#char-filters-reference) ou um filtro de caracteres personalizado especificado na definição do índice.|  
|Criador|Necessário. Defina como um dos criadores predefinidos listados na tabela [criadores](#Tokenizers) abaixo ou em um criador personalizado especificado na definição do índice.|  
|TokenFilters|Defina como um dos filtros de token predefinidos listados na tabela [filtros de token](#TokenFilters) ou um filtro de token personalizado especificado na definição do índice.|  

> [!NOTE]
> É necessário que você configure seu analisador personalizado para não produzir tokens com mais de 300 caracteres. A indexação falha para documentos com esses tokens. Para aparar ou ignorá-los, use o **TruncateTokenFilter** e o **LengthTokenFilter** , respectivamente.  Verifique os [**filtros de token**](#TokenFilters) para referência.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Filtros de caracteres

 Um filtro de caractere é usado para preparar o texto de entrada antes de ser processado pelo criador. Por exemplo, eles podem substituir determinados caracteres ou símbolos. Você pode ter vários filtros de caractere em um analisador personalizado. Os filtros de caracteres são executados na ordem em que estão listados.  

|||  
|-|-|  
|Nome|Ele deve conter apenas letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos e é limitado a 128 caracteres.|  
|Tipo|Tipo de filtro Char na lista de filtros de caracteres com suporte. Consulte **char_filter_type** coluna na tabela [filtros de caracteres](#char-filters-reference) abaixo.|  
|Opções|Deve ser uma opção válida de um determinado tipo de [filtros de caractere](#char-filters-reference) .|  

### <a name="tokenizers"></a>Criadores

 Um criador divide o texto contínuo em uma sequência de tokens, como dividir uma frase em palavras.  

 Você pode especificar exatamente um criador por analisador personalizado. Se precisar de mais de um criador, você poderá criar vários analisadores personalizados e atribuí-los em uma base de campo por campo em seu esquema de índice.  
Um analisador personalizado pode usar um criador predefinido com opções padrão ou personalizadas.  

|||  
|-|-|  
|Nome|Ele deve conter apenas letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos e é limitado a 128 caracteres.|  
|Tipo|Criador nome da lista de criadores com suporte. Consulte **tokenizer_type** coluna na tabela [criadores](#Tokenizers) abaixo.|  
|Opções|Deve ser uma opção válida de um determinado tipo criador listado na tabela [criadores](#Tokenizers) abaixo.|  

### <a name="token-filters"></a>Filtros de token

 Um filtro de token é usado para filtrar ou modificar os tokens gerados por um criador. Por exemplo, você pode especificar um filtro de minúsculas que converte todos os caracteres em minúsculas.   
Você pode ter vários filtros de token em um analisador personalizado. Os filtros de token são executados na ordem em que estão listados.  

|||  
|-|-|  
|Nome|Ele deve conter apenas letras, dígitos, espaços, traços ou sublinhados, só pode começar e terminar com caracteres alfanuméricos e é limitado a 128 caracteres.|  
|Tipo|Nome do filtro de token da lista de filtros de token com suporte. Consulte **token_filter_type** coluna na tabela [filtros de token](#TokenFilters) abaixo.|  
|Opções|Devem ser [filtros de token](#TokenFilters) de um determinado tipo de filtro de token.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Referência de propriedade

Esta seção fornece os valores válidos para os atributos especificados na definição de um analisador personalizado, criador, filtro de caracteres ou filtro de token no índice. Analisadores, criadores e filtros que são implementados usando o Apache Lucene têm links para a documentação da API do Lucene.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Referência de analisadores predefinidos

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[chaves](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (o tipo se aplica somente quando as opções estão disponíveis) |Trata todo o conteúdo de um campo como um único token. Isso é útil para dados como CEPs, IDs e alguns nomes de produtos.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|O separa de maneira flexível o texto em termos por meio de um padrão de expressão regular.<br /><br /> **Opções**<br /><br /> minúsculas (tipo: bool) – determina se os termos são minúsculos. O padrão é true.<br /><br /> [padrão](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (Type: String)-um padrão de expressão regular para corresponder separadores de token. O padrão é \w +.<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres) – sinalizadores de expressão regulares. O padrão é uma cadeia de caracteres vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, comentários, dotall, LITERAL, MULTILINHA, UNICODE_CASE, UNIX_LINES<br /><br /> palavras irrelevantes (tipo: matriz de cadeia de caracteres)-uma lista de palavras irrelevantes. O padrão é uma lista vazia.|  
|[simple](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(o tipo se aplica somente quando as opções estão disponíveis) |Divide o texto em não letras e os converte em minúsculas. |  
|[standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Também conhecido como standard. Lucene)|StandardAnalyzer|Standard Lucene Analyzer, composto pelo criador padrão, pelo filtro de minúsculas e pelo filtro de parada.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. O padrão é 255. Tokens maiores que o comprimento máximo são divididos. O comprimento máximo do token que pode ser usado é de 300 caracteres.<br /><br /> palavras irrelevantes (tipo: matriz de cadeia de caracteres)-uma lista de palavras irrelevantes. O padrão é uma lista vazia.|  
|standardasciifolding.lucene|(o tipo se aplica somente quando as opções estão disponíveis) |Standard Analyzer com filtro de dobra em ASCII. |  
|[deixar](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Divide o texto em não letras, aplica os filtros de token minúsculo e palavra irrelevante.<br /><br /> **Opções**<br /><br /> palavras irrelevantes (tipo: matriz de cadeia de caracteres)-uma lista de palavras irrelevantes. O padrão é uma lista predefinida para o inglês. |  
|[diferente](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(o tipo se aplica somente quando as opções estão disponíveis) |Um analisador que usa o espaço em branco criador. Tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> os tipos de analisador são sempre prefixados no código com "#Microsoft. Azure. Search", de modo que "PatternAnalyzer" seria realmente especificado como "#Microsoft. Azure. Search. PatternAnalyzer". Removemos o prefixo para fins de brevidade, mas o prefixo é necessário em seu código. 
 
O analyzer_type só é fornecido para analisadores que podem ser personalizados. Se não houver opções, como é o caso com o analisador de palavra-chave, não há nenhum associado #Microsoft. Azure. Search.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referência de filtros de caracteres

Na tabela a seguir, os filtros de caracteres que são implementados usando o Apache Lucene estão vinculados à documentação da API do Lucene.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Um filtro Char que tenta retirar construções HTML.|  
|[correlação](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Um filtro de caractere que aplica mapeamentos definidos com a opção mapeamentos. A correspondência é a altura (correspondência de padrão mais longa em um determinado ponto ganha). A substituição pode ser a cadeia de caracteres vazia.<br /><br /> **Opções**<br /><br /> mapeamentos (tipo: matriz de cadeia de caracteres) – uma lista de mapeamentos do seguinte formato: "a = > b" (todas as ocorrências do caractere "a" são substituídas pelo caractere "b"). Necessário.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|Um filtro Char que substitui caracteres na cadeia de caracteres de entrada. Ele usa uma expressão regular para identificar as sequências de caracteres a serem preservadas e um padrão de substituição para identificar os caracteres a serem substituídos. Por exemplo, texto de entrada = "AA BB AA BB", Pattern = "(AA)\\\s + (BB)" Replacement = "$ 1 # $2", Result = "AA # BB AA # BB".<br /><br /> **Opções**<br /><br /> padrão (tipo: cadeia de caracteres) – obrigatório.<br /><br /> substituição (tipo: cadeia de caracteres) – obrigatório.|  

 os tipos de filtro de <sup>1</sup> caractere são sempre prefixados no código com "#Microsoft. Azure. Search", de modo que "MappingCharFilter" seria realmente especificado como "#Microsoft. Azure. Search. MappingCharFilter. Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código. Observe que char_filter_type é fornecida somente para filtros que podem ser personalizados. Se não houver opções, como é o caso com html_strip, não há nenhum associado #Microsoft. Azure. Search.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Referência de criadores

Na tabela a seguir, os criadores que são implementados usando o Apache Lucene estão vinculados à documentação da API do Lucene.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[classic](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|Criador baseada em gramática que é adequada para processar a maioria dos documentos em idiomas europeus.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores que o comprimento máximo são divididos.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Cria tokens a entrada de uma borda em n-grams de tamanhos especificados.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) – padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) – padrão: 2, máximo: 300. Deve ser maior que minGram.<br /><br /> tokenChars (tipo: matriz de cadeia de caracteres)-classes de caractere a serem mantidas nos tokens. Valores permitidos: <br />"letra", "dígito", "espaço em branco", "pontuação", "símbolo". O padrão é uma matriz vazia-mantém todos os caracteres. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Emite a entrada inteira como um único token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. Padrão: 256, máximo: 300. Tokens maiores que o comprimento máximo são divididos.|  
|[letras](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Divide o texto em não letras. Tokens com mais de 255 caracteres são divididos.|  
|[lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Divide o texto em não letras e os converte em minúsculas. Tokens com mais de 255 caracteres são divididos.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Divide o texto usando regras específicas do idioma.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token, padrão: 255, máximo: 300. Tokens maiores que o comprimento máximo são divididos. Tokens com mais de 300 caracteres são divididos primeiro em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto de maxTokenLength.<br /><br />isSearchTokenizer (Type: bool)-definido como true se for usado como a pesquisa criador, definido como false se usado como a indexação criador. <br /><br /> idioma (tipo: cadeia de caracteres)-idioma a ser usado, padrão "inglês". Os valores permitidos incluem:<br />"Bengali", "búlgaro", "catalão", "chineseSimplified", "chineseTraditional", "croata", "tcheco", "dinamarquês", "holandês", "inglês", "francês", "alemão", "grego", "Guzerate", "híndi", "Islandês", "indonésio", "italiano", "japonês", "Kannada", " coreano "," Malaio "," malaiala "," Marat "," norwegianBokmaal "," polonês "," Português "," portugueseBrazilian "," Punjabi "," romeno "," russo "," serbianCyrillic "," serbianLatin "," esloveno "," espanhol "," Sueco "," tâmil "," télugo "," tailandês "," Ucraniano "," urdu "," vietnamita " |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Divide o texto usando regras específicas do idioma e reduz as palavras a seus formulários base<br /><br /> **Opções**<br /><br />maxTokenLength (tipo: int)-o comprimento máximo do token, padrão: 255, máximo: 300. Tokens maiores que o comprimento máximo são divididos. Tokens com mais de 300 caracteres são divididos primeiro em tokens de comprimento 300 e, em seguida, cada um desses tokens é dividido com base no conjunto de maxTokenLength.<br /><br /> isSearchTokenizer (Type: bool)-definido como true se for usado como a pesquisa criador, definido como false se usado como a indexação criador.<br /><br /> idioma (tipo: cadeia de caracteres)-idioma a ser usado, padrão "inglês". Os valores permitidos incluem:<br />"árabe", "Bengali", "búlgaro", "catalão", "croata", "tcheco", "dinamarquês", "holandês", "inglês", "estoniano", "finlandês", "francês", "alemão", "grego", "Guzerate", "hebraico", "híndi", "húngaro", "Islandês", "indonésio", "italiano", "Kannada", " Letão "," lituano "," Malaio "," malaiala "," Marat "," norwegianBokmaal "," polonês "," Português "," portugueseBrazilian "," Punjabi "," romeno "," russo "," serbianCyrillic "," serbianLatin "," eslovaco "," esloveno "," espanhol "," Sueco "," Tâmil "," télugo "," Turco "," ucraniano "," urdu " |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Cria tokens a entrada em n-gramas dos tamanhos especificados.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) – padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) – padrão: 2, máximo: 300. Deve ser maior que minGram. <br /><br /> tokenChars (tipo: matriz de cadeia de caracteres)-classes de caractere a serem mantidas nos tokens. Valores permitidos: "letra", "dígito", "espaço em branco", "pontuação", "símbolo". O padrão é uma matriz vazia-mantém todos os caracteres. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Criador para hierarquias semelhantes a caminho.<br /><br /> **Opções**<br /><br /> delimitador (tipo: cadeia de caracteres)-padrão: '/.<br /><br /> substituição (tipo: cadeia de caracteres) – se definido, substitui o caractere delimitador. O mesmo padrão é o valor do delimitador.<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. Padrão: 300, máximo: 300. Caminhos maiores que maxTokenLength são ignorados.<br /><br /> Reverse (Type: bool)-se true, gera um token na ordem inversa. Padrão: false.<br /><br /> Skip (tipo: bool)-tokens iniciais a serem ignorados. O padrão é 0.|  
|[pattern](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Esse criador usa a correspondência de padrão Regex para construir tokens distintos.<br /><br /> **Opções**<br /><br /> [padrão](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (tipo: cadeia de caracteres)-padrão de expressão regular. O padrão é \w +. <br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (tipo: cadeia de caracteres) – sinalizadores de expressão regulares. O padrão é uma cadeia de caracteres vazia. Valores permitidos: CANON_EQ, CASE_INSENSITIVE, comentários, dotall, LITERAL, MULTILINHA, UNICODE_CASE, UNIX_LINES<br /><br /> Grupo (tipo: int)-qual grupo deve ser extraído em tokens. O padrão é-1 (Split).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Quebra o texto seguindo as [regras de segmentação de texto Unicode](https://unicode.org/reports/tr29/).<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores que o comprimento máximo são divididos.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|URLs e emails do cria tokens como um token.<br /><br /> **Opções**<br /><br /> maxTokenLength (tipo: int)-o comprimento máximo do token. Padrão: 255, máximo: 300. Tokens maiores que o comprimento máximo são divididos.|  
|[diferente](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(o tipo se aplica somente quando as opções estão disponíveis) |Divide o texto em espaço em branco. Tokens com mais de 255 caracteres são divididos.|  

 <sup>1</sup> os tipos de criador são sempre prefixados no código com "#Microsoft. Azure. Search", de modo que "ClassicTokenizer" seria realmente especificado como "#Microsoft. Azure. Search. ClassicTokenizer". Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código. Observe que tokenizer_type é fornecida somente para criadores que podem ser personalizadas. Se não houver opções, como é o caso com a letra criador, não há nenhum #Microsoft associado. Azure. Search Type.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Referência de filtros de token

Na tabela a seguir, os filtros de token que são implementados usando o Apache Lucene estão vinculados à documentação da API do Lucene.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Descrição e opções**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Um filtro de token que aplica o normalizador árabe para normalizar o orthography.|  
|[apóstrofo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Remove todos os caracteres após um apóstrofo (incluindo a própria apóstrofe). |  
|[asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converte caracteres Unicode alfabéticos, numéricos e simbólicos que não estão nos primeiros 127 caracteres ASCII (o bloco Unicode "básico latino") em seus equivalentes ASCII, se houver.<br /><br /> **Opções**<br /><br /> preserveOriginal (Type: bool)-se true, o token original será mantido. O padrão é false.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Forma bigramas de termos CJK que são gerados a partir de StandardTokenizer.<br /><br /> **Opções**<br /><br /> ignoreScripts (tipo: matriz de cadeia de caracteres)-scripts a serem ignorados. Os valores permitidos incluem: "Han", "hiragana", "katakana", "Hangul". O padrão é uma lista vazia.<br /><br /> outputUnigrams (Type: bool)-Defina como true se você quiser sempre gerar unigrams e bigrams. O padrão é false.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza as diferenças de largura CJK. Dobra as variantes ASCII de largura inteira nas variantes de katakana de meia largura e média básica equivalentes no kana equivalente. |  
|[classic](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Remove o possessivos em inglês e os pontos de acrônimos. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Construa bigrams para termos que ocorrem com frequência durante a indexação. Os termos únicos ainda são indexados também, com bigrams sobrepostos.<br /><br /> **Opções**<br /><br /> commonWords (tipo: matriz de cadeia de caracteres)-o conjunto de palavras comuns. O padrão é uma lista vazia. Necessário.<br /><br /> ignoreCase (tipo: bool)-se true, a correspondência não diferencia maiúsculas de minúsculas. O padrão é false.<br /><br /> querymode (tipo: bool) – gera bigrams e, em seguida, remove palavras comuns e termos únicos seguidos de uma palavra comum. O padrão é false.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|O decompõe palavras compostas encontradas em muitas linguagens germânicos.<br /><br /> **Opções**<br /><br /> lista de palavras (tipo: matriz de cadeia de caracteres)-a listagem de palavras com a qual corresponder. O padrão é uma lista vazia. Necessário.<br /><br /> minWordSize (tipo: int)-somente palavras mais longas que essa é processada. O padrão é 5.<br /><br /> minSubwordSize (tipo: int)-somente as subpalavras mais longas do que isso são emitidas. O padrão é 2.<br /><br /> maxSubwordSize (tipo: int)-somente as subpalavras menores que isso são emitidas. O padrão é 15.<br /><br /> onlyLongestMatch (tipo: bool) – adicione apenas a subpalavra correspondente mais longa à saída. O padrão é false.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|Gera n-grams dos tamanhos especificados a partir da frente ou da parte posterior de um token de entrada.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) – padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) – padrão: 2, máximo 300. Deve ser maior que minGram.<br /><br /> lado (tipo: cadeia de caracteres) – especifica em qual lado da entrada o n-grama deve ser gerado. Valores permitidos: "front", "Back" |  
|[corrotina](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Remove elisões. Por exemplo, "l'avion" (o plano) é convertido em "Avion" (plano).<br /><br /> **Opções**<br /><br /> artigos (tipo: matriz de cadeia de caracteres)-um conjunto de artigos a serem removidos. O padrão é uma lista vazia. Se não houver nenhuma lista de artigos definida, por padrão, todos os artigos em francês serão removidos.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza caracteres alemão de acordo com a heurística do [algoritmo German2 bola](https://snowballstem.org/algorithms/german2/stemmer.html) .|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza o texto em Híndi para remover algumas diferenças nas variações de ortografia. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|Normaliza a representação Unicode do texto em idiomas indianos.
|[manter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Um filtro de token que mantém apenas os tokens com texto contido na lista especificada de palavras.<br /><br /> **Opções**<br /><br /> keepWords (tipo: matriz de cadeia de caracteres)-uma lista de palavras a serem mantidas. O padrão é uma lista vazia. Necessário.<br /><br /> keepWordsCase (Type: bool)-se true, minúsculas todas as palavras primeiro. O padrão é false.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Marca os termos como palavras-chave.<br /><br /> **Opções**<br /><br /> Palavras-chave (tipo: matriz de cadeia de caracteres)-uma lista de palavras a serem marcadas como palavras-chave. O padrão é uma lista vazia. Necessário.<br /><br /> ignoreCase (tipo: bool)-se verdadeiro, use todas as palavras em letras minúsculas primeiro. O padrão é false.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Emite cada token de entrada duas vezes uma vez como palavra-chave e uma vez como não palavra-chave. |  
|[kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Um filtro de kstem de alto desempenho para inglês. |  
|[length](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|Remove palavras que são muito longas ou muito curtas.<br /><br /> **Opções**<br /><br /> min (tipo: int)-o número mínimo. Padrão: 0, máximo: 300.<br /><br /> Max (tipo: int)-o número máximo. Padrão: 300, máximo: 300.|  
|[limit](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Limita o número de tokens durante a indexação.<br /><br /> **Opções**<br /><br /> maxTokenCount (tipo: int)-número máximo de tokens a serem produzidos. O padrão é 1.<br /><br /> consumeAllTokens (tipo: bool) – se todos os tokens da entrada devem ser consumidos mesmo se maxTokenCount for atingido. O padrão é false.|  
|[lowercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza o texto do token para letras minúsculas. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Gera n-grams dos tamanhos especificados.<br /><br /> **Opções**<br /><br /> minGram (tipo: int) – padrão: 1, máximo: 300.<br /><br /> maxGram (tipo: int) – padrão: 2, máximo 300. Deve ser maior que minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Usa regexes Java para emitir vários tokens, um para cada grupo de captura em um ou mais padrões.<br /><br /> **Opções**<br /><br /> padrões (tipo: matriz de cadeia de caracteres) – uma lista de padrões para corresponder a cada token. Necessário.<br /><br /> preserveOriginal (Type: bool)-Defina como true para retornar o token original, mesmo se um dos padrões corresponder, padrão: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Um filtro de token que aplica um padrão a cada token no fluxo, substituindo ocorrências de correspondência com a cadeia de caracteres de substituição especificada.<br /><br /> **Opções**<br /><br /> padrão (tipo: cadeia de caracteres) – obrigatório.<br /><br /> substituição (tipo: cadeia de caracteres) – obrigatório.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis) |Aplica a normalização para persa. |  
|[Phonetic](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|Crie tokens para correspondências fonéticas.<br /><br /> **Opções**<br /><br /> codificador (tipo: cadeia de caracteres) – codificador fonético a ser usado. Os valores permitidos incluem: "metaphone", "doubleMetaphone", "SOUNDEX", "refinedSoundex", "caverphone1", "caverphone2", "Cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Padrão: "metaphone". O padrão é o metaphone.<br /><br /> Consulte [codificador](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) para obter mais informações.<br /><br /> Replace (tipo: bool)-true se os tokens codificados devem substituir os tokens originais, false se eles devem ser adicionados como sinônimos. O padrão é true.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Transforma o fluxo de token de acordo com o [algoritmo de lematização de carregador](https://tartarus.org/~martin/PorterStemmer/). |  
|[reverse](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Reverte a cadeia de caracteres do token. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza o uso dos caracteres Scandinavian intercambiáveis. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Folds Scandinavian characters åÅäæÄÆ->a and öÖøØ->o. Ele também discrimina o uso de vogais duplas AA, AE, ao, OE e OO, deixando apenas a primeira. |  
|[shingle](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|Cria combinações de tokens como um único token.<br /><br /> **Opções**<br /><br /> maxShingleSize (tipo: int) – o padrão é 2.<br /><br /> minShingleSize (tipo: int) – o padrão é 2.<br /><br /> outputUnigrams (Type: bool)-se true, o fluxo de saída conterá os tokens de entrada (unigrams), bem como shingles. O padrão é true.<br /><br /> outputUnigramsIfNoShingles (Type: bool)-se true, substitua o comportamento de outputUnigrams = = false para aqueles horários em que nenhum shingles está disponível. O padrão é false.<br /><br /> tokenSeparator (Type: String)-a cadeia de caracteres a ser usada ao unir tokens adjacentes para formar um Shingle. O padrão é "".<br /><br /> filterToken (Type: String)-a cadeia de caracteres a ser inserida para cada posição na qual não há nenhum token. O padrão é "_".|  
|[snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Filtro de token bola.<br /><br /> **Opções**<br /><br /> idioma (tipo: cadeia de caracteres)-os valores permitidos incluem: "armênio", "Basco", "catalão", "dinamarquês", "holandês", "inglês", "finlandês", "francês", "alemão", "German2", "húngaro", "italiano", "KP", "Lovins", "norueguês", "carregador", "Português", "romeno", " russo "," espanhol "," Sueco "," Turco "|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|Normaliza a representação Unicode do texto Sorani.<br /><br /> **Opções**<br /><br /> Nenhum.|  
|lematizador|StemmerTokenFilter|Filtro de lematização específico a um idioma.<br /><br /> **Opções**<br /><br /> idioma (tipo: cadeia de caracteres)-os valores permitidos incluem: <br /> -   ["árabe"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armênio"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["Basco"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brasileiro"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-"búlgaro"<br />-   ["catalão"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["dinamarquês"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["inglês"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["Porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["Lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finlandês"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lightFinnish"<br />-   ["french"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-"Galego"<br />- "minimalGalician"<br />-   ["alemão"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["German2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   ["grego"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-"híndi"<br />-   ["húngaro"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonésio"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irlandês"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italiano"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Letão"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norueguês"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["Português"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romeno"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russo"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["espanhol"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Sueco"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["Turco"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Quaisquer termos originados no dicionário são marcados como palavras-chave, o que impede a lematização da cadeia. Deve ser colocado antes de qualquer filtro de lematização.<br /><br /> **Opções**<br /><br /> regras (tipo: matriz de cadeia de caracteres) – regras de lematização no seguinte formato "Word = > tronco", por exemplo "Executed = > Run". O padrão é uma lista vazia.  Necessário.|  
|[palavras irrelevantes](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Remove palavras irrelevantes de um fluxo de token. Por padrão, o filtro usa uma lista de palavras irrelevantes predefinida para inglês.<br /><br /> **Opções**<br /><br /> palavras irrelevantes (tipo: matriz de cadeia de caracteres)-uma lista de palavras irrelevantes. Não será possível especificar se um stopwordsList for especificado.<br /><br /> stopwordsList (Type: String)-uma lista predefinida de palavras irrelevantes. Não será possível especificar se palavras irrelevantes for especificado. Os valores permitidos incluem: "Arabic", "armênio", "Basco", "Português", "búlgaro", "catalão", "tcheco", "dinamarquês", "holandês", "inglês", "finlandês", "francês", "Galego", "alemão", "grego", "híndi", "húngaro", "indonésio", "irlandês", "italiano", "Letão "," norueguês "," persa "," Português "," romeno "," russo "," Sorani "," espanhol "," Sueco "," tailandês "," Turco ", padrão:" inglês ". Não será possível especificar se palavras irrelevantes for especificado. <br /><br /> ignoreCase (tipo: bool)-se true, todas as palavras serão mais baixas em primeiro lugar. O padrão é false.<br /><br /> removeTrailing (tipo: bool)-se verdadeiro, ignorar o último termo de pesquisa se for uma palavra de parada. O padrão é true.
|[synonym](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|Faz a correspondência de sinônimos de palavra única ou múltipla em um fluxo de token.<br /><br /> **Opções**<br /><br /> sinônimos (tipo: matriz de cadeia de caracteres) – obrigatório. Lista de sinônimos em um dos dois formatos a seguir:<br /><br /> -incrível, Unbelievable, fabuloso = > incrível – todos os termos no lado esquerdo do símbolo = > são substituídos por todos os termos no lado direito.<br /><br /> -incrível, Unbelievable, fabuloso, incrível – uma lista separada por vírgulas de palavras equivalentes. Defina a opção expandir para alterar a forma como essa lista é interpretada.<br /><br /> ignoreCase (tipo: bool)-diferencia maiúsculas de minúsculas de entrada para correspondência. O padrão é false.<br /><br /> Expand (tipo: bool)-se true, todas as palavras na lista de sinônimos (se = notação de > não são usadas) são mapeadas entre si. <br />A lista a seguir: incrível, Unbelievable, fabuloso, incrível é equivalente a: incrível, Unbelievable, fabuloso, incrível = > incrível, Unbelievable, fabuloso, incrível<br /><br />-Se false, a seguinte lista: incrível, Unbelievable, fabuloso, incrível são equivalentes a: incrível, Unbelievable, fabuloso, incrível = > incrível.|  
|[trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Corta o espaço em branco à esquerda e à direita dos tokens. |  
|[truncar](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|Trunca os termos em um comprimento específico.<br /><br /> **Opções**<br /><br /> comprimento (tipo: int)-padrão: 300, máximo: 300. Necessário.|  
|[unique](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|Filtra os tokens com o mesmo texto que o token anterior.<br /><br /> **Opções**<br /><br /> onlyOnSamePosition (tipo: bool) – se definido, remova duplicatas somente na mesma posição. O padrão é true.|  
|[uppercase](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(o tipo se aplica somente quando as opções estão disponíveis)  |Normaliza o texto do token para letras maiúsculas. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Divide palavras em subpalavras e executa transformações opcionais em grupos de subpalavras.<br /><br /> **Opções**<br /><br /> generateWordParts (Type: bool) – faz com que partes de palavras sejam geradas, por exemplo, "AzureSearch" torna-se "Azure" "Search". O padrão é true.<br /><br /> generateNumberParts (Type: bool)-faz com que as subpalavras de número sejam geradas. O padrão é true.<br /><br /> catenateWords (Type: bool)-faz com que as execuções máximas de palavras do Word sejam catenated, por exemplo, "Azure-Search" se torna "AzureSearch". O padrão é false.<br /><br /> catenateNumbers (Type: bool) – faz com que as execuções máximas de partes numéricas sejam catenated, por exemplo, "1-2" torna-se "12". O padrão é false.<br /><br /> catenateAll (Type: bool) – faz com que todas as partes da subpalavra sejam catenated, por exemplo, "Azure-Search-1" torna-se "AzureSearch1". O padrão é false.<br /><br /> splitOnCaseChange (Type: bool)-se true, divide as palavras em caseChange, por exemplo, "AzureSearch" torna-se "Azure" "Search". O padrão é true.<br /><br /> preserveOriginal-faz com que palavras originais sejam preservadas e adicionadas à lista de subpalavras. O padrão é false.<br /><br /> splitOnNumerics (Type: bool)-se true, divisões em números, por exemplo "Azure1Search", se torna "Azure" "1" "Search". O padrão é true.<br /><br /> stemEnglishPossessive (Type: bool)-faz com que a "s" à direita seja removida para cada subpalavra. O padrão é true.<br /><br /> protectedWords (tipo: matriz de cadeia de caracteres)-tokens a serem protegidos de serem delimitados. O padrão é uma lista vazia.|  

 <sup>1</sup> os tipos de filtro de token são sempre prefixados no código com "#Microsoft. Azure. Search", de modo que "ArabicNormalizationTokenFilter" seria realmente especificado como "#Microsoft. Azure. Search. ArabicNormalizationTokenFilter".  Removemos o prefixo para reduzir a largura da tabela, mas lembre-se de incluí-lo em seu código.  


## <a name="see-also"></a>Ver também  
 [APIs REST do Azure Pesquisa Cognitiva](https://docs.microsoft.com/rest/api/searchservice/)   
 Os [analisadores no Azure Pesquisa Cognitiva > exemplos](search-analyzers.md#examples)    
 [Criar índice &#40;API REST do Azure pesquisa cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
