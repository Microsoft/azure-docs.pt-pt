---
title: Importação e ingestão de dados em índices de pesquisa
titleSuffix: Azure Cognitive Search
description: Povoe e carregue dados para um índice em Pesquisa Cognitiva Azure a partir de fontes de dados externas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282760"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Visão geral da importação de dados - Pesquisa Cognitiva Azure

Na Pesquisa Cognitiva Azure, as consultas executam sobre o seu conteúdo carregado e guardado num índice de [pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para a população de um índice: *empurre* os seus dados para o índice programáticamente, ou aponte um [indexador](search-indexer-overview.md) de Pesquisa Cognitiva Azure numa fonte de dados suportada para *puxar* os dados.

Com qualquer uma das abordagems, o objetivo é *carregar dados* de uma fonte externa de dados para um índice de Pesquisa Cognitiva Azure. A Pesquisa Cognitiva Azure permitir-lhe-á criar um índice vazio, mas até empurrar ou puxar dados para dentro dele, não é questionável.

## <a name="pushing-data-to-an-index"></a>Enviar dados para um índice
O modelo push, usado para enviar os seus dados programáticamente para a Pesquisa Cognitiva Azure, é a abordagem mais flexível. Em primeiro lugar, não tem restrições quanto ao tipo de origem de dados. Qualquer conjunto de dados composto por documentos JSON pode ser empurrado para um índice de Pesquisa Cognitiva Azure, assumindo que cada documento no conjunto de dados tem campos de mapeamento de campos definidos no seu esquema de índice. Em segundo lugar, não tem restrições relativamente à frequência de execução. Pode enviar alterações para os índices tantas vezes quanto quiser. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo push também permite fazer o upload de documentos para a Pesquisa Cognitiva Azure, independentemente do local onde os seus dados se encontra.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Como empurrar dados para um índice de pesquisa cognitiva Azure

Pode utilizar as APIs seguintes para carregar um ou múltiplos documentos para um índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Para uma introdução a cada metodologia, consulte [Quickstart: Crie um índice de pesquisa cognitiva Azure utilizando powerShell](search-create-index-rest-api.md) ou [ C# Quickstart: Crie um índice de pesquisa cognitiva azure utilizando .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Ações de indexação: upload, fusão, fusãoOrUpload, excluir

Pode controlar o tipo de ação de indexação numa base por documento, especificando se o documento deve ser carregado na totalidade, fundido com conteúdo de documento existente, ou eliminado.

Na API REST, emita pedidos HTTP POST com organismos de pedido jSON para o URL final do seu índice de pesquisa cognitiva Azure. Cada objeto JSON na matriz de "valor" contém a chave do documento e especifica se uma ação de indexação adiciona, atualiza ou elimina conteúdo documental. Para um exemplo de código, consulte [os documentos de carga](search-get-started-dotnet.md#load-documents).

No .NET SDK, embaas os seus dados num objeto `IndexBatch`. Um `IndexBatch` engloba uma coleção de objetos `IndexAction`, cada um dos quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação a realizar nesse documento. Para um exemplo de código, consulte o [ C# Quickstart](search-get-started-dotnet.md).


| @search.action | Descrição | Campos necessários para cada documento | Notas |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Um ação `upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. No SDK .NET, isto inclui campos de tipo `DataType.Collection(DataType.String)`. Na API REST, isto inclui campos de tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Esta ação tem o mesmo comportamento de `merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

## <a name="decide-which-indexing-action-to-use"></a>Decidir a ação de indexação a utilizar
Para importar dados utilizando o .NET SDK (carregar, fundir, eliminar e fundir OrUpload). Dependendo das ações que escolher abaixo, apenas determinados campos tem de ser incluídos para cada documento:


### <a name="formulate-your-query"></a>Formular a consulta
Existem duas formas de [pesquisar o índice através da API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma forma consiste em emitir um pedido de HTTP POST, em que os parâmetros da sua consulta são definidos num objeto JSON no corpo do pedido. A outra forma consiste em emitir um pedido de HTTP GET, em que os parâmetros da consulta são definidos no URL do pedido. O POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) em relação ao tamanho dos parâmetros de consulta do que o GET. Por este motivo, recomendamos a utilização do POST, salvo se tiver circunstâncias especiais em que a utilização do GET seja mais conveniente.

Para o POST e o GET, é necessário fornecer o *nome do serviço*, o *nome do índice* e a *versão de API* correta (no momento de publicação deste documento a versão de API atual é `2019-05-06`) no URL do pedido. No GET, os parâmetros de consulta são fornecidos na *cadeia de consulta* no final do URL. Veja a seguir o formato URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

O formato para o POST é o mesmo, contudo, apenas com a versão de API nos parâmetros de cadeia de consulta.


## <a name="pulling-data-into-an-index"></a>Extrair dados para um índice
O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o seu índice. Na Pesquisa Cognitiva Azure, esta capacidade é implementada através de *indexadores*, atualmente disponíveis para estas plataformas:

+ [Armazenamento de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Armazenamento de tabelas](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Base de Dados SQL do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores ligam índices a uma origem de dados (geralmente, uma tabela, vista ou estrutura equivalente) e mapeiam os campos da origem para os campos equivalentes nos índices. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado para o índice especificado. Todos os indexadores suportam o agendamento, de modo a que possa especificar a frequência com que os dados devem ser atualizados. A maioria dos indexadores disponibilizam o registo de alterações, se as origens de dados o suportarem. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Como puxar dados para um índice de pesquisa cognitiva Azure

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Uma vantagem para usar o portal é que a Pesquisa Cognitiva Azure pode geralmente gerar um esquema de índice padrão para si, lendo os metadados do conjunto de dados de origem. Pode modificar o índice gerado até o índice ser processado, após o qual as únicas edições ao esquema permitidas são as que não requerem nova indexação. Se as alterações que quiser fazer influenciarem o esquema diretamente, terá de recriar o índice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificar a importação de dados com o explorador de pesquisa

Uma forma rápida de efetuar uma verificação preliminar no upload do documento é utilizar o **explorador de pesquisa** no portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o [parâmetro de consulta searchMode](/rest/api/searchservice/search-documents) predefinido. Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Numerosas amostras de código de [pesquisa cognitiva Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou prontamente disponíveis, oferecendo uma maneira fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Quando executa o indexador pré-configurado na fonte de dados da amostra, é criado e carregado com documentos que podem ser consultados no explorador de Pesquisa ou por código que escreve.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Instruções do portal: criar, carregar e consultar índices](search-get-started-portal.md)
