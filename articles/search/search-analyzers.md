---
title: Analisadores de linguística e o processamento de texto - Azure Search
description: Analisadores de atribuir a campos de texto pesquisável num índice para substituir a predefinição Lucene padrão com alternativas personalizadas, predefinidas ou específicas de idioma.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 3e6f0a2b9b935df9b12cf9146ebf05f1b1c84855
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578769"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analisadores de processamento no Azure Search de texto

Uma *analisador* é um componente dos [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de consulta e documentos indexados. Analisadores de diferentes manipulam texto de formas diferentes dependendo do cenário. Analisadores de idiomas processam texto usando as regras lingüísticas para melhorar a qualidade de pesquisa, enquanto outros analisadores de realizar tarefas mais básicas como converter caracteres em minúsculas, por exemplo. 

Analisadores de idiomas são utilizadas com mais frequência e existe o analisador de idioma predefinido atribuído a todos os campos pesquisáveis no índice da Azure Search. As seguintes transformações de idioma são característicos durante a análise de texto:

+ Não essencial palavras (palavras de paragem) e pontuação são removidos.
+ Frases e palavras hifenizadas são divididas em componentes.
+ Palavras de maiúsculas estão em minúsculas.
+ Palavras são reduzidas para formulários de raiz, de modo a que pode ser encontrada uma correspondência, independentemente do tempo verbal.

Converter de analisadores de idioma um texto de entrada para o primitivo ou formulários de raiz que são eficientes para armazenamento de informações e a recuperação. Conversão ocorre durante a indexação, quando o índice é criado e, em seguida, novamente durante a pesquisa quando o índice é de leitura. É provável que mais obter os resultados de pesquisa que espera se utilizar o analisador de mesmo para as duas operações.

## <a name="default-analyzer"></a>Analisador de predefinição  

O Azure Search utiliza a [analyzer Apache Lucene Standard (standard lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como padrão, que divide texto em elementos a seguir a ["Segmentação de texto Unicode"](https://unicode.org/reports/tr29/) regras. Além disso, o analisador padrão converte todos os carateres para o formato de minúsculas. Documentos indexados e termos de pesquisa percorrer a análise durante a indexação e de processamento de consultas.  

Ele é usado automaticamente em todos os campos pesquisáveis. É possível substituir a predefinição de forma de campo por campo. Analisadores alternativos podem ser um [analisador de idioma](index-add-language-analyzers.md), [analisador personalizado](index-add-custom-analyzers.md), ou um analisador predefinido do [lista de analisadores disponíveis](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista seguinte descreve os analisadores estão disponíveis no Azure Search.

| Categoria | Descrição |
|----------|-------------|
| [Analisador de Lucene padrão](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predefinição. Não é necessária nenhum especificação ou configuração. Executa este analyzer para fins gerais bem para a maioria das linguagens e cenários.|
| Analisadores predefinidos | Oferecido como um produto deve ser usado como-é. <br/>Existem dois tipos: especializadas e idioma. O que os torna "predefinido" é referenciá-los por nome, sem qualquer configuração ou a personalização. <br/><br/>[Especializada (linguagem desconhecida) analisadores](index-add-custom-analyzers.md#AnalyzerTable) são utilizadas quando entradas de texto requerem processamento especializado ou processamento mínimo. Analisadores de idioma não predefinidos incluem **Asciifolding**, **palavra-chave**, **padrão**, **simples**, **parar**, **Espaços em branco**.<br/><br/>[Analisadores de idiomas](index-add-language-analyzers.md) são utilizados quando precisar de suporte linguístico avançado para idiomas individuais. O Azure Search suporta 35 analisadores de idiomas do Lucene e 50 analisadores de processamento de linguagem natural da Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se para uma configuração definida pelo utilizador de uma combinação de elementos existentes, constituída por um atomizador (obrigatório) e filtros opcionais (char ou token).|

Algumas predefinidas analisadores, tal como **padrão** ou **parar**, suporta um conjunto limitado de opções de configuração. Para definir essas opções, efetivamente cria um analisador personalizado, consistindo no analisador de predefinida e uma das opções alternativas documentados em [predefinidos de analisador de referência](index-add-custom-analyzers.md#AnalyzerTable). Como com qualquer configuração personalizada, forneça a nova configuração com um nome, como *myPatternAnalyzer* para distingui-la a partir do analisador de Lucene padrão.

## <a name="how-to-specify-analyzers"></a>Como especificar analisadores

1. (para apenas analisadores personalizados) Criar uma determinada **analyzer** secção na definição do índice. Para obter mais informações, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [adicionar analisadores personalizados](index-add-custom-analyzers.md).

2. Num [campo definição](https://docs.microsoft.com/rest/api/searchservice/create-index) no índice, defina o campo **analyzer** propriedade para o nome de um analisador de destino (por exemplo, `"analyzer" = "keyword"`. Valores válidos incluem o nome de um analisador predefinido, o analisador de idioma ou o analisador personalizado também definido no esquema do índice. Tencione atribuir analyzer na fase de definição de índice antes do índice é criado no serviço.

3. Opcionalmente, em vez de um **analisador** propriedade, pode definir diferentes analisadores de indexação e consulta utilizando o **indexAnalyzer** e **searchAnalyzer** campo parâmetros. Usaria analisadores diferentes para preparação de dados e a obtenção de se uma dessas atividades necessário uma transformação específica não são necessária por outros.

Atribuindo **analisador** ou **indexAnalyzer** a um campo que já tenha sido criado fisicamente não é permitida. Se isto não está clara, reveja a tabela seguinte para uma análise detalhada dos quais ações exigem uma recompilação e por que.
 
 | Cenário | Impacto | Passos |
 |----------|--------|-------|
 | Adicionar um novo campo | minimal | Se o campo ainda não existe no esquema, não há nenhum revisão do campo porque o campo ainda não tiver uma presença física no seu índice. Pode usar [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) para adicionar um novo campo para um índice existente, e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para preenchê-lo.|
 | Adicionar uma **analisador** ou **indexAnalyzer** para um campo indexado existente. | [rebuild](search-howto-reindex.md) | O índice invertido para esse campo tem de ser recriado desde o início e o conteúdo para os campos têm de ser reindexado. <br/> <br/>Para índices em permanente desenvolvimento, [elimine](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [criar](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice de retirada a nova definição de campo. <br/> <br/>Para índices em produção, pode diferir a reconstrução de um através da criação de um novo campo para fornecer a definição revisada e começar a usá-lo no lugar do antigo. Uso [índice de atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) incorporar o novo campo e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para preenchê-lo. Mais tarde, como parte da manutenção planeada de índice, é possível limpar o índice para remover campos obsoletos. |

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

O melhor momento para adicionar e atribuir analisadores é durante o desenvolvimento do Active Directory, quando remover e recriar índices é a rotina.

Como uma definição de índice solidifies, pode acrescentar novas construções de análise para um índice, mas tem de passar os **allowIndexDowntime** sinalizador para [atualização índice](https://docs.microsoft.com/rest/api/searchservice/update-index) se quiser evitar este erro:

*"Não permitida porque iria provocar períodos de indisponibilidade de atualização de índice. Para adicionar novos analisadores, tokenizers, filtros de token ou filtros de caractere a um índice existente, defina o parâmetro de consulta 'allowIndexDowntime' como 'true' no pedido de atualização de índice. Tenha em atenção que esta operação irá colocar o seu índice offline para, pelo menos, alguns segundos, fazendo com que a indexação e consulta de pedidos a falhar. Disponibilidade de desempenho e de escrita do índice pode ser afetada durante vários minutos, depois do índice é atualizado ou mais índices muito grandes."*

O mesmo se aplica ao atribuir um analisador para um campo. Um analisador é uma parte integral da definição do campo, para que possa adicionar apenas quando o campo é criado. Se pretender adicionar os analisadores de campos existentes, terá [de remover e recriar](search-howto-reindex.md) o índice, ou adicionar um novo campo com o analisador que pretende.

Como observado, uma exceção é o **searchAnalyzer** variante. Das três maneiras de especificar analisadores (**analisador**, **indexAnalyzer**, **searchAnalyzer**), apenas o **searchAnalyzer** atributo pode ser alterado num campo existente.

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta secção oferece orientação sobre como trabalhar com os analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura e escrita, a menos que tenha requisitos específicos

O Azure Search permite-lhe especificar diferentes analisadores de indexação e pesquisa via adicionais **indexAnalyzer** e **searchAnalyzer** parâmetros de campo. Se não for especificado, o analisador de conjunto com o **analyzer** propriedade é utilizada para a indexação e a pesquisa. Se `analyzer` é especificado, é utilizado o analisador de Lucene padrão de predefinição.

Regra geral é usar o mesmo analyzer para indexação e consulta, a menos que necessidades específicas determinem o contrário. Certifique-se de que testar minuciosamente. Quando o processamento de texto é diferente no tempo de indexação e pesquisa, corre o risco de erro de correspondência entre os termos de consulta e termos indexados quando a pesquisa e indexação de analisador de configurações não está alinhado.

### <a name="test-during-active-development"></a>Testar durante o desenvolvimento do Active Directory

Substituir o analisador padrão requer uma recompilação de índice. Se possível, decida sobre qual analisadores para utilizar durante o desenvolvimento do Active Directory, antes de implementar um índice em produção.

### <a name="inspect-tokenized-terms"></a>Inspecione os termos com token

Se uma pesquisa não retornar resultados esperados, o cenário mais provável é o token discrepâncias entre as entradas de termo na consulta e com token termos no índice. Se os tokens não forem iguais, correspondências não materializar. Para inspecionar a saída de tokenizer, recomendamos que utilize o [API analisar](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como uma ferramenta de investigação. A resposta é composta por tokens, à medida que gerados por um analisador específico.

### <a name="compare-english-analyzers"></a>Compare os analisadores em inglês

O [demonstração do Search Analyzer](https://alice.unearth.ai/) é uma aplicação de demonstração de terceiros que mostra uma comparação lado a lado do analisador de Lucene padrão, o analisador de idioma inglês do Lucene e o processador de idioma natural da Microsoft. O índice é fixo; ela contém o texto de uma história popular. Para cada entrada de pesquisa é fornecer, resultados de cada analisador são apresentados nos painéis adjacentes, dando-lhe uma noção de como cada analisador processa a mesma cadeia de caracteres. 

<a name="examples"></a>

## <a name="rest-examples"></a>Exemplos REST

Os exemplos abaixo mostram as definições do analyzer para alguns cenários essenciais.

+ [Exemplo de analisador personalizado](#Custom-analyzer-example)
+ [Atribuir analisadores para um exemplo de campo](#Per-field-analyzer-assignment-example)
+ [Misturar analisadores de indexação e pesquisa](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemplo do analisador de idioma](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemplo de analisador personalizado

Este exemplo ilustra uma definição de analisador com opções personalizadas. Opções personalizadas para os filtros de char, tokenizers e filtros de token são especificadas em separado como construções com nome e, em seguida, referenciadas na definição do analyzer. Elementos predefinidos são utilizados como-é e simplesmente referenciado por nome.

Percorrendo neste exemplo:

* Analisadores são uma propriedade da classe de campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Ele pode ser apenas superficialmente personalizado (por exemplo, personalizar uma única opção de um filtro) ou personalizado em vários locais.
* Neste caso, o analisador personalizado é "my_analyzer", que por sua vez usa um atomizador padrão personalizado "my_standard_tokenizer" e dois filtros de token: filtro de asciifolding em minúsculas e personalizadas "my_asciifolding".
* Também define a 2 de char personalizado de filtros "map_dash" e "remove_whitespace". Primeiro substitui todos os traços com carateres de sublinhado, enquanto a segunda remove todos os espaços. Espaços precisam ser codificados nas regras de mapeamento de UTF-8. Os filtros de char são aplicados antes de atomização e afetarão os tokens resultantes (os padrão atomizador quebras no dash e espaços, mas não no caráter de sublinhado).

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

### <a name="per-field-analyzer-assignment-example"></a>Exemplo de atribuição de analisador por campo

O analisador padrão é a predefinição. Suponha que pretende substituir a predefinição com um analisador de predefinidos diferente, como o analisador padrão. Se não forem opções personalizadas de definição, só precisa de especificá-lo pelo nome na definição do campo.

O elemento "analyzer" substitui o analisador padrão numa base de campo por campo. Não há nenhuma substituição global. Neste exemplo, `text1` utiliza o analisador padrão e `text2`, que não especifica um analisador, utiliza a predefinição.

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

As APIs incluem os atributos de índice adicional para especificar diferentes analisadores de indexação e pesquisa. O **searchAnalyzer** e **indexAnalyzer** atributos tem de ser especificados como um par, substituindo o único **analisador** atributo.


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

### <a name="language-analyzer-example"></a>Exemplo do analisador de idioma

Campos que contenham as cadeias de caracteres em diferentes idiomas podem usar um analisador de idioma, enquanto outros campos mantém a predefinição (ou utilizam outro analisador predefinida ou personalizada). Se usar um analisador de idioma, tem de ser utilizado para operações de indexação e pesquisa. Campos que utilizam um analisador de idioma não é possível ter diferentes analisadores para indexação e pesquisa.

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

## <a name="c-examples"></a>C#Exemplos

Se estiver a utilizar os exemplos de código do SDK do .NET, pode acrescentar estes exemplos para utilizar ou configurar os analisadores.

+ [Atribuir um analisador de interno](#Assign-a-language-analyzer)
+ [Configurar um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de idioma

Qualquer analisador que é utilizado como-é, sem qualquer configuração, é especificada numa definição de campo. Não existe nenhum requisito para a criação de uma construção do analyzer. 

Este exemplo atribui analisadores English da Microsoft e francês aos campos de descrição. É um trecho de código obtido a partir de uma definição de maior do índice de hotéis, usando a classe de Hotel no arquivo hotels.cs de a criar o [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) exemplo.

Chamar [analisador](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), a especificação do [AnalyzerName classe](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) que fornece todos os analisadores de texto suportados no Azure Search.

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

Quando a personalização ou configuração é necessária, terá de adicionar uma construção do analyzer para um índice. Depois de defini-lo, pode adicioná-lo a definição de campo como demonstrado no exemplo anterior.

Uso [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) para criar o objeto. Para obter mais exemplos, consulte [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

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

## <a name="next-steps"></a>Passos Seguintes

+ Reveja nossa explicação abrangente das [completa como funciona a pesquisa de texto no Azure Search](search-lucene-query-architecture.md). Este artigo utiliza exemplos para explicar os comportamentos que podem parecer achem à primeira vista.

+ Experimente a sintaxe de consulta adicionais do [documentos sobre pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) secção de exemplo ou a partir de [sintaxe de consulta simples](query-simple-syntax.md) no Explorador de pesquisa no portal.

+ Saiba como aplicar [analisadores lexicais de idioma específico](index-add-language-analyzers.md).

+ [Configurar os analisadores personalizados](index-add-custom-analyzers.md) para processamento mínimo ou processamento especializado em campos individuais.

+ [Compare os analisadores de padrão e em inglês](https://alice.unearth.ai/) nos painéis adjacentes neste site da web de demonstração. 

## <a name="see-also"></a>Consulte também

 [Procurar nos documentos de REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completa](query-lucene-syntax.md) 
 
 [Processar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
