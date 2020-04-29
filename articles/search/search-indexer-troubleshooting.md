---
title: Problemas de problemas comum de indexador de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrija erros e problemas comuns com os indexadores em Pesquisa Cognitiva Azure, incluindo ligação de fonte de dados, firewall e documentos em falta.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190922"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Problemas de resolução de problemas comum de indexação na Pesquisa Cognitiva de Azure

Os indexadores podem encontrar uma série de problemas ao indexar dados para a Pesquisa Cognitiva Azure. As principais categorias de falhas incluem:

* [Ligação a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Inesão documental a um índice](#index-errors)

## <a name="connection-errors"></a>Erros de ligação

> [!NOTE]
> Os indexadores têm um suporte limitado para aceder a fontes de dados e outros recursos que são garantidos pelos mecanismos de segurança da rede Azure. Atualmente, os indexadores só podem aceder a fontes de dados através dos mecanismos de restrição de alcance de endereçoip correspondentes ou das regras nsg quando aplicável. Os detalhes para aceder a cada fonte de dados suportada podem ser encontrados abaixo.
>
> Pode descobrir o endereço IP do seu serviço de pesquisa, pingando `<your-search-service-name>.search.windows.net`o seu nome de domínio totalmente qualificado (por exemplo, ).
>
> Pode descobrir a gama de `AzureCognitiveSearch` endereços IP da etiqueta de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) utilizando [ficheiros JSON descarregados](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) ou através da [API De identificação](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)de etiquetas de serviço . A gama de endereços IP é atualizada semanalmente.

### <a name="configure-firewall-rules"></a>Configurar as regras de firewall

O Armazenamento Azure, cosmosDB e Azure SQL fornecem uma firewall configurável. Não há uma mensagem de erro específica quando a firewall está ativada. Tipicamente, os erros de `The remote server returned an error: (403) Forbidden` firewall `Credentials provided in the connection string are invalid or have expired`são genéricos e parecem ou .

Existem 2 opções para permitir que os indexadores acedam a estes recursos em tal caso:

* Desative a firewall, permitindo o acesso a todas **as redes** (se possível).
* Em alternativa, pode permitir o acesso ao endereço IP do `AzureCognitiveSearch` seu serviço de pesquisa e à gama de endereços IP da etiqueta de [serviço](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) nas regras de firewall do seu recurso (restrição de intervalo de endereço IP).

Os detalhes para configurar as restrições de intervalo de endereçoip para cada tipo de fonte de dados podem ser encontrados a partir dos seguintes links:

* [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [BD do Cosmos](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Limitação**: Tal como indicado na documentação acima para o Armazenamento Do Azure, as restrições de intervalo de endereçoIP só funcionarão se o seu serviço de pesquisa e a sua conta de armazenamento estiverem em diferentes regiões.

As funções Azure (que poderiam ser usadas como uma [habilidade Custom Web Api)](cognitive-search-custom-skill-web-api.md)também suportam restrições de [endereçoIP](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). A lista de endereços IP para configurar seria o endereço IP `AzureCognitiveSearch` do seu serviço de pesquisa e a gama de endereços IP da etiqueta de serviço.

Os detalhes para aceder a dados no servidor SQL num VM Azure estão [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) delineados

### <a name="configure-network-security-group-nsg-rules"></a>Configure regras do grupo de segurança da rede (NSG)

Ao aceder a dados numa instância gerida pela SQL, ou quando um VM Azure é usado como o serviço web URI para uma [habilidade Api Web Personalizada,](cognitive-search-custom-skill-web-api.md)os clientes não devem estar preocupados com endereços IP específicos.

Nesses casos, o VM Azure ou a instância gerida pelo SQL podem ser configurados para residir dentro de uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir dentro e fora das subredes de rede virtuais e interfaces de rede.

A `AzureCognitiveSearch` etiqueta de serviço pode ser utilizada diretamente nas [regras nsg](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) de entrada sem necessidade de procurar a sua gama de endereços IP.

Mais detalhes para aceder a dados em uma instância gerida sQL são delineados [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexing" não está habilitado

A Pesquisa Cognitiva Azure tem uma dependência implícita na indexação de Cosmos DB. Se desligar a indexação automática em Cosmos DB, a Pesquisa Cognitiva Azure devolve um estado de sucesso, mas não indexa o conteúdo do contentor. Para obter instruções sobre como verificar as definições e ligar a indexação, consulte [Gerir a indexação em Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos inprocessáveis ou não apoiados

Os documentos do indexador blob [que documentam os formatos são explicitamente suportados. .](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Às vezes, um recipiente de armazenamento de bolhas contém documentos não apoiados. Outras vezes pode haver documentos problemáticos. Pode evitar parar o seu indexante nestes documentos [alterando as opções de configuração:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

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

O indexante blob [encontra e extrai texto de bolhas num recipiente](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração do texto incluem:

* O documento contém apenas imagens digitalizadas. As bolhas PDF que têm conteúdo não-texto, como imagens digitalizadas (JPGs), não produzem resultados num pipeline padrão de indexação de bolhas. Se tiver conteúdo de imagem com elementos de texto, pode utilizar [a procura cognitiva](cognitive-search-concept-image-scenarios.md) para encontrar e extrair o texto.
* O indexante blob está configurado apenas para indexar metadados. Para extrair conteúdo, o indexante blob deve ser configurado para [extrair conteúdo e metadados:](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)

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

Os indexadores encontram documentos de uma fonte de [dados.](https://docs.microsoft.com/rest/api/searchservice/create-data-source) Por vezes, um documento da fonte de dados que deveria ter sido indexado parece estar ausente de um índice. Existem algumas razões comuns para estes erros poderem acontecer:

* O documento não foi indexado. Verifique o portal para obter uma corrida de indexador bem-sucedida.
* O documento foi atualizado após a execução do indexante. Se o seu indexante estiver dentro de um [horário,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)acabará por refazer e recolher o documento.
* A [consulta](/rest/api/searchservice/create-data-source) especificada na fonte de dados exclui o documento. Os indexadores não podem indexar documentos que não fazem parte da fonte de dados.
* [Mapeamentos](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) de campo ou enriquecimento de [IA](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) mudaram o documento e parece diferente do que se espera.
* Utilize o documento de [procura Ção API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para encontrar o seu documento.
