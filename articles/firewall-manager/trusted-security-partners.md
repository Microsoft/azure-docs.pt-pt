---
title: O que são parceiros de segurança fidedignos do Azure Firewall Manager (pré-visualização)
description: Saiba mais sobre os parceiros de segurança fidedignos do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436778"
---
# <a name="what-are-trusted-security-partners-preview"></a>O que são parceiros de segurança fidedignos (pré-visualização)?

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

*Parceiros de segurança fidedignos (pré-visualização)* no Azure Firewall Manager permite-lhe utilizar a sua segurança familiar, de melhor procriação, de terceiros como serviço (SECaaS) para proteger o acesso à Internet para os seus utilizadores.

Com uma configuração rápida, pode garantir um hub com um parceiro de segurança suportado, e encaminhar e filtrar o tráfego de Internet das suas Redes Virtuais (VNets) ou localizações de filiais dentro de uma região. Isto é feito utilizando a gestão automatizada da rota, sem configurar e gerir rotas definidas pelo utilizador (UDRs).

Pode implementar centros seguros configurados com o parceiro de segurança à sua escolha em várias regiões do Azure para obter conectividade e segurança para os seus utilizadores em qualquer parte do mundo nessas regiões. Com a capacidade de utilizar a oferta do parceiro de segurança para o tráfego de aplicações Internet/SaaS, e o Azure Firewall para tráfego privado nos centros seguros, pode agora começar a construir a sua vantagem de segurança no Azure que está perto dos seus utilizadores e aplicações distribuídos globalmente.

Para esta pré-visualização, os parceiros de segurança suportados são **ZScaler** e **iboss**. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

![Parceiros de segurança fidedignos](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Cenários-chave

Pode utilizar os parceiros de segurança para filtrar o tráfego da Internet nos seguintes cenários:

- Rede Virtual (VNet) para a Internet

   Aproveite a proteção avançada de internet consciente do utilizador para as suas cargas de trabalho em nuvem em funcionamento no Azure.

- Sucursal para a Internet

   Aproveite a sua conectividade Azure e distribuição global para adicionar facilmente filtragem nSaaS de terceiros para ramificação para cenários de Internet. Pode construir a sua rede global de trânsito e borda de segurança utilizando o Azure Virtual WAN.

Os seguintes cenários são apoiados:
-   VNet para internet através de uma oferta de parceiros de terceiros.
-   Sucursal para a Internet através de uma oferta de parceiros de terceiros.
-   Sucursal para a Internet através de uma oferta de parceiros de terceiros, o resto do tráfego privado (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) via Azure Firewall.

O seguinte cenário não é suportado:

- A VNet para a Internet através de uma oferta de parceiros não pode ser combinada com o Azure Firewall para tráfego privado. Consulte as seguintes limitações.

## <a name="current-limitations"></a>Limitações atuais

- Para a VNet para a Internet, não é possível misturar a adição de Firewall Azure para tráfego privado e uma oferta de parceiropara tráfego na Internet. Pode enviar tráfego de Internet para o Azure Firewall ou um parceiro de segurança de terceiros que oferece no centro virtual seguro, mas não ambos. 
- Pode implantar no máximo um parceiro de segurança por centro virtual. Se precisar de alterar o fornecedor, tem de remover o parceiro existente e adicionar um novo.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>As melhores práticas para filtragem de tráfego na Internet em centros virtuais seguros

O tráfego de internet normalmente inclui tráfego web. Mas também inclui tráfego destinado a aplicações SaaS como Office 365 (O365) e serviços públicos azure PaaS como Azure Storage, Azure Sql, e assim por diante. Seguem-se as recomendações de boas práticas para o tratamento do tráfego a estes serviços:

### <a name="handling-azure-paas-traffic"></a>Manuseamento do tráfego Azure PaaS
 
- Utilize o Firewall Azure para proteção se o seu tráfego for constituído maioritariamente por Azure PaaS, e o acesso a recursos para as suas aplicações pode ser filtrado usando endereços IP, FQDNs, etiquetas de serviço ou etiquetas FQDN.

- Utilize uma solução de parceiro de terceiros nos seus hubs se o seu tráfego for composto por acesso à aplicação SaaS, ou se necessitar de filtragem consciente do utilizador (por exemplo, para as suas cargas de trabalho de infraestrutura de ambiente de trabalho virtuais (VDI) ou se necessitar de capacidades avançadas de filtragem da Internet.

![Todos os cenários para O Gestor de Firewall azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Tráfego do Gabinete de Manuseamento 365 (O365)

Em cenários de localização de filiais distribuídos globalmente, deve redirecionar o tráfego do Office 365 diretamente para a sucursal antes de enviar o tráfego restante de Internet que o seu hub azure segurou.

Para o Office 365, a latência e desempenho da rede são cruciais para a experiência bem sucedida do utilizador. Para atingir estes objetivos em torno do desempenho ideal e da experiência do utilizador, os clientes devem implementar o Office 365 de fuga direta e local antes de considerar em rotar o resto do tráfego de Internet através do Azure.

Os princípios de conectividade da [rede office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) exigem que as principais ligações de rede do Office 365 sejam direcionadas localmente a partir do ramo de utilizador ou dispositivo móvel e diretamente através da Internet para o ponto de presença mais próximo da rede microsoft.

Além disso, as ligações do Office 365 são fortemente encriptadas para privacidade e utilizam protocolos eficientes e proprietários por razões de desempenho. Isto torna impraticável e impactante sujeitar essas ligações a soluções tradicionais de segurança ao nível da rede. Por estas razões, recomendamos vivamente que os clientes enviem o Tráfego do Office 365 diretamente de filiais, antes de enviar o resto do tráfego através do Azure. A Microsoft estabeleceu uma parceria com vários fornecedores de soluções SD-WAN, que se integram com o Azure e o Office 365 e facilitam aos clientes a fuga direta e local da Internet do Office 365. Para mais detalhes veja [como posso definir as minhas políticas de O365 via Virtual WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Passos seguintes

[Implemente uma oferta de segurança fidedigna num hub seguro, utilizando](deploy-trusted-security-partner.md)o Azure Firewall Manager .
