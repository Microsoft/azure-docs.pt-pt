---
title: Quais são as opções de arquitetura do Azure Firewall Manager?
description: Compare e contraste usando a rede virtual hub ou arquiteturas de hub virtual seguras com Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563153"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quais são as opções de arquitetura do Azure Firewall Manager?

O Azure Firewall Manager pode fornecer gestão de segurança para dois tipos de arquitetura de rede:

- **centro virtual seguro**

   [Um Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerido pela Microsoft que permite criar facilmente arquiteturas de hub e spoke. Quando as políticas de segurança e encaminhamento estão associadas a tal hub, é referido como um *[centro virtual seguro.](secured-virtual-hub.md)* 
- **rede virtual de hub**

   Esta é uma rede virtual Azure padrão que você cria e gere a si mesmo. Quando as políticas de segurança estão associadas a tal hub, é referida como uma *rede virtual de hub*. Neste momento, apenas a Política de Firewall Azure é apoiada. Pode espreitar redes virtuais que contêm os seus servidores e serviços de carga de trabalho. Também pode gerir firewalls em redes virtuais autónomas que não são espreitadas por qualquer fala.

## <a name="comparison"></a>Comparação

A tabela a seguir compara estas duas opções de arquitetura e pode ajudá-lo a decidir qual é a pessoa certa para os requisitos de segurança da sua organização:


|  |**Rede virtual do hub**|**Hub virtual seguro**  |
|---------|---------|---------|
|**Recurso subjacente**     |Rede virtual|Virtual WAN Hub|
|**Hub & Spoke**     |Utiliza o espreitamento da rede virtual|Automatizado usando a ligação de rede virtual do hub|
|**Conectividade on-prem**     |Gateway VPN até 10 Gbps e 30 ligações S2S; ExpressRoute|Gateway VPN mais escalável até 20 Gbps e 1000 ligações S2S; Rota Expressa|
|**Conectividade de ramo automatizado usando SDWAN**      |Não suportado|Suportado|
|**Hubs por região**     |Múltiplas Redes Virtuais por região|Único Hub Virtual por região. Múltiplos centros possíveis com várias WANs Virtuais|
|**Azure Firewall - vários endereços IP públicos**      |Cliente fornecido|Auto gerado|
|**Zonas de disponibilidade de firewall Azure**     |Suportado|Ainda não está disponível|
|**Segurança avançada na Internet com segurança de terceiros como parceiros de serviço**     |Conectividade VPN estabelecida e gerida pelo cliente ao serviço de eleição do parceiro|Automatizado através do fluxo de fornecedores de parceiros de segurança e experiência de gestão de parceiros|
|**Gestão centralizada da rota para encaminhar o tráfego para o centro**     |Rota definida pelo utilizador gerida pelo cliente|Suportado com BGP|
|**Suporte a vários fornecedores de segurança**|Suportado com túneis forçados configurados manualmente para firewalls de terceiros|Suporte automatizado para dois fornecedores de segurança: Azure Firewall para filtragem de tráfego privado e terceiros para filtragem de Internet|
|**Firewall de Aplicações Web no Gateway de Aplicação** |Suportado em Rede Virtual|Atualmente suportado na rede de fala|
|**Aparelho virtual de rede**|Suportado em Rede Virtual|Atualmente suportado na rede de fala|
|**Suporte padrão de proteção Azure DDoS**|Yes|Não|

## <a name="next-steps"></a>Próximos passos

- Rever [visão geral do Azure Firewall Manager](deployment-overview.md)
- Saiba mais sobre [os Centros Virtuais Seguros.](secured-virtual-hub.md)