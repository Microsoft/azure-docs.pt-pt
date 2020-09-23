---
title: Regiões que suportam Zonas de Disponibilidade em Azure
description: Para criar aplicações altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode utilizar para executar os seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 09/18/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: d7a158d91295aedc14f1f913ae152c496066fab5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891685"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiões que suportam Zonas de Disponibilidade em Azure

Availability Zones é uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. Para obter mais informações sobre Zonas de Disponibilidade, consulte [Regiões e Zonas de Disponibilidade em Azure.](az-overview.md)

Esta secção lista os serviços e regiões do Azure que suportam Zonas de Disponibilidade.

Os serviços disponíveis em cada região, juntamente com o roteiro de disponibilidade, podem ser encontrados em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)

## <a name="americas"></a>Américas

| Serviço | E.U.A. Central | E.U.A. Leste | E.U.A. Leste 2 | E.U.A. Oeste 2 | Canadá Central
| --- | :---: | :---: | :---: | :---: | :---: |
| **Computação** |  |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Ambientes de Serviço de Aplicações Azure ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento redundante de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Redes** |  |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Balanceador de carga padrão             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicação (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Base de Dados SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: (Pré-visualização) | :heavy_check_mark: |
| Cache do Azure para Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |
| Autocarro de serviço (Premium Tier Only)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identidade** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |   |

## <a name="europe"></a>Europa

| Serviço | França Central | Europa do Norte | Sul do Reino Unido | Europa Ocidental |
| --- | :---: | :---: | :---: | :---: |
| **Computação** |  |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Ambientes de Serviço de Aplicações Azure ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento redundante de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Redes** |  |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Balanceador de carga padrão             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicação (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Base de Dados SQL                       | :heavy_check_mark: | :heavy_check_mark: (Pré-visualização) | :heavy_check_mark: | :heavy_check_mark: |
| Cache do Azure para Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração**  |  |  |  |  |
| Autocarro de serviço (Premium Tier Only)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identidade** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Ásia-Pacífico

| Serviço | Leste do Japão | Sudeste Asiático | Leste da Austrália |
| --- | :---: | :---: | :---: |
| **Computação** |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Ambientes de Serviço de Aplicações Azure ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento redundante de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Redes** |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Balanceador de carga padrão             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicação (V2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bases de dados** |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Base de Dados SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Cache do Azure para Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |
| Autocarro de serviço (Premium Tier Only)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identidade** |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Outro

O Azure também oferece apoio às Zonas de Disponibilidade nas seguintes regiões:

- US Gov - Virginia
- África do Sul Norte
- E.U.A. Centro-Sul

Para saber mais sobre o suporte de Zonas de Disponibilidade nestas três regiões, contacte o seu representante de vendas ou cliente da Microsoft ou abra um pedido de suporte técnico.

## <a name="next-steps"></a>Passos seguintes

- [Regiões e Zonas de Disponibilidade no Azure](az-overview.md)
