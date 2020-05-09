---
title: Analisadores para processamento linguístico e de texto
titleSuffix: Azure Cognitive Search
description: Atribuir os analisadores a campos de texto pesquisáveis num índice para substituir o Lucene padrão padrão padrão por padrão padrão padrão por alternativas personalizadas, predefinidas ou específicas da linguagem.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d7be56fa48887e2ee500f1b253c078bde16d91e6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891234"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analisadores para processamento de texto em Pesquisa Cognitiva Azure

Um *analisador* é um componente do motor de pesquisa de [texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cordas de consulta e documentos indexados. Diferentes analisadores manipulam texto de diferentes maneiras dependendo do cenário. Os analisadores de linguagem processam texto usando regras linguísticas para melhorar a qualidade da pesquisa, enquanto outros analisadores executam tarefas mais básicas como converter caracteres em minúsculas, por exemplo. 

O segmento de vídeo seguinte avança para uma explicação de como o processamento de texto funciona na Pesquisa Cognitiva Azure.

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=132&end=189]


Os analisadores de linguagem são os mais utilizados, e há um analisador de linguagem padrão atribuído a todos os campos pesquisáveis num índice de Pesquisa Cognitiva Azure. As seguintes transformações linguísticas são típicas durante a análise de texto:

+ As palavras não essenciais (cronovers) e a pontuação são removidas.
+ Frases e palavras hifenizes são divididas em componentes.
+ As palavras maiúsculas são minúsculas.
+ As palavras são reduzidas a formas de raiz para que um fósforo possa ser encontrado independentemente do tenso.

Os analisadores de linguagem convertem uma entrada de texto em formas primitivas ou raiz sem condições que sejam eficientes para armazenamento e recuperação de informação. A conversão ocorre durante a indexação, quando o índice é construído, e novamente durante a pesquisa quando o índice é lido. É mais provável que obtenha os resultados de pesquisa que espera se utilizar o mesmo analisador para ambas as operações.

## <a name="default-analyzer"></a>Analisador padrão  

A Pesquisa Cognitiva Azure utiliza o [analisador Padrão Apache Lucene (lucene padrão)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) como padrão, que quebra o texto em elementos que seguem as regras [de "Segmentação de Texto Unicode".](https://unicode.org/reports/tr29/) Além disso, o analisador padrão converte todos os caracteres na sua forma minúscula. Tanto os documentos indexados como os termos de pesquisa passam pela análise durante o processamento de indexação e consulta.  

É usado automaticamente em todos os campos pesquisáveis. Pode anular o padrão numa base de campo a campo. Os analisadores alternativos podem ser um analisador de [linguagem,](index-add-language-analyzers.md) [um analisador personalizado,](index-add-custom-analyzers.md)ou um analisador predefinido da [lista de analisadores disponíveis.](index-add-custom-analyzers.md#AnalyzerTable)


## <a name="types-of-analyzers"></a>Tipos de analisadores

A lista seguinte descreve quais os analisadores disponíveis na Pesquisa Cognitiva Azure.

| Categoria | Descrição |
|----------|-------------|
| [Analisador Lucene Padrão](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Predefinição. Não é necessária especificação ou configuração. Este analisador de propósito geral tem um bom desempenho para a maioria das línguas e cenários.|
| Analisadores predefinidos | Oferecido como um produto acabado destinado a ser usado como está. <br/>Existem dois tipos: especializados e linguísticos. O que os torna "predefinidos" é que os referenciamos pelo nome, sem configuração ou personalização. <br/><br/>[Os analisadores especializados (agnósticos linguísticos)](index-add-custom-analyzers.md#AnalyzerTable) são utilizados quando as inputs de texto requerem processamento especializado ou processamento mínimo. Os analisadores predefinidos não-linguísticos incluem **Asciifolding,** **Palavra-chave,** **Padrão,** **Simples,** **Paragem,** **Espaço Branco.**<br/><br/>[Os analisadores de linguagem](index-add-language-analyzers.md) são usados quando você precisa de um apoio linguístico rico para línguas individuais. A Azure Cognitive Search suporta 35 analisadores de língualucene e 50 analisadores de processamento de linguagem natural da Microsoft. |
|[Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Refere-se a uma configuração definida pelo utilizador de uma combinação de elementos existentes, constituído por um tokenizer (obrigatório) e filtros opcionais (carvão ou ficha).|

Alguns analisadores predefinidos, como **Padrão** ou **Stop,** suportam um conjunto limitado de opções de configuração. Para definir estas opções, cria efetivamente um analisador personalizado, constituído pelo analisador predefinido e uma das opções alternativas documentadas na Referência do [Analisador Predefinido](index-add-custom-analyzers.md#AnalyzerTable). Como em qualquer configuração personalizada, forneça a sua nova configuração com um nome, como *o myPatternAnalyzer* para distingui-lo do analisador Lucene Pattern.

## <a name="how-to-specify-analyzers"></a>Como especificar os analisadores

1. (apenas para analisadores personalizados) Crie uma secção **de analisador** nomeada na definição de índice. Para mais informações, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e também [adicionar analisadores personalizados.](index-add-custom-analyzers.md)

2. Numa [definição](https://docs.microsoft.com/rest/api/searchservice/create-index) de campo no índice, defino a propriedade do **analisador** do `"analyzer" = "keyword"`campo para o nome de um analisador-alvo (por exemplo, . Os valores válidos incluem o nome de um analisador predefinido, analisador de linguagem ou analisador personalizado também definido no esquema do índice. Plano de atribuição de analisador na fase de definição de índice antes de o índice ser criado no serviço.

3. Opcionalmente, em vez de uma propriedade **de analisador,** pode definir diferentes analisadores para indexação e consulta utilizando os parâmetros de campo **indexAnalyzer** e **searchAnalyzer.** Utilizaria diferentes analisadores para a preparação e recuperação de dados se uma dessas atividades exigisse uma transformação específica não necessária pela outra.

> [!NOTE]
> Não é possível utilizar um [analisador](index-add-language-analyzers.md) de linguagem diferente no tempo de indexação do que no momento da consulta para um campo. Essa capacidade é reservada para [analisadores personalizados.](index-add-custom-analyzers.md) Por esta razão, se tentar definir as propriedades **do SearchAnalyzer** ou **indexAnalyzer** para o nome de um analisador de idiomas, a API REST devolverá uma resposta de erro. Em vez disso, deve usar a propriedade **do analisador.**

Não é permitido atribuir **o analisador** ou **indexAnalyzer** a um campo que já foi fisicamente criado. Se alguma destas medidas não for clara, reveja a tabela seguinte para uma repartição das quais as ações requerem uma reconstrução e porquê.
 
 | Cenário | Impacto | Passos |
 |----------|--------|-------|
 | Adicione um novo campo | mínimo | Se o campo ainda não existe no esquema, não há revisão de campo a fazer porque o campo ainda não tem uma presença física no seu índice. Pode utilizar o [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) para adicionar um novo campo a um índice existente e fundir o [OrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para o povoar.|
 | Adicione um **analisador** ou **indexAnalyzer** a um campo indexado existente. | [reconstruir](search-howto-reindex.md) | O índice invertido para esse campo deve ser recriado do zero para cima, e o conteúdo desses campos deve ser reindexado. <br/> <br/>Para índices em desenvolvimento ativo, [elimine](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [crie](https://docs.microsoft.com/rest/api/searchservice/create-index) o índice para pegar a nova definição de campo. <br/> <br/>Para os índices de produção, pode adiar uma reconstrução criando um novo campo para fornecer a definição revista e começar a usá-la no lugar do antigo. Utilize o Índice de [Atualização](https://docs.microsoft.com/rest/api/searchservice/update-index) para incorporar o novo campo e fundir o [OrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para o povoar. Mais tarde, como parte da manutenção planeada do índice, pode limpar o índice para remover campos obsoletos. |

## <a name="when-to-add-analyzers"></a>Quando adicionar analisadores

A melhor altura para adicionar e atribuir analisadores é durante o desenvolvimento ativo, quando a queda e recriação de índices é rotina.

Como uma definição de índice se solidifica, pode anexar novas construções de análise a um índice, mas terá de passar a bandeira **de permitir IndexDowntime** para [Atualizar o Index](https://docs.microsoft.com/rest/api/searchservice/update-index) se quiser evitar este erro:

*"A atualização do índice não é permitida porque causaria tempo de inatividade. A fim de adicionar novos analisadores, tokenizers, filtros de fichas ou filtros de caracteres a um índice existente, definiu o parâmetro de consulta 'allowIndexDowntime' como "verdadeiro" no pedido de atualização do índice. Note que esta operação irá colocar o seu índice offline durante pelo menos alguns segundos, fazendo com que os seus pedidos de indexação e consulta falhem. A disponibilidade de desempenho e escrita do índice pode ser prejudicada durante vários minutos após a atualização do índice, ou mais para índices muito grandes."*

O mesmo acontece quando se atribui um analisador a um campo. Um analisador é parte integrante da definição do campo, por isso só pode adicioná-lo quando o campo é criado. Se quiser adicionar os analisadores aos campos existentes, terá de [largar e reconstruir](search-howto-reindex.md) o índice, ou adicionar um novo campo com o analisador que deseja.

Como notado, uma exceção é a variante **searchAnalyzer.** Das três formas de especificar os analisadores **(analisador,** **indexAnalyzer,** **searchAnalyzer),** apenas o atributo **do searchAnalyzer** pode ser alterado num campo existente.

## <a name="recommendations-for-working-with-analyzers"></a>Recomendações para trabalhar com analisadores

Esta secção oferece conselhos sobre como trabalhar com os analisadores.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Um analisador para leitura-escrita a menos que tenha requisitos específicos

A Pesquisa Cognitiva Azure permite especificar diferentes analisadores para indexação e pesquisa através de parâmetros de campo de **indexador** e **pesquisaAnalyzer** adicionais. Se não especificado, o conjunto de analisador com a propriedade **do analisador** é usado tanto para indexação como para pesquisa. Se `analyzer` não for especificado, o analisador Padrão Lucene padrão padrão é usado.

Uma regra geral é usar o mesmo analisador tanto para indexação como para consulta, a menos que requisitos específicos ditem o contrário. Certifique-se de testar bem. Quando o processamento de texto difere no tempo de pesquisa e indexação, corre o risco de desfasamento entre termos de consulta e termos indexados quando as configurações de análise de pesquisa e indexação não estão alinhadas.

### <a name="test-during-active-development"></a>Teste durante o desenvolvimento ativo

Sobrepor o analisador padrão requer uma reconstrução do índice. Se possível, decida quais os analisadores a utilizar durante o desenvolvimento ativo, antes de enrolar um índice na produção.

### <a name="inspect-tokenized-terms"></a>Inspecionar termos tokenizados

Se uma pesquisa não devolver os resultados esperados, o cenário mais provável são discrepâncias simbólicas entre entradas de prazo na consulta e termos tokenizados no índice. Se as fichas não forem as mesmas, os fósforos não se concretizam. Para inspecionar a saída de tokenizer, recomendamos a utilização da [API de análise](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) como ferramenta de investigação. A resposta consiste em fichas, geradas por um analisador específico.

<a name="examples"></a>

## <a name="rest-examples"></a>Exemplos de REST

Os exemplos abaixo mostram definições de analisadores para alguns cenários-chave.

+ [Exemplo de analisador personalizado](#Custom-analyzer-example)
+ [Atribuir analisadores a um exemplo de campo](#Per-field-analyzer-assignment-example)
+ [Mistura de analisadores para indexação e pesquisa](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Exemplo de analisador de linguagem](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Exemplo de analisador personalizado

Este exemplo ilustra uma definição de analisador com opções personalizadas. As opções personalizadas para filtros de carvão, tokenizers e filtros de fichas são especificadas separadamente como construções nomeadas, e depois referenciadas na definição de analisador. Os elementos predefinidos são usados como eis e simplesmente referenciados pelo nome.

Caminhando por este exemplo:

* Os analisadores são uma propriedade da classe de campo para um campo pesquisável.
* Um analisador personalizado faz parte de uma definição de índice. Pode ser ligeiramente personalizado (por exemplo, personalizar uma única opção num só filtro) ou personalizado em vários lugares.
* Neste caso, o analisador personalizado é "my_analyzer", que por sua vez utiliza um tokenizer padrão personalizado "my_standard_tokenizer" e dois filtros simbólicos: filtro de asciifolding "my_asciifolding".
* Também define 2 filtros de carvão personalizados "map_dash" e "remove_whitespace". O primeiro substitui todas as traços por sublinhados, enquanto o segundo remove todos os espaços. Os espaços precisam de ser codificados nas regras de mapeamento. Os filtros de carvão são aplicados antes da tokenização e afetarão os tokens resultantes (o tokenizer padrão rompe-se no painel e nos espaços, mas não em sublinhado).

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

O analisador Standard é o padrão. Suponha que queira substituir o padrão por um analisador predefinido diferente, como o analisador de padrões. Se não estiver a definir opções personalizadas, basta especificar pelo nome na definição de campo.

O elemento "analisador" sobrepõe-se ao analisador Standard numa base de campo a campo. Não há nenhuma sobreposição global. Neste exemplo, `text1` utiliza o analisador de padrões e, `text2`que não especifica um analisador, utiliza o padrão.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mistura de analisadores para operações de indexação e pesquisa

As APIs incluem atributos de índice adicionais para especificar diferentes analisadores para indexação e pesquisa. Os atributos **searchAnalyzer** e **indexAnalyzer** devem ser especificados como um par, substituindo o atributo único **do analisador.**


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

### <a name="language-analyzer-example"></a>Exemplo de analisador de linguagem

Os campos que contêm cordas em diferentes línguas podem usar um analisador de linguagem, enquanto outros campos mantêm o padrão (ou usar algum outro analisador predefinido ou personalizado). Se utilizar um analisador de linguagem, este deve ser utilizado tanto para operações de indexação como de pesquisa. Os campos que usam um analisador de linguagem não podem ter diferentes analisadores para indexação e pesquisa.

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

## <a name="c-examples"></a>C# exemplos

Se estiver a utilizar as amostras de código .NET SDK, pode anexar estes exemplos para utilizar ou configurar os analisadores.

+ [Atribuir um analisador incorporado](#Assign-a-language-analyzer)
+ [Configure um analisador](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Atribuir um analisador de línguas

Qualquer analisador que seja usado como está, sem configuração, é especificado numa definição de campo. Não há necessidade de criar uma construção de analisador. 

Este exemplo atribui aos analisadores microsoft inglês e francês os campos de descrição. É um corte retirado de uma definição maior do índice de hotéis, criando usando a classe Hotel no arquivo hotels.cs da amostra [DotNetHowTo.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), especificando o tipo [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) fornecendo um analisador de texto suportado na Pesquisa Cognitiva Azure.

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

Crie um objeto [CustomAnalyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) Para mais exemplos, consulte [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

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

+ Reveja a nossa explicação exaustiva sobre como funciona a pesquisa completa de [texto em Azure Cognitive Search](search-lucene-query-architecture.md). Este artigo usa exemplos para explicar comportamentos que podem parecer contraintuitivos na superfície.

+ Experimente sintaxe adicional de consulta da secção de exemplo de [Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) de Pesquisa ou de [sintaxe](query-simple-syntax.md) de consulta simples no explorador de pesquisa no portal.

+ Aprenda a aplicar [analisadores lexicais específicos da linguagem](index-add-language-analyzers.md).

+ [Configure os analisadores personalizados](index-add-custom-analyzers.md) para um processamento mínimo ou processamento especializado em campos individuais.

## <a name="see-also"></a>Consulte também

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Pesquisar Documentos com a API REST) 

 [Sintaxe de consulta simples](query-simple-syntax.md) 

 [Sintaxe de consulta Lucene completo](query-lucene-syntax.md) 
 
 [Processar os resultados da pesquisa](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
