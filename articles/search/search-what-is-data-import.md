---
title: Importação e ingestão de dados em índices de pesquisa
titleSuffix: Azure Cognitive Search
description: Povoar e carregar dados para um índice na Pesquisa Cognitiva Azure a partir de fontes de dados externas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: b57d55e91918ba612ad42acd5e6059ae0dbd0090
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422455"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Visão geral da importação de dados - Azure Cognitive Search

Na Pesquisa Cognitiva Azure, as consultas executam sobre o seu conteúdo carregado e guardado num [índice de pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para povoar um índice: *empurrar* os seus dados para o índice programáticamente, ou apontar um [indexante de Pesquisa Cognitiva Azure](search-indexer-overview.md) numa fonte de dados suportada para *obter* os dados.

Com qualquer uma das abordagens, o objetivo é carregar dados de uma fonte de dados externas para um índice de Pesquisa Cognitiva Azure. A Azure Cognitive Search permitir-lhe-á criar um índice vazio, mas até que empurre ou puxe dados para dentro, não é questionável.

> [!NOTE]
> Se [o enriquecimento de IA](cognitive-search-concept-intro.md) for um requisito de solução, deve utilizar o modelo de puxar (indexantes) para carregar um índice. O processamento externo é suportado apenas através de skillsets ligados a um indexante.

## <a name="pushing-data-to-an-index"></a>Enviar dados para um índice

O modelo push, usado para enviar os seus dados programáticamente para a Azure Cognitive Search, é a abordagem mais flexível. Em primeiro lugar, não tem restrições quanto ao tipo de origem de dados. Qualquer conjunto de dados composto por documentos JSON pode ser empurrado para um índice de Pesquisa Cognitiva Azure, assumindo que cada documento no conjunto de dados tem campos de mapeamento para campos definidos no seu esquema de índice. Em segundo lugar, não tem restrições relativamente à frequência de execução. Pode enviar alterações para os índices tantas vezes quanto quiser. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo push também permite fazer o upload de documentos para a Azure Cognitive Search, independentemente de onde os seus dados estão.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Como empurrar dados para um índice de Pesquisa Cognitiva Azure

Pode utilizar as APIs seguintes para carregar um ou múltiplos documentos para um índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe IndexDocumentsA](/dotnet/api/azure.search.documents.models.indexdocumentsaction) ou [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) 

Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Para uma introdução a cada metodologia, consulte [Quickstart: Criar um índice de Pesquisa Cognitiva Azure utilizando PowerShell](./search-get-started-powershell.md) ou [C# Quickstart: Criar um índice de pesquisa cognitiva Azure utilizando .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Ações de indexação: upload, fusão, fusãoOrUpload, eliminar

Pode controlar o tipo de ação de indexação por documento, especificando se o documento deve ser carregado na totalidade, fundido com o conteúdo do documento existente ou eliminado.

Na API REST, emita http post solicitando aos corpos de pedido json para o URL do seu índice de pesquisa cognitiva Azure. Cada objeto JSON na matriz de "valor" contém a chave do documento e especifica se uma ação de indexação adiciona, atualiza ou elimina o conteúdo do documento. Para um exemplo de código, consulte [documentos de carga](search-get-started-dotnet.md#load-documents).

No .NET SDK, embale os seus dados num `IndexBatch` objeto. Um `IndexBatch` encapsula uma coleção de `IndexAction` objetos, cada um dos quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação a executar nesse documento. Para um exemplo de código, consulte o [C# Quickstart](search-get-started-dotnet.md).


| @search.action | Description | Campos necessários para cada documento | Notas |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Um ação `upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. No .NET SDK, isto inclui campos de tipo `DataType.Collection(DataType.String)` . Na API REST, isto inclui campos de `Collection(Edm.String)` tipo. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Esta ação tem o mesmo comportamento de `merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

### <a name="formulate-your-query"></a>Formular a consulta

Existem duas formas de [pesquisar o índice através da API REST](/rest/api/searchservice/Search-Documents). Uma forma consiste em emitir um pedido de HTTP POST, em que os parâmetros da sua consulta são definidos num objeto JSON no corpo do pedido. A outra forma consiste em emitir um pedido de HTTP GET, em que os parâmetros da consulta são definidos no URL do pedido. O POST tem [limites mais flexíveis](/rest/api/searchservice/Search-Documents) em relação ao tamanho dos parâmetros de consulta do que o GET. Por este motivo, recomendamos a utilização do POST, salvo se tiver circunstâncias especiais em que a utilização do GET seja mais conveniente.

Para post e GET, você precisa fornecer o seu nome de *serviço,* *nome de índice,* e uma *versão API* no URL de pedido. 

No GET, os parâmetros de consulta são fornecidos na *cadeia de consulta* no final do URL. Veja a seguir o formato URL:

```http
    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06
```

O formato para POST é o mesmo, mas com `api-version` os parâmetros de cadeia de consulta.

## <a name="pulling-data-into-an-index"></a>Extrair dados para um índice

O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o seu índice. Na Pesquisa Cognitiva do Azure, esta capacidade é implementada através *de indexadores,* atualmente disponíveis para estas plataformas:

+ [Armazenamento de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Armazenamento de tabelas](search-howto-indexing-azure-tables.md)
+ [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)
+ [Base de Dados Azure SQL, SQL Managed Instance e SQL Server em VMs Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores ligam índices a uma origem de dados (geralmente, uma tabela, vista ou estrutura equivalente) e mapeiam os campos da origem para os campos equivalentes nos índices. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado para o índice especificado. Todos os indexadores suportam o agendamento, de modo a que possa especificar a frequência com que os dados devem ser atualizados. A maioria dos indexadores disponibilizam o registo de alterações, se as origens de dados o suportarem. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice.

### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Como puxar dados para um índice de Pesquisa Cognitiva Azure

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [.NET SDK](/dotnet/api/azure.search.documents.indexes.searchindexerclient).

Uma vantagem para usar o portal é que a Azure Cognitive Search pode normalmente gerar um esquema de índice padrão para si, lendo os metadados do conjunto de dados de origem. Pode modificar o índice gerado até o índice ser processado, após o qual as únicas edições ao esquema permitidas são as que não requerem nova indexação. Se as alterações que quiser fazer influenciarem o esquema diretamente, terá de recriar o índice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificar a importação de dados com o explorador de pesquisa

Uma forma rápida de realizar uma verificação preliminar no upload do documento é usar o **Explorador de Pesquisa** no portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em definições predefinições, tais como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o parâmetro de consulta de [pesquisa](/rest/api/searchservice/search-documents)padrão . Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Numerosas amostras de código de [pesquisa cognitiva do Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou prontamente disponíveis, oferecendo uma maneira fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Quando executam o indexante pré-configurado na fonte de dados da amostra, um índice é criado e carregado com documentos que podem ser consultados no Explorador de Busca ou por código que escreve.

## <a name="see-also"></a>Ver também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Instruções do portal: criar, carregar e consultar índices](search-get-started-portal.md)