---
title: Permitir o acesso aos intervalos IP indexantes
titleSuffix: Azure Cognitive Search
description: Configure as regras de firewall IP para permitir o acesso de dados por um indexante de Pesquisa Cognitiva Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757248"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Configure as regras de firewall IP para permitir ligações indexantes (Pesquisa Cognitiva Azure)

As regras de firewall IP sobre recursos Azure, tais como contas de armazenamento, contas Dessos dB e Azure SQL Servers apenas permitem tráfego originário de intervalos IP específicos para aceder a dados.

Este artigo descreve como configurar as regras IP, através do portal Azure, para uma conta de armazenamento para que os indexantes de Pesquisa Cognitiva Azure possam aceder aos dados de forma segura. Embora específica para o Azure Storage, a abordagem também funciona para outros recursos Azure que usam regras de firewall IP para garantir o acesso aos dados.

> [!NOTE]
> As regras de firewall IP para conta de armazenamento só são eficazes se a conta de armazenamento e o serviço de pesquisa estiverem em diferentes regiões. Se a sua configuração não permitir isto, recomendamos utilizar a [opção de exceção de serviço fidedigno](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Obtenha o endereço IP do serviço de pesquisa

Obtenha o nome de domínio totalmente qualificado (FQDN) do seu serviço de pesquisa. Isto vai `<search-service-name>.search.windows.net` parecer. Você pode descobrir o FQDN procurando o seu serviço de pesquisa no portal Azure.

   ![Obtenha serviço FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Obtenha serviço FQDN")

O endereço IP do serviço de pesquisa pode ser obtido através da realização de um `nslookup` (ou `ping` a) do FQDN. No exemplo abaixo, adicionaria "150.0.0.1" a uma regra de entrada na firewall de armazenamento Azure. Pode demorar até 15 minutos após a atualização das definições de firewall para que o indexante do serviço de pesquisa possa aceder à conta de Armazenamento Azure.

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

São utilizados endereços IP adicionais para pedidos originários do [ambiente de execução multi-inquilino](search-indexer-securing-resources.md#indexer-execution-environment)do indexante. Pode obter esta gama de endereços IP a partir da etiqueta de serviço.

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

A forma mais fácil de adicionar intervalos de endereço IP à regra de firewall de uma conta de armazenamento é através do portal Azure. Localize a conta de armazenamento no portal e navegue para o separador **Firewalls e redes virtuais.**

   ![Firewall e redes virtuais](media\search-indexer-howto-secure-access\storage-firewall.png "Firewall e redes virtuais")

Adicione os três endereços IP obtidos anteriormente (1 para o serviço de pesquisa IP, 2 para a `AzureCognitiveSearch` etiqueta de serviço) na gama de endereços e selecione **Guardar**.

   ![Regras IP de firewall](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Regras IP de firewall")

As regras de firewall demoram 5 a 10 minutos a serem atualizadas, após o que os indexantes devem poder aceder aos dados na conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

- [Configure firewalls de armazenamento Azure](../storage/common/storage-network-security.md)
- [Configure firewall IP para Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Configure firewall IP para Azure SQL Server](../azure-sql/database/firewall-configure.md)