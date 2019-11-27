---
title: incluir ficheiro
description: incluir ficheiro
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485514"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede-Azure Resource Manager os seguintes limites se aplicam somente a recursos de rede gerenciados por meio de **Azure Resource Manager** por região por assinatura. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Recentemente, aumentamos todos os limites padrão para seus limites máximos. Se não houver uma coluna de limite máximo, o recurso não terá limites ajustáveis. Se você tivesse esses limites aumentados pelo suporte no passado e não vir os limites atualizados nas tabelas a seguir, [abra uma solicitação de atendimento ao cliente online sem encargos](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Limite máximo/padrão | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3\.000 |
| Emparelhamentos de rede virtual por rede virtual |500 |
| [Gateways de rede virtual (gateways de VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65.536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500,000 |
| Placas de interface de rede |65.536 |
| Grupos de Segurança de Rede |5,000 |
| Regras do NSG por NSG |1,000 |
| Endereços IP e intervalos especificados para origem ou destino em um grupo de segurança |4,000 |
| Grupos de segurança de aplicações |3\.000 |
| Grupos de segurança de aplicativo por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança de aplicativo |4,000 |
| Grupos de segurança de aplicativo que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo usuário |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |
| Certificados raiz ponto a site por gateway de VPN do Azure |20 |
| Toques de rede virtual |100 |
| Configurações de toque de interface de rede por toque de rede virtual |100 |

#### <a name="publicip-address"></a>Limites de endereço IP público
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos-dinâmicos | 1\.000 para Basic. |Contacte o suporte. |
| Endereços IP públicos-estáticos | 1\.000 para Basic. |Contacte o suporte. |
| Endereços IP públicos-estáticos | 1\.000 para Standard.|Contacte o suporte. |
| Comprimento do prefixo IP público | /28 | Contacte o suporte. |

#### <a name="load-balancer"></a>Limites de balanceador de carga
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

| Resource | Limite máximo/padrão |
| --- | --- |
| Balanceadores de carga | 1,000 | 
| Regras por recurso, básico | 250 |
| Regras por recurso, Standard | 1,500 | 
| Regras por configuração de IP | 299 |
| Regras por NIC | 300 |
| Configurações de IP de front-end, básicas | 200 |
| Configurações de IP de front-end, Standard | 600 |
| Pool de back-ends, básico | 100, conjunto de disponibilidade único |
| Pool de back-ends, Standard | 1\.000, rede virtual única |
| Recursos de back-end por balanceador de carga, Standard<sup>1</sup> | 150 |
| Portas de alta disponibilidade, Standard | 1 por front-end interno |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquina virtual autônoma, recursos de conjunto de disponibilidade e recursos de conjunto de escala de máquina virtual.

#### <a name="virtual-networking-limits-classic"></a>Os limites a seguir se aplicam somente a recursos de rede gerenciados por meio do modelo de implantação **clássico** por assinatura. Saiba como [Exibir o uso atual de recursos em relação aos limites de sua assinatura](../articles/networking/check-usage-against-limits.md).

| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de rede local |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4\.096 |4\.096 |
| Fluxos TCP ou UDP simultâneos por NIC de uma máquina virtual ou instância de função |500.000, até 1 milhão para duas ou mais NICs. |500.000, até 1 milhão para duas ou mais NICs. |
| NSGs (grupos de segurança de rede) |200 |200 |
| Regras do NSG por NSG |1,000 |1,000 |
| Tabelas de rotas definidas pelo usuário |200 |200 |
| Rotas definidas pelo usuário por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmico) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implementação |5 |Contactar o suporte |
| VIP privado (balanceamento de carga interno) por implantação |1 |1 |
| Listas de controle de acesso de ponto de extremidade (ACLs) |50 |50 |
