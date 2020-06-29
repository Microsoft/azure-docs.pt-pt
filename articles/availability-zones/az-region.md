---
title: Regiões que suportam Zonas de Disponibilidade em Azure
description: Para criar aplicações altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade fornecem locais fisicamente separados que pode utilizar para executar os seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: fc9c1cc2304c20a37031bdc274c0e56d77f7dbf3
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482365"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiões que suportam Zonas de Disponibilidade em Azure

Availability Zones é uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. Para obter mais informações sobre Zonas de Disponibilidade, consulte [Regiões e Zonas de Disponibilidade em Azure.](az-overview.md)

## <a name="services-support-by-region"></a>Apoio de serviços por região

Esta secção lista os serviços e regiões do Azure que suportam Zonas de Disponibilidade.

Os serviços disponíveis em cada região, juntamente com o roteiro de disponibilidade, podem ser encontrados em [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/)

|                                 |Américas |              |           |           | Europa |              |          |              | Ásia-Pacífico |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |E.U.A. Central|E.U.A. Leste|E.U.A. Leste 2|E.U.A.Oeste 2|França Central|Europa do Norte|Sul do Reino Unido|Europa Ocidental|Leste do Japão|Ásia Sudeste|Leste da Austrália|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Ambientes de Serviço de Aplicações Azure ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Armazenamento redundante de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Redes**                     |            |              |           |           |                |              |          |             |            |                |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Balanceador de carga padrão     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Gateway de VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Gateway do ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Gateway de aplicação (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bases de dados**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Base de Dados SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;(Pré-visualização) | &#10003;       | &#10003;(Pré-visualização)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Cache do Azure para Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    |   |
| **Análise**                       |            |              |           |           |                |              |          |             |            |                |                |
| Hubs de Eventos                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |  &#10003;              |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |                |
| Autocarro de serviço (Premium Tier Only) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |  &#10003;              |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identidade**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |


## <a name="next-steps"></a>Passos seguintes

- [Regiões e Zonas de Disponibilidade em Azure](az-overview.md)
