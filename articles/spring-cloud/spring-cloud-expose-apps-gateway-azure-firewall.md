---
title: Expor aplicações à Internet utilizando o Gateway de Aplicações e a Azure Firewall
description: Como expor aplicações à Internet usando o Gateway de Aplicações e a Azure Firewall
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738723"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Expor aplicações à Internet utilizando o Gateway de Aplicações e a Azure Firewall

Este documento explica como expor aplicações à Internet usando o Application Gateway e o Azure Firewall. Quando uma instância de serviço Azure Spring Cloud é implantada na sua rede virtual, as aplicações na instância de serviço só são acessíveis na rede privada. Para tornar as aplicações acessíveis na Internet, é necessário integrar-se com o **Azure Application Gateway** e, opcionalmente, com **o Azure Firewall.**

## <a name="prerequisites"></a>Pré-requisitos

- [Versão Azure CLI 2.0.4 ou posterior](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definir variáveis

Defina variáveis para o grupo de recursos e rede virtual que criou como direcionado para [implementar a nuvem de mola do Azure na rede virtual Azure (injeção VNet)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Personalize os valores com base no seu ambiente real.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Iniciar sessão no Azure

Faça login no Azure CLI e escolha a sua subscrição ativa.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Criar recursos de rede

O **Azure Application Gateway** a ser criado juntar-se-á à mesma rede virtual que - ou rede virtual espreitada para - a instância de serviço Azure Spring Cloud. Primeiro crie uma nova sub-rede para o Gateway de aplicações na rede virtual utilizando `az network vnet subnet create` , e também crie um endereço IP público como frontend do Gateway de aplicações utilizando `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Criar um gateway de aplicação

Crie um gateway de aplicações utilizando `az network application-gateway create` e especifique o nome de domínio privado e qualificado da sua aplicação (FQDN) como servidores no pool de backend. Em seguida, atualize a definição HTTP utilizando `az network application-gateway http-settings update` para utilizar o nome do anfitrião a partir do pool backend.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Pode levar até 30 minutos para a Azure criar o gateway de aplicação. Depois de criado, verifique a saúde do backend usando `az network application-gateway show-backend-health` .  Isto examina se o gateway de aplicação chega à sua aplicação através do seu FQDN privado.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

A saída indica o estado saudável da piscina de backend.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Aceda à sua aplicação utilizando o FRONTend PUBLIC IP do gateway de aplicações

Obtenha o endereço IP público do gateway de aplicações utilizando `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Copie e cole o endereço IP público na barra de endereço do seu navegador.

  ![App em IP público](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Ver também

- [Resolução de problemas Azure Spring Cloud em VNET](spring-cloud-troubleshooting-vnet.md)
- [Responsabilidades do cliente para correr Azure Spring Cloud em VNET](spring-cloud-vnet-customer-responsibilities.md)