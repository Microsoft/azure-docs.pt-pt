---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184406"
---
### <a name="virtual-networks"></a>Redes Virtuais

|  |  |
|---------|---------|
| [SKUs de Gateways de Aplicação permitidos](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Requer que os gateways de aplicação utilizem um SKU aprovado. Especifica uma matriz de SKUs aprovados. |
| [SKUs de Gateway de vNet permitidos](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Requer que os gateways de rede virtual utilizem um SKU aprovado. Especifica uma matriz de SKUs aprovados. |
| [SKUs de Balanceadores de Carga permitidos](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Requer que os balanceadores de carga de rede virtual utilizem um SKU aprovado. Especifica uma matriz de SKUs aprovados. |
| [Não existe nenhum peering de rede para a rede do ExpressRoute](../articles/governance/policy/samples/no-peering-express-route-network.md) | Proíbe um peering de rede de ser associado a uma rede num grupo de recursos especificado. Utilize para impedir a ligação com a infraestrutura de rede gerida central. Especifique o nome do grupo de recursos para impedir a associação. |
| [Nenhuma Tabela de Rotas Definida pelo Utilizador](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Proíbe a implementação de redes virtuais numa tabela de rotas definida pelo utilizador. |
| [NSG X em cada sub-rede](../articles/governance/policy/samples/nsg-on-subnet.md) | Exige que um grupo de segurança de rede específico seja utilizado em cada sub-rede virtual. Especifica o ID do grupo de segurança de rede a utilizar. |
| [Utilizar sub-rede aprovada para interfaces de rede de VM](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Exige que as interfaces de rede utilizem uma sub-rede aprovada. Especifica o ID da sub-rede aprovada. |
| [Utilizar vNet aprovada para interfaces de rede de VM](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Exige que as interfaces de rede utilizem uma rede virtual aprovada. Especifica o ID da rede virtual aprovada. |