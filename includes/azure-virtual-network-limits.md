---
title: incluir ficheiro
description: incluir ficheiro
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 511354633b9f88f3d6cd2e2170ce3b7ca1f4ecdb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82095910"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limites de networking - Gestor de Recursos Azure
Os seguintes limites aplicam-se apenas aos recursos de networking geridos através **do Gestor de Recursos Azure** por região por subscrição. Saiba como [visualizar o seu uso atual de recursos contra os seus limites](../articles/networking/check-usage-against-limits.md)de subscrição .

> [!NOTE]
> Recentemente aumentámos todos os limites de incumprimento para os seus limites máximos. Se não houver uma coluna de limite máximo, o recurso não tem limites ajustáveis. Se teve estes limites aumentados pelo suporte no passado e não vê limites atualizados nas tabelas seguintes, abra um pedido de apoio ao [cliente online sem custos](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Recurso | Limite | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3.000 |
| Peerings de rede virtual por rede virtual |500 |
| [Gateways de rede virtuais (gateways VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways de rede virtual (gateways ExpressRoute) por rede virtual](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65 536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos simultâneos de TCP ou UDP por NIC de uma máquina virtual ou exemplo de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Cartões de interface de rede |65 536 |
| Grupos de Segurança de Rede |5000 |
| Regras do NSG por NSG |1,000 |
| Endereços IP e gamas especificadas para fonte ou destino num grupo de segurança |4000 |
| Grupos de segurança de aplicações |3.000 |
| Grupos de segurança de aplicações por configuração IP, por NIC |20 |
| Configurações IP por grupo de segurança de aplicações |4000 |
| Grupos de segurança de aplicações que podem ser especificados dentro de todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rotas definidas pelo utilizador |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |
| Certificados de raiz ponto-a-local por Gateway VPN Azure |20 |
| TAPs de rede virtual |100 |
| Configurações TAP de interface de rede por rede virtual TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limites de endereços IP públicos
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos<sup>1</sup> | 10 para o Básico. | Contacte o suporte. |
| Endereços IP públicos estáticos<sup>1</sup> | 10 para o Básico. | Contacte o suporte. |
| Endereços IP públicos padrão<sup>1</sup> | 10 | Contacte o suporte. |
| Prefixos de IP Públicos | limitado pelo número de IPs Públicos Standard numa subscrição | Contacte o suporte. |
| Comprimento de prefixo ip público | /28 | Contacte o suporte. |

<sup>1</sup> Os limites de predefinição para endereços IP públicos variam por tipo de categoria de oferta, tais como Free Trial, Pay-As-You-Go, CSP. Por exemplo, o padrão para subscrições do Acordo Empresarial é de 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limites de equilíbrio de carga
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como [visualizar o seu uso atual de recursos contra os seus limites](../articles/networking/check-usage-against-limits.md)de subscrição .

**Balanceador de Carga Padrão**

| Recurso                                | Limite         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras por recurso                      | 1500                         |
| Regras por NIC (em todos os IPs num NIC) | 300                           |
| Configurações IP frontend              | 600                           |
| Tamanho da piscina de backend                       | 1.000 configurações IP, rede virtual única |
| Recursos de backend <sup>por Load Balancer 1<sup> | 150                   |
| Portas de alta disponibilidade                 | 1 por frontend interno       |
| Regras de saída por Balancer de Carga        | 600                           |
| [Tempo limite de inatividade da TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minutos/30 minutos          |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquinas virtuais autónomas, recursos definidos de disponibilidade e grupos de colocação de escala de máquina virtual.

**Balanceador de Carga Básico**

| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs num NIC) | 300                          |
| Configurações IP frontend              | 200                          |
| Tamanho da piscina de backend                       | 300 configurações IP, conjunto de disponibilidade única |
| Conjuntos de disponibilidade por Balancer de carga     | 150                          |

<a name="virtual-networking-limits-classic"></a>Os seguintes limites aplicam-se apenas aos recursos de networking geridos através do modelo de implantação **clássico** por subscrição. Saiba como [visualizar o seu uso atual de recursos contra os seus limites](../articles/networking/check-usage-against-limits.md)de subscrição .

| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de rede local |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4,096 |4,096 |
| Fluxos simultâneos de TCP ou UDP por NIC de uma máquina virtual ou exemplo de função |500.000, até 1.000.000 para dois ou mais NICs. |500.000, até 1.000.000 para dois ou mais NICs. |
| Grupos de Segurança de Rede (NSGs) |200 |200 |
| Regras do NSG por NSG |1,000 |1,000 |
| Tabelas de rotas definidas pelo utilizador |200 |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmico) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| VIP público por implementação |5 |Contactar o suporte |
| VIP privado (equilíbrio interno de carga) por implantação |1 |1 |
| Listas de controlo de acesso de ponto final (ACLs) |50 |50 |
