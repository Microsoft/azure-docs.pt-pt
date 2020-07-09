---
title: Criar uma definição de índice e conceitos
titleSuffix: Azure Cognitive Search
description: Introdução aos termos e conceitos de índice na Pesquisa Cognitiva Azure, incluindo componentes e estrutura física.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d2b8b2fecbf85e6590294f1fbd7ff2a4453b9e87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79282786"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Criar um índice básico na Pesquisa Cognitiva Azure

Em Azure Cognitive Search, um *índice* é uma loja persistente de *documentos* e outras construções usadas para pesquisa de texto filtrada e completa em um serviço de Pesquisa Cognitiva Azure. Conceptualmente, um documento é uma única unidade de dados pesmáveis no seu índice. Por exemplo, um retalhista de comércio eletrónico pode ter um documento para cada artigo que vende, uma empresa jornalística pode ter um documento para cada artigo, etc. Mapeamento destes conceitos para equivalentes da base de dados mais familiares: um *índice* é, conceptualmente, semelhante a uma *tabela* e os *documentos* são quase equivalentes a *linhas* numa tabela.

Quando adiciona ou carrega um índice, a Azure Cognitive Search cria estruturas físicas com base no esquema que fornece. Por exemplo, se um campo no seu índice for marcado como pes pespável, é criado um índice invertido para esse campo. Mais tarde, quando adiciona ou envia documentos, ou envia consultas de pesquisa para a Azure Cognitive Search, está a enviar pedidos para um índice específico no seu serviço de pesquisa. Os campos de carregamento com valores de documento são chamados *de indexação* ou ingestão de dados.

Pode criar um índice no portal REST [API,](search-create-index-rest-api.md)ou [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Chegar ao design do índice certo é normalmente alcançado através de várias iterações. A utilização de uma combinação de ferramentas e APIs pode ajudá-lo a finalizar rapidamente o seu design.

1. Determine se pode utilizar um [indexante.](search-indexer-overview.md#supported-data-sources) Se os seus dados externos forem uma das fontes de dados suportadas, pode protótipo e carregar um índice utilizando o assistente [**de dados De importação.**](search-import-data-portal.md)

2. Se não puder utilizar **dados de importação,** ainda pode [criar um índice inicial no portal](search-create-index-portal.md), adicionando campos, tipos de dados e atribuindo atributos usando controlos na página Add **Index.** O portal mostra quais os atributos disponíveis para diferentes tipos de dados. Se você é novo no design de índice, isso é útil.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-create-index-portal/field-attributes.png "Adicionar página de índice mostrando atributos por tipo de dados")
  
   Ao clicar em **Criar,** todas as estruturas físicas que suportam o seu índice são criadas no seu serviço de pesquisa.

3. Descarregue o esquema de índice usando [Get Index REST API](https://docs.microsoft.com/rest/api/searchservice/get-index) e uma ferramenta de teste web como [o Carteiro](search-get-started-postman.md). Tem agora uma representação JSON do índice que criou no portal. 

   Neste momento, está a mudar para uma abordagem baseada em códigos. O portal não é adequado para a iteração porque não é possível editar um índice que já está criado. Mas pode usar o Carteiro e o REST para as restantes tarefas.

4. [Carregue o seu índice com dados.](search-what-is-data-import.md) A Azure Cognitive Search aceita documentos JSON. Para carregar os seus dados programáticamente, pode utilizar o Carteiro com documentos JSON na carga útil do pedido. Se os seus dados não forem facilmente expressos como JSON, este passo será o mais intensivo de trabalho.

5. Consulte o seu índice, examine os resultados e itere ainda mais no esquema de índice até começar a ver os resultados que espera. Pode utilizar [**o Explorador de Pesquisa**](search-explorer.md) ou Carteiro para consultar o seu índice.

6. Continue a utilizar o código para iterar sobre o seu design.  

Porque as estruturas físicas são criadas no serviço, [a queda e a recriação de índices](search-howto-reindex.md) são necessárias sempre que se faz alterações materiais para uma definição de campo existente. Isto significa que, durante o desenvolvimento, deve planear reconstruções frequentes. Pode considerar trabalhar com um subconjunto dos seus dados para fazer com que as reconstruções vão mais rápido. 

O código, em vez de uma abordagem de portal, é recomendado para o design iterativo. Se depender do portal para a definição de índice, terá de preencher a definição de índice em cada reconstrução. Como alternativa, ferramentas como [o Carteiro e a API REST](search-get-started-postman.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão em fase inicial. Pode escruissar alterações incrementais a uma definição de índice num organismo de pedido e, em seguida, enviar o pedido ao seu serviço para recriar um índice utilizando um esquema atualizado.

## <a name="components-of-an-index"></a>Componentes de um índice

Schematicamente, um índice de Pesquisa Cognitiva Azure é composto pelos seguintes elementos. 

A recolha de [*campos*](#fields-collection) é tipicamente a maior parte de um índice, onde cada campo é nomeado, dactilografado e atribuído com comportamentos admissíveis que determinam como é usado. Outros elementos incluem [sugestivos,](#suggesters) [perfis de pontuação,](#scoring-profiles) [analisadores](#analyzers) com componentes para suportar opções de personalização, [CORS](#cors) e [chave de encriptação.](#encryption-key)

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

Na definição do esquema, tem de especificar o nome, o tipo e os atributos de cada campo do índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar o modo de utilização do campo. As tabelas seguintes enumeram os tipos e os atributos que pode especificar.

### <a name="data-types"></a>Tipos de dados
| Tipo | Description |
| --- | --- |
| *Edm.String* |Texto que pode opcionalmente ser tokenized para pesquisa de texto completo (quebra de palavras, cauling, e assim por diante). |
| *Collection(Edm.String)* |Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções. |
| *Edm.Boolean* |Contém valores verdadeiro/falso. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de hora de data representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica no mundo. |

Pode encontrar informações mais detalhadas sobre os tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)pela Azure Cognitive Search aqui.

### <a name="index-attributes"></a>Atributos do índice

Exatamente um campo no seu índice deve ser o designado como um campo **chave** que identifica exclusivamente cada documento.

Outros atributos determinam como um campo é usado numa aplicação. Para melhor, **para** que possa ser divulgado o mesmo ponto de saúde. 

As APIs que usa para construir um índice têm diferentes comportamentos predefinidos. Para as [APIs REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index), a maioria dos atributos são ativados por padrão (por exemplo, **pesjáveis** e **recuperáveis** são verdadeiros para campos de cordas) e muitas vezes só precisa de os definir se quiser desligá-los. Para o .NET SDK, o oposto é verdade. Em qualquer propriedade que não se desconte explicitamente, o padrão é desativar o comportamento de pesquisa correspondente, a menos que o ative especificamente.

| Atributo | Descrição |
| --- | --- |
| `key` |Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| `retrievable` |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| `filterable` |Permite que o campo seja utilizado nas consultas de filtro.  |
| `Sortable` |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| `facetable` |Permite a um campo ser utilizado numa estrutura de [navegação por facetas](search-faceted-navigation.md) para filtragem do utilizador auto-direcionada. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| `searchable` |Marca o campo como pesquisável no texto completo. |

## <a name="index-size"></a>Tamanho do índice

O tamanho de um índice é determinado pelo tamanho dos documentos que carrega, mais a configuração do índice, como se inclui sugestivos e como define atributos em campos individuais. A imagem a seguir ilustra padrões de armazenamento de índices resultantes de várias combinações de atributos.

O índice baseia-se na fonte de dados [de amostras imobiliárias incorporada,](search-get-started-portal.md) que pode indexar e consultar no portal. Embora os esquemas de índice não sejam mostrados, pode inferir os atributos com base no nome do índice. Por exemplo, o índice *de procuração imobiliária* tem o atributo **pesmável** selecionado e nada mais, o índice *realestate-recuperável* tem o atributo **recuperável** selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributos](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributos")

Embora estas variantes de índice sejam artificiais, podemos referir-nos a elas para comparações amplas de como os atributos afetam o armazenamento. A definição **recuperável** aumenta o tamanho do índice? Não. Adicionar campos a um **Indicador** aumenta o tamanho do índice? Sim.

Os índices que suportam filtro e classificação são proporcionalmente maiores do que aqueles que suportam apenas a pesquisa completa de texto. Filtrar e ordenar as operações para obter correspondências exatas, requerendo a presença de documentos intactos. Em contraste, os campos pes pesjáveis que suportam o texto completo e a pesquisa fuzzy usam índices invertidos, que são povoados com termos simbólicos que consomem menos espaço do que documentos inteiros. 

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação da Azure Cognitive Search e pode mudar sem aviso prévio. Não há garantias de que o comportamento atual permaneça no futuro.

## <a name="suggesters"></a>Sugestões
Um sugestivo é uma secção do esquema que define quais os campos de um índice que são usados para suportar consultas auto-completas ou de tipo-à-frente em pesquisas. Normalmente, as cadeias parciais de pesquisa são enviadas para as [Sugestões (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) enquanto o utilizador está digitando uma consulta de pesquisa, e a API devolve um conjunto de documentos ou frases sugeridos. 

Os campos adicionados a um sugestivo são usados para construir termos de pesquisa de tipo-à-frente. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para obter mais informações sobre a criação de uma estrutura sugestiva, consulte [Add suggesters](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Perfis de classificação

Um [perfil de pontuação](index-add-scoring-profiles.md) é uma secção do esquema que define comportamentos de pontuação personalizados que permitem influenciar quais itens aparecem mais altos nos resultados da pesquisa. Os perfis de pontuação são compostos por pesos e funções de campo. Para usá-los, especifique um perfil pelo nome na cadeia de consulta.

Um perfil de pontuação padrão funciona nos bastidores para calcular uma pontuação de pesquisa de cada item num conjunto de resultados. Podes usar o perfil de pontuação interna e sem nome. Em alternativa, **desafine o PredefinidoSSesofilar** para utilizar um perfil personalizado como padrão, invocado sempre que um perfil personalizado não seja especificado na cadeia de consulta.

## <a name="analyzers"></a>Analisadores

O elemento dos analisadores define o nome do analisador de idiomas para usar para o campo. Para obter mais informações sobre a gama de analisadores disponíveis para si, consulte [adicionar analisadores a um índice de Pesquisa Cognitiva Azure](search-analyzers.md). Os analisadores só podem ser utilizados com campos pesquisáveis. Uma vez que o analisador é atribuído a um campo, ele não pode ser alterado a menos que você reconstrua o índice.

## <a name="cors"></a>CORS

O JavaScript do lado do cliente não pode ligar para apis por padrão, uma vez que o navegador impedirá todos os pedidos de origem cruzada. Para permitir consultas de origem cruzada no seu índice, ative o CORS (Partilha de Recursos de Origem Cruzada) definindo o atributo **corsOptions.** Por razões de segurança, apenas as APIs de consulta suportam o CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Esta é uma lista de origens que terá acesso ao seu índice. Isto significa que qualquer código JavaScript servido a partir dessas origens será permitido consultar o seu índice (assumindo que fornece a chave api correta). Cada origem é tipicamente da `protocol://<fully-qualified-domain-name>:<port>` forma, embora `<port>` muitas vezes seja omitida. Consulte [a partilha de recursos cross-origin (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se quiser permitir o acesso a todas as origens, inclua `*` como um único item na matriz permitida **Porgins.** *Esta prática não é recomendada para serviços de pesquisa de produção,* mas é muitas vezes útil para o desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores utilizam este valor para determinar a duração (em segundos) para cache respostas de pré-voo CORS. Este deve ser um número inteiro não negativo. Quanto maior for este valor, melhor será o desempenho, mas quanto mais tempo demorar para que as mudanças de política do CORS produzam efeitos. Se não estiver definido, utilizar-se-á uma duração predefinida de 5 minutos.

## <a name="encryption-key"></a>Chave de encriptação

Embora todos os índices de Pesquisa Cognitiva do Azure sejam encriptados por padrão utilizando teclas geridas pela Microsoft, os índices podem ser configurados para serem encriptados com **chaves geridas pelo cliente** no Key Vault. Para saber mais, consulte [Gerir as teclas de encriptação em Azure Cognitive Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Próximos passos

Com uma compreensão da composição do índice, pode continuar no portal para criar o seu primeiro índice.

> [!div class="nextstepaction"]
> [Adicione um índice (portal)](search-create-index-portal.md)
