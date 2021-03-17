---
title: Limites de solução Azure VMware
description: Limitações da Solução Azure VMware.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622307"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela seguinte descreve os limites máximos para a Solução VMware Azure.

| **Recurso** | **Limite** |
| :-- | :-- |
| Aglomerados por nuvem privada | 12 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nódes por nuvem privada | 64 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos de site HCX | 3 com edição avançada, 10 com edição Enterprise |
| AVS ExpressRoute max ligados SDDCs | 4 |
| Velocidade portuária AVS ExpressRoute | 10 Gbps<br />O portal de rede virtual utilizado determina a largura de banda real. Para mais detalhes, consulte [sobre as portas de rede virtual ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| IPs públicos expostos via vWAN | 100 |
| vSAN limites de capacidade | 75% do total utilizável (mantenha 25% disponível para SLA)  |

Para outros limites específicos VMware, utilize a [ferramenta máxima de configuração VMware!](https://configmax.vmware.com/)
