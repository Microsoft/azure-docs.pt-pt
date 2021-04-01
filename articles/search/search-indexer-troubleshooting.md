---
title: Resolução de problemas problemas problemas comuns de indexação de pesquisa
titleSuffix: Azure Cognitive Search
description: Corrija erros e problemas comuns com indexadores na Pesquisa Cognitiva Azure, incluindo ligação de fonte de dados, firewall e documentos em falta.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355130"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Problemas de resolução de problemas problemas de indexante comum na Pesquisa Cognitiva do Azure

Os indexantes podem encontrar uma série de problemas ao indexar dados na Pesquisa Cognitiva do Azure. As principais categorias de insucesso incluem:

* [Ligação a uma fonte de dados ou outros recursos](#connection-errors)
* [Processamento de documentos](#document-processing-errors)
* [Ingestão de documentos a um índice](#index-errors)

## <a name="connection-errors"></a>Erros de ligação

> [!NOTE]
> Os indexantes têm um suporte limitado para aceder a fontes de dados e outros recursos que são protegidos pelos mecanismos de segurança da rede Azure. Atualmente, os indexantes só podem aceder a fontes de dados através de mecanismos de restrição de intervalo de endereço IP correspondentes ou regras NSG quando aplicável. Os detalhes para aceder a cada fonte de dados suportado podem ser encontrados abaixo.
>
> Pode descobrir o endereço IP do seu serviço de pesquisa, verificando o seu nome de domínio totalmente qualificado (por exemplo, `<your-search-service-name>.search.windows.net` ).
>
> Pode descobrir a gama de endereços IP da tag de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) utilizando [ficheiros JSON transferíveis](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) ou através da [API de Descoberta de Marca de Serviço.](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) O intervalo de endereços IP é atualizado semanalmente.

### <a name="configure-firewall-rules"></a>Configurar as regras de firewall

Azure Storage, CosmosDB e Azure SQL fornecem uma firewall configurável. Não há nenhuma mensagem de erro específica quando a firewall está ativada. Normalmente, os erros de firewall são genéricos e parecem `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired` ou.

Existem 2 opções para permitir que os indexantes acedam a estes recursos em tal caso:

* Desative a firewall, permitindo o acesso a partir de **Todas as Redes** (se possível).
* Em alternativa, pode permitir o acesso ao endereço IP do seu serviço de pesquisa e ao intervalo de identificação IP da etiqueta de `AzureCognitiveSearch` [serviço](../virtual-network/service-tags-overview.md#available-service-tags) nas regras de firewall do seu recurso (restrição de intervalo de endereço IP).

Os detalhes para configurar as restrições de intervalo de endereços IP para cada tipo de fonte de dados podem ser encontrados a partir dos seguintes links:

* [Armazenamento do Azure](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [BD do Cosmos](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [SQL do Azure](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Limitação**: Conforme indicado na documentação acima para o Armazenamento Azure, as restrições de intervalo de endereços IP só funcionarão se o seu serviço de pesquisa e a sua conta de armazenamento estiverem em diferentes regiões.

As funções Azure (que poderiam ser usadas como [uma habilidade Custom Web Api](cognitive-search-custom-skill-web-api.md)) também suportam [restrições de endereço IP](../azure-functions/ip-addresses.md#ip-address-restrictions). A lista de endereços IP para configurar seria o endereço IP do seu serviço de pesquisa e o intervalo de endereço IP da tag de `AzureCognitiveSearch` serviço.

Os detalhes para aceder aos dados no servidor SQL num VM Azure estão delineados [aqui](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Configure regras do grupo de segurança da rede (NSG)

Ao aceder a dados num caso gerido pelo SQL, ou quando um Azure VM é usado como o serviço web URI para uma [habilidade Personalizada Web Api,](cognitive-search-custom-skill-web-api.md)os clientes não precisam de se preocupar com endereços IP específicos.

Nesses casos, o Azure VM, ou a instância gerida pelo SQL pode ser configurado para residir dentro de uma rede virtual. Em seguida, um grupo de segurança de rede pode ser configurado para filtrar o tipo de tráfego de rede que pode fluir dentro e fora das sub-redes de rede virtuais e interfaces de rede.

A `AzureCognitiveSearch` etiqueta de serviço pode ser usada diretamente nas [regras NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) de entrada sem precisar de procurar a sua gama de endereços IP.

Mais detalhes para aceder a dados em um exemplo gerido sql são delineados [aqui](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexing" não está habilitado

A Azure Cognitive Search tem uma dependência implícita na indexação de Cosmos DB. Se desativar a indexação automática em Cosmos DB, a Azure Cognitive Search retorna um estado de sucesso, mas não consegue indexar o conteúdo do contentor. Para obter instruções sobre como verificar as definições e ligar a indexação, consulte [Gerir a indexação em Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="document-processing-errors"></a>Erros de processamento de documentos

### <a name="unprocessable-or-unsupported-documents"></a>Documentos não processáveis ou não suportados

O indexante blob [documenta quais os formatos de documentos são explicitamente suportados.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). . Às vezes, um recipiente de armazenamento de bolhas contém documentos não suportados. Outras vezes pode haver documentos problemáticos. Pode evitar parar o seu indexante nestes documentos [alterando opções de configuração:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Conteúdo de documento em falta

O indexante blob [encontra e extrai texto de bolhas num recipiente](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Alguns problemas com a extração de texto incluem:

* O documento contém apenas imagens digitalizadas. As bolhas PDF que têm conteúdo não texto, como imagens digitalizadas (JPGs), não produzem resultados num pipeline de indexação de blob padrão. Se tiver conteúdo de imagem com elementos de texto, pode utilizar a [pesquisa cognitiva](cognitive-search-concept-image-scenarios.md) para encontrar e extrair o texto.
* O indexante blob é configurado apenas para metadados de índice. Para extrair o conteúdo, o indexante blob deve ser configurado para [extrair tanto o conteúdo como os metadados:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Erros de índice

### <a name="missing-documents"></a>Documentos em falta

Os indexantes encontram documentos a partir de uma fonte de [dados.](/rest/api/searchservice/create-data-source) Por vezes, um documento da fonte de dados que deveria ter sido indexado parece estar ausente de um índice. Há algumas razões comuns para estes erros acontecerem:

* O documento não foi indexado. Verifique se o portal tem um indexer bem sucedido.
* Verifique o valor [de rastreio de alterações.](/rest/api/searchservice/create-data-source#data-change-detection-policies) Se o seu alto valor de marca de água for uma data definida para um futuro, então quaisquer documentos que tenham uma data inferior a esta serão ignorados pelo indexante. Pode compreender o estado de rastreio de alterações do seu indexante utilizando os campos "InitialTrackingState" e "FinalTrackingState" no estado do [indexante](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* O documento foi atualizado após a execução do indexante. Se o seu indexante estiver na [programação,](/rest/api/searchservice/create-indexer#indexer-schedule)irá eventualmente repetir e recolher o documento.
* A [consulta](/rest/api/searchservice/create-data-source) especificada na fonte de dados exclui o documento. Os indexantes não podem indexar documentos que não fazem parte da fonte de dados.
* [Os mapeamentos de campo](/rest/api/searchservice/create-indexer#fieldmappings) ou [o enriquecimento de IA](./cognitive-search-concept-intro.md) alteraram o documento e parece diferente do que se espera.
* Utilize o [documento de procura API](/rest/api/searchservice/lookup-document) para encontrar o seu documento.