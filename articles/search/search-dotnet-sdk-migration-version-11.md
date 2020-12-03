---
title: Upgrade para a versão 11 da NET SDK
titleSuffix: Azure Cognitive Search
description: Migrar código para a versão 11 da Azure Cognitive Search .NET SDK a partir de versões mais antigas. Saiba o que é novo e quais as alterações de código necessárias.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 260df85f3e380e40d153fc17ce77bd56ca068982
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532827"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade para Azure Cognitive Search .NET SDK versão 11

Se estiver a utilizar a versão 10.0 ou mais antiga do [.NET SDK,](/dotnet/api/overview/azure/search)este artigo irá ajudá-lo a atualizar para a versão 11 e para a biblioteca **de clientesAzure.Search.Doc.**

A versão 11 é uma biblioteca de clientes totalmente redesenhada, lançada pela equipa de desenvolvimento Azure SDK (versões anteriores foram produzidas pela equipa de desenvolvimento de Pesquisa Cognitiva Azure). A biblioteca foi redesenhada para uma maior consistência com outras bibliotecas de clientes Azure, assumindo uma dependência do [Azure.Core](/dotnet/api/azure.core) e [System.Text.Js,](/dotnet/api/system.text.json)e implementando abordagens familiares para tarefas comuns.

Algumas diferenças fundamentais que vai notar na nova versão incluem:

+ Um pacote e biblioteca em oposição a vários
+ Um novo nome de pacote: `Azure.Search.Documents` em vez de `Microsoft.Azure.Search` .
+ Três clientes em vez de dois: `SearchClient` `SearchIndexClient` , `SearchIndexerClient`
+ Nomear diferenças entre uma série de APIs e pequenas diferenças estruturais que simplificam algumas tarefas

> [!NOTE]
> Reveja o [**registo de alteração**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) de uma lista de alterações em .NET SDK versão 11.

## <a name="package-and-library-consolidation"></a>Consolidação de pacotes e bibliotecas

A versão 11 consolida vários pacotes e bibliotecas num só. Após a migração, terá menos bibliotecas para gerir.

+ [Azure.Search.Docpacote de uments](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [Referência da API para a biblioteca do cliente](/dotnet/api/overview/azure/search.documents-readme)

## <a name="client-differences"></a>Diferenças de clientes

Se for caso disso, a tabela seguinte mapeia as bibliotecas do cliente entre as duas versões.

| Âmbito de operações | Microsoft.Azure.Search &nbsp; (v10) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| Cliente usado para consultas e para preencher um índice. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [PesquisaDesseient](/dotnet/api/azure.search.documents.searchclient) |
| Cliente utilizado para índices, analisadores, mapas de sinónimo | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Cliente utilizado para indexadores, fontes de dados, skillsets | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient **(novo)**](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` existe em ambas as versões, mas suporta coisas diferentes. Na versão 10, `SearchIndexClient` crie índices e outros objetos. Na versão 11, `SearchIndexClient` funciona com os índices existentes. Para evitar confusões ao atualizar o código, esteja atento à ordem em que as referências do cliente são atualizadas. Seguir a sequência de [etapas para atualizar](#UpgradeSteps) deve ajudar a atenuar quaisquer problemas de substituição de cordas.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Nomeação e outras diferenças de API

Além das diferenças de clientes (anotados anteriormente e assim omitidos aqui), várias outras APIs foram renomeadas e, em alguns casos, redesenhadas. As diferenças de nome de classe são resumidas abaixo. Esta lista não é exaustiva, mas altera as alterações da API por tarefa, o que pode ser útil para revisões em blocos de código específicos. Para obter uma lista de atualizações da API, consulte o registo de [alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` no GitHub.

### <a name="authentication-and-encryption"></a>Autenticação e encriptação

| Versão 10 | Versão 11 equivalente |
|------------|-----------------------|
| [Pesquisacredentias](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (existiu na [pré-visualização do SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) como uma característica geralmente disponível) | [PesquisaResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Índices, analisadores, mapas de sinónimos

| Versão 10 | Versão 11 equivalente |
|------------|-----------------------|
| [Índice](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Campo](/dotnet/api/microsoft.azure.search.models.field) | [Campo de Busca](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analisador](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (também, `AnalyzerName` `LexicalAnalyzerName` para) |
| [Analisar Requesto](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [Analisar Opções de Texto](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (também, `StandardTokenizerV2` `LuceneStandardTokenizerV2` a) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalisadoTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (também, `TokenizerName` `LexicalTokenizerName` a) |
| [SynonymMap.Formato](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Nenhum. Remova as referências a `Format` . |

As definições de campo são simplificadas: [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField,](/dotnet/api/azure.search.documents.indexes.models.simplefield) [ComplexField](/dotnet/api/azure.search.documents.indexes.models.complexfield) são novos APIs para criar definições de campo.

### <a name="indexers-datasources-skillsets"></a>Indexantes, fontes de dados, skillsets

| Versão 10 | Versão 11 equivalente |
|------------|-----------------------|
| [Indexador](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [Fonte de Dados](/dotnet/api/microsoft.azure.search.models.datasource) | [PesquisaIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Habilidade](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Skillset](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [PesquisaIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Importação de dados

| Versão 10 | Versão 11 equivalente |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAcção](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Definições e resultados de consulta

| Versão 10 | Versão 11 equivalente |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) ou [SearchResults,](/dotnet/api/azure.search.documents.models.searchresults-1)dependendo se o resultado é um único documento ou múltiplo. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [Sugerir Resultados](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [Parametros de pesquisa](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Opções de Pesquisa](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>O que está na versão 11

Cada versão de uma biblioteca de clientes Azure Cognitive Search tem como alvo uma versão correspondente da API REST. A API REST é considerada fundamental para o serviço, com os SDKs individuais a embrulhar uma versão da API REST. Como um desenvolvedor .NET, pode ser útil rever a [documentação da API REST](/rest/api/searchservice/) se quiser mais antecedentes em objetos ou operações específicos.

A versão 11 tem como alvo o [serviço de pesquisa 2020-06-30](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Como a versão 11 é também uma nova biblioteca de clientes construída de zero para cima, a maior parte do esforço de desenvolvimento tem-se centrado na equivalência com a versão 10, com algum suporte de recurso REST API ainda pendente.

A versão 11.0 suporta totalmente os seguintes objetos e operações:

+ Criação e gestão de índices
+ Criação e gestão de mapas de synonym
+ Todos os tipos de consulta e sintaxe (exceto filtros geo-espaciais)
+ Objetos indexantes e operações para indexar fontes de dados do Azure, incluindo fontes de dados e skillsets

A versão 11.1 acrescenta o seguinte:

+ [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (adicionado em 11.1)
+ [Propriedade serializadora](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (adicionada em 11.1) para suportar a serialização personalizada

### <a name="pending-features"></a>Funcionalidades pendentes

As seguintes funcionalidades da versão 10 ainda não estão disponíveis na versão 11. Se necessitar destas funcionalidades, adiste a migração até que sejam apoiadas.

+ tipos geoespaciais
+ [Arquivo de dados de conhecimento](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passos para atualizar

Os passos seguintes iniciam-no numa migração de códigos, percorrendo o primeiro conjunto de tarefas necessárias, especialmente no que diz respeito às referências dos clientes.

1. Instale o [ pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) clicando à direita nas referências do seu projeto e selecionando "Gerir pacotes nuget..." em Estúdio Visual.

1. Substitua as diretivas para Microsoft.Azure.Search com as seguintes:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Para aulas que requerem serialização JSON, `using Newtonsoft.Json` substitua por `using System.Text.Json.Serialization` .

1. Rever o código de autenticação do cliente. Em versões anteriores, utilizaria propriedades no objeto do cliente para definir a chave API (por exemplo, a propriedade [SearchServiceClient.Credentials).](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) Na versão atual, utilize a classe [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) para passar a chave como credencial, para que, se necessário, possa atualizar a tecla API sem criar novos objetos de cliente.

   As propriedades dos clientes foram simplificadas para `Endpoint` `ServiceName` apenas, e `IndexName` (se for caso disso). O exemplo a seguir utiliza a classe [Uri](/dotnet/api/system.uri) do sistema para fornecer o ponto final e a classe [Ambiente](/dotnet/api/system.environment) para ler no valor-chave:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Adicione novas referências de clientes para objetos relacionados com indexante. Se estiver a utilizar indexantes, fontes de dados ou skillsets, altere as referências do cliente ao [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient). Este cliente é novo na versão 11 e não tem antecedentes.

1. Rever coleções e listas. No novo SDK, todas as listas são apenas de leitura para evitar problemas a jusante se a lista contiver valores nulos. A alteração de código é adicionar itens a uma lista. Por exemplo, em vez de atribuir cordas a uma propriedade Select, irá adicioná-las da seguinte forma:

   ```csharp
   var options = new SearchOptions
    {
       SearchMode = SearchMode.All,
       IncludeTotalCount = true
    };

    // Select fields to return in results.
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Tags");
    options.Select.Add("Rooms");
    options.Select.Add("Rating");
    options.Select.Add("LastRenovationDate");
   ```

   Selecione, Facets, SearchFields, SourceFields, ScoringParameters e OrderBy são todas as listas que precisam agora de ser reconstruídas.

1. Atualizar referências de clientes para consultas e importação de dados. Os casos de [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) devem ser alterados para [SearchClient](/dotnet/api/azure.search.documents.searchclient). Para evitar confusão de nomes, certifique-se de que apanha todas as instâncias antes de seguir para o passo seguinte.

1. Atualizar referências de clientes para índice, mapa de sinónimo e objetos de analisador. As instâncias do [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) devem ser alteradas para [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Para o restante do seu código, atualize classes, métodos e propriedades para utilizar as APIs da nova biblioteca. A secção [de diferenças de nomeação](#naming-differences) é um lugar para começar, mas também pode rever o [registo de alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md).

   Se tiver dificuldade em encontrar APIs equivalentes, sugerimos que se faça um teste para [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) que possamos melhorar a documentação ou investigar o problema.

1. Reconstruir a solução. Depois de corrigir quaisquer erros ou avisos de construção, pode escoar alterações adicionais na sua aplicação para tirar partido de [novas funcionalidades.](#WhatsNew)

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Quebrando mudanças na versão 11

Dadas as mudanças drásticas nas bibliotecas e APIs, um upgrade para a versão 11 não é trivial e constitui uma mudança de rutura no sentido de que o seu código deixará de ser compatível com a versão 10 e mais cedo. Para uma revisão completa das diferenças, consulte o [registo de alterações](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) para `Azure.Search.Documents` .

Em termos de atualizações da versão de serviço, em que as alterações de código na versão 11 se relacionam com a funcionalidade existente (e não apenas com uma refactor das APIs), encontrará as seguintes alterações de comportamento:

+ [O algoritmo de classificação BM25](index-ranking-similarity.md) substitui o algoritmo de classificação anterior por tecnologia mais recente. Novos serviços usarão este algoritmo automaticamente. Para os serviços existentes, deve definir parâmetros para usar o novo algoritmo.

+ [Os resultados ordenados](search-query-odata-orderby.md) para valores nulos mudaram nesta versão, com os valores nulos a aparecerem primeiro se o tipo for `asc` e durar se o tipo for `desc` . Se escreveu código para lidar com a classificação dos valores nulos, deve rever e potencialmente remover esse código se já não for necessário.

## <a name="next-steps"></a>Passos seguintes

+ [Azure.Search.Docpacote de uments](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Exemplos no GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [ referênciaAzure.Search.DocAPI](/dotnet/api/overview/azure/search.documents-readme)