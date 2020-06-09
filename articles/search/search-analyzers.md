---
title: Analisadores para processamento linguístico e de texto
titleSuffix: Azure Cognitive Search
description: Atribua os analisadores a campos de texto pesquisáveis num índice para substituir o padrão padrão Lucene por alternativas personalizadas, predefinidas ou específicas da linguagem.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: a7fb5d9274771fb736e9373e343a1d520fdbbe55
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553130"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analisadores para processamento de texto em Pesquisa Cognitiva Azure

Um *analisador* é um componente do motor de pesquisa de [texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de consulta e documentos indexados. Os processos são transformadores, modificando uma cadeia através de ações como estas:

+ Remover palavras não essenciais (palavras de ordem) e pontuação
+ Divida frases e palavras hifenizadas em componentes
+ Minúsculas palavras maiússãos
+ Reduza as palavras em formas de raiz primitivas para eficiência de armazenamento e para que os fósforos possam ser encontrados independentemente do tempo

A análise ocorre durante a indexação quando o índice é construído, e depois novamente durante a execução de consulta quando o índice é lido. É mais provável que obtenha os resultados de pesquisa que espera se utilizar o mesmo analisador para ambas as operações.

Se não estiver familiarizado com a análise de texto, ouça o seguinte videoclipe para uma breve explicação de como o processamento de texto funciona na Pesquisa Cognitiva Azure.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]

## <a name="default-analyzer"></a>Analisador padrão  

Nas consultas de Pesquisa Cognitiva Azure, um analisador de texto é automaticamente invocado em todos os campos de cordas marcados como pesmáveis. 

Por padrão, a Azure Cognitive Search utiliza o [analisador Apache Lucene Standard (padrão lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), que quebra o texto em elementos seguindo as regras [de "Unicode Text Segmentation".](https://unicode.org/reports/tr29/) Além disso, o analisador padrão converte todos os caracteres para a sua forma minúscula. Tanto os documentos indexados como os termos de pesquisa passam pela análise durante o processamento de indexação e consulta.  

Pode anular o padrão numa base campo a campo. Os analisadores alternativos podem ser um [analisador de linguagem](index-add-language-analyzers.md) para o processamento linguístico, um [analisador personalizado,](index-add-custom-analyzers.md)ou um analisador predefinido da [lista de analisadores disponíveis.](index-add-custom-analyzers.md#AnalyzerTable)

## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista que se segue descreve quais os analisadores disponíveis na Pesquisa Cognitiva de Azure.

| Categoria | Description |
|----------|-------------|
| [Analisador padrão lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predefinição. Não é necessária especificação ou configuração. Este analisador de propósito geral tem um bom desempenho para muitas línguas e cenários.|
| Analisadores predefinidos | Oferecido como um produto acabado destinado a ser utilizado como tal. <br/>Há dois tipos: especializados e linguagem. O que os torna "predefinidos" é que os referencia pelo nome, sem configuração ou personalização. <br/><br/>[Os analisadores especializados (linguísticos agnósticos)](index-add-custom-analyzers.md#AnalyzerTable) são utilizados quando as entradas de texto requerem processamento especializado ou processamento mínimo. Os analisadores predefinidos não linguísticos incluem **Asciifolding,** **Palavra-chave,** **Padrão,** **Simples,** **Paragem,** **Espaço Branco.**<br/><br/>[Os analisadores linguísticos](index-add-language-analyzers.md) são usados quando você precisa de um apoio linguístico rico para línguas individuais. A Azure Cognitive Search suporta 35 analisadores de língua Lucene e 50 analisadores de processamento de linguagem natural da Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se a uma configuração definida pelo utilizador de uma combinação de elementos existentes, composta por um tokenizer (obrigatório) e filtros opcionais (char ou token).|

Alguns analisadores predefinidos, tais como **Pattern** ou **Stop,** suportam um conjunto limitado de opções de configuração. Para definir estas opções, cria efetivamente um analisador personalizado, constituído pelo analisador predefinido e uma das opções alternativas documentadas no [Referenciador de Analisador Predefinido.](index-add-custom-analyzers.md#AnalyzerTable) Como em qualquer configuração personalizada, forneça a sua nova configuração com um nome, como *myPatternAnalyzer* para distingui-la do analisador Desímile Padrão.

## <a name="how-to-specify-analyzers"></a>Como especificar os analisadores

1. (apenas para analisadores personalizados) Crie uma secção **de analisador** nomeada na definição de índice. Para obter mais informações, consulte [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [Adicione analisadores personalizados.](index-add-custom-analyzers.md)

2. Numa [definição](https://docs.microsoft.com/rest/api/searchservice/create-index) de campo no índice, desaponte a propriedade do **analisador** do campo para o nome de um analisador-alvo (por exemplo, `"analyzer" = "keyword"` . Valores válidos incluem nome de um analisador predefinido, analisador de linguagem ou analisador personalizado também definido no esquema de índice. Plano de atribuição de analisador na fase de definição de índice antes de o índice ser criado no serviço.

3. Opcionalmente, em vez de uma propriedade **de analisador,** você pode definir diferentes analisadores para indexar e consultar usando os parâmetros de campo **indexAnlyzer** e **searchAnalyzer.** Utilizaria diferentes analisadores para a preparação e recuperação de dados se uma dessas atividades exigisse uma transformação específica não necessária pela outra.

> [!NOTE]
> Não é possível utilizar um [analisador de linguagem](index-add-language-analyzers.md) diferente no tempo de indexação do que no tempo de consulta para um campo. Essa capacidade é reservada para [analisadores personalizados.](index-add-custom-analyzers.md) Por esta razão, se tentar definir as propriedades **de PesquisaNallyzer** ou **IndexAnalyzer** para o nome de um analisador de idiomas, a API REST devolverá uma resposta de erro. Em vez disso, deve utilizar a propriedade **do analisador.**

Não é permitido atribuir **o analisador** ou **indexAnalzer** a um campo que já foi criado fisicamente. Se alguma coisa não for clara, reveja a tabela seguinte para uma repartição das ações que requerem uma reconstrução e porquê.
 
 | Cenário | Impacto | Passos |
 |----------|--------|-------|
 | Adicionar um novo campo | mínimo | Se o campo ainda não existe no esquema, não há nenhuma revisão de campo a fazer porque o campo ainda não tem uma presença física no seu índice. Pode utilizar [o Índice de Atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) para adicionar um novo campo a um índice existente e fundir [oOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para o povoar.|
 | Adicione um **analisador** ou **indexAnalyzer** a um campo indexado existente. | [reconstruir](search-howto-reindex.md) | O índice invertido para esse campo deve ser recriado do zero para cima, e o conteúdo desses campos deve ser reindexado. <br/> <br/>Para os índices em desenvolvimento ativo, [elimine](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para captar a nova definição de campo. <br/> <br/>Para os índices de produção, pode adiar uma reconstrução criando um novo campo para fornecer a definição revista e começar a usá-la no lugar do antigo. Utilize o [Índice de Atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) para incorporar o novo campo e [fundir oOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para o povoar. Mais tarde, como parte da manutenção planeada do índice, pode limpar o índice para remover campos obsoletos. |

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

A melhor altura para adicionar e atribuir analisadores é durante o desenvolvimento ativo, quando cair e recriar índices é rotina.

À medida que uma definição de índice se solidifica, pode anexar novas construções de análise a um índice, mas terá de passar a bandeira **de tempo de acesso** ao Índice de [Atualização,](https://docs.microsoft.com/rest/api/searchservice/update-index) se quiser evitar este erro:

*"A atualização do índice não é permitida porque causaria tempo de inatividade. A fim de adicionar novos analisadores, tokenizers, filtros de fichas ou filtros de caracteres a um índice existente, defina o parâmetro de consulta 'allowIndexDowntime' para "verdadeiro" no pedido de atualização de índice. Note que esta operação irá colocar o seu índice offline durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. A disponibilidade de desempenho e de escrita do índice pode ser prejudicada por vários minutos após a atualização do índice, ou mais tempo para índices muito grandes."*

O mesmo se aplica ao atribuir um analisador a um campo. Um analisador é parte integrante da definição do campo, por isso só pode adicioná-lo quando o campo é criado. Se quiser adicionar analisadores aos campos existentes, terá de [deixar cair e reconstruir](search-howto-reindex.md) o índice, ou adicionar um novo campo com o analisador que pretende.

Como notado, uma exceção é a variante **searchAnalyzer.** Das três formas de especificar os analisadores **(analisador**, **indexAnalyzer,** **searchAnalyzer),** apenas o **atributo searchAnalyzer** pode ser alterado num campo existente.

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta secção oferece conselhos sobre como trabalhar com os analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para ler-escrever a menos que tenha requisitos específicos

A Azure Cognitive Search permite especificar diferentes analisadores para indexar e pesquisar através de parâmetros adicionais **de índiceAnlyzer** e **searchAnalyzer.** Se não especificado, o conjunto de analisador com a propriedade **do analisador** é usado tanto para indexar como para pesquisar. Se `analyzer` não for especificado, o analisador Standard Lucene padrão é utilizado.

Uma regra geral é utilizar o mesmo analisador tanto para indexar como para consultas, a menos que requisitos específicos decidam o contrário. Certifique-se de testar bem. Quando o processamento de texto difere no tempo de pesquisa e indexação, corre o risco de desfasamento entre termos de consulta e termos indexados quando as configurações do analisador de pesquisa e indexação não estão alinhadas.

### <a name="test-during-active-development"></a>Teste durante o desenvolvimento ativo

A sobredição do analisador padrão requer uma reconstrução de índice. Se possível, decida quais os analisadores a utilizar durante o desenvolvimento ativo, antes de lançar um índice na produção.

### <a name="inspect-tokenized-terms"></a>Inspecione os termos simbólicos

Se uma pesquisa não devolver os resultados esperados, o cenário mais provável são as discrepâncias simbólicas entre entradas de prazo na consulta e termos simbólicos no índice. Se as fichas não forem as mesmas, os fósforos não se concretizam. Para inspecionar a saída do tokenizer, recomendamos a utilização da [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como ferramenta de investigação. A resposta consiste em fichas, como gerado por um analisador específico.

<a name="examples"></a>

## <a name="rest-examples"></a>Exemplos DE REPOUSO

Os exemplos abaixo mostram definições de analisador para alguns cenários-chave.

+ [Exemplo de analisador personalizado](#Custom-analyzer-example)
+ [Atribuir analisadores a um exemplo de campo](#Per-field-analyzer-assignment-example)
+ [Misturar analisadores para indexação e pesquisa](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemplo de analisador de idiomas](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemplo de analisador personalizado

Este exemplo ilustra uma definição de analisador com opções personalizadas. As opções personalizadas para filtros de carvão, tokenizers e filtros de fichas são especificadas separadamente como construções nomeadas e, em seguida, referenciadas na definição do analisador. Os elementos predefinidos são usados como é e simplesmente referenciados pelo nome.

Caminhando por este exemplo:

* Os analisadores são uma propriedade da classe de campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Pode ser ligeiramente personalizado (por exemplo, personalizar uma única opção num filtro) ou personalizado em vários locais.
* Neste caso, o analisador personalizado é "my_analyzer", que por sua vez utiliza um tokenizer padrão personalizado "my_standard_tokenizer" e dois filtros simbólicos: filtro de asciifolding minúsculo e personalizado "my_asciifolding".
* Também define 2 filtros de carvão personalizados "map_dash" e "remove_whitespace". O primeiro substitui todos os traços por sublinhados enquanto o segundo remove todos os espaços. Os espaços precisam de ser UTF-8 codificados nas regras de mapeamento. Os filtros de carvão são aplicados antes da tokenização e afetarão os tokens resultantes (o tokenizer padrão quebra-se no traço e nos espaços, mas não em sublinhado).

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

O analisador Standard é o padrão. Suponha que pretende substituir o padrão por um analisador predefinido diferente, como o analisador de padrões. Se não estiver a definir opções personalizadas, só precisa de especificar o nome na definição de campo.

O elemento "analisador" substitui o analisador Standard numa base campo a campo. Não há sobreposição global. Neste exemplo, `text1` usa o analisador de padrões `text2` e, que não especifica um analisador, utiliza o padrão.

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

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos **searchAnalyzer** e **indexAnalyzer** devem ser especificados como um par, substituindo o atributo **de um único analisador.**


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

### <a name="language-analyzer-example"></a>Exemplo de analisador de idiomas

Os campos que contêm cordas em diferentes línguas podem usar um analisador de idiomas, enquanto outros campos retêm o padrão (ou usam algum outro analisador predefinido ou personalizado). Se utilizar um analisador de idiomas, este deve ser utilizado tanto para operações de indexação como para operações de pesquisa. Os campos que usam um analisador de idiomas não podem ter diferentes analisadores para indexar e pesquisar.

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

## <a name="c-examples"></a>Exemplos C#

Se estiver a utilizar as amostras de código .NET SDK, pode anexar estes exemplos para utilizar ou configurar os analisadores.

+ [Atribuir um analisador incorporado](#Assign-a-language-analyzer)
+ [Configure um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de idiomas

Qualquer analisador que seja utilizado como-é, sem configuração, é especificado numa definição de campo. Não há necessidade de criar uma construção de analisador. 

Este exemplo atribui aos analisadores de inglês e francês da Microsoft aos campos de descrição. É um corte retirado de uma definição maior do índice de hotéis, criando usando a classe Hotel no arquivo hotels.cs da amostra [DotNetHowTo.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), especificando o tipo [Dename Analisador](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) fornecendo um analisador de texto suportado na Pesquisa Cognitiva Azure.

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

### <a name="define-a-custom-analyzer"></a>Defina um analisador personalizado

Quando for necessária personalização ou configuração, terá de adicionar uma construção de analisador a um índice. Uma vez definido, pode adicioná-lo a definição de campo como demonstrado no exemplo anterior.

Crie um objeto [PersonalizadoAnalyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) Para mais exemplos, consulte [CustomAnalyzerTests.cs.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)

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

+ Reveja a nossa explicação completa de como a pesquisa completa de [texto funciona na Pesquisa Cognitiva Azure.](search-lucene-query-architecture.md) Este artigo usa exemplos para explicar comportamentos que podem parecer contraintuitivos na superfície.

+ Experimente sintaxe de consulta adicional a partir da secção de exemplo [de Documentos de Busca](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) ou a partir de [sintaxe](query-simple-syntax.md) de consulta simples no explorador de pesquisa no portal.

+ Saiba como aplicar [analisadores lexical específicos](index-add-language-analyzers.md)da linguagem.

+ [Configure os analisadores personalizados](index-add-custom-analyzers.md) para o processamento mínimo ou para o processamento especializado em campos individuais.

## <a name="see-also"></a>Ver também

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completo](query-lucene-syntax.md) 
 
 [Processar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
