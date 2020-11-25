---
title: Visão geral dos grupos de segurança de aplicações Azure
titlesuffix: Azure Virtual Network
description: Conheça o uso de grupos de segurança de aplicações.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 3542ae2e94c2fa3d3e9d6100738b2aabded94d15
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005270"
---
# <a name="application-security-groups"></a>Grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Pode reutilizar a política de segurança em escala sem a manutenção manual de endereços IP explícitos. A plataforma lida com a complexidade dos endereços IP explícitos e dos múltiplos conjuntos de regras, permitindo-lhe focar-se na lógica de negócio. Para compreender melhor os grupos de segurança de rede, considere o exemplo seguinte:

![Grupos de segurança de aplicações](./media/security-groups/application-security-groups.png)

Na imagem anterior, *NIC1* e *NIC2* são membros do grupo de segurança de rede *AsgWeb*. *NIC3* é membro do grupo de segurança de rede *AsgLogic*. *NIC4* é membro do grupo de segurança de rede *AsgDb*. Embora cada interface de rede neste exemplo seja membro de apenas um grupo de segurança de rede, uma interface de rede pode ser membro de vários grupos de segurança de aplicações, até aos limites do [Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Não está associado nenhum grupo de segurança de rede às interfaces de rede. *NSG1* está associado a ambas as sub-redes e contém as seguintes regras:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Esta regra é necessária para permitir o tráfego da Internet para os servidores Web. Uma vez que o tráfego de entrada a partir da Internet é negado pela regra de segurança **DenyAllInbound** predefinida, não é necessária qualquer regra adicional para os grupos de segurança de aplicações *AsgLogic* ou *AsgDb*.

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Permitir |

## <a name="deny-database-all"></a>Deny-Database-All

Uma vez que a regra de segurança predefinida **AllowVNetInBound** permite todas as comunicações entre recursos na mesma rede virtual, esta regra é necessária para negar o tráfego de todos os recursos.

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Qualquer | Negar |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Esta regra permite o tráfego do grupo de segurança de aplicações *AsgLogic* para o grupo de segurança de aplicações *AsgDb*. A prioridade desta regra é mais alta do que a da regra *Deny-Database-All*. Como resultado, esta regra é processada antes da regra *Deny-Database-All*, de modo a que o tráfego do grupo de segurança de aplicações *AsgLogic* seja permitido, ao passo que o restante tráfego é bloqueado.

|Prioridade|Origem|Portas de origem| Destino | Portas de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Permitir |

As regras que especifiquem grupos de segurança de aplicações como a origem ou o destino só são aplicadas às interfaces de rede que são membros do grupo de segurança de aplicações. Se a interface de rede não for membro de um grupo de segurança de aplicações, a regra não é aplicada à interface de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

Os grupos de segurança de aplicação têm as seguintes restrições:

-    Há limites ao número de grupos de segurança de rede que podem ser incluídos numa subscrição, bem como outros limites relacionados com os grupos de segurança de aplicações. Para obter mais detalhes, veja [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Pode especificar um grupo de segurança de aplicações como origem e destino numa regra de segurança. Não pode especificar vários grupos de segurança de aplicações na origem ou no destino.
- Todas as interfaces de rede atribuídas a um grupo de segurança de aplicações têm de existir na mesma rede virtual em que se encontra a primeira interface de rede atribuída ao grupo de segurança da aplicação. Por exemplo, se a primeira interface de rede atribuída a um grupo de segurança de aplicações denominado *AsgWeb* estiver na rede virtual denominada *VNet1*, todas interfaces de rede subsequentes atribuídas a *ASGWeb* têm de existir em *VNet1*. Não é possível adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança de aplicação.
- Se especificar um grupo de segurança de aplicação como a origem e o destino numa regra de segurança, as interfaces de rede em ambos os grupos de segurança de aplicações têm de existir na mesma rede virtual. Por exemplo, se *AsgLogic* contivesse interfaces de rede de *VNet1* e *AsgDb* contivesse interfaces de rede de *VNet2*, não poderia atribuir *AsgLogic* como a origem e *AsgDb* como o destino numa regra. Todas as interfaces de rede para os grupos de segurança de origem e de destino têm de estar na mesma rede virtual.

> [!TIP]
> Para minimizar o número de regras de segurança de que precisa, bem como a necessidade de as alterar, planei os grupos de segurança de aplicações de que precisa e crie regras com etiquetas de serviço ou grupos de segurança de aplicações em vez de endereços IP individuais ou intervalos de endereços IP, sempre que possível.

## <a name="next-steps"></a>Passos seguintes

* Leia o artigo [Create a network security group](tutorial-filter-network-traffic.md) (Criar um grupo de segurança de rede).
