---
title: Quais são as opções de arquitetura do Azure Firewall Manager?
description: Compare e contraste usando a rede virtual hub ou arquiteturas de hub virtual seguras com O Gestor de Firewall Azure.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444579"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Quais são as opções de arquitetura do Azure Firewall Manager?

O Azure Firewall Manager pode fornecer gestão de segurança para dois tipos de arquitetura de rede:

- **centro virtual seguro**

   Um [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerido pela Microsoft que permite criar facilmente arquiteturas de hub e faladas. Quando as políticas de segurança e encaminhamento estão associadas a tal hub, é referido como um *[centro virtual seguro.](secured-virtual-hub.md)* 
- **rede virtual hub**

   Esta é uma rede virtual azure padrão que você cria e gere. Quando as políticas de segurança são associadas a tal hub, é referida como uma *rede virtual hub*. Neste momento, apenas a Política de Firewall Azure é apoiada. Pode peer-spoke redes virtuais que contêm os seus servidores e serviços de carga de trabalho. Também pode gerir firewalls em redes virtuais autónomas que não são espreitadas por qualquer fala.

## <a name="comparison"></a>Comparação

A tabela seguinte compara estas duas opções de arquitetura e pode ajudá-lo a decidir qual é o certo para os requisitos de segurança da sua organização:


|  |**Rede virtual do hub**|**Centro virtual seguro**  |
|---------|---------|---------|
|**Recurso subjacente**     |Rede virtual|Centro Wan Virtual|
|**Centro & Falado**     |Utiliza o peering de rede virtual|Automatizado usando conexão de rede virtual hub|
|**Conectividade on-prem**     |VpN Gateway até 10 Gbps e 30 ligações S2S; Rota expresso|Mais escalável VPN Gateway até 20 Gbps e 1000 Ligações S2S; Rota expressa|
|**Conectividade automatizada do ramo usando SDWAN**      |Não suportado|Suportado|
|**Centros por região**     |Múltiplas Redes Virtuais por região|Hub Virtual Único por região. Múltiplos hubs possíveis com várias WANs virtuais|
|**Firewall Azure - múltiplos endereços IP públicos**      |Cliente fornecido|Auto gerado. Para estar disponível pela GA.|
|**Zonas de disponibilidade de firewall azure**     |Suportado|Não disponível na pré-visualização. Estar disponível pela GA|
|**Segurança avançada da Internet com segurança de terceiros como parceiros de serviço**     |Conectividade VPN estabelecida e gerida pelo cliente ao serviço parceiro de eleição|Automatizado através de fluxo de parceiro de segurança fidedigno e experiência de gestão de parceiros|
|**Gestão de rotas centralizadas para encaminhar tráfego para o centro**     |Rota definida pelo utilizador gerida pelo cliente|Suportado usando BGP|
|**Firewall de Aplicações Web no Gateway de Aplicação** |Suportado em Rede Virtual|Atualmente suportado em rede de porta-voz|
|**Aparelho virtual da rede**|Suportado em Rede Virtual|Atualmente suportado em rede de porta-voz|

## <a name="next-steps"></a>Passos seguintes

- Rever visão geral de [implementação do Gestor de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [centros virtuais seguros.](secured-virtual-hub.md)