---
title: Importação de dados para ingestão de dados para um índice de pesquisa - Azure Search
description: Preencha e carregar dados para um índice da Azure Search a partir de origens de dados externas.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7d95ae1f750c59c121e998c6f51f9439b1b0339a
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287099"
---
# <a name="data-import-overview---azure-search"></a>Importação de dados descrição geral - Azure Search

No Azure Search, as consultas são executadas nos seus conteúdos carregados para e guardou numa [índice de pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para preencher um índice: *push* seus dados para o índice programaticamente, ou ponto de um [indexador de Azure Search](search-indexer-overview.md) numa origem de dados suportada para  *Extração* nos dados.

Com qualquer uma das abordagens, o objetivo é *carregar dados* de uma origem de dados externos para um índice da Azure Search. O Azure Search permitirá que criar um índice vazio, mas até push ou extrair dados para ele, não é passível de consulta.

## <a name="pushing-data-to-an-index"></a>Enviar dados para um índice
O modelo de envio, utilizado para enviar programaticamente os seus dados para o Azure Search, é a abordagem mais flexível. Em primeiro lugar, não tem restrições quanto ao tipo de origem de dados. Qualquer conjunto de dados composto por documentos JSON pode ser enviado para um índice do Azure Search, pressupondo que cada documento desse conjunto tem campos que sejam mapeados para os campos definidos no esquema de índice. Em segundo lugar, não tem restrições relativamente à frequência de execução. Pode enviar alterações para os índices tantas vezes quanto quiser. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo de envio também permite carregar documentos para a Azure Search independentemente do local de armazenamento dos dados.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Como enviar dados para um índice do Azure Search

Pode utilizar as APIs seguintes para carregar um ou múltiplos documentos para um índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Para obter uma introdução a cada metodologia, veja [início rápido: Criar um índice da Azure Search com o PowerShell e a API REST](search-create-index-rest-api.md) ou [início rápido: Criar um índice da Azure Search no C# ](search-import-data-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Ações de indexação: carregar, intercalar, mergeOrUpload, eliminar

Pode controlar o tipo de ação de indexação numa base por documento, especificando se o documento deve ser carregado no total, intercalado com o conteúdo de documento existente ou excluídos.

Na API do REST, emita pedidos de HTTP POST com corpos de pedido JSON para o URL de ponto final do seu índice da Azure Search. Cada objeto JSON na matriz "valor" contém a chave do documento e especifica uma ação de indexação adições, atualizações, ou elimina o conteúdo do documento. Para obter um exemplo de código, consulte [carregar documentos](search-create-index-rest-api.md#load-documents).

No SDK do .NET, empacotar os dados para um `IndexBatch` objeto. Uma `IndexBatch` encapsula uma coleção de `IndexAction` objetos, cada um deles contendo um documento e uma propriedade que informa a Azure Search qual a ação a realizar nesse documento. Para obter um exemplo de código, consulte [construir IndexBatch](search-import-data-dotnet.md#construct-indexbatch).


| @search.action | Descrição | Campos necessários para cada documento | Notas |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Um ação `upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. No SDK do .NET, isto inclui campos do tipo `DataType.Collection(DataType.String)`. Na API do REST, isto inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Esta ação tem o mesmo comportamento de `merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

## <a name="decide-which-indexing-action-to-use"></a>Decidir a ação de indexação a utilizar
Para importar dados utilizando o SDK de .NET, (carregar, intercalar, delete e mergeOrUpload). Dependendo das ações que escolher abaixo, apenas determinados campos tem de ser incluídos para cada documento:


### <a name="formulate-your-query"></a>Formular a consulta
Existem duas formas de [pesquisar o índice através da API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma forma consiste em emitir um pedido de HTTP POST, em que os parâmetros da sua consulta são definidos num objeto JSON no corpo do pedido. A outra forma consiste em emitir um pedido de HTTP GET, em que os parâmetros da consulta são definidos no URL do pedido. O POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) em relação ao tamanho dos parâmetros de consulta do que o GET. Por este motivo, recomendamos a utilização do POST, salvo se tiver circunstâncias especiais em que a utilização do GET seja mais conveniente.

Para o POST e o GET, é necessário fornecer o *nome do serviço*, o *nome do índice* e a *versão de API* correta (no momento de publicação deste documento a versão de API atual é `2017-11-11`) no URL do pedido. No GET, os parâmetros de consulta são fornecidos na *cadeia de consulta* no final do URL. Veja a seguir o formato URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2017-11-11

O formato para o POST é o mesmo, contudo, apenas com a versão de API nos parâmetros de cadeia de consulta.


## <a name="pulling-data-into-an-index"></a>Extrair dados para um índice
O modelo de extração pesquisa uma origem de dados suportada e carrega automaticamente os dados para o seu índice. No Azure Search, esta capacidade é implementada através dos *indexadores*, disponíveis atualmente nestas plataformas:

+ [Armazenamento de blobs](search-howto-indexing-azure-blob-storage.md)
+ [Armazenamento de tabelas](search-howto-indexing-azure-tables.md)
+ [BD do Cosmos para o Azure](https://aka.ms/documentdb-search-indexer)
+ [Base de Dados SQL do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores ligam índices a uma origem de dados (geralmente, uma tabela, vista ou estrutura equivalente) e mapeiam os campos da origem para os campos equivalentes nos índices. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado para o índice especificado. Todos os indexadores suportam o agendamento, de modo a que possa especificar a frequência com que os dados devem ser atualizados. A maioria dos indexadores disponibilizam o registo de alterações, se as origens de dados o suportarem. Os indexadores, através do registo de alterações e eliminações aos documentos existentes, além do reconhecimento de novos documentos, suprimem a gestão ativa dos dados no índice. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Como extrair dados para um índice do Azure Search

A funcionalidade de indexador está exposta no [portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Uma vantagem de utilizar o portal é que, geralmente, o Azure Search consegue gerar um esquema de índice predefinido por si, ao ler os metadados do conjunto de dados de origem. Pode modificar o índice gerado até o índice ser processado, após o qual as únicas edições ao esquema permitidas são as que não requerem nova indexação. Se as alterações que quiser fazer influenciarem o esquema diretamente, terá de recriar o índice. 

## <a name="verify-data-import-with-search-explorer"></a>Verifique se a importação de dados com o Explorador de pesquisa

Uma forma rápida de fazer uma verificação preliminar no carregamento do documento é usar **Explorador de pesquisa** no portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o [parâmetro de consulta searchMode](/rest/api/searchservice/search-documents) predefinido. Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Vários [exemplos de código do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou disponíveis a pronto, o que lhe dá uma forma fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Ao executar o indexador pré-configurado na origem de dados de exemplo, um índice é criado e carregado com documentos que, em seguida, podem ser consultados no Explorador de pesquisa ou ao código que escreve.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Instruções do portal: criar, carregar e consultar índices](search-get-started-portal.md)
