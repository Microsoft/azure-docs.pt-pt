---
title: 'Terraform: Implementar frontend Web App e backend Web App conectada de forma segura com integração VNet e Ponto Final Privado'
description: Saiba como utilizar o fornecedor terraform para o Serviço de Aplicações para implementar duas aplicações web conectadas de forma segura com o Private Endpoint e a VNet Integration
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91739838"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Criar duas aplicações web conectadas de forma segura com a integração Private Endpoint e VNet

Este artigo ilustra um exemplo de utilização da integração [Private Endpoint](../networking/private-endpoint.md) e [VNet](../web-sites-integrate-with-vnet.md) regional para ligar duas aplicações web (frontend e backend) seguindo de forma segura estes passos:
- Implementar um VNet
- Criar a primeira sub-rede para a integração
- Crie a segunda sub-rede para o ponto final privado, tem de definir um parâmetro específico para desativar as políticas de rede
- Implementar um plano de Serviço de Aplicações do tipo PremiumV2 ou PremiumV3, necessário para a funcionalidade Private Endpoint
- Crie a aplicação web frontend com configurações específicas de aplicações para consumir a zona privada de DNS, [mais detalhes](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Ligue a aplicação web frontend à sub-rede de integração
- Crie a aplicação web backend
- Crie a zona privada DNS com o nome da zona de ligação privada para aplicações web privatelink.azurewebsites.net
- Ligue esta zona ao VNet
- Crie o ponto final privado para a aplicação web backend na sub-rede endpoint e registe nomes DNS (website e SCM) na zona privada de DNS anteriormente criada

## <a name="how-to-use-terraform-in-azure"></a>Como usar terraform em Azure

Navegue pela [documentação do Azure](/azure/developer/terraform/) para aprender a usar terraforme com Azure.

## <a name="the-complete-terraform-file"></a>O ficheiro terraform completo

Para utilizar este ficheiro tem de alterar a propriedade do nome para recursos frontwebapp e backwebapp (o nome do webapp deve ser um nome DNS exclusivo em todo o mundo). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Passos seguintes


> [Saiba mais sobre a utilização do Terraform em Azure](/azure/developer/terraform/)