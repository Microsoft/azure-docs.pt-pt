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
ms.openlocfilehash: 3e66bf61caf786473d89eab9a6567bb05aff0d19
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457490"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede - os seguintes limites só se aplicam para recursos geridos através de rede do Azure Resource Manager **do Azure Resource Manager** por região por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Aumentámos recentemente todos os limites predefinidos para os limites máximos. Se não houver nenhuma coluna de limite máximo, o recurso não tem limites ajustável. Se tivesse estes limites aumentados em suporte no passado e não vir os limites atualizados nas tabelas seguintes, [abra um pedido de suporte do cliente online sem custos](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Limite de máximo/predefinido | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3\.000 |
| Peerings de rede virtual por rede virtual |500 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65,536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500,000 |
| Placas de interface de rede |65,536 |
| Grupos de Segurança de Rede |5,000 |
| Regras do NSG por NSG |1,000 |
| Endereços IP e intervalos especificados para a origem ou destino num grupo de segurança |4,000 |
| Grupos de segurança de aplicações |3\.000 |
| Grupos de segurança de aplicação por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança de aplicações |4,000 |
| Grupos de segurança de aplicações que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo utilizador |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |
| Certificados de raiz de ponto a site por Gateway de VPN do Azure |20 |
| Toca de rede virtual |100 |
| Configurações de TOQUE de interface de rede por TOQUE de rede virtual |100 |

#### <a name="publicip-address"></a>Limites de endereço IP públicos
| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmico | 1000 para o básico. |Contacte o suporte. |
| Endereços IP públicos - estáticos | 1000 para o básico. |Contacte o suporte. |
| Endereços IP públicos - estáticos | 200 para o Standard.|Contacte o suporte. |
| Comprimento do prefixo IP público | /28 | Contacte o suporte. |

#### <a name="load-balancer"></a>Limites do Balanceador de carga
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md).

| Resource | Limite de máximo/predefinido |
| --- | --- |
| Balanceadores de carga | 1,000 | 
| Regras por recurso, Basic | 250 |
| Regras por recurso, Standard | 1,500 | 
| Regras por configuração de IP | 299 |
| Regras por NIC | 300 |
| Configurações de IP de front-end, Basic | 200 |
| Configurações de IP de front-end, Standard | 600 |
| Conjunto de back-end, Basic | 100, único conjunto de disponibilidade |
| Conjunto de back-end, Standard | 1000, única rede virtual |
| Recursos de back-end por Balanceador de carga Standard<sup>1</sup> | 150 |
| Portas de elevada disponibilidade, Standard | 1 por interno front-end |

<sup>1</sup>o limite é de até 150 recursos, em qualquer combinação de recursos da máquina virtual autónoma, recursos e recursos do conjunto de dimensionamento de máquinas virtuais do conjunto de disponibilidade.

#### <a name="virtual-networking-limits-classic"></a>Os seguintes limites só se aplicam para recursos geridos através de rede a **clássico** modelo de implementação por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md).

| Resource | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de rede local |20 |Contacte o suporte. |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4,096 |4,096 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500 000, até 1.000.000 para dois ou mais NICs. |500 000, até 1.000.000 para dois ou mais NICs. |
| Grupos de segurança de rede (NSGs) |200 |200 |
| Regras do NSG por NSG |1,000 |1,000 |
| Tabelas de rotas definidas pelo utilizador |200 |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmico) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implementação |5 |Contacte o suporte |
| VIP privado (Balanceamento de carga interno) por implementação |1 |1 |
| Listas de controlo de acesso de ponto final (ACLs) |50 |50 |
