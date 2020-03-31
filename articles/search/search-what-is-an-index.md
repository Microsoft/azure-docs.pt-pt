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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282786"
---
# <a name="create-a-basic-index-in-azure-cognitive-search"></a>Criar um índice básico na Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, um *índice* é uma loja persistente de *documentos* e outras construções usadas para pesquisa de texto filtrada e completa em um serviço de Pesquisa Cognitiva Azure. Conceptualmente, um documento é uma única unidade de dados pesquisáveis no seu índice. Por exemplo, um retalhista de comércio eletrónico pode ter um documento para cada artigo que vende, uma empresa jornalística pode ter um documento para cada artigo, etc. Mapeamento destes conceitos para equivalentes da base de dados mais familiares: um *índice* é, conceptualmente, semelhante a uma *tabela* e os *documentos* são quase equivalentes a *linhas* numa tabela.

Quando adiciona ou carrega um índice, a Pesquisa Cognitiva Azure cria estruturas físicas com base no esquema que fornece. Por exemplo, se um campo no seu índice for marcado como pesquisável, é criado um índice invertido para esse campo. Mais tarde, quando adiciona ou envia documentos, ou envia consultas de pesquisa para a Pesquisa Cognitiva Azure, está a enviar pedidos para um índice específico no seu serviço de pesquisa. Os campos de carregamento com valores documentais são chamados *de indexação* ou ingestão de dados.

Pode criar um índice no portal, [REST API,](search-create-index-rest-api.md)ou [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Fluxo de trabalho recomendado

Chegar ao design de índice certo é tipicamente alcançado através de múltiplas iterações. Usar uma combinação de ferramentas e APIs pode ajudá-lo a finalizar o seu design rapidamente.

1. Determine se pode usar um [indexador](search-indexer-overview.md#supported-data-sources). Se os seus dados externos forem uma das fontes de dados suportadas, pode protótipo e carregar um índice utilizando o assistente de [**dados da Importação.**](search-import-data-portal.md)

2. Se não puder utilizar **dados de Importação,** ainda pode [criar um índice inicial no portal,](search-create-index-portal.md)adicionar campos, tipos de dados e atribuir atributos utilizando controlos na página **Add Index.** O portal mostra quais os atributos disponíveis para diferentes tipos de dados. Se é novo no design de índices, isto é útil.

   ![Adicionar página de índice mostrando atributos por tipo de dados](media/search-create-index-portal/field-attributes.png "Adicionar página de índice mostrando atributos por tipo de dados")
  
   Quando clicar em **Criar**, todas as estruturas físicas que suportam o seu índice são criadas no seu serviço de pesquisa.

3. Descarregue o esquema de índice usando [a API](https://docs.microsoft.com/rest/api/searchservice/get-index) Get Index REST e uma ferramenta de teste web como [o Carteiro](search-get-started-postman.md). Tem agora uma representação da JSON do índice que criou no portal. 

   Neste momento, está a mudar para uma abordagem baseada em códigos. O portal não é adequado para a iteração porque não se pode editar um índice que já está criado. Mas pode usar o Carteiro e o REST para as restantes tarefas.

4. [Carregue o seu índice com dados](search-what-is-data-import.md). A Pesquisa Cognitiva Azure aceita documentos JSON. Para carregar os seus dados programáticamente, pode utilizar o Carteiro com documentos JSON na carga útil do pedido. Se os seus dados não forem facilmente expressos como JSON, este passo será o mais intensivo de mão de obra.

5. Consulte o seu índice, examine os resultados e eita ainda mais o esquema do índice até começar a ver os resultados que espera. Pode utilizar o explorador de [**pesquisa**](search-explorer.md) ou o Carteiro para consultar o seu índice.

6. Continue a usar código para iterar sobre o seu design.  

Como as estruturas físicas são criadas no serviço, [a queda e recriação de índices](search-howto-reindex.md) é necessária sempre que se faz alterações materiais numa definição de campo existente. Isto significa que, durante o desenvolvimento, deve planear reconstruções frequentes. Pode considerar trabalhar com um subconjunto dos seus dados para que as reconstruções sejam mais rápidas. 

O código, em vez de uma abordagem do portal, é recomendado para o design iterativo. Se confiar no portal para definição de índice, terá de preencher a definição de índice em cada reconstrução. Como alternativa, ferramentas como [o Carteiro e a API REST](search-get-started-postman.md) são úteis para testes de prova de conceito quando os projetos de desenvolvimento ainda estão em fases iniciais. Pode fazer alterações incrementais numa definição de índice num organismo de pedido e, em seguida, enviar o pedido para o seu serviço para recriar um índice usando um esquema atualizado.

## <a name="components-of-an-index"></a>Componentes de um índice

Schematicamente, um índice de Pesquisa Cognitiva Azure é composto pelos seguintes elementos. 

A coleção de [*campos*](#fields-collection) é tipicamente a maior parte de um índice, onde cada campo é nomeado, dactilografado e atribuído com comportamentos admissíveis que determinam como é usado. Outros elementos incluem [sugestões,](#suggesters) [perfis de pontuação,](#scoring-profiles) [analisadores](#analyzers) com componentes para apoiar opções de personalização, [CORS](#cors) e chave de [encriptação.](#encryption-key)

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

## <a name="fields-collection-and-field-attributes"></a>Atributos de coleção e campo de campos

Na definição do esquema, tem de especificar o nome, o tipo e os atributos de cada campo do índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar o modo de utilização do campo. As tabelas seguintes enumeram os tipos e os atributos que pode especificar.

### <a name="data-types"></a>Tipos de dados
| Tipo | Descrição |
| --- | --- |
| *Edm.String* |Texto que pode opcionalmente ser tokenizado para pesquisa de texto completo (quebra de palavras, confraternizar, etc. |
| *Collection(Edm.String)* |Uma lista de cadeias que pode, opcionalmente, ser atomizada para pesquisa em texto completo. Não existe um limite superior teórico do número de itens numa coleção, contudo, o limite superior de 16 MB de tamanho de payload aplica-se às coleções. |
| *Edm.Boolean* |Contém valores verdadeiro/falso. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de data representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica no mundo. |

Pode encontrar informações mais detalhadas sobre os tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)pela Azure Cognitive Search aqui .

### <a name="index-attributes"></a>Atributos do índice

Exatamente um campo no seu índice deve ser designado como um campo **chave** que identifica exclusivamente cada documento.

Outros atributos determinam como um campo é usado numa aplicação. Por exemplo, o atributo **pesquisável** é atribuído a todos os campos que devem ser incluídos numa pesquisa completa de texto. 

As APIs que usa para construir um índice têm comportamentos padrão variados. Para as [APIs rest](https://docs.microsoft.com/rest/api/searchservice/Create-Index), a maioria dos atributos são ativados por padrão (por exemplo, **pesquisáveis** e **recuperáveis** são verdadeiros para campos de cordas) e muitas vezes só precisa de os definir se quiser desligá-los. Para o .NET SDK, o oposto é verdade. Em qualquer propriedade que não configura explicitamente, o padrão é desativar o comportamento de pesquisa correspondente, a menos que o ative especificamente.

| Atributo | Descrição |
| --- | --- |
| `key` |Uma cadeia que fornece o ID único de cada documento, utilizada para a procura de documentos. Todos os índices devem ter uma chave. Apenas um campo pode ser uma chave e o tipo deve ser definido para Edm.String. |
| `retrievable` |Especifica se um campo pode ser devolvido num resultado da pesquisa. |
| `filterable` |Permite que o campo seja utilizado nas consultas de filtro.  |
| `Sortable` |Permite a uma consulta ordenar os resultados da pesquisa através deste campo. |
| `facetable` |Permite a um campo ser utilizado numa estrutura de [navegação por facetas](search-faceted-navigation.md) para filtragem do utilizador auto-direcionada. Geralmente os campos com valores repetitivos que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas. |
| `searchable` |Marca o campo como pesquisável no texto completo. |

## <a name="index-size"></a>Tamanho do índice

O tamanho de um índice é determinado pelo tamanho dos documentos que envia, mais a configuração do índice, como se inclui sugestionantes e como define atributos em campos individuais. A imagem seguinte ilustra padrões de armazenamento de índices resultantes de várias combinações de atributos.

O índice [baseia-se](search-get-started-portal.md) na fonte de dados da amostra imobiliária incorporada, que pode indexar e consultar no portal. Embora os esquemas de índice não sejam mostrados, pode inferir os atributos com base no nome do índice. Por exemplo, o índice *de pesquisa de imóveis* tem o atributo **pesquisável** selecionado e nada mais, o índice de *recuperação de imóveis* tem o atributo **recuperável** selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção do atributo](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção do atributo")

Embora estas variantes de índice sejam artificiais, podemos referir-nos a elas para comparações amplas de como os atributos afetam o armazenamento. A fixação **recuperável** aumenta o tamanho do índice? Não. Adicionar campos a um **Suggester** aumenta o tamanho do índice? Sim.

Os índices que suportam o filtro e a classificação são proporcionalmente maiores do que aqueles que suportam apenas a pesquisa completa de texto. Filtrar e ordenar as operações para obter correspondências exatas, exigindo a presença de documentos intactos. Em contraste, os campos pesquisáveis que suportam o uso de texto completo e de pesquisa difusa utilizam índices invertidos, que são povoados com termos simbólicos que consomem menos espaço do que documentos inteiros. 

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação da Pesquisa Cognitiva Azure e pode mudar sem aviso prévio. Não há garantias de que o comportamento atual persista no futuro.

## <a name="suggesters"></a>Sugestões
Um sugestor é uma secção do esquema que define quais os campos de um índice que são usados para suportar consultas automáticas completas ou tipo-à-frente nas pesquisas. Tipicamente, as cadeias de pesquisa parciais são enviadas para as [Sugestões (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions) enquanto o utilizador está a escrever uma consulta de pesquisa, e a API devolve um conjunto de documentos ou frases sugeridos. 

Os campos adicionados a um sugestor são usados para construir termos de pesquisa tipo-ahead. Todos os termos de pesquisa são criados durante a indexação e armazenados separadamente. Para obter mais informações sobre a criação de uma estrutura sugerida, consulte [Adicionar sugestões](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Perfis de classificação

Um [perfil de pontuação](index-add-scoring-profiles.md) é uma secção do esquema que define comportamentos de pontuação personalizados que permitem influenciar quais itens aparecem mais altos nos resultados da pesquisa. Os perfis de pontuação são compostos por pesos e funções de campo. Para usá-los, especifice um perfil por nome na corda de consulta.

Um perfil de pontuação padrão opera nos bastidores para calcular uma pontuação de pesquisa para cada item em um conjunto de resultados. Podeusar o perfil interno e sem nome. Alternativamente, **desetprendo o perfil de predefinição** para utilizar um perfil personalizado como padrão, invocado sempre que um perfil personalizado não for especificado na cadeia de consulta.

## <a name="analyzers"></a>Analisadores

O elemento analisador define o nome do analisador de linguagem para usar para o campo. Para obter mais informações sobre a gama de analisadores disponíveis, consulte [adicionar analisadores a um índice](search-analyzers.md)de pesquisa cognitiva Azure . Os analisadores só podem ser utilizados com campos pesquisáveis. Uma vez que o analisador é atribuído a um campo, não pode ser alterado a menos que reconstrua o índice.

## <a name="cors"></a>CORS

O JavaScript do lado do cliente não pode ligar para nenhuma APIs por padrão, uma vez que o navegador irá impedir todos os pedidos de origem cruzada. Para permitir consultas de origem cruzada ao seu índice, ative o CORS (Cross-Origin Resource Sharing) definindo o atributo **corsOptions.** Por razões de segurança, apenas as APIs de consulta suportam o CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Esta é uma lista de origens que terá acesso ao seu índice. Isto significa que qualquer código JavaScript servido a partir dessas origens será autorizado a consultar o seu índice (assumindo que fornece a chave api correta). Cada origem é tipicamente `protocol://<fully-qualified-domain-name>:<port>` `<port>` da forma, embora muitas vezes seja omitida. Consulte a [partilha de recursos de origem cruzada (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se quiser permitir o acesso a `*` todas as origens, inclua como um único item na matriz **de Origems permitidas.** *Esta não é uma prática recomendada para serviços* de pesquisa de produção, mas é muitas vezes útil para o desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores usam este valor para determinar a duração (em segundos) para cache respostas pré-voo CORS. Isto deve ser um inteiro não negativo. Quanto maior for este valor, melhor desempenho será, mas quanto mais tempo demorarem as alterações da política cors a produzirem efeitos. Se não estiver definido, será utilizada uma duração predefinida de 5 minutos.

## <a name="encryption-key"></a>Chave de encriptação

Embora todos os índices de Pesquisa Cognitiva Azure sejam encriptados por padrão utilizando chaves geridas pela Microsoft, os índices podem ser configurados para serem encriptados com **chaves geridas pelo cliente** no Key Vault. Para saber mais, consulte [Gerir chaves de encriptação em Pesquisa Cognitiva Azure](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Passos seguintes

Com uma compreensão da composição do índice, pode continuar no portal para criar o seu primeiro índice.

> [!div class="nextstepaction"]
> [Adicione um índice (portal)](search-create-index-portal.md)
