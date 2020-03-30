---
title: Implementar conjuntos de escala de máquinavirtual com IPv6 em Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implementar conjuntos de escala de máquinas virtuais com iPv6 numa rede virtual Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164994"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>Implementar conjuntos de escala de máquina virtual com IPv6 em Azure (Pré-visualização)

Este artigo mostra-lhe como implementar uma dupla stack (IPv4 + IPv6) Conjunto de escala de máquina virtual com um equilíbrio de carga externo de dupla pilha numa rede virtual Azure. O processo para criar um conjunto de máquinas virtuais com capacidade IPv6 é quase idêntico ao processo de criação de VMs individuais descritos [aqui](ipv6-configure-standard-load-balancer-template-json.md). Começará com os passos semelhantes aos descritos para VMs individuais:
1.  Crie IPv4 e IPv6 IPs públicos.
2.  Crie um equilíbrio de carga de pilhas duplas.  
3.  Crie regras do grupo de segurança da rede (NSG).  

O único passo diferente dos VMs individuais é criar a configuração da interface de rede (NIC) que utiliza o recurso conjunto de escala de máquina virtual: networkProfile/networkInterfaceConfigurations. A estrutura JSON é semelhante à do objeto Microsoft.Network/networkInterfaces utilizado para VMs individuais com a adição de definição do NIC e do IPv4 IpConfiguration como interface primária utilizando o **"primário":** atributo verdadeiro como visto no seguinte exemplo:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Modelo de conjunto de escala de máquina virtual jSON

Para implementar uma dupla pilha (IPv4 + IPv6) Conjunto de escala de máquina virtual com dupla pilha externa balancedor de carga e modelo de amostra de visão de rede virtual [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o suporte iPv6 em redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
