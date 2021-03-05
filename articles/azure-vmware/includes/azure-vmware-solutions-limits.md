---
title: Limites de solução Azure VMware
description: Limitações da Solução Azure VMware.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194047"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela seguinte descreve os limites máximos para a Solução VMware Azure.

| **Recurso** | **Limite** |
| :-- | :-- |
| Aglomerados por nuvem privada | 4 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nódes por nuvem privada | 64 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos de site HCX | 3 com edição avançada, 10 com edição Enterprise |
| AVS ExpressRoute max ligados SDDCs | 4 |
| Velocidade portuária AVS ExpressRoute | 10 Gbps | 
| IPs públicos expostos via vWAN | 100 |
| vSAN limites de capacidade | 75% do total utilizável (mantenha 25% disponível para SLA)  |

Para outros limites específicos VMware, utilize a [ferramenta máxima de configuração VMware!](https://configmax.vmware.com/)
