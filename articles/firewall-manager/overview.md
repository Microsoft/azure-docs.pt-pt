---
title: O que é a Pré-visualização do Gestor de Firewall do Azure?
description: Conheça as funcionalidades do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366279"
---
# <a name="what-is-azure-firewall-manager-preview"></a>O que é a Pré-visualização do Gestor de Firewall do Azure?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview é um serviço de gestão de segurança que fornece a política de segurança central e a gestão de rotas para perímetros de segurança baseados na nuvem. 

O Firewall Manager pode fornecer gestão de segurança para dois tipos de arquitetura de rede:

- **centro virtual seguro**

   Um [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) é um recurso gerido pela Microsoft que permite criar facilmente arquiteturas de hub e faladas. Quando as políticas de segurança e encaminhamento estão associadas a tal hub, é referido como um *[centro virtual seguro.](secured-virtual-hub.md)* 
- **rede virtual hub**

   Esta é uma rede virtual azure padrão que você cria e gere. Quando as políticas de segurança são associadas a tal hub, é referida como uma *rede virtual hub*. Neste momento, apenas a Política de Firewall Azure é apoiada. Pode peer-spoke redes virtuais que contêm os seus servidores e serviços de carga de trabalho. Também pode gerir firewalls em redes virtuais autónomas que não são espreitadas por qualquer fala.

Para uma comparação detalhada de *arquiteturas de rede virtual e hub seguras,* veja quais são as opções de [arquitetura Azure Firewall Manager?](vhubs-and-vnets.md)

![firewall-manager](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Funcionalidades de pré-visualização do Gestor de Firewall Azure

A Pré-visualização do Azure Firewall Manager oferece as seguintes funcionalidades:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Implementação e configuração central da Firewall Azure

Pode implantar centralmente e configurar várias instâncias do Azure Firewall que abrangem diferentes regiões e subscrições do Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Políticas hierárquicas (globais e locais)

Pode utilizar o Azure Firewall Manager Preview para gerir centralmente as políticas de Firewall Do Azure em vários centros virtuais seguros. As suas equipas centrais de TI podem ser autoras de políticas globais de firewall para impor a política de firewall de largura da organização entre as equipas. As políticas de firewall de autoria local permitem um modelo de self-service DevOps para uma melhor agilidade.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrado com segurança de terceiros como serviço de segurança avançada

Além do Azure Firewall, pode integrar a segurança de terceiros como prestadores de serviços (SECaaS) para fornecer proteção adicional de rede para as suas ligações de Internet VNet e branch.

Esta funcionalidade está disponível apenas com implementações de hub virtual seguras.

- Filtragem de tráfego VNet para Internet (V2I)

   - Filtrar o tráfego de rede virtual de saída com o seu fornecedor de segurança preferido de terceiros.
   - Aproveite a proteção avançada de internet consciente do utilizador para as suas cargas de trabalho em nuvem em funcionamento no Azure.

- Filtragem de tráfego de ramificação para a Internet (B2I)

   Aproveite a sua conectividade Azure e distribuição global para adicionar facilmente filtragem de terceiros para ramificação aos cenários da Internet.

Para obter mais informações sobre fornecedores de segurança fidedignos, consulte [O que é que o Azure Firewall Manager confia nos parceiros de segurança (pré-visualização)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gestão de rotas centralizadas

Encaminhe facilmente o tráfego para o seu centro seguro para filtrar e registar sem a necessidade de configurar manualmente as Rotas Definidas do Utilizador (UDR) em redes virtuais faladas. 

Esta funcionalidade está disponível apenas com implementações de hub virtual seguras.

Pode utilizar fornecedores de terceiros para filtragem de tráfego Branch to Internet (B2I), lado a lado com o Azure Firewall para O Ramo para VNet (B2V), VNet para VNet (V2V) e VNet para internet (V2I). Também pode utilizar fornecedores de terceiros para filtragem de tráfego V2I desde que o Firewall Azure não seja necessário para B2V ou V2V. 

## <a name="region-availability"></a>Disponibilidade de região

As políticas de firewall azure podem ser usadas em todas as regiões. Por exemplo, pode criar uma política nos EUA Ocidentais e usá-la nos EUA Orientais. 

## <a name="known-issues"></a>Problemas conhecidos

A Pré-visualização do Gestor de Firewall Azure tem os seguintes problemas conhecidos:

|Problema  |Descrição  |Mitigação  |
|---------|---------|---------|
|Limitações de filtragem de terceiros.|A filtragem de tráfego V2I com fornecedores de terceiros não é suportada com o Azure Firewall B2V e V2V.|Investigação|
|O trânsito não está atualmente a ser suportado.|O Office 365 e o Azure Public PaaS não são atualmente apoiados. Como tal, a seleção de um fornecedor de terceiros para V2I ou B2I também envia todo o tráfego Azure Public PaaS e Office 365 através do serviço de parceiros.|Investigando o trânsito a dividir-se no centro.
|Um centro virtual seguro por região.|Não se pode ter mais do que um centro virtual seguro por região.|Crie múltiplas WANs virtuais numa região.|
|As políticas de base devem estar na mesma região que a política local.|Crie todas as suas políticas locais na mesma região que a política de base. Ainda se pode aplicar uma política criada numa região num centro seguro de outra região.|Investigação|
|Comunicação inter-hub não funciona ndo com o Secured Virtual Hub|O Centro Virtual Seguro para a comunicação Secured Virtual Hub ainda não é suportado.|Investigação|
|Todos os Centros Virtuais Seguros que partilham o mesmo WAN virtual devem estar no mesmo grupo de recursos.|Este comportamento está alinhado com os Centros Wan Virtuais hoje.|Crie vários WANs virtuais para permitir a criação de centros virtuais seguros em diferentes grupos de recursos.|
|Os grupos IP não são apoiados na Política de Firewall.|Os grupos IP estão em pré-visualização pública e atualmente apenas suportados com as regras tradicionais de firewall.|Correção em curso.
|As subscrições do Cloud Solution Provider (CSP) não foram suportadas.|Atualmente, as [subscrições de CSP](https://azure.microsoft.com/offers/ms-azr-0145p/) não são suportadas.|Investigação

## <a name="next-steps"></a>Passos seguintes

- Rever visão geral de [implementação do Gestor de Firewall do Azure](deployment-overview.md)
- Saiba mais sobre [centros virtuais seguros.](secured-virtual-hub.md)