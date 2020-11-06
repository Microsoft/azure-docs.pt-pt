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
ms.openlocfilehash: 0d5c9c4a0191c6d5effd8f6067cb6602ed239125
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329471"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Limites de rede - Gestor de Recursos Azure
Os seguintes limites aplicam-se apenas aos recursos de rede geridos através **do Azure Resource Manager** por região por subscrição. Saiba como ver o [uso do seu recurso atual contra os limites de subscrição.](../articles/networking/check-usage-against-limits.md)

> [!NOTE]
> Recentemente, aumentámos todos os limites de incumprimento para os seus limites máximos. Se não houver uma coluna limite máximo, o recurso não tem limites ajustáveis. Se tiver estes limites aumentados pelo apoio no passado e não vir limites atualizados nas tabelas seguintes, [abra gratuitamente um pedido de apoio ao cliente online.](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Recurso | Limite | 
| --- | --- |
| Redes virtuais |1,000 |
| Sub-redes por rede virtual |3.000 |
| Peerings de rede virtual por rede virtual |500 |
| [Gateways de rede virtual (gateways VPN) por rede virtual](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Gateways de rede virtual (gateways ExpressRoute) por rede virtual](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servidores DNS por rede virtual |20 |
| Endereços IP privados por rede virtual |65 536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Endereços IP públicos por interface de rede |256 |
| Endereços IP públicos por máquina virtual |256 |
| [Fluxos simultâneos de TCP ou UDP por NIC de uma máquina virtual ou instância de função](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Cartões de interface de rede |65 536 |
| Grupos de Segurança de Rede |5000 |
| Regras do NSG por NSG |1,000 |
| Endereços IP e intervalos especificados para origem ou destino num grupo de segurança |4000 |
| Grupos de segurança de aplicações |3.000 |
| Grupos de segurança de aplicações por configuração IP, por NIC |20 |
| Configurações IP por grupo de segurança de aplicação |4000 |
| Grupos de segurança de aplicações que podem ser especificados dentro de todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas de rota definidas pelo utilizador |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |
| Certificados de raiz ponto-a-local por Gateway Azure VPN |20 |
| TAPs de rede virtual |100 |
| Configurações de interface de rede TAP por rede virtual TAP |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limites de endereços IP públicos
| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos<sup>1</sup> | 10 para o Básico. | Contacte o suporte. |
| Endereços IP públicos estáticos<sup>1</sup> | 10 para o Básico. | Contacte o suporte. |
| Endereços IP públicos<sup>padrão 1</sup> | 10 | Contacte o suporte. |
| [Endereços IP públicos por Grupo de Recursos](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Contacte o suporte. | 
| Prefixos de IP Públicos | limitado pelo número de IPs públicos padrão numa subscrição | Contacte o suporte. |
| Comprimento do prefixo IP público | /28 | Contacte o suporte. |

<sup>1</sup> Os limites predefinidos para endereços IP públicos variam por tipo de categoria de oferta, tais como Teste Gratuito, Pay-As-You-Go, CSP. Por exemplo, o padrão para subscrições do Enterprise Agreement é 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limites do balançador de carga
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como ver o [uso do seu recurso atual contra os limites de subscrição.](../articles/networking/check-usage-against-limits.md)

**Balanceador de carga padrão**

| Recurso                                | Limite         |
|-----------------------------------------|-------------------------------|
| Balanceadores de carga                          | 1,000                         |
| Regras por recurso                      | 1500                         |
| Regras por NIC (em todos os IPs de um NIC) | 300                           |
| Configurações IP frontend              | 600                           |
| Tamanho da piscina backend                       | 1.000 configurações IP, rede virtual única |
| Recursos de backend por Balanceador de Carga <sup> 1<sup> | 150                   |
| Portas de alta disponibilidade                 | 1 por frontend interno       |
| Regras de saída por Balanceador de Carga        | 600                           |
| Balançadores de carga por VM                   | 2 (1 Público e 1 interno)   |

<sup>1</sup> O limite é de até 150 recursos, em qualquer combinação de recursos de máquinas virtuais autónomos, recursos definidos de disponibilidade e grupos de colocação de escala de máquina virtual.

**Balanceador de carga básico**

| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Balanceadores de carga                          | 1,000                        |
| Regras por recurso                      | 250                          |
| Regras por NIC (em todos os IPs de um NIC) | 300                          |
| Configurações IP frontend              | 200                          |
| Tamanho da piscina backend                       | 300 configurações IP, conjunto de disponibilidade única |
| Conjuntos de disponibilidade por balanceador de carga     | 1                            |
| Balançadores de carga por VM                   | 2 (1 Público e 1 interno)  |

<a name="virtual-networking-limits-classic"></a>Os seguintes limites aplicam-se apenas aos recursos de networking geridos através do modelo **clássico** de implementação por subscrição. Saiba como ver o [uso do seu recurso atual contra os limites de subscrição.](../articles/networking/check-usage-against-limits.md)

| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |100 |100 |
| Sites de rede local |20 |50 |
| Servidores DNS por rede virtual |20 |20 |
| Endereços IP privados por rede virtual |4,096 |4,096 |
| Fluxos simultâneos de TCP ou UDP por NIC de uma máquina virtual ou instância de função |500.000, até 1.000.000 para dois ou mais NICs. |500.000, até 1.000.000 para dois ou mais NICs. |
| Grupos de Segurança de Rede (NSGs) |200 |200 |
| Regras do NSG por NSG |200 |1,000 |
| Tabelas de rota definidas pelo utilizador |200 |200 |
| Rotas definidas pelo utilizador por tabela de rotas |400 |400 |
| Endereços IP públicos (dinâmico) |500 |500 |
| Endereços IP públicos reservados |500 |500 |
| IP público por implantação |5 |Contactar o suporte |
| IP privado (equilíbrio interno da carga) por implantação |1 |1 |
| Listas de controlo de acesso ao ponto final (ACLs) |50 |50 |
