---
title: O que são os fornecedores de parceiros de segurança Azure Firewall Manager?
description: Saiba mais sobre os fornecedores de parceiros de segurança do Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563609"
---
# <a name="what-are-security-partner-providers"></a>O que são fornecedores de parceiros de segurança?

*Os fornecedores de parceiros* de segurança no Azure Firewall Manager permitem-lhe utilizar a sua segurança familiar, de melhor raça e terceiros como um serviço (SECaaS) para proteger o acesso à Internet para os seus utilizadores.

Com uma configuração rápida, pode proteger um hub com um parceiro de segurança suportado, e encaminhar e filtrar o tráfego de Internet a partir das suas Redes Virtuais (VNets) ou localizações de filiais dentro de uma região. Pode fazê-lo com gestão automatizada de rotas, sem configurar e gerir rotas definidas pelo utilizador (UDRs).

Pode implementar centros seguros configurados com o parceiro de segurança à sua escolha em várias regiões do Azure para obter conectividade e segurança para os seus utilizadores em qualquer parte do mundo nessas regiões. Com a capacidade de utilizar a oferta do parceiro de segurança para o tráfego de aplicações Internet/SaaS, e a Azure Firewall para tráfego privado nos centros seguros, pode agora começar a construir a sua vantagem de segurança no Azure que está perto dos seus utilizadores e aplicações distribuídos globalmente.

Os parceiros de segurança suportados são **ZScaler,** **Check Point** (pré-visualização) e **iboss** (pré-visualização).

![Fornecedores de parceiros de segurança](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Cenários-chave

Pode utilizar os parceiros de segurança para filtrar o tráfego da Internet em seguintes cenários:

- Rede Virtual (VNet) para internet

   Aproveite a proteção avançada da Internet consciente do utilizador para as suas cargas de trabalho em nuvem em execução no Azure.

- Ramo para internet

   Aproveite a conectividade Azure e a distribuição global para adicionar facilmente a filtragem NSaaS de terceiros para os cenários da Internet. Pode construir a sua rede global de trânsito e borda de segurança utilizando a Azure Virtual WAN.

São apoiados os seguintes cenários:
- VNet/Branch para internet através de um fornecedor de parceiros de segurança e do outro tráfego (falou com falou, falou com a filial, ramo para falar) via Azure Firewall.
- VNet/Sucursal para internet através de fornecedor de parceiros de segurança

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

Além disso, as ligações do Office 365 são encriptadas para privacidade e utilização de protocolos eficientes e proprietários por razões de desempenho. Isto torna impraticável e impactante sujeitar essas ligações a soluções tradicionais de segurança ao nível da rede. Por estas razões, recomendamos vivamente que os clientes enviem o tráfego do Office 365 diretamente das agências, antes de enviarem o resto do tráfego através do Azure. A Microsoft estabeleceu uma parceria com vários fornecedores de soluções SD-WAN, que se integram com o Azure e o Office 365 e facilitam aos clientes a fuga direta e local da Internet do Office 365. Para mais [detalhes, como posso definir as minhas políticas de O365 através do VIRTUAL WAN?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>Próximos passos

[Implementar um parceiro de segurança que ofereça num centro seguro, utilizando o Azure Firewall Manager](deploy-trusted-security-partner.md).
