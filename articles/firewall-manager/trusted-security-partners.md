---
title: O que são os fornecedores de parceiros de segurança do Azure Firewall Manager (pré-visualização)
description: Saiba mais sobre os fornecedores de parceiros de segurança do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: victorh
ms.openlocfilehash: 3d430deae191fbc9f9ab5bbbc2b83ee4640dd831
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791532"
---
# <a name="what-are-security-partner-providers-preview"></a>O que são os fornecedores de parceiros de segurança (pré-visualização)?

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

*Os fornecedores de parceiros de segurança (pré-visualização)* no Azure Firewall Manager permitem-lhe utilizar a sua segurança familiar, de melhor raça e terceiros como ofertas de serviço (SECaaS) para proteger o acesso à Internet para os seus utilizadores.

Com uma configuração rápida, pode proteger um hub com um parceiro de segurança suportado, e encaminhar e filtrar o tráfego de Internet a partir das suas Redes Virtuais (VNets) ou localizações de filiais dentro de uma região. Isto é feito usando a gestão automatizada de rotas, sem configurar e gerir rotas definidas pelo utilizador (UDRs).

Pode implementar centros seguros configurados com o parceiro de segurança à sua escolha em várias regiões do Azure para obter conectividade e segurança para os seus utilizadores em qualquer parte do mundo nessas regiões. Com a capacidade de utilizar a oferta do parceiro de segurança para o tráfego de aplicações Internet/SaaS, e a Azure Firewall para tráfego privado nos centros seguros, pode agora começar a construir a sua vantagem de segurança no Azure que está perto dos seus utilizadores e aplicações distribuídos globalmente.

Para esta pré-visualização, os parceiros de segurança apoiados são **ZScaler,** **Check Point**e **iboss**. As regiões apoiadas são WestCentralUS, NorthCentralUS, WestUS, WestUS2 e EastUS.

![Fornecedores de parceiros de segurança](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Cenários-chave

Pode utilizar os parceiros de segurança para filtrar o tráfego da Internet em seguintes cenários:

- Rede Virtual (VNet) para internet

   Aproveite a proteção avançada da Internet consciente do utilizador para as suas cargas de trabalho em nuvem em execução no Azure.

- Ramo para internet

   Aproveite a conectividade Azure e a distribuição global para adicionar facilmente a filtragem NSaaS de terceiros para os cenários da Internet. Pode construir a sua rede global de trânsito e borda de segurança utilizando a Azure Virtual WAN.

São apoiados os seguintes cenários:
-   VNet para Internet através de uma oferta de parceiros de terceiros.
-   Filial para a Internet através de uma oferta de parceiros de terceiros.
-   Sucursal para a Internet através de uma oferta de parceiros de terceiros, o resto do tráfego privado (Spoke-to-Spoke, Spoke-to-Branches, Branch-to-Spokes) via Azure Firewall.

O seguinte cenário não é suportado:

- VNet para internet através de uma oferta de parceiro não pode ser combinado com Azure Firewall para tráfego privado. Consulte as seguintes limitações.

## <a name="current-limitations"></a>Limitações atuais

- Para o VNet na Internet, não é possível misturar a adição de Azure Firewall para tráfego privado e uma oferta de parceiros para tráfego de Internet. Pode enviar tráfego de Internet para a Azure Firewall ou um parceiro de segurança de terceiros que oferece no centro virtual seguro, mas não ambos. 
- Pode implementar no máximo um parceiro de segurança por centro virtual. Se precisar de mudar o fornecedor, tem de remover o parceiro existente e adicionar um novo.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Boas práticas para filtragem de tráfego de Internet em centros virtuais seguros

O tráfego de internet normalmente inclui tráfego web. Mas também inclui tráfego destinado a aplicações saaS como Office 365 (O365) e Azure serviços públicos PaaS como Azure Storage, Azure Sql, e assim por diante. Seguem-se recomendações de boas práticas para a manipulação do tráfego para estes serviços:

### <a name="handling-azure-paas-traffic"></a>Manuseamento do tráfego de Azure PaaS
 
- Utilize o Azure Firewall para proteção se o seu tráfego for maioritariamente constituído por Azure PaaS, e o acesso a recursos para as suas aplicações pode ser filtrado usando endereços IP, FQDNs, tags de serviço ou tags FQDN.

- Utilize uma solução de parceiro de terceiros nos seus hubs se o seu tráfego for composto por acesso à aplicação SaaS, ou se necessitar de filtragem consciente do utilizador (por exemplo, para as suas cargas de trabalho de infraestrutura de ambiente de trabalho virtuais (VDI) ou se precisar de capacidades avançadas de filtragem da Internet.

![Todos os cenários para Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Tráfego do Gabinete de Gestão 365 (O365)

Em cenários de localização de ramificação distribuídos globalmente, você deve redirecionar o tráfego do Office 365 diretamente na sucursal antes de enviar o tráfego de Internet restante seu centro seguro Azure.

Para o Office 365, a latência e desempenho da rede são fundamentais para uma experiência de utilizador bem sucedida. Para alcançar estes objetivos em torno do desempenho ideal e da experiência do utilizador, os clientes devem implementar o Office 365 escape direto e local antes de considerar o encaminhamento do resto do tráfego da Internet através do Azure.

[Os princípios de conectividade da rede do Office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) exigem que as ligações de rede do Office 365 sejam encaminhada localmente da filial do utilizador ou dispositivo móvel e diretamente através da Internet para o ponto de presença mais próximo da rede da Microsoft.

Além disso, as ligações do Office 365 são fortemente encriptadas para privacidade e utilização de protocolos eficientes e proprietários por razões de desempenho. Isto torna impraticável e impactante sujeitar essas ligações a soluções tradicionais de segurança ao nível da rede. Por estas razões, recomendamos vivamente que os clientes enviem o tráfego do Office 365 diretamente das agências, antes de enviarem o resto do tráfego através do Azure. A Microsoft estabeleceu uma parceria com vários fornecedores de soluções SD-WAN, que se integram com o Azure e o Office 365 e facilitam aos clientes a fuga direta e local da Internet do Office 365. Para mais [detalhes, como posso definir as minhas políticas de O365 através do VIRTUAL WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)


## <a name="next-steps"></a>Passos seguintes

[Implementar um parceiro de segurança que ofereça num centro seguro, utilizando o Azure Firewall Manager](deploy-trusted-security-partner.md).
