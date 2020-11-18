---
title: Criar um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Introduz conceitos e ferramentas de indexação na Pesquisa Cognitiva Azure, incluindo definições de esquema e estrutura de dados físicos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: aa7c06c3bad59bad11fa288631042cca86109706
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701138"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Criar um índice básico de pesquisa em Azure Cognitive Search

Na Pesquisa Cognitiva Azure, um *índice de pesquisa* armazena conteúdo pesmável utilizado para texto completo e consultas filtradas. Um índice é definido por um esquema e guardado para o serviço, com a importação de dados a seguir como segundo passo. 

Os índices contêm *documentos.* Conceptualmente, um documento é uma única unidade de dados pesmáveis no seu índice. Um retalhista pode ter um documento para cada produto, uma organização noticiosa pode ter um documento para cada artigo, e assim por diante. Mapear estes conceitos para equivalentes de base de dados mais familiares: um *índice de pesquisa* equivale a uma *tabela*, e *os documentos* são aproximadamente equivalentes a *linhas* numa tabela.

A estrutura física de um índice é determinada pelo esquema, com campos marcados como "pesmáveis" resultando num índice invertido criado para esse campo. 

Pode criar um índice com as seguintes ferramentas e APIs:

* No portal Azure, utilize índice **de adicionar** ou importar o assistente **de dados**
* Utilizando o [Índice de Criação (REST API)](/rest/api/searchservice/create-index)
* Utilizando o [.NET SDK](./search-get-started-dotnet.md)

É mais fácil aprender com uma ferramenta de portal. O portal aplica requisitos e regras de esquema para tipos de dados específicos, tais como a não permitir capacidades completas de pesquisa de texto em campos numéricos. Uma vez que tenha um índice viável, pode transitar para código recuperando a definição JSON do serviço usando [Get Index (REST API)](/rest/api/searchservice/get-index) e adicioná-lo à sua solução.

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Chegar a um design de índice final é um processo iterativo. É comum começar com o portal para criar o índice inicial e depois mudar para código para colocar o índice sob controlo de origem.

1. Determine se pode utilizar [**dados de importação.**](search-import-data-portal.md) O assistente executa a indexação baseada em indexante tudo-em-um se os dados de origem forem de um tipo de fonte de [dados suportado em Azure](search-indexer-overview.md#supported-data-sources).

1. Se não puder utilizar **os dados de importação,** comece com **Add Index** para definir o esquema.

   ![Adicionar comando de índice](media/search-what-is-an-index/add-index.png "Adicionar comando de índice")

1. Forneça um nome e uma chave usados para identificar de forma única cada documento de pesquisa no índice. A chave é obrigatória e deve ser do tipo Edm.String. Durante a importação, você deve planejar mapear um campo único em dados de origem para este campo. 

   O portal dá-lhe um `id` campo para a chave. Para anular o `id` padrão, crie um novo campo (por exemplo, uma nova definição de campo chamada ) `HotelId` e, em seguida, selecione-o na **Chave**.

   ![Preencha as propriedades necessárias](media/search-what-is-an-index//field-attributes.png "Preencha as propriedades necessárias")

1. Adicione mais campos. O portal mostra-lhe quais [os atributos de campo](#index-attributes) disponíveis para diferentes tipos de dados. Se você é novo no design de índice, isso é útil.

   Se os dados de entrada forem hierárquicos de natureza, atribua o tipo de dados [complexo](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de amostras incorporado, Hotéis, ilustra tipos complexos usando um Endereço (contém vários sub-campos) que tem uma relação um-para-um com cada hotel, e uma coleção complexa de quartos, onde vários quartos estão associados a cada hotel. 

1. Atribua todos [os Analisadores](#analyzers) aos campos de cordas antes da criação do índice. Faça o mesmo para [os sugestivos](#suggesters) se pretender obter um preconto automático em campos específicos.

1. Clique em **Criar** para construir as estruturas físicas no seu serviço de pesquisa.

1. Após a criação de um índice, utilize comandos adicionais para rever definições ou adicionar mais elementos.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-what-is-an-index//field-definitions.png "Adicionar página de índice mostrando atributos por tipo de dados")

1. Descarregue o esquema de índice usando [Get Index (REST API)](/rest/api/searchservice/get-index) e uma ferramenta de teste web como [o Carteiro.](search-get-started-rest.md) Tem agora uma representação JSON do índice que pode adaptar-se ao código.

1. [Carregue o seu índice com dados.](search-what-is-data-import.md) A Azure Cognitive Search aceita documentos JSON. Para carregar os seus dados programáticamente, pode utilizar o Carteiro com documentos JSON na carga útil do pedido. Se os seus dados não forem facilmente expressos como JSON, este passo será o mais intensivo de trabalho. 

    Uma vez que um índice é carregado com dados, a maioria das edições para os campos existentes exigirão que você caia e reconstrua um índice.

1. Consulte o seu índice, examine os resultados e itere ainda mais no esquema de índice até começar a ver os resultados que espera. Pode utilizar [**o Explorador de Pesquisa**](search-explorer.md) ou Carteiro para consultar o seu índice.

Durante o desenvolvimento, planeie reconstruções frequentes. Porque as estruturas físicas são criadas no serviço, [a queda e a recriação de índices](search-howto-reindex.md) são necessárias para a maioria das modificações a uma definição de campo existente. Pode considerar trabalhar com um subconjunto dos seus dados para fazer com que as reconstruções vão mais rápido. 

> [!Tip]
> O código, em vez de uma abordagem do portal, é recomendado para trabalhar em design de índice e importação de dados simultaneamente. Como alternativa, ferramentas como [o Postman e o Visual Studio Code](search-get-started-rest.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão em fase inicial. Pode escruissar alterações incrementais a uma definição de índice num organismo de pedido e, em seguida, enviar o pedido ao seu serviço para recriar um índice utilizando um esquema atualizado.

## <a name="index-schema"></a>Esquema de índice

Um índice é necessário para ter um nome e um campo-chave designado (de Edm.string) na coleção de campos. A recolha de [*campos*](#fields-collection) é tipicamente a maior parte de um índice, onde cada campo é nomeado, dactilografado e atribuído com comportamentos admissíveis que determinam como é usado. 

Outros elementos incluem [sugestivos](#suggesters), [perfis de pontuação,](#scoringprofiles) [analisadores](#analyzers) usados para processar cordas em fichas de acordo com regras linguísticas ou outras características suportadas pelo analisador, e definições [de script remoto de origem cruzada (CORS).](#corsoptions)

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Atributos de coleção de campos e de campo

Os campos têm um nome, um tipo que classifica os dados armazenados, e atributos que especificam como o campo é usado.

### <a name="data-types"></a>Tipos de dados

| Tipo | Description |
|------|-------------|
| Edm.String |Texto que pode opcionalmente ser tokenized para pesquisa de texto completo (quebra de palavras, cauling, e assim por diante). |
| Coleção (Edm.String) |Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções. |
| Edm.Boolean |Contém valores verdadeiro/falso. |
| Edm.Int32 |Valores inteiros de 32 bits. |
| Edm.Int64 |Valores inteiros de 64 bits. |
| Edm.Double |Dados numéricos de dupla precisão. |
| Edm.DateTimeOffset |Valores de hora de data representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| Edm.GeographyPoint |Um ponto que representa uma localização geográfica no mundo. |

Para obter mais informações, consulte [os tipos de dados suportados.](/rest/api/searchservice/Supported-data-types)

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributos

Os atributos de campo determinam como um campo é utilizado, por exemplo, se é utilizado na pesquisa em texto completo, navegação por facetas, operações de ordenação e assim sucessivamente. 

Os campos de cordas são frequentemente marcados como "pesmáveis" e "recuperáveis". Os campos utilizados para reduzir os resultados da pesquisa incluem "sortível", "filtrado" e "facetable".

|Atributo|Descrição|  
|---------------|-----------------|  
|"Pesmável" |Texto completo pesquisável, sujeito a análise lexical, como separação de palavras durante a indexação. Se definir um campo pesquisável para um valor como "sunny day", será dividido internamente nos tokens individuais "sunny" e "day". Para obter detalhes, veja [Como funciona a pesquisa em texto completo](search-lucene-query-architecture.md).|  
|"filtrado" |Referenciado na consulta $filter. Os campos filtráveis do tipo `Edm.String` ou `Collection(Edm.String)` não são submetidos a separação de palavras, pelo que as comparações destinam-se apenas a correspondências exatas. Por exemplo, se definir um campo f para "sunny day", `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` sim. |  
|"tributável" |Por predefinição, o sistema ordena os resultados por classificação, mas pode configurar a ordenação com base nos campos nos documentos. Campos do tipo `Collection(Edm.String)` não podem ser "ordenados". |  
|"facetable" |Normalmente, é utilizado numa apresentação de resultados de pesquisa que inclui uma contagem de resultados por categoria (por exemplo, hotéis numa cidade específica). Esta opção não pode ser utilizada com campos do tipo `Edm.GeographyPoint`. Os campos do tipo `Edm.String` que são filtrados, "ordenados", ou "facetable" podem ter no máximo 32 quilobytes de comprimento. Para mais detalhes, veja [Criar um Índice (API REST)](/rest/api/searchservice/create-index).|  
|"chave" |Identificador exclusivo de documentos no índice. Deve ser selecionado exatamente um campo como o campo de chave e tem de ser do tipo `Edm.String`.|  
|"recuperável" |Determina se o campo pode ser devolvido num resultado da pesquisa. Isto é útil quando quiser utilizar um campo (como *margem de lucro*) como mecanismo de filtro, ordenação ou classificação, mas não quer que o campo esteja visível para o utilizador final. Este atributo tem de ser `true` para campos `key`.|  

Embora possa adicionar novos campos em qualquer altura, as definições de campos existentes estão bloqueadas durante o ciclo de vida do índice. Por este motivo, os programadores utilizam normalmente o portal para criar índices simples, testar ideias ou utilizar as páginas do portal para procurar uma definição. A iteração frequente através de uma estrutura de índice é mais eficiente se seguir uma abordagem baseada em código de modo a poder reconstruir o índice facilmente.

> [!NOTE]
> As APIs que usa para construir um índice têm diferentes comportamentos predefinidos. Para as [APIs REST](/rest/api/searchservice/Create-Index), a maioria dos atributos são ativados por padrão (por exemplo, "pesmável" e "recuperável" são verdadeiros para campos de cordas) e muitas vezes só precisa de os definir se quiser desligá-los. Para o .NET SDK, o oposto é verdade. Em qualquer propriedade que não se desconte explicitamente, o padrão é desativar o comportamento de pesquisa correspondente, a menos que o ative especificamente.

## `analyzers`

O elemento dos analisadores define o nome do analisador de idiomas para usar para o campo. Para obter mais informações sobre a gama de analisadores disponíveis para si, consulte [adicionar analisadores a um índice de Pesquisa Cognitiva Azure](search-analyzers.md). Os analisadores só podem ser utilizados com campos pesquisáveis. Uma vez que o analisador é atribuído a um campo, ele não pode ser alterado a menos que você reconstrua o índice.

## `suggesters`

Um sugestivo é uma secção do esquema que define quais os campos de um índice que são usados para suportar consultas auto-completas ou de tipo-à-frente em pesquisas. Normalmente, as cadeias parciais de pesquisa são enviadas para as [Sugestões (REST API)](/rest/api/searchservice/suggestions) enquanto o utilizador está digitando uma consulta de pesquisa, e a API devolve um conjunto de documentos ou frases sugeridos. 

Os campos adicionados a um sugestivo são usados para construir termos de pesquisa de tipo-à-frente. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para obter mais informações sobre a criação de uma estrutura sugestiva, consulte [Add suggesters](index-add-suggesters.md).

## `corsOptions`

O JavaScript do lado do cliente não pode ligar para apis por padrão, uma vez que o navegador impedirá todos os pedidos de origem cruzada. Para permitir consultas de origem cruzada no seu índice, ative o CORS (Partilha de Recursos de Origem Cruzada) definindo o atributo **corsOptions.** Por razões de segurança, apenas as APIs de consulta suportam o CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Esta é uma lista de origens que terá acesso ao seu índice. Isto significa que qualquer código JavaScript servido a partir dessas origens será permitido consultar o seu índice (assumindo que fornece a chave api correta). Cada origem é tipicamente da `protocol://<fully-qualified-domain-name>:<port>` forma, embora `<port>` muitas vezes seja omitida. Consulte [a partilha de recursos cross-origin (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se quiser permitir o acesso a todas as origens, inclua `*` como um único item na matriz permitida **Porgins.** *Esta prática não é recomendada para serviços de pesquisa de produção,* mas é muitas vezes útil para o desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores utilizam este valor para determinar a duração (em segundos) para cache respostas de pré-voo CORS. Este deve ser um número inteiro não negativo. Quanto maior for este valor, melhor será o desempenho, mas quanto mais tempo demorar para que as mudanças de política do CORS produzam efeitos. Se não estiver definido, utilizar-se-á uma duração predefinida de 5 minutos.

## `scoringProfiles`

Um [perfil de pontuação](index-add-scoring-profiles.md) é uma secção do esquema que define comportamentos de pontuação personalizados que permitem influenciar quais itens aparecem mais altos nos resultados da pesquisa. Os perfis de pontuação são compostos por pesos e funções de campo. Para usá-los, especifique um perfil pelo nome na cadeia de consulta.

Um perfil de pontuação padrão funciona nos bastidores para calcular uma pontuação de pesquisa de cada item num conjunto de resultados. Podes usar o perfil de pontuação interna e sem nome. Em alternativa, **desafine o PredefinidoSSesofilar** para utilizar um perfil personalizado como padrão, invocado sempre que um perfil personalizado não seja especificado na cadeia de consulta.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributos e tamanho do índice (implicações de armazenamento)

O tamanho de um índice é determinado pelo tamanho dos documentos que carrega, mais a configuração do índice, como se inclui sugestivos e como define atributos em campos individuais. 

A imagem a seguir ilustra padrões de armazenamento de índices resultantes de várias combinações de atributos. O índice baseia-se no **índice de amostra de imóveis,** que pode criar facilmente utilizando o assistente de dados De importação. Embora os esquemas de índice não sejam mostrados, pode inferir os atributos com base no nome do índice. Por exemplo, o índice *de procuração imobiliária* tem o atributo "pesmável" selecionado e nada mais, o índice *realestate-recuperável* tem o atributo "recuperável" selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributos](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributos")

Embora estas variantes de índice sejam artificiais, podemos referir-nos a elas para comparações amplas de como os atributos afetam o armazenamento. A definição "recuperável" aumenta o tamanho do índice? Não. Adicionar campos a um **indicador aumenta** o tamanho do índice? Sim.

Os índices que suportam filtro e classificação são proporcionalmente maiores do que os índices que suportam apenas a pesquisa completa de texto. Isto porque filtrar e classificar as operações para obter correspondências exatas, exigindo a presença de cordas de texto verbatim. Em contraste, os campos pes pesjáveis que suportam consultas de texto completo usam índices invertidos, que são povoados com termos simbólicos que consomem menos espaço do que documentos inteiros. 

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação da Azure Cognitive Search e pode mudar sem aviso prévio. Não há garantias de que o comportamento atual permaneça no futuro.

## <a name="next-steps"></a>Passos seguintes

Com uma compreensão da composição do índice, pode continuar no portal para criar o seu primeiro índice. Recomendamos começar com o assistente de **dados de Importação,** escolhendo a *amostra realestate-us-us-amostra* ou *hotéis-amostra* de fontes de dados hospedados.

> [!div class="nextstepaction"]
> [Assistente de dados de importação (portal)](search-get-started-portal.md)

Para ambos os conjuntos de dados, o assistente pode inferir um esquema de índice, importar os dados e obter um índice pesmável que pode consultar usando o Search Explorer. Encontre estas fontes de dados na página de dados do assistente de **dados "Importar".** **Import data**

   ![Criar um índice de amostra](media/search-what-is-an-index//import-wizard-sample-data.png "Criar um índice de amostra")