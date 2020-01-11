---
title: Solucionar problemas comuns do indexador de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrija erros e problemas comuns com indexadores no Azure Pesquisa Cognitiva, incluindo conexão de fonte de dados, firewall e documentos ausentes.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d1efd44614cc2384043b32da20f38c91f006459c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863109"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Solucionando problemas comuns do indexador no Azure Pesquisa Cognitiva

Os indexadores podem ter vários problemas ao indexar dados no Azure Pesquisa Cognitiva. As principais categorias de falha incluem:

* [Conectando-se a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documentos para um índice](#index-errors)

## <a name="connection-errors"></a>Erros de ligação

> [!NOTE]
> Os indexadores têm suporte limitado para acessar fontes de dados e outros recursos protegidos pelos mecanismos de segurança de rede do Azure. Atualmente, os indexadores só podem acessar fontes de dados por meio de mecanismos de restrição de intervalo de endereços IP correspondentes ou regras NSG quando aplicável. Os detalhes para acessar cada fonte de dados com suporte podem ser encontrados abaixo.
>
> Você pode encontrar o endereço IP do serviço de pesquisa executando ping no nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net`).
>
> Você pode descobrir o intervalo de endereços IP de `AzureCognitiveSearch` [marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) para a região específica em que o serviço de pesquisa cognitiva do Azure está presente usando [arquivos JSON baixáveis](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou por meio da [API de descoberta de marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). O intervalo de endereços IP é atualizado semanalmente.

### <a name="configure-firewall-rules"></a>Configurar regras de firewall

O armazenamento do Azure, o CosmosDB e o SQL do Azure fornecem um firewall configurável. Não há nenhuma mensagem de erro específica quando o firewall está habilitado. Normalmente, os erros de firewall são genéricos e parecem `The remote server returned an error: (403) Forbidden` ou `Credentials provided in the connection string are invalid or have expired`.

Há duas opções para permitir que os indexadores acessem esses recursos em uma instância desse tipo:

* Desabilite o firewall, permitindo o acesso de **todas as redes** (se possível).
* Como alternativa, você pode permitir o acesso para o endereço IP do serviço de pesquisa e o intervalo de endereços IP da [marca de serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) `AzureCognitiveSearch` nas regras de firewall do recurso (restrição de intervalo de endereços IP).

Os detalhes para configurar restrições de intervalo de endereços IP para cada tipo de fonte de dados podem ser encontrados nos seguintes links:

* [Storage do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [BD do Cosmos](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Limitação**: conforme indicado na documentação acima para o armazenamento do Azure, as restrições de intervalo de endereços IP só funcionarão se o serviço de pesquisa e sua conta de armazenamento estiverem em regiões diferentes.

O Azure Functions (que pode ser usado como uma [habilidade de API Web personalizada](cognitive-search-custom-skill-web-api.md)) também dá suporte a [restrições de endereço IP](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). A lista de endereços IP a serem configurados seria o endereço IP do serviço de pesquisa e o intervalo de endereços IP da marca de serviço `AzureCognitiveSearch`.

Os detalhes para acessar dados no SQL Server em uma VM do Azure são descritos [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configurar regras do NSG (grupo de segurança de rede)

Ao acessar dados em uma instância gerenciada do SQL ou quando uma VM do Azure é usada como o URI do serviço Web para uma [habilidade personalizada da API Web](cognitive-search-custom-skill-web-api.md), os clientes não precisam se preocupar com endereços IP específicos.

Nesses casos, a VM do Azure ou a instância gerenciada do SQL podem ser configuradas para residir em uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir para dentro e fora das sub-redes e interfaces de rede da rede virtual.

A marca de serviço `AzureCognitiveSearch` pode ser usada diretamente nas regras de [NSG](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) de entrada sem a necessidade de Pesquisar seu intervalo de endereços IP.

Mais detalhes para acessar dados em uma instância gerenciada do SQL são descritos [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>A "indexação" do CosmosDB não está habilitada

O Azure Pesquisa Cognitiva tem uma dependência implícita na indexação de Cosmos DB. Se você desativar a indexação automática no Cosmos DB, o Pesquisa Cognitiva do Azure retornará um estado bem-sucedido, mas falhará ao indexar o conteúdo do contêiner. Para obter instruções sobre como verificar as configurações e ativar a indexação, consulte [gerenciar a indexação no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos não processáveis ou sem suporte

O indexador de blob [documenta quais formatos de documento têm suporte explícito.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Às vezes, um contêiner de armazenamento de BLOBs contém documentos sem suporte. Outras vezes, pode haver documentos problemáticos. Você pode evitar interromper o indexador nesses documentos [alterando as opções de configuração](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo de documento ausente

O indexador de blob [localiza e extrai texto de BLOBs em um contêiner](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração de texto incluem:

* O documento contém apenas imagens digitalizadas. Os BLOBs PDF que têm conteúdo de não texto, como imagens digitalizadas (JPGs), não produzem resultados em um pipeline de indexação de blob padrão. Se você tiver conteúdo de imagem com elementos de texto, poderá usar a [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para localizar e extrair o texto.
* O indexador de blob está configurado para indexar apenas os metadados. Para extrair o conteúdo, o indexador de BLOB deve ser configurado para [extrair o conteúdo e os metadados](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

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

### <a name="missing-documents"></a>Documentos ausentes

Indexadores localizam documentos de uma [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Às vezes, um documento da fonte de dados que deveria ter sido indexado parece estar ausente de um índice. Há alguns motivos comuns pelos quais esses erros podem ocorrer:

* O documento não foi indexado. Verifique o portal para obter uma execução bem-sucedida do indexador.
* O documento foi atualizado após a execução do indexador. Se o indexador estiver em um [agendamento](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), ele eventualmente será executado novamente e selecionará o documento.
* A [consulta](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) especificada na fonte de dados exclui o documento. Indexadores não podem indexar documentos que não fazem parte da fonte de dados.
* Os [mapeamentos de campo](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) ou o [enriquecimento de ai](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) alteraram o documento e parecem diferentes do esperado.
* Use a [API de documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para localizar o documento.
