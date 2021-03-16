---
title: O que é o Azure Firewall Manager?
description: Saiba mais sobre as funcionalidades do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/16/2021
ms.author: victorh
ms.openlocfilehash: 506799f94676be007cf94320e3958bd305ce85f0
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573460"
---
# <a name="what-is-azure-firewall-manager"></a>O que é o Azure Firewall Manager?

O Azure Firewall Manager é um serviço de gestão de segurança que fornece política de segurança central e gestão de rotas para perímetros de segurança baseados na nuvem. 

O Gestor de Firewall pode fornecer gestão de segurança para dois tipos de arquitetura de rede:

- **Hub virtual seguro**

   [Um Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerido pela Microsoft que permite criar facilmente arquiteturas de hub e spoke. Quando as políticas de segurança e encaminhamento estão associadas a tal hub, é referido como um *[centro virtual seguro](secured-virtual-hub.md)*. 
- **Rede virtual do hub**

   Esta é uma rede virtual Azure padrão que você cria e gere a si mesmo. Quando as políticas de segurança estão associadas a tal hub, é referida como uma *rede virtual de hub*. Neste momento, apenas a Política de Firewall Azure é apoiada. Pode espreitar redes virtuais que contêm os seus servidores e serviços de carga de trabalho. Também pode gerir firewalls em redes virtuais autónomas que não são espreitadas por qualquer fala.

Para uma comparação detalhada das arquiteturas *de rede virtual segura* e *hub,* veja quais são as opções de [arquitetura do Azure Firewall Manager?](vhubs-and-vnets.md)

![gestor de firewall](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Funcionalidades do Azure Firewall Manager

O Azure Firewall Manager oferece as seguintes funcionalidades:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implantação e configuração central da Firewall Azure

Pode implantar e configurar centralmente vários casos de Firewall Azure que abrangem diferentes regiões e subscrições do Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Políticas hierárquicas (globais e locais)

Você pode usar O Azure Firewall Manager para gerir centralmente as políticas do Azure Firewall em vários centros virtuais protegidos. As suas equipas centrais de TI podem autorizar políticas globais de firewall para impor a política de firewall da organização entre as equipas. As políticas de firewall da autoria local permitem um modelo de self-service DevOps para uma melhor agilidade.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrado com segurança de terceiros para segurança avançada

Além do Azure Firewall, pode integrar a segurança de terceiros como um fornecedor de serviço (SECaaS) para fornecer proteção adicional de rede para as suas ligações VNet e branch Internet.

Esta funcionalidade está disponível apenas com implementações de hub virtual seguras.

- Filtragem de tráfego VNet para Internet (V2I)

   - Filtre o tráfego de rede virtual de saída com o seu fornecedor de segurança de terceiros preferido.
   - Aproveite a proteção avançada da Internet consciente do utilizador para as suas cargas de trabalho em nuvem em execução no Azure.

- Filtragem de tráfego de sucursal para internet (B2I)

   Aproveite a conectividade Azure e a distribuição global para adicionar facilmente filtragem de terceiros para os cenários da Internet.

Para obter mais informações sobre os fornecedores de parceiros de segurança, consulte [quais são os fornecedores de parceiros de segurança do Azure Firewall Manager?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gestão de rotas centralizadas

Encaminhe facilmente o tráfego para o seu hub seguro para filtragem e registo sem a necessidade de configurar manualmente as Rotas Definidas pelo Utilizador (UDR) em redes virtuais faladas. 

Esta funcionalidade está disponível apenas com implementações de hub virtual seguras.

Pode utilizar fornecedores de terceiros para filtragem de tráfego branch to Internet (B2I), lado a lado com Azure Firewall para Branch para VNet (B2V), VNet para VNet (V2V) e VNet para Internet (V2I). Também pode utilizar fornecedores de terceiros para filtragem de tráfego V2I, desde que o Azure Firewall não seja necessário para B2V ou V2V. 

## <a name="region-availability"></a>Disponibilidade de região

As políticas de firewall do Azure podem ser usadas em todas as regiões. Por exemplo, pode criar uma política nos EUA ocidentais e usá-la nos EUA. 

## <a name="known-issues"></a>Problemas conhecidos

O Azure Firewall Manager tem os seguintes problemas conhecidos:

|Problema  |Description  |Mitigação  |
|---------|---------|---------|
|Divisão de tráfego|A divisão de tráfego da Microsoft 365 e do Azure Public PaaS não está suportada neste momento. Como tal, selecionar um fornecedor de terceiros para V2I ou B2I também envia todo o tráfego Azure Public PaaS e Microsoft 365 através do serviço de parceiros.|A investigar a divisão de trânsito no centro.
|Um centro virtual seguro por região|Não se pode ter mais do que um centro virtual seguro por região.|Crie várias WANs virtuais numa região.|
|As políticas de base devem estar na mesma região que a política local|Crie todas as suas políticas locais na mesma região que a política de base. Pode ainda aplicar uma política criada numa região num centro seguro de outra região.|A investigar|
|Filtrar o tráfego inter-hub em implementações de centros virtuais seguros|O Hub Virtual Seguro para a filtragem de comunicação do Hub Virtual Garantido ainda não está suportado. No entanto, a comunicação do hub para hub ainda funciona se a filtragem de tráfego privado através do Azure Firewall não estiver ativada.|A investigar|
|Raios de voz em regiões diferentes do centro virtual|Os raios de voz em diferentes regiões do que o centro virtual não são apoiados.|A investigar<br><br>Crie um hub por região e pares VNets na mesma região que o hub.|
|Sucursal para sucursal de tráfego com filtragem de tráfego privado habilitado|O tráfego de sucursal não é suportado quando a filtragem do tráfego privado está ativada. |A investigar.<br><br>Não proteja o tráfego privado se a conectividade do ramo for fundamental.|
|Todos os Hubs Virtuais Seguros que partilham o mesmo WAN virtual devem estar no mesmo grupo de recursos.|Este comportamento está alinhado com os Hubs WAN Virtuais hoje em dia.|Crie várias WANs virtuais para permitir a criação de Hubs Virtuais Seguros em diferentes grupos de recursos.|
|Falha na adição de endereço IP em massa|A firewall do hub seguro entra num estado falhado se adicionar vários endereços IP públicos.|Adicione pequenos incrementos de endereços IP públicos. Por exemplo, adicione 10 de cada vez.|
|DDoS Protection Standard não suportado com centros virtuais seguros|A Norma de Proteção DDoS não está integrada com vWANs.|A investigar|
|Registos de atividade não totalmente suportados|A política de firewall não suporta atualmente registos de atividade.|A investigar|
|Algumas definições de firewall não são migradas quando a firewall é migrada para usar a Política de Firewall|As zonas de disponibilidade e os endereços privados SNAT não são migrados quando migra para a Política de Firewall do Azure.|A investigar| 

## <a name="next-steps"></a>Passos seguintes

- Rever [visão geral do Azure Firewall Manager](deployment-overview.md)
- Saiba mais sobre [os Centros Virtuais Seguros.](secured-virtual-hub.md)
