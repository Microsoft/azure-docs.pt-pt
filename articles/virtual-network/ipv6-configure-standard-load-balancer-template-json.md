---
title: Implemente uma aplicação de dupla pilha IPv6 na rede virtual Azure - Modelo de Manjedoura de Recursos
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar uma aplicação de dupla pilha IPv6 com O Equilíbrio de Carga Padrão na rede virtual Azure utilizando modelos VM do Gestor de Recursos Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 1f9531b5d1decfd462a82b9d389c5af519591c83
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420651"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>Implementar uma aplicação de dupla pilha IPv6 na rede virtual Azure - Modelo

Este artigo fornece uma lista de tarefas de configuração IPv6 com a parte do modelo VM do Gestor de Recursos Azure que se aplica. Utilize o modelo descrito neste artigo para implementar uma aplicação dual stack (IPv4 + IPv6) utilizando o Standard Load Balancer em Azure que inclui uma rede virtual de dupla pilha com subnets IPv4 e IPv6, um Balancer de Carga Padrão com configurações frontais duplas (IPv4 + IPv6), VMs com NICs que têm uma configuração IP dupla, grupo de segurança de rede e IPs públicos. 

## <a name="required-configurations"></a>Configurações necessárias

Procure as secções do modelo no modelo para ver onde devem ocorrer.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>IPv6 addressSpace para a rede virtual

Secção do modelo para adicionar:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Sub-rede IPv6 dentro do endereço de rede virtual IPv6Space

Secção do modelo para adicionar:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Configuração IPv6 para o NIC

Secção do modelo para adicionar:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Regras do grupo de segurança de rede IPv6 (NSG)

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Configuração condicional

Se estiver a utilizar um aparelho virtual de rede, adicione as rotas IPv6 na Tabela de Rotas. Caso contrário, esta configuração é opcional.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Configuração opcional

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Acesso à Internet IPv6 para a rede virtual

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Endereços IP públicos do IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 Extremidade frontal para balanceor de carga

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 Back-end address pool para Balancer de Carga

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Regras do equilíbrio de carga IPv6 para associar portas de entrada e saída

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Modelo VM da amostra JSON
Para implementar uma aplicação de dupla pilha IPv6 na rede virtual Azure utilizando o modelo do Gestor de Recursos Azure, veja o modelo de amostra [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/).

## <a name="next-steps"></a>Passos seguintes

Pode encontrar detalhes sobre preços para [endereços IP públicos,](https://azure.microsoft.com/pricing/details/ip-addresses/) [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/)da rede ou [Balancer de Carga](https://azure.microsoft.com/pricing/details/load-balancer/).
