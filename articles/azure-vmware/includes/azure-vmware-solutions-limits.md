---
title: Limites de solução Azure VMware
description: Limitações da Solução Azure VMware.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582285"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela seguinte descreve os limites máximos para a Solução VMware Azure.

| **Recurso** | **Limite** |
| :-- | :-- |
| Aglomerados por nuvem privada | 12 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nódes por nuvem privada | 96 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos de site HCX | 3 com edição avançada, 10 com edição Enterprise |
| AVS ExpressRoute max ligados SDDCs | 4 |
| Velocidade portuária AVS ExpressRoute | 10 Gbps<br />O portal de rede virtual utilizado determina a largura de banda real. Para mais detalhes, consulte [sobre as portas de rede virtual ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| IPs públicos expostos via vWAN | 100 |
| vSAN limites de capacidade | 75% do total utilizável (mantenha 25% disponível para SLA)  |

Para outros limites específicos VMware, utilize a [ferramenta máxima de configuração VMware!](https://configmax.vmware.com/)
