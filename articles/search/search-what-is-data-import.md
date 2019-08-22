---
title: Importação de dados para ingestão de dados para um índice de pesquisa-Azure Search
description: Popular e carregar dados em um índice em Azure Search de fontes de dados externas.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 71ee63dfbe880cbf6018f3dd13d360850ed994f9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647337"
---
# <a name="data-import-overview---azure-search"></a>Visão geral de importação de dados-Azure Search

No Azure Search, as consultas são executadas em seu conteúdo carregado e salvo em um [índice de pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para popular um índice: *enviar por push* seus dados para o índice programaticamente ou apontar um [Azure Search indexador](search-indexer-overview.md) em uma fonte de dados com suporte para efetuar *pull* dos dados.

Com qualquer abordagem, o objetivo é *carregar dados* de uma fonte de dados externa em um índice Azure Search. Azure Search permitirá que você crie um índice vazio, mas até enviar por Push ou efetuar pull de dados para ele, ele não será passível de consulta.

## <a name="pushing-data-to-an-index"></a>Enviar dados para um índice
O modelo de envio, utilizado para enviar programaticamente os seus dados para o Azure Search, é a abordagem mais flexível. Em primeiro lugar, não tem restrições quanto ao tipo de origem de dados. Qualquer conjunto de dados composto por documentos JSON pode ser enviado para um índice do Azure Search, pressupondo que cada documento desse conjunto tem campos que sejam mapeados para os campos definidos no esquema de índice. Em segundo lugar, não tem restrições relativamente à frequência de execução. Pode enviar alterações para os índices tantas vezes quanto quiser. Para aplicações com requisitos de latência muito baixos (por exemplo, se quiser que as operações de pesquisa estejam sincronizadas com bases de dados de inventário dinâmicas), o modelo de envio é a única opção.

Esta abordagem é mais flexível do que o modelo de extração, pois pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, consoante o limite que ocorrer primeiro). O modelo de envio também permite carregar documentos para a Azure Search independentemente do local de armazenamento dos dados.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Como enviar dados para um índice do Azure Search

Pode utilizar as APIs seguintes para carregar um ou múltiplos documentos para um índice:

+ [Adicionar, Atualizar ou Eliminar Documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Atualmente não existe qualquer suporte de ferramentas para o envio de dados através do portal.

Para obter uma introdução a cada metodologia, [consulte início rápido: Criar um índice de Azure Search usando](search-create-index-rest-api.md) o [ PowerShell ou C# o início rápido: Criar um índice de Azure Search usando o](search-get-started-dotnet.md)SDK do .net.

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Ações de indexação: upload, mesclagem, mergeOrUpload, excluir

Você pode controlar o tipo de ação de indexação em uma base por documento, especificando se o documento deve ser carregado de forma completa, mesclado com o conteúdo do documento existente ou excluído.

Na API REST, emita solicitações HTTP POST com corpos de solicitação JSON para a URL de ponto de extremidade do índice Azure Search. Cada objeto JSON na matriz "value" contém a chave do documento e especifica se uma ação de indexação adiciona, atualiza ou exclui o conteúdo do documento. Para obter um exemplo de código, consulte [carregar documentos](search-get-started-dotnet.md#load-documents).

No SDK do .net, empacote seus dados em um `IndexBatch` objeto. Um `IndexBatch` encapsula uma coleção de `IndexAction` objetos, cada um contendo um documento e uma propriedade que informa Azure Search a ação a ser executada nesse documento. Para obter um exemplo de código, consulte o guia de [ C# início rápido](search-get-started-dotnet.md).


| @search.action | Descrição | Campos necessários para cada documento | Notas |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Um ação `upload` é semelhante a um "upsert" onde o documento será inserido se for novo e atualizado/substituído se já existir. |chave, juntamente com quaisquer outros campos que pretende definir |Quando atualizar/substituir um documento existente, qualquer campo que não está especificado no pedido terá o respetivo campo definido como `null`. Isto ocorre mesmo quando o campo foi anteriormente definido para um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existe no índice, a intercalação irá falhar. |chave, juntamente com quaisquer outros campos que pretende definir |Qualquer campo que especifique numa intercalação irá substituir o campo existente no documento. No SDK do .NET, isso inclui campos do tipo `DataType.Collection(DataType.String)`. Na API REST, isso inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com o valor `["budget"]` e executar uma intercalação com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Esta ação tem o mesmo comportamento de `merge` caso um documento com a chave especificada já exista no índice. Se o documento não existir, tem um comportamento semelhante `upload` a um novo documento. |chave, juntamente com quaisquer outros campos que pretende definir |- |
| `delete` |Remove o documento especificado do índice. |apenas chave |Quaisquer campos que especificar diferentes do campo de chave serão ignorados. Se pretender remover um campo individual de um documento, utilize `merge` em vez disso e simplesmente defina o campo explicitamente como nulo. |

## <a name="decide-which-indexing-action-to-use"></a>Decidir a ação de indexação a utilizar
Para importar dados usando o SDK do .NET, (carregar, mesclar, excluir e mergeOrUpload). Dependendo das ações que escolher abaixo, apenas determinados campos tem de ser incluídos para cada documento:


### <a name="formulate-your-query"></a>Formular a consulta
Existem duas formas de [pesquisar o índice através da API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma forma consiste em emitir um pedido de HTTP POST, em que os parâmetros da sua consulta são definidos num objeto JSON no corpo do pedido. A outra forma consiste em emitir um pedido de HTTP GET, em que os parâmetros da consulta são definidos no URL do pedido. O POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) em relação ao tamanho dos parâmetros de consulta do que o GET. Por este motivo, recomendamos a utilização do POST, salvo se tiver circunstâncias especiais em que a utilização do GET seja mais conveniente.

Para o POST e o GET, é necessário fornecer o *nome do serviço*, o *nome do índice* e a *versão de API* correta (no momento de publicação deste documento a versão de API atual é `2019-05-06`) no URL do pedido. No GET, os parâmetros de consulta são fornecidos na *cadeia de consulta* no final do URL. Veja a seguir o formato URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

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

## <a name="verify-data-import-with-search-explorer"></a>Verificar a importação de dados com o Search Explorer

Uma maneira rápida de executar uma verificação preliminar no carregamento do documento é usar o **Search Explorer** no Portal. O explorador permite-lhe consultar índices sem ter de escrever qualquer código. A experiência de pesquisa baseia-se em predefinições, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e o [parâmetro de consulta searchMode](/rest/api/searchservice/search-documents) predefinido. Os resultados são devolvidos em JSON, de modo a que possa inspecionar todo o documento.

> [!TIP]
> Vários [exemplos de código do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de dados incorporados ou disponíveis a pronto, o que lhe dá uma forma fácil de começar. O portal também disponibiliza um indexador e uma origem de dados de exemplo, que consiste num pequeno conjunto de dados de imobiliário (com o nome “realestate-us-sample"). Quando você executa o indexador pré-configurado na fonte de dados de exemplo, um índice é criado e carregado com documentos que podem ser consultados no Search Explorer ou pelo código que você escreve.

## <a name="see-also"></a>Consulte também

+ [Descrição geral do Indexador](search-indexer-overview.md)
+ [Instruções do portal: criar, carregar e consultar índices](search-get-started-portal.md)
