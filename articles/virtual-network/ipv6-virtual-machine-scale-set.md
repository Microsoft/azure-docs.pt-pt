---
title: Implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 no Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164994"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Implantar conjuntos de dimensionamento de máquinas virtuais com IPv6 no Azure (versão prévia)

Este artigo mostra como implantar um conjunto de dimensionamento de máquinas virtuais de pilha dupla (IPv4 + IPv6) com um balanceador de carga externo de pilha dupla em uma rede virtual do Azure. O processo para criar um conjunto de dimensionamento de máquinas virtuais compatível com IPv6 é praticamente idêntico ao processo de criação de VMs individuais descritas [aqui](ipv6-configure-standard-load-balancer-template-json.md). Você começará com as etapas que são semelhantes àquelas descritas para VMs individuais:
1.  Crie IPs públicos IPv4 e IPv6.
2.  Crie um balanceador de carga de pilha dupla.  
3.  Criar regras de NSG (grupo de segurança de rede).  

A única etapa que é diferente das VMs individuais é criar a configuração da NIC (interface de rede) que usa o recurso do conjunto de dimensionamento de máquinas virtuais: networkProfile/networkInterfaceConfigurations. A estrutura JSON é semelhante à do objeto Microsoft. Network/networkInterfaces usado para VMs individuais com a adição de configuração da NIC e da IpConfiguration IPv4 como a interface primária usando o atributo **"Primary": true** como visto no exemplo a seguir:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Exemplo de JSON de modelo de conjunto de dimensionamento de máquinas virtuais

Para implantar um conjunto de dimensionamento de máquinas virtuais de pilha dupla (IPv4 + IPv6) com o Load Balancer externo de pilha dupla e o modelo de exibição de rede virtual [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md).
