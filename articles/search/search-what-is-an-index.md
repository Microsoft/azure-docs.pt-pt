---
title: Criar um índice
titleSuffix: Azure Cognitive Search
description: Introduz conceitos e ferramentas de indexação na Pesquisa Cognitiva Azure, incluindo definições de esquema e estrutura de dados físicos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 96594d573c308727217f537e5421dcb79f02c2ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433799"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Criação de índices de pesquisa na Pesquisa Cognitiva Azure

A Pesquisa Cognitiva armazena conteúdo pes pesjável utilizado para texto completo e consultas filtradas num *índice de pesquisa.* Um índice é definido por um esquema e guardado para o serviço, com a importação de dados a seguir como segundo passo. 

Os índices contêm *documentos de pesquisa.* Conceptualmente, um documento é uma única unidade de dados pesmáveis no seu índice. Um retalhista pode ter um documento para cada produto, uma organização noticiosa pode ter um documento para cada artigo, e assim por diante. Mapear estes conceitos para equivalentes de base de dados mais familiares: um *índice de pesquisa* equivale a uma *tabela*, e *os documentos* são aproximadamente equivalentes a *linhas* numa tabela.

## <a name="whats-an-index-schema"></a>O que é um esquema de índice?

A estrutura física de um índice é determinada pelo esquema. A recolha de 'campos' é tipicamente a maior parte de um índice, onde cada campo é nomeado, atribuído um tipo de [dados](/rest/api/searchservice/Supported-data-types), e atribuído com comportamentos admissíveis que determinam como é usado.

```json
{
  "name": "name_of_index, unique across the service",
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
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Outros elementos são colapsados por brevidade, mas os seguintes links podem fornecer o detalhe: [sugestivos](index-add-suggesters.md), [perfis de pontuação,](index-add-scoring-profiles.md) [analisadores](search-analyzers.md) usados para processar cordas em fichas de acordo com regras linguísticas ou outras características suportadas pelo analisador, e configurações [de script remoto de origem cruzada (CORS).](#corsoptions)

## <a name="choose-a-client"></a>Escolha um cliente

Existem várias formas de criar um índice de pesquisa. Recomendamos o portal Azure ou SDKs para o desenvolvimento precoce e testes de prova de conceito.

Durante o desenvolvimento, planeie reconstruções frequentes. Porque as estruturas físicas são criadas no serviço, [a queda e a recriação de índices](search-howto-reindex.md) são necessárias para a maioria das modificações a uma definição de campo existente. Pode considerar trabalhar com um subconjunto dos seus dados para fazer com que as reconstruções vão mais rápido.

### <a name="permissions"></a>Permissões

Todas as operações relacionadas com um índice de pesquisa, incluindo pedidos GET a sua definição, requerem [uma chave api de administração](search-security-api-keys.md) no pedido.

### <a name="limits"></a>Limites

Todos os [níveis de serviço limitam](search-limits-quotas-capacity.md#index-limits) o número de objetos que pode criar. Se estiver a experimentar no nível Livre, só pode ter 3 índices a qualquer momento.

### <a name="use-azure-portal-to-create-a-search-index"></a>Use o portal Azure para criar um índice de pesquisa

O portal fornece duas opções para criar um índice de pesquisa: [**Assistente de dados de importação**](search-import-data-portal.md) e Índice de **Adição** que fornece campos para especificar um esquema de índice. O assistente embala em operações adicionais, criando também um indexante, fonte de dados e dados de carregamento. Se isto for mais do que o que deseja, deve utilizar **o Add Index** ou outra abordagem.

A imagem que se segue mostra onde pode encontrar **Add Index** no portal. **Os dados de importação** são aqui ao lado.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Adicionar comando de índice" border="true":::

> [!Tip]
> O design de índices através do portal aplica requisitos e regras de esquema para tipos de dados específicos, tais como a não permitir capacidades completas de pesquisa de texto em campos numéricos. Uma vez que tenha um índice viável, pode copiar o JSON do portal e adicioná-lo à sua solução.

### <a name="use-a-rest-client"></a>Use um cliente REST

Tanto o Código do Carteiro como o Código do Estúdio Visual (com uma extensão para Azure Cognitive Search) podem funcionar como um cliente de índice de pesquisa. Utilizando qualquer uma das ferramentas, pode ligar-se ao seu serviço de pesquisa e enviar pedidos [de Criar Índice (REST).](/rest/api/searchservice/create-index) Existem inúmeros tutoriais e exemplos que demonstram clientes REST para criar objetos. 

Comece com qualquer um destes artigos para aprender sobre cada cliente:

+ [Criar um índice de pesquisa usando REST e Carteiro](search-get-started-rest.md)
+ [Começar com Visual Studio Code e Azure Cognitive Search](search-get-started-vs-code.md)

Consulte as operações do [Índice (REST)](/rest/api/searchservice/index-operations) para obter ajuda na formulação de pedidos de índice.

### <a name="use-an-sdk"></a>Use um SDK

Para pesquisa cognitiva, os Azure SDKs implementam funcionalidades geralmente disponíveis. Como tal, pode utilizar qualquer um dos SDKs para criar um índice de pesquisa. Todos eles fornecem um **SearchIndexClient** que tem métodos para criar e atualizar índices.

| Azure SDK | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [azure-search-dotnet-samples/quickstart/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Índices](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations.py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Definir campos

Um documento de pesquisa é definido pela `fields` coleção. Vai precisar de campos para consultas e chaves. Provavelmente também vai precisar de campos para suportar filtros, facetas e tipos. Também pode precisar de campos para dados que um utilizador nunca vê, por exemplo, pode querer campos para margens de lucro ou promoções de marketing que pode usar para modificar o ranking de pesquisa.

Um campo do tipo Edm.String deve ser designado como a chave do documento. É usado para identificar exclusivamente cada documento de pesquisa e é sensível a casos. Pode recuperar um documento através da sua chave para preencher uma página de detalhes.

Se os dados de entrada forem hierárquicos de natureza, atribua o tipo de dados [complexo](search-howto-complex-data-types.md) para representar as estruturas aninhadas. O conjunto de dados de amostras incorporado, Hotéis, ilustra tipos complexos usando um Endereço (contém vários sub-campos) que tem uma relação um-para-um com cada hotel, e uma coleção complexa de quartos, onde vários quartos estão associados a cada hotel. 

Atribua todos os analisadores aos campos de cordas antes da criação do índice. Faça o mesmo para os sugestivos se pretender ativar automaticamente em campos específicos.

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

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributos e tamanho do índice (implicações de armazenamento)

O tamanho de um índice é determinado pelo tamanho dos documentos que carrega, mais a configuração do índice, como se inclui sugestivos e como define atributos em campos individuais. 

A imagem a seguir ilustra padrões de armazenamento de índices resultantes de várias combinações de atributos. O índice baseia-se no **índice de amostra de imóveis,** que pode criar facilmente utilizando o assistente de dados De importação. Embora os esquemas de índice não sejam mostrados, pode inferir os atributos com base no nome do índice. Por exemplo, o índice *de procuração imobiliária* tem o atributo "pesmável" selecionado e nada mais, o índice *realestate-recuperável* tem o atributo "recuperável" selecionado e nada mais, e assim por diante.

![Tamanho do índice com base na seleção de atributos](./media/search-what-is-an-index/realestate-index-size.png "Tamanho do índice com base na seleção de atributos")

Embora estas variantes de índice sejam artificiais, podemos referir-nos a elas para comparações amplas de como os atributos afetam o armazenamento. A definição "recuperável" aumenta o tamanho do índice? N.º Adicionar campos a um **indicador aumenta** o tamanho do índice? Sim. 

Tornar um campo filtrado ou ordenado também aumenta o consumo de armazenamento porque os campos filtrados e classificados não são tokenizados para que as sequências de caracteres possam ser correspondidas verbatim.

Também não refletem o impacto dos [analisadores.](search-analyzers.md) Se estiver a utilizar o tokenizer edgeNgram para armazenar sequências verbatim de caracteres (a, ab, abc, abcd), o tamanho do índice será maior do que se usasse um analisador padrão.

> [!Note]
> A arquitetura de armazenamento é considerada um detalhe de implementação da Azure Cognitive Search e pode mudar sem aviso prévio. Não há garantias de que o comportamento atual permaneça no futuro.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Sobre `corsOptions`

Os esquemas de índice incluem uma secção para a definição `corsOptions` . O JavaScript do lado do cliente não pode ligar para apis por padrão, uma vez que o navegador impedirá todos os pedidos de origem cruzada. Para permitir consultas de origem cruzada no seu índice, ative o CORS (Partilha de Recursos de Origem Cruzada) definindo o atributo **corsOptions.** Por razões de segurança, apenas as APIs de consulta suportam o CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Esta é uma lista de origens que terá acesso ao seu índice. Isto significa que qualquer código JavaScript servido a partir dessas origens será permitido consultar o seu índice (assumindo que fornece a chave api correta). Cada origem é tipicamente da `protocol://<fully-qualified-domain-name>:<port>` forma, embora `<port>` muitas vezes seja omitida. Consulte [a partilha de recursos cross-origin (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se quiser permitir o acesso a todas as origens, inclua `*` como um único item na matriz permitida **Porgins.** *Esta prática não é recomendada para serviços de pesquisa de produção,* mas é muitas vezes útil para o desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores utilizam este valor para determinar a duração (em segundos) para cache respostas de pré-voo CORS. Este deve ser um número inteiro não negativo. Quanto maior for este valor, melhor será o desempenho, mas quanto mais tempo demorar para que as mudanças de política do CORS produzam efeitos. Se não estiver definido, utilizar-se-á uma duração predefinida de 5 minutos.

## <a name="next-steps"></a>Passos seguintes

Você pode obter as mãos sobre a experiência criando um índice usando quase qualquer amostra ou walkthrough para pesquisa cognitiva. Para começar, pode escolher qualquer um dos arranques rápidos da tabela de conteúdos.

Mas também vai querer familiarizar-se com metodologias para carregar um índice com dados. A definição de índices e as estratégias de importação de dados são definidas em conjunto. Os seguintes artigos fornecem mais informações sobre o carregamento de um índice.

+ [Descrição geral da importação de dados](search-what-is-data-import.md)

+ [Adicionar, atualizar ou eliminar documentos (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 