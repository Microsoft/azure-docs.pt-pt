---
title: Criar uma definição de índice e conceitos-Azure Search
description: Introdução aos termos e conceitos de índice no Azure Search, incluindo partes de componente e de estrutura física.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a26cfc578f12044cb5834f202a0fed5d0a30274
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647375"
---
# <a name="create-a-basic-index-in-azure-search"></a>Criar um índice básico no Azure Search

No Azure Search, um *índice* é um repositório persistente de *documentos* e outras construções usadas para pesquisa de texto completo e filtrada em um serviço de Azure Search. Conceitualmente, um documento é uma única unidade de dados pesquisáveis no índice. Por exemplo, um retalhista de comércio eletrónico pode ter um documento para cada artigo que vende, uma empresa jornalística pode ter um documento para cada artigo, etc. Mapeamento destes conceitos para equivalentes da base de dados mais familiares: um *índice* é, conceptualmente, semelhante a uma *tabela* e os *documentos* são quase equivalentes a *linhas* numa tabela.

Quando você adiciona ou carrega um índice, Azure Search cria estruturas físicas com base no esquema fornecido. Por exemplo, se um campo no índice for marcado como pesquisável, um índice invertido será criado para esse campo. Posteriormente, quando você adicionar ou carregar documentos ou enviar consultas de pesquisa para Azure Search, você está enviando solicitações para um índice específico em seu serviço de pesquisa. O carregamento de campos com valores de documento é chamado de indexação ou ingestão de dados.

Você pode criar um índice no portal, na [API REST](search-create-index-rest-api.md)ou no [SDK do .net](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Chegar ao design correto do índice é normalmente obtido por meio de várias iterações. O uso de uma combinação de ferramentas e APIs pode ajudá-lo a finalizar seu design rapidamente.

1. Determine se você pode usar um [indexador](search-indexer-overview.md#supported-data-sources). Se os dados externos forem uma das fontes de dados com suporte, você poderá protótipo e carregamento de um índice usando o assistente de [**importação de dados**](search-import-data-portal.md) .

2. Se não for possível usar **importar dados**, você ainda poderá [criar um índice inicial no portal](search-create-index-portal.md), adicionando campos, tipos de dados e atribuindo atributos usando controles na página **Adicionar índice** . O portal mostra quais atributos estão disponíveis para diferentes tipos de dados. Se você for novo no design de índice, isso será útil.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-create-index-portal/field-attributes.png "Adicionar página de índice mostrando atributos por tipo de dados")
  
   Quando você clica em **criar**, todas as estruturas físicas que dão suporte ao índice são criadas no serviço de pesquisa.

3. Baixe o esquema de índice usando a [API REST do índice Get](https://docs.microsoft.com/rest/api/searchservice/get-index) e uma ferramenta de teste da Web como o [postmaster](search-get-started-postman.md). Agora você tem uma representação JSON do índice criado no Portal. 

   Você está alternando para uma abordagem baseada em código neste ponto. O portal não é adequado para iteração porque você não pode editar um índice que já foi criado. Mas você pode usar o postmaster e o REST para as tarefas restantes.

4. [Carregue o índice com dados](search-what-is-data-import.md). Azure Search aceita documentos JSON. Para carregar seus dados programaticamente, você pode usar o postmaster com documentos JSON na carga de solicitação. Se seus dados não forem expressos facilmente como JSON, essa etapa será a mais trabalhoso.

5. Consulte o índice, examine os resultados e itere ainda mais no esquema de índice até começar a ver os resultados esperados. Você pode usar o [**Search Explorer**](search-explorer.md) ou o postmaster para consultar o índice.

6. Continue usando o código para iterar em seu design.  

Como as estruturas físicas são criadas no serviço, é necessário descartar [e recriar índices](search-howto-reindex.md) sempre que você fizer alterações materiais em uma definição de campo existente. Isso significa que durante o desenvolvimento, você deve planejar as recompilações frequentes. Você pode considerar trabalhar com um subconjunto de seus dados para fazer com que as recompilações se tornem mais rápidas. 

O código, em vez de uma abordagem de portal, é recomendado para design iterativo. Se você depender do portal para definição de índice, terá que preencher a definição de índice em cada recompilação. Como alternativa, ferramentas como [o postmaster e a API REST](search-get-started-postman.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão em fases iniciais. Você pode fazer alterações incrementais em uma definição de índice em um corpo de solicitação e, em seguida, enviar a solicitação para o serviço para recriar um índice usando um esquema atualizado.

## <a name="components-of-an-index"></a>Componentes de um índice

Em esquemático, um índice de Azure Search é composto pelos elementos a seguir. 

A [*coleção Fields*](#fields-collection) normalmente é a parte maior de um índice, onde cada campo é nomeado, digitado e atribuído com comportamentos permitidos que determinam como ele é usado. Outros elementos incluem [](#suggesters)sugestores, [perfis de Pontuação](#scoring-profiles), analisadores com partes de componente para dar suporte à personalização, [CORS](#cors) e opções de [chave de criptografia](#encryption-key) . [](#analyzers)

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

## <a name="fields-collection-and-field-attributes"></a>Atributos de campo e coleção de campos

Na definição do esquema, tem de especificar o nome, o tipo e os atributos de cada campo do índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar o modo de utilização do campo. As tabelas seguintes enumeram os tipos e os atributos que pode especificar.

### <a name="data-types"></a>Tipos de dados
| Type | Descrição |
| --- | --- |
| *Edm.String* |Texto que pode, opcionalmente, ser indexado para pesquisa de texto completo (quebra de palavras, lematização e assim por diante). |
| *Collection(Edm.String)* |Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções. |
| *Edm.Boolean* |Contém valores verdadeiro/falso. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de data e hora representados no formato OData V4 (por `yyyy-MM-ddTHH:mm:ss.fffZ` exemplo `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`, ou). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica no mundo. |

Pode encontrar mais informações detalhadas sobre os [tipos de dados suportados aqui](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) do Azure Search.

### <a name="index-attributes"></a>Atributos de índice

Exatamente um campo no índice deve ser designado como um campo de **chave** que identifica exclusivamente cada documento.

Outros atributos determinam como um campo é usado em um aplicativo. Por exemplo, o atributo pesquisável é atribuído a cada campo que deve ser incluído em uma pesquisa de texto completo. 

As APIs usadas para criar um índice têm comportamentos padrão variados. Para as [APIs REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index), a maioria dos atributos é habilitada por padrão ( por exemplo, pesquisável e **recuperável** são verdadeiros para campos de cadeia de caracteres) e, muitas vezes, você só precisa defini-los se desejar desativá-los. Para o SDK do .NET, o oposto é true. Em qualquer propriedade que você não definir explicitamente, o padrão é desabilitar o comportamento de pesquisa correspondente, a menos que você o habilite especificamente.

| Atributo | Descrição |
| --- | --- |
| `key` |Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| `retrievable` |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| `filterable` |Permite que o campo seja utilizado nas consultas de filtro. |
| `Sortable` |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| `facetable` |Permite a um campo ser utilizado numa estrutura de [navegação por facetas](search-faceted-navigation.md) para filtragem do utilizador auto-direcionada. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| `searchable` |Marca o campo como pesquisável no texto completo. |


## <a name="storage-implications"></a>Implicações de armazenamento

Os atributos selecionados têm um impacto no armazenamento. A captura de tela a seguir ilustra os padrões de armazenamento de índice resultantes de várias combinações de atributos.

O índice é baseado na fonte de dados de [exemplo interna de imóveis](search-get-started-portal.md) , que você pode indexar e consultar no Portal. Embora os esquemas de índice não sejam mostrados, você pode inferir os atributos com base no nome do índice. Por exemplo, *realestate –* índice pesquisável tem o atributo pesquisável selecionado e nada mais, o índice *realestate-recuperável* tem o atributo **recuperável** selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributo](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributo")

Embora essas variantes de índice sejam artificiais, podemos nos referir a elas para comparações amplas de como os atributos afetam o armazenamento. A definição da **recuperação** aumenta o tamanho do índice? Não. Adicionar campos a um Sugestor aumenta o tamanho do índice? Sim.

Os índices que dão suporte ao filtro e à classificação são proporcionalmente maiores que os índices que dão suporte à pesquisa de texto completo. O motivo é que a consulta de filtro e classificação em correspondências exatas para que os documentos sejam armazenados intactos. Por outro lado, os campos pesquisáveis que dão suporte à pesquisa difusa e de texto completo usam índices invertidos, que são preenchidos com termos com token que consomem menos espaço do que documentos inteiros.

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação de Azure Search e pode ser alterada sem aviso prévio. Não há nenhuma garantia de que o comportamento atual persistirá no futuro.

## <a name="suggesters"></a>Sugestões
Um Sugestor é uma seção do esquema que define quais campos em um índice são usados para dar suporte a consultas de preenchimento automático ou de tipo antecipado em pesquisas. Normalmente, as cadeias de caracteres de pesquisa parciais são enviadas para as [sugestões (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions) enquanto o usuário está digitando uma consulta de pesquisa e a API retorna um conjunto de frases sugeridas. 

Os campos adicionados a um Sugestor são usados para criar termos de pesquisa de tipo antecipado. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para obter mais informações sobre como criar uma estrutura de sugestão, consulte [Adicionar](index-add-suggesters.md)sugestores.

## <a name="scoring-profiles"></a>Perfis de classificação

Um [perfil de Pontuação](index-add-scoring-profiles.md) é uma seção do esquema que define comportamentos de Pontuação personalizados que permitem que você influencie quais itens aparecem mais acima nos resultados da pesquisa. Os perfis de pontuação são compostos de funções e pesos de campo. Para usá-los, especifique um perfil por nome na cadeia de caracteres de consulta.

Um perfil de Pontuação padrão opera nos bastidores para calcular uma pontuação de pesquisa para cada item em um conjunto de resultados. Você pode usar o perfil de Pontuação interno e sem nome. Como alternativa, defina **defaultScoringProfile** para usar um perfil personalizado como o padrão, invocado sempre que um perfil personalizado não for especificado na cadeia de caracteres de consulta.

## <a name="analyzers"></a>Analisadores

O elemento de analisadores define o nome do analisador de idioma a ser usado para o campo. Para obter mais informações sobre o intervalo de analisadores disponíveis para você, consulte [adicionando analisadores a um índice de Azure Search](search-analyzers.md). Os analisadores só podem ser usados com campos pesquisáveis. Depois que o analisador for atribuído a um campo, ele não poderá ser alterado, a menos que você reconstrua o índice.

## <a name="cors"></a>CORS

O JavaScript do lado do cliente não pode chamar APIs por padrão, pois o navegador impedirá todas as solicitações entre origens. Para permitir consultas entre origens para seu índice, habilite o CORS (compartilhamento de recursos entre origens) definindo o atributo **corsOptions** . Por motivos de segurança, somente APIs de consulta dão suporte a CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Esta é uma lista de origens que receberão acesso ao índice. Isso significa que qualquer código JavaScript servido por essas origens terá permissão para consultar seu índice (supondo que ele forneça a chave de API correta). Cada origem geralmente é do formulário `protocol://<fully-qualified-domain-name>:<port>` , embora `<port>` geralmente é omitido. Consulte o [compartilhamento de recursos entre origens (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se você quiser permitir o acesso a todas as origens, `*` inclua como um único item na matriz **allowedOrigins** . *Essa não é uma prática recomendada para serviços de pesquisa de produção* , mas geralmente é útil para desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores usam esse valor para determinar a duração (em segundos) para respostas de simulação de CORS de cache. Este deve ser um número inteiro não negativo. Quanto maior esse valor, melhor será o desempenho, mas mais tempo levará para que as alterações da política de CORS entrem em vigor. Se não estiver definido, uma duração padrão de 5 minutos será usada.

## <a name="encryption-key"></a>Chave de criptografia

Embora todos os índices do Azure Search sejam criptografados por padrão usando chaves gerenciadas pela Microsoft, os índices podem ser configurados para serem criptografados com **chaves gerenciadas pelo cliente** no Key Vault. Para saber mais, consulte [gerenciar chaves de criptografia em Azure Search](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Passos Seguintes

Com uma compreensão da composição de índice, você pode continuar no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Adicionar um índice (Portal)](search-create-index-portal.md)
