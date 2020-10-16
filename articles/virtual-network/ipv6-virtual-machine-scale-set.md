---
title: Implementar conjuntos de escala de máquina virtual com IPv6 em Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar conjuntos de escala de máquina virtual com O IPv6 numa rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710002"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Implementar conjuntos de escala de máquina virtual com IPv6 em Azure

Este artigo mostra-lhe como implantar uma pilha dupla (IPv4 + IPv6) Conjunto de balança de máquina virtual com um balançador de carga externo de pilha dupla numa rede virtual Azure. O processo de criação de um conjunto de escala de máquina virtual com capacidade IPv6 é quase idêntico ao processo de criação de VMs individuais descritos [aqui](ipv6-configure-standard-load-balancer-template-json.md). Você começará com os passos que são semelhantes aos descritos para VMs individuais:
1.    Criar IPv4 e IPv6 Public IPs.
2.    Crie um equilibrador de carga de pilha dupla.  
3.    Criar regras do grupo de segurança de rede (NSG).  

O único passo que é diferente dos VM individuais é criar a configuração da interface de rede (NIC) que utiliza o recurso de conjunto de escala de máquina virtual: networkProfile/networkInterfaceConfigurations. A estrutura JSON é semelhante à do objeto Microsoft.Network/networkInterfaces utilizado para VMs individuais com a adição de configuração do NIC e do IpConfiguration IPv4 como a interface primária utilizando o **"primário": atributo verdadeiro**  como visto no seguinte exemplo:

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>Modelo de conjunto de escala de máquina virtual de amostra JSON

Para implementar uma pilha dupla (IPv4 + IPv6) Conjunto de balança de máquina virtual com balanceador de carga externo de pilha dupla e modelo de amostra de visualização de rede virtual [aqui](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o suporte do IPv6 nas redes virtuais Azure, veja [o que é o IPv6 para a Rede Virtual Azure?](ipv6-overview.md)
