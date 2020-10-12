---
title: Permitir o acesso aos intervalos IP indexantes
titleSuffix: Azure Cognitive Search
description: Como orientar isso descreve como configurar regras de firewall IP para que os indexantes possam ter acesso.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463781"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Criação de regras de firewall IP para permitir o acesso do indexante

As regras de firewall IP sobre recursos Azure, tais como contas de armazenamento, contas Dessos dB e servidores Azure SQL apenas permitem tráfego originário de intervalos IP específicos para aceder a dados.

Este artigo descreverá como configurar as regras ip, através do portal Azure, para uma conta de armazenamento para que os indexantes de Pesquisa Cognitiva Azure possam aceder aos dados de forma segura. Embora específico de armazenamento, este guia pode ser traduzido diretamente para outros recursos Azure que também oferecem regras de firewall IP para garantir o acesso aos dados.

> [!NOTE]
> As regras de firewall IP para conta de armazenamento só são eficazes se a conta de armazenamento e o serviço de pesquisa estiverem em diferentes regiões. Se a sua configuração não permitir isto, recomendamos utilizar a [opção de exceção de serviço fidedigno](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obtenha o endereço IP do serviço de pesquisa

Obtenha o nome de domínio totalmente qualificado (FQDN) do seu serviço de pesquisa. Isto vai `<search-service-name>.search.windows.net` parecer. Você pode descobrir o FQDN procurando o seu serviço de pesquisa no portal Azure.

   ![Obtenha serviço FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenha serviço FQDN")

O endereço IP do serviço de pesquisa pode ser obtido através da realização de um `nslookup` (ou `ping` a) do FQDN. Este será um dos endereços IP para adicionar às regras de firewall.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Obtenha os intervalos de endereço IP para a etiqueta de serviço "AzureCognitiveSearch"

Os intervalos de endereço IP para a `AzureCognitiveSearch` etiqueta de serviço podem ser obtidos através da descoberta [API (pré-visualização)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) ou do [ficheiro JSON transferível](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Para esta passagem, assumindo que o serviço de pesquisa é a nuvem pública do Azure, o [ficheiro Azure Public JSON](https://www.microsoft.com/download/details.aspx?id=56519) deve ser descarregado.

   ![Baixar ficheiro JSON](media\search-indexer-howto-secure-access\service-tag.png "Baixar ficheiro JSON")

A partir do ficheiro JSON, assumindo que o serviço de pesquisa está no Centro-Oeste dos EUA, a lista de endereços IP para o ambiente de execução indexante multi-inquilinos são listados abaixo.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Para /32 endereços IP, deixe cair o "/32" (52.253.133.74/32 -> 52.253.133,74), outros podem ser utilizados verbatim.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Adicione as gamas de endereços IP às regras de firewall IP

A forma mais fácil de adicionar intervalos de endereço IP à regra de firewall de uma conta de armazenamento é através do portal Azure. Localize a conta de armazenamento no portal e navegue para o separador "**Firewalls e redes virtuais".**

   ![Firewall e redes virtuais](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e redes virtuais")

Adicione os três endereços IP obtidos anteriormente (1 para o serviço de pesquisa IP, 2 para a `AzureCognitiveSearch` etiqueta de serviço) na gama de endereços e clique em "**Guardar**"

   ![Regras IP de firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regras IP de firewall")

As regras de firewall demoram 5 a 10 minutos a ser atualizadas após os quais os indexantes poderão aceder aos dados na conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como obter os dois conjuntos de endereços IP para permitir o acesso a índices, use os seguintes links para atualizar as regras de firewall IP para algumas fontes de dados comuns.

- [Configure firewalls de armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Configure firewall IP para CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Configure firewall IP para servidor Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)