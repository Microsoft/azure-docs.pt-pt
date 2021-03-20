---
title: Arquitetura da rede Azure
description: Este artigo fornece uma descrição geral da rede de infraestruturas Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567877"
---
# <a name="azure-network-architecture"></a>Arquitetura da rede Azure
A arquitetura da rede Azure fornece conectividade da Internet aos datacenters Azure. Qualquer carga de trabalho implementada (IaaS, PaaS e SaaS) em Azure está a alavancar a rede de datacenter Azure.

## <a name="network-topology"></a>Topologia da rede
A arquitetura de rede de um datacenter Azure consiste nos seguintes componentes:

- Rede edge
- Ampla rede de área
- Rede regional de gateways
- Rede datacenter

![Diagrama da rede Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Componentes de rede
Uma breve descrição dos componentes da rede.

- Rede edge

   - Ponto de demarcação entre a rede microsoft e outras redes (por exemplo, Internet, rede empresarial)
   - Fornece Internet e [ExpressRoute](../../expressroute/expressroute-introduction.md) olhando para Azure

- Ampla rede de área

   - Rede de espinhas traseiras inteligente da Microsoft que cobre o globo
   - Proporciona conectividade entre [regiões de Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

- Porta de entrada regional

   - Ponto de agregação para todos os centros de dados de uma região do Azure
   - Proporciona uma conectividade maciça entre centros de dados dentro de uma região do Azure (por exemplo, várias centenas de terabits por datacenter)

- Rede datacenter

   - Fornece conectividade entre servidores dentro do datacenter com baixa largura de banda sobresubscrita

Os componentes de rede acima são projetados para fornecer a máxima disponibilidade para suportar o negócio de nuvem sempre ligado e sempre disponível. A redundância é projetada e incorporada na rede a partir do aspeto físico até ao protocolo de controlo.

## <a name="datacenter-network-resiliency"></a>Resiliência da rede datacenter
Vamos ilustrar o princípio do design de resiliência usando a rede datacenter.

A rede datacenter é uma versão modificada de uma [rede Clos,](https://en.wikipedia.org/wiki/Clos_network)fornecendo alta largura de banda bi-secções para tráfego em escala de nuvem. A rede é construída usando um grande número de dispositivos de mercadorias para reduzir o impacto causado por falha de hardware individual. Estes dispositivos estão estrategicamente localizados em diferentes locais físicos com potência separada e domínio de arrefecimento para reduzir o impacto de um evento ambiental.  No plano de controlo, todos os dispositivos de rede estão a funcionar como modo de encaminhamento do modelo OSI Layer 3, o que elimina a questão histórica do circuito de tráfego. Todos os caminhos entre diferentes níveis estão ativos para fornecer alta redundância e largura de banda utilizando Equal-Cost encaminhamento multi-path (ECMP).

O diagrama seguinte demonstra que a rede do datacenter é construída por diferentes níveis de dispositivos de rede. As barras do diagrama representam grupos de dispositivos de rede que proporcionam redundância e alta conectividade de largura de banda.

![Rede datacenter](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
