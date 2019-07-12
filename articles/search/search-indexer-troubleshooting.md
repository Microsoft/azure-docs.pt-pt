---
title: Resolver problemas de indexador de pesquisa comuns - Azure Search
description: Corrigir erros e problemas comuns com indexadores na pesquisa do Azure, incluindo a ligação à origem de dados, a firewall e documentos em falta.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 1cb3260fa11354de963318a023fec912d082eae4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653407"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Resolução de problemas comuns do indexador no Azure Search

Indexadores podem executar numa série de problemas durante a indexação dos dados para o Azure Search. As principais categorias de falha incluem:

* [Ligar a uma origem de dados](#data-source-connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documento para um índice](#index-errors)

## <a name="data-source-connection-errors"></a>Erros de ligação de origem de dados

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Firewall de conta de armazenamento

Armazenamento do Azure fornece uma firewall configurável. Por predefinição, a firewall está desativada para que o Azure Search pode ligar à sua conta de armazenamento.

Não existe nenhuma mensagem de erro específico quando a firewall está ativada. Normalmente, os erros de firewall aspeto `The remote server returned an error: (403) Forbidden`.

Pode verificar que a firewall está ativada na [portal](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). A única solução de suporte é desabilitar o firewall ao optar por permitir acesso a partir ["Todas as redes"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Se o indexador não tem um conjunto de capacidades anexado, _poderá_ tentou [adicionar uma exceção](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) para os endereços IP do seu serviço de pesquisa. No entanto, este cenário não é suportado e não é garantido para trabalhar.

Pode encontrar o endereço IP do seu serviço de pesquisa ao enviar pings para o respetivo FQDN (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>BD do Cosmos

#### <a name="indexing-isnt-enabled"></a>Indexação não está ativada

O Azure Search tem uma dependência implícita de indexação do Cosmos DB. Se desativar a indexação automática no Cosmos DB, o Azure Search devolve um Estado com êxito, mas ocorre uma falha de conteúdos do contentor de índice. Para obter instruções sobre como verificar as definições e ative a indexação, consulte [gerir a indexação no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Documento de processamento de erros

### <a name="unprocessable-or-unsupported-documents"></a>Documentos unprocessable ou não suportados

O indexador blob [documentos que formatos de documento explicitamente são suportados.](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Às vezes, um contentor de armazenamento de blob contém documentos não suportados. Outras vezes pode haver documentos problemáticos. Pode evitar a parar o indexador nestes documentos por [alterar opções de configuração](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo de documento em falta

O indexador blob [localiza e extrai o texto de blobs num contentor](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com extrair texto incluem:

* O documento contém apenas imagens digitalizadas. Blobs PDF que têm o conteúdo de que não sejam de texto, como imagens digitalizadas (JPGs), não produzam resultados num pipeline de indexação de BLOBs padrão. Se tiver conteúdo da imagem com elementos de texto, pode usar [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para localizar e extraia o texto.
* O indexador blob está configurado para apenas os metadados do índice. Para extrair o conteúdo, o indexador blob tem de ser configurado para [extrair o conteúdo e metadados](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erros de índice

### <a name="missing-documents"></a>Documentos em falta

Indexadores encontrar os documentos a partir de um [origem de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Por vezes, um documento da origem de dados que deve ter sido indexado parece estar em falta um índice. Existem algumas das razões comuns para que estes erros podem acontecer:

* O documento ainda não foi indexado. Verifique o portal para uma execução do indexador com êxito.
* O documento foi atualizado após o execução do indexador. Se o indexador numa [agenda](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), eventualmente, volte a executar e pegar o documento.
* O [consulta](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) especificados nos dados origem exclui o documento. Indexadores não é possível indexar documentos que não fazem parte da origem de dados.
* [Mapeamentos de campo](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) ou [pesquisa cognitiva](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) alterou o documento que é diferente do que o esperado.
* Utilize o [documento de pesquisa API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para localizar o seu documento.
