---
title: Analisadores para processamento linguístico e de texto – Azure Search
description: Atribua analisadores a campos de texto pesquisáveis em um índice para substituir o Lucene padrão standard por alternativas personalizadas, predefinidas ou específicas de idioma.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: heidist
manager: nitinme
author: HeidiSteen
ms.openlocfilehash: 85ebc75a22a4b27803df758d3f411a46a6206eb7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987627"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analisadores para processamento de texto no Azure Search

Um *analisador* é um componente do [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de caracteres de consulta e documentos indexados. Analisadores diferentes manipulam texto de maneiras diferentes, dependendo do cenário. Os analisadores de linguagem processam o texto usando regras linguísticas para melhorar a qualidade da pesquisa, enquanto outros analisadores executam tarefas mais básicas, como a conversão de caracteres em letras minúsculas, por exemplo. 

Os analisadores de idioma são usados com mais frequência e há um analisador de idioma padrão atribuído a cada campo pesquisável em um índice de Azure Search. As seguintes transformações de idioma são típicas durante a análise de texto:

+ Palavras não essenciais (palavras irrelevantes) e pontuação são removidas.
+ Frases e palavras hifenizadas são divididas em partes de componentes.
+ As palavras em letras maiúsculas são minúsculas.
+ As palavras são reduzidas para formulários raiz para que uma correspondência possa ser encontrada, independentemente de conjugação.

Os analisadores de linguagem convertem uma entrada de texto em formulários primitivos ou raiz que são eficientes para o armazenamento e a recuperação de informações. A conversão ocorre durante a indexação, quando o índice é compilado e, em seguida, durante a pesquisa quando o índice é lido. É mais provável que você obtenha os resultados da pesquisa esperados se usar o mesmo analisador para ambas as operações.

## <a name="default-analyzer"></a>Analisador padrão  

Azure Search usa o [analisador padrão Apache Lucene (Lucene padrão)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como o padrão, que quebra o texto em elementos seguindo as regras de ["segmentação de texto Unicode"](https://unicode.org/reports/tr29/) . Além disso, o analisador padrão converte todos os caracteres em sua forma de minúsculas. Os documentos indexados e os termos de pesquisa passam pela análise durante a indexação e o processamento de consultas.  

Ele é usado automaticamente em todos os campos pesquisáveis. Você pode substituir o padrão em uma base Field-by-Field. Analisadores alternativos podem ser um [analisador de linguagem](index-add-language-analyzers.md), um [analisador personalizado](index-add-custom-analyzers.md)ou um analisador predefinido da [lista de analisadores disponíveis](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista a seguir descreve quais analisadores estão disponíveis no Azure Search.

| Categoria | Descrição |
|----------|-------------|
| [Analisador Lucene Standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predefinição. Nenhuma especificação ou configuração é necessária. Esse analisador de uso geral funciona bem para a maioria dos idiomas e cenários.|
| Analisadores predefinidos | Oferecido como um produto concluído, destinado a ser usado no estado em que se encontra. <br/>Há dois tipos: especializado e linguagem. O que os torna "predefinidos" é que você os referencia por nome, sem nenhuma configuração ou personalização. <br/><br/>[Analisadores especializados (independentes de idioma)](index-add-custom-analyzers.md#AnalyzerTable) são usados quando as entradas de texto exigem processamento especializado ou mínimo de processamento. Analisadores predefinidos que não são de idioma incluem **Asciifolding**, **palavra-chave**, **padrão**, **simples**, **parar**, **espaço em branco**.<br/><br/>[Analisadores de linguagem](index-add-language-analyzers.md) são usados quando você precisa de suporte linguístico avançado para idiomas individuais. O Azure Search dá suporte a analisadores de linguagem 35 Lucene e a 50 analisadores de processamento de idioma natural da Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se a uma configuração definida pelo usuário de uma combinação de elementos existentes, consistindo em um criador (obrigatório) e filtros opcionais (Char ou token).|

Alguns analisadores predefinidos, como **Pattern** ou **Stop**, dão suporte a um conjunto limitado de opções de configuração. Para definir essas opções, você cria efetivamente um analisador personalizado, que consiste no analisador predefinido e em uma das opções alternativas documentadas na [referência do analisador predefinido](index-add-custom-analyzers.md#AnalyzerTable). Assim como ocorre com qualquer configuração personalizada, forneça uma nova configuração com um nome, como *myPatternAnalyzer* para distingui-la do analisador de padrão Lucene.

## <a name="how-to-specify-analyzers"></a>Como especificar analisadores

1. (somente para analisadores personalizados) Crie uma seção do **analisador** nomeado na definição do índice. Para obter mais informações, consulte [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [Adicionar analisadores personalizados](index-add-custom-analyzers.md).

2. Em uma [definição de campo](https://docs.microsoft.com/rest/api/searchservice/create-index) no índice, defina a propriedade do **analisador** do campo como o nome de um analisador de destino (por exemplo, `"analyzer" = "keyword"`. Os valores válidos incluem o nome de um analisador predefinido, um analisador de linguagem ou um analisador personalizado também definido no esquema de índice. Planeje a atribuição do analisador na fase de definição de índice antes de o índice ser criado no serviço.

3. Opcionalmente, em vez de uma propriedade do **analisador** , você pode definir diferentes analisadores para indexação e consulta usando os parâmetros de campo **indexAnalyzer** e **searchAnalyzer** . Você usaria analisadores diferentes para preparação e recuperação de dados se uma dessas atividades exigisse uma transformação específica não necessária para a outra.

Não é permitido atribuir o **Analyzer** ou o **indexAnalyzer** a um campo que já foi criado fisicamente. Se algum deles estiver incorreto, examine a tabela a seguir para obter uma análise de quais ações exigem uma recompilação e por quê.
 
 | Cenário | Impacto | Passos |
 |----------|--------|-------|
 | Adicionar um novo campo | Muito | Se o campo ainda não existir no esquema, não haverá nenhuma revisão de campo a ser feita, pois o campo ainda não tem uma presença física no índice. Você pode usar [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para adicionar um novo campo a um índice existente e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para preenchê-lo.|
 | Adicione um **analisador** ou **indexAnalyzer** a um campo indexado existente. | [constitui](search-howto-reindex.md) | O índice invertido para esse campo deve ser recriado do zero e o conteúdo desses campos deve ser reindexado. <br/> <br/>Para índices em desenvolvimento ativo, [exclua](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para selecionar a nova definição de campo. <br/> <br/>Para índices em produção, você pode adiar uma recompilação criando um novo campo para fornecer a definição revisada e começar a usá-lo no lugar do antigo. Use [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index) para incorporar o novo campo e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para preenchê-lo. Posteriormente, como parte do serviço de índice planejado, você pode limpar o índice para remover campos obsoletos. |

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

O melhor momento para adicionar e atribuir analisadores é durante o desenvolvimento ativo, ao descartar e recriar índices é uma rotina.

Como uma definição de índice solidifies, você pode acrescentar novas construções de análise a um índice, mas você precisará passar o sinalizador **allowIndexDowntime** para [atualizar o índice](https://docs.microsoft.com/rest/api/searchservice/update-index) se desejar evitar esse erro:

*"Atualização de índice não permitida porque isso causaria tempo de inatividade. Para adicionar novos analisadores, criadores, filtros de token ou filtros de caractere a um índice existente, defina o parâmetro de consulta ' allowIndexDowntime ' como ' true ' na solicitação de atualização de índice. Observe que essa operação colocará o índice offline por pelo menos alguns segundos, fazendo com que as solicitações de indexação e consulta falhem. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado ou mais tempo para índices muito grandes. "*

O mesmo se aplica ao atribuir um analisador a um campo. Um analisador é parte integrante da definição do campo, para que você só possa adicioná-lo quando o campo for criado. Se você quiser adicionar analisadores a campos existentes, será necessário [descartar e recriar](search-howto-reindex.md) o índice ou adicionar um novo campo com o analisador desejado.

Como observado, uma exceção é a variante **searchAnalyzer** . Das três maneiras de especificar analisadores (**Analyzer**, **indexAnalyzer**, **searchAnalyzer**), somente o atributo **searchAnalyzer** pode ser alterado em um campo existente.

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta seção oferece conselhos sobre como trabalhar com analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura/gravação, a menos que você tenha requisitos específicos

Azure Search permite especificar diferentes analisadores para indexação e pesquisa por meio de parâmetros de campo **indexAnalyzer** e **searchAnalyzer** adicionais. Se não for especificado, o analisador definido com a propriedade **Analyzer** será usado para indexação e pesquisa. Se `analyzer` não for especificado, o analisador padrão Lucene padrão será usado.

Uma regra geral é usar o mesmo analisador para indexação e consulta, a menos que requisitos específicos ditem o contrário. Certifique-se de testar exaustivamente. Quando o processamento de texto é diferente no tempo de pesquisa e indexação, você corre o risco de incompatibilidade entre termos de consulta e termos indexados quando as configurações do analisador de pesquisa e indexação não estiverem alinhadas.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento ativo

A substituição do analisador Standard requer uma recompilação de índice. Se possível, decida quais analisadores usar durante o desenvolvimento ativo, antes de distribuir um índice para produção.

### <a name="inspect-tokenized-terms"></a>Inspecionar termos com token

Se uma pesquisa não retornar resultados esperados, o cenário mais provável é discrepâncias de token entre as entradas de termo na consulta e os termos com token no índice. Se os tokens não forem iguais, as correspondências falharão ao materializar. Para inspecionar a saída do criador, é recomendável usar a [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como uma ferramenta de investigação. A resposta consiste em tokens, conforme gerado por um analisador específico.

<a name="examples"></a>

## <a name="rest-examples"></a>Exemplos de REST

Os exemplos abaixo mostram as definições do analisador para alguns cenários principais.

+ [Exemplo de analisador personalizado](#Custom-analyzer-example)
+ [Exemplo de atribuir analisadores a um campo](#Per-field-analyzer-assignment-example)
+ [Misturar analisadores para indexação e pesquisa](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemplo do analisador de linguagem](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemplo de analisador personalizado

Este exemplo ilustra uma definição do analisador com opções personalizadas. As opções personalizadas para filtros de caractere, criadores e filtros de token são especificadas separadamente como construções nomeadas e, em seguida, referenciadas na definição do analisador. Os elementos predefinidos são usados no estado em que se encontram e são simplesmente referenciados pelo nome.

Percorrendo este exemplo:

* Os analisadores são uma propriedade da classe de campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Ele pode ser levemente personalizado (por exemplo, personalizar uma única opção em um filtro) ou personalizado em vários locais.
* Nesse caso, o analisador personalizado é "my_analyzer", que por sua vez usa um criador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro asciifolding minúsculo e personalizado "my_asciifolding".
* Ele também define dois filtros de caractere personalizado "map_dash" e "remove_whitespace". O primeiro substitui todos os traços por sublinhados, enquanto o segundo remove todos os espaços. Os espaços precisam ser codificados em UTF-8 nas regras de mapeamento. Os filtros de caracteres são aplicados antes da geração de tokens e afetarão os tokens resultantes (o criador padrão é interrompido em Dash e espaços, mas não no sublinhado).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Exemplo de atribuição do analisador por campo

O analisador padrão é o padrão. Suponha que você deseja substituir o padrão por um analisador predefinido diferente, como o analisador de padrões. Se você não estiver definindo opções personalizadas, precisará especificá-las apenas pelo nome na definição de campo.

O elemento "Analyzer" substitui o analisador padrão de acordo com o campo. Não há nenhuma substituição global. Neste exemplo, `text1` usa o analisador de padrões e `text2`, que não especifica um analisador, usa o padrão.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Misturar analisadores para operações de indexação e pesquisa

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos **searchAnalyzer** e **indexAnalyzer** devem ser especificados como um par, substituindo o único atributo do **analisador** .


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Exemplo do analisador de linguagem

Os campos que contêm cadeias de caracteres em idiomas diferentes podem usar um analisador de idioma, enquanto outros campos retêm o padrão (ou usam algum outro analisador predefinido ou personalizado). Se você usar um analisador de linguagem, ele deverá ser usado para operações de indexação e pesquisa. Os campos que usam um analisador de linguagem não podem ter analisadores diferentes para indexação e pesquisa.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#disso

Se você estiver usando os exemplos de código do SDK do .NET, poderá acrescentar esses exemplos para usar ou configurar os analisadores.

+ [Atribuir um analisador interno](#Assign-a-language-analyzer)
+ [Configurar um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de linguagem

Qualquer analisador usado como está, sem configuração, é especificado em uma definição de campo. Não há nenhum requisito para a criação de uma construção do analisador. 

Este exemplo atribui os analisadores em inglês e francês da Microsoft aos campos de descrição. É um trecho extraído de uma definição maior do índice de hotéis, criando usando a classe Hotel no arquivo hotels.cs do exemplo [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

O Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), especificando o tipo [analyzername](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) , fornecendo um analisador de texto com suporte no Azure Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definir um analisador personalizado

Quando a personalização ou configuração for necessária, será necessário adicionar uma construção do analisador a um índice. Depois de defini-lo, você pode adicioná-lo à definição de campo, conforme demonstrado no exemplo anterior.

Crie um objeto [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) . Para obter mais exemplos, consulte [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Passos seguintes

+ Examine nossa explicação abrangente de [como funciona a pesquisa de texto completo em Azure Search](search-lucene-query-architecture.md). Este artigo usa exemplos para explicar os comportamentos que podem parecer um contador intuitivo na superfície.

+ Tente sintaxe de consulta adicional na seção de exemplo [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) ou na [sintaxe de consulta simples](query-simple-syntax.md) no Gerenciador de pesquisa no Portal.

+ Saiba como aplicar [analisadores léxicos específicos a um idioma](index-add-language-analyzers.md).

+ [Configure analisadores personalizados](index-add-custom-analyzers.md) para o processamento mínimo ou processamento especializado em campos individuais.

## <a name="see-also"></a>Ver também

 [Pesquisar API REST de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completa](query-lucene-syntax.md) 
 
 [Processar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
