---
title: incluir ficheiro
description: incluir ficheiro
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 02/07/2019
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: d43424e1377f7fa9cb8b272cc4ffbf3e5323f2ba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57457896"
---
<a name="virtual-networking-limits-classic"></a>Os limites seguintes só se aplicam a recursos de rede geridos através do modelo de implementação clássica por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md).

| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |50 |100 |
| Sites de rede local |20 |contactar o suporte |
| Servidores de DNS por rede virtual |20 |20 |
| Endereços de IP privados por rede virtual |4096 |4096 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500 mil, até 1000 K para dois ou mais NICs |500 mil, até 1000 K para dois ou mais NICs |
| Grupos de Segurança de Rede (NSG) |100 |200 |
| Regras do NSG por NSG |200 |1000 |
| Tabelas da rota definida pelo utilizador |100 |200 |
| Rotas definidas pelo utilizador por tabela de rota |100 |400 |
| Endereços IP públicos (dinâmico) |5 |contactar o suporte |
| Endereços IP públicos reservados |20 |contactar o suporte |
| VIP público por implementação |5 |contactar o suporte |
| VIP privado (ILB) por implementação |1 |1 |
| Listas de Controlo de Acesso de Ponto Final (ACLs) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de Rede - Azure Resource Manager
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Aumentámos recentemente todos os limites predefinidos para os limites máximos. Se não existir nenhuma **limite máximo** coluna, em seguida, o recurso não tem limites ajustável. Se tenha tido a estes limites aumentados em suporte no passado e não vir os limites atualizados conforme mostrado a seguir, pode [abra um pedido de suporte do cliente online sem custos](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Recurso | Limite predefinido | 
| --- | --- |
| Redes virtuais |1000 |
| Sub-redes por rede virtual |3000 |
| Peerings de rede virtual por rede Virtual |100 |
| Servidores de DNS por rede virtual |20 |
| Endereços de IP privados por rede virtual |65536 |
| Endereços IP privados por interface de rede |256 |
| Endereços IP privados por máquina virtual |256 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500 K |
| Interfaces de Rede (NIC) |65536 |
| Grupos de Segurança de Rede (NSG) |5000 |
| Regras do NSG por NSG |1000 |
| Endereços IP e intervalos especificados para a origem ou destino num grupo de segurança |4000 |
| Grupos de segurança de aplicações |3000 |
| Grupos de segurança de aplicação por configuração de IP, por NIC |20 |
| Configurações de IP por grupo de segurança de aplicações |4000 |
| Grupos de segurança de aplicações que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |100 |
| Tabelas da rota definida pelo utilizador |200 |
| Rotas definidas pelo utilizador por tabela de rota |400 |
| Certificados de Raiz do Ponto-a-Site por Gateway de VPN |20 |
| Toca de rede virtual |100 |
| Configurações de TOQUE de interface de rede por TOQUE de rede virtual |100 |

#### <a name="publicip-address"></a>Limites de endereço IP públicos
| Recurso | Limite predefinido | Limite Máximo |
| --- | --- | --- |
| Endereços IP públicos - dinâmico |1000 (básico) |contactar o suporte |
| Endereços IP públicos - estáticos |200 (básico) |contactar o suporte |
| Endereços IP públicos - estáticos |(Standard) 200 |contactar o suporte |
| Tamanho de prefixo de IP público (pré-visualização) | /28 | /28 |

#### <a name="load-balancer"></a>Limites do Balanceador de carga
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição. Saiba como [ver a utilização de recursos atual em relação a seus limites de subscrição](../articles/networking/check-usage-against-limits.md)

| Recurso | Limite predefinido |
| --- | --- |
| Balanceador de Carga | 1000 | 
| Regras por recurso, Basic | 250 |
| Regras por recurso, Standard | 1500 | 
| Regras por configuração de IP | 299 |
| Regras por NIC | 500 |
| Configurações de IP de front-end, Basic | 200 |
| Configurações de IP de front-end, Standard | 600 |
| Conjunto de back-end, Basic | Conjunto de disponibilidade de único de 100, |
| Conjunto de back-end, Standard | 1000, único VNet |
| Recursos de back-end por Balanceador de carga Standard * | 150 |
| HA portas, Standard | 1 por front-end interno |

* * Até 150 recursos, qualquer combinação de recursos de máquina virtual autónoma, recursos de conjuntos de disponibilidade, e recursos do conjunto de dimensionamento de máquina virtual.

