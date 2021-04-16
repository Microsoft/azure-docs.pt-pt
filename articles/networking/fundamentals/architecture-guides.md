---
title: Documentação de arquitetura Azure Networking
description: Conheça a documentação de arquitetura de referência disponível para serviços de networking Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484800"
---
# <a name="azure-networking-architecture-documentation"></a>Documentação de arquitetura Azure Networking

Este artigo fornece informações sobre guias de arquitetura que podem ajudá-lo a explorar os diferentes serviços de networking em Azure disponíveis para a construção das suas aplicações.

## <a name="networking-overview"></a>Descrição Geral do funcionamento em rede

A tabela seguinte inclui artigos que fornecem uma visão geral de um datacenter virtual e um hub e falou topologia em Azure.

|Título |Descrição  |
|---------|---------|
|[Datacenters Virtuais](/azure/architecture/vdc/networking-virtual-datacenter)   | Fornece uma perspetiva de networking de um datacenter virtual em Azure.       |
|[Topologia hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Fornece uma visão geral do hub e falou topologia de rede em Azure juntamente com informações sobre limites de subscrição e múltiplos hubs.          |

## <a name="connect-to-azure-resources"></a>Ligar aos recursos da Azure

A tabela seguinte inclui artigos sobre serviços de rede Azure que fornecem conectividade entre os recursos Azure, conectividade de uma rede no local para recursos Azure, e sucursal para a conectividade de filial em Azure.

|Título |Descrição  |
|---------|---------|
|[Adicionar espaços de endereço IP a redes virtuais espreitadas](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Fornece scripts que ajudam a adicionar espaços de endereço IP a redes virtuais espreitadas.        |
|[Ligar servidores autónomos ao utilizar o Adaptador de Rede do Azure](/azure/architecture/hybrid/azure-network-adapter)   | Mostra como ligar um servidor autónomo no local às redes virtuais do Microsoft Azure utilizando o Adaptador de Rede Azure que implementa através do Windows Admin Center.        |
|[Escolha entre peering de rede virtual e gateways de VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Compara duas formas de ligar redes virtuais no Azure: o espreitamento de rede virtual e os gateways VPN.        |
|[Ligar uma rede no local ao Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Compara opções para ligar uma rede no local a uma Rede Virtual Azure (VNet). Para cada opção, está disponível uma arquitetura de referência mais detalhada.        |
|[Arquitetura de conectividade SD-WAN com Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Descreve as diferentes opções de conectividade para interligar um SOFTWARE privado DEFINIDO WAN (SD-WAN) com Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Implementar aplicações altamente disponíveis

A tabela que se segue inclui artigos que descrevem como implementar as suas aplicações para alta disponibilidade utilizando uma combinação de serviços de rede Azure.

|Título |Descrição  |
|---------|---------|
|[Aplicação multi-região n-tier](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Descreve uma aplicação de nível N multi-região que usa o Gestor de Tráfego para encaminhar os pedidos de entrada para uma região primária e se essa região ficar indisponível, o Gestor de Tráfego falha na região secundária.      |
| [SaaS multi-inquilino no Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Usa uma solução multi-inquilino que inclui uma combinação de Porta Frontal e Gateway de aplicação.  A Porta frontal ajuda a carregar o tráfego de equilíbrio entre regiões e rotas de Gateway de aplicação e o tráfego de cargas internamente na aplicação aos vários serviços que satisfazem as necessidades do negócio dos clientes.  |
| [Aplicação web multi-nível construída para alta disponibilidade e recuperação de desastres ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Implementa aplicações resilientes de vários níveis construídas para alta disponibilidade e recuperação de desastres. Se a região primária ficar indisponível, o Gestor de Tráfego falha na região secundária.  |
|[IaaS: Aplicação web com base de dados relacional](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Descreve como usar recursos espalhados por várias zonas para fornecer uma arquitetura de alta disponibilidade para hospedar uma aplicação web de Infraestruturas como um Serviço (IaaS) e base de dados do SQL Server.     |
|[Partilhar a localização em tempo real com serviços do Azure sem servidor de baixo custo](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Usa a Porta Frontal Azure para fornecer uma maior disponibilidade para as suas aplicações do que a implantação numa única região. Se uma falha regional afetar a região primária, pode utilizar o Front Door para efetuar a ativação pós-falha para a região secundária.      |
|[Aplicações virtuais de rede de elevada disponibilidade](/azure/architecture/reference-architectures/dmz/nva-ha)     | Mostra como implantar um conjunto de aparelhos virtuais de rede (NVAs) para uma elevada disponibilidade em Azure.        |
|[Equilíbrio de carga multi-região com Gestor de Tráfego e Gateway de Aplicação](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Descreve como implementar aplicações resilientes de vários níveis em várias regiões do Azure, a fim de alcançar a disponibilidade e uma robusta infraestrutura de recuperação de desastres.        |

## <a name="secure-your-network-resources"></a>Proteja os seus recursos de rede

A tabela seguinte inclui artigos que descrevem como proteger os seus recursos de rede utilizando os serviços de rede Azure.

|Título |Descrição  |
|---------|---------|
|[Melhores práticas de segurança de rede](../../security/fundamentals/network-best-practices.md) |Discute uma coleção de boas práticas da Azure para melhorar a segurança da sua rede.         |
[Guia de Arquitetura do Azure Firewall](/azure/architecture/example-scenario/firewalls/) | Fornece uma abordagem estruturada para a conceção de firewalls altamente disponíveis em Azure usando aparelhos virtuais de terceiros.        |
|[Implementar uma rede híbrida segura](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Descreve uma arquitetura que implementa um DMZ, também chamado de rede de perímetro, entre a rede no local e uma rede virtual Azure. Todo o tráfego de entrada e saída passa pela Firewall Azure.        |
|[Proteção e governação de cargas de trabalho com segmentação de nível de rede](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Descreve os três padrões comuns usados para organizar cargas de trabalho em Azure a partir de uma perspetiva de networking.   Cada um destes padrões proporciona um tipo diferente de isolamento e conectividade.      |
|[Firewall e Gateway de Aplicação para redes virtuais](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Descreve os serviços de segurança da Rede Virtual Azure, como o Azure Firewall e o Azure Application Gateway, quando usar cada serviço e opções de design de rede que combinam ambos.      |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md)
