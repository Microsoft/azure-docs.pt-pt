---
title: O que é a versão prévia do Gerenciador de firewall do Azure?
description: Saiba mais sobre os recursos do Gerenciador de firewall do Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: 3e19a2a45dde8a951e0ae3a4bd3c8d019609a5e1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502381"
---
# <a name="what-is-azure-firewall-manager-preview"></a>O que é a versão prévia do Gerenciador de firewall do Azure?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A versão prévia do Gerenciador de firewall do Azure é um serviço de gerenciamento de segurança que fornece política de segurança central e gerenciamento de rotas para perímetros de segurança baseados em nuvem. Ele funciona com o [Hub de WAN virtual do Azure](../virtual-wan/virtual-wan-about.md#resources), um recurso gerenciado pela Microsoft que permite criar facilmente arquiteturas de Hub e spoke. Quando as políticas de segurança e roteamento são associadas a esse Hub, ele é conhecido como um *[Hub virtual protegido](secured-virtual-hub.md)* . 

![Gerenciador de firewall](media/overview/firewall-manager-conceptual.png)

## <a name="azure-firewall-manager-preview-features"></a>Recursos de visualização do Azure firewall Manager

A versão prévia do Gerenciador de firewall do Azure oferece os seguintes recursos:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implantação e configuração do firewall central do Azure

Você pode implantar e configurar centralmente várias instâncias do firewall do Azure que abrangem diferentes regiões e assinaturas do Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Políticas hierárquicas (globais e locais)

Você pode usar a visualização do Gerenciador de firewall do Azure para gerenciar centralmente políticas de firewall do Azure em vários hubs virtuais protegidos. Suas equipes de ti central podem criar políticas globais de firewall para impor a política de firewall de toda a organização entre as equipes. As políticas de firewall criadas localmente permitem um modelo de autoatendimento DevOps para melhorar a agilidade.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrado com segurança como serviço de terceiros para segurança avançada

Além do firewall do Azure, você pode integrar provedores de SECaaS (segurança como serviço) de terceiros para fornecer proteção adicional de rede para suas conexões de Internet de VNet e filiais.

- Filtragem de tráfego de VNet para a Internet (V2I)

   - Filtre o tráfego de rede virtual de saída com seu provedor de segurança de terceiros preferido.
   - Aproveite a proteção avançada da Internet com reconhecimento de usuário para suas cargas de trabalho de nuvem em execução no Azure.

- Filtragem de tráfego de ramificação para a Internet (B2I)

   Aproveite sua conectividade do Azure e a distribuição global para adicionar facilmente filtragem de terceiros para cenários de Branch para Internet.

Para obter mais informações sobre provedores de segurança confiáveis, consulte [o que são parceiros de segurança confiáveis do Azure firewall Manager (versão prévia)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gerenciamento de rota centralizado

Encaminhe facilmente o tráfego para o Hub protegido para filtragem e registro sem a necessidade de configurar manualmente as rotas definidas pelo usuário (UDR) em redes virtuais do spoke. Você pode usar provedores de terceiros para a filtragem de tráfego de Branch para a Internet (B2I), lado a lado com o Firewall do Azure para B2V (Branch to VNet), VNet para VNet (V2V) e VNet para a Internet (V2I). Você também pode usar provedores de terceiros para a filtragem de tráfego V2I, contanto que o Firewall do Azure não seja necessário para B2V ou V2V. 

## <a name="region-availability"></a>Disponibilidade de região

As seguintes regiões têm suporte para a visualização pública:

- Europa Ocidental, Europa Setentrional, França central, sul da França, Sul do Reino Unido Oeste do Reino Unido
- Leste da Austrália, Austrália Central, Austrália Central 2, sudeste da Austrália
- Canadá Central
- Leste dos EUA, oeste dos EUA, leste dos EUA 2, Sul EUA Central, oeste dos EUA 2, EUA Central, norte EUA Central, Oeste EUA Central

As políticas de firewall do Azure só podem ser criadas nessas regiões, mas podem ser usadas entre regiões. Por exemplo, você pode criar uma política no oeste dos EUA e usá-la no leste dos EUA. 

## <a name="known-issues"></a>Problemas conhecidos

A versão prévia do Gerenciador de firewall do Azure tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
|Não há suporte para VNets central criadas manualmente|Atualmente, o Gerenciador de firewall do Azure dá suporte a redes criadas com hubs virtuais. O uso de sua própria VNet de Hub criada manualmente ainda não tem suporte.|Por enquanto, use o Gerenciador de firewall do Azure com redes de Hub e spoke criadas com hubs virtuais<br>Investigando atualmente.
|Limitações de filtragem de terceiros|Não há suporte para a filtragem de tráfego V2I com provedores de terceiros com o Firewall do Azure B2V e V2V.|Investigando atualmente.|
|Divisão de tráfego sem suporte no momento|O Office 365 e a divisão de tráfego de PaaS público do Azure não têm suporte no momento. Dessa forma, a seleção de um provedor de terceiros para V2I ou B2I também envia todo o tráfego de PaaS público do Azure e do Office 365 por meio do serviço de parceiro.|Investigando atualmente a divisão de tráfego no Hub.
|Um hub por região|Você não pode ter mais de um hub por região|Crie várias WANs virtuais em uma região.|
|As políticas de base devem estar na mesma região que a política local|Crie todas as políticas locais na mesma região que a política de base. Você ainda pode aplicar uma política que foi criada em uma região em um hub protegido de outra região.|Investigando atualmente.|

## <a name="next-steps"></a>Passos seguintes

- Examinar a [visão geral da implantação do Azure firewall Manager Preview](deployment-overview.md)
- Saiba mais sobre os [hubs virtuais protegidos](secured-virtual-hub.md).