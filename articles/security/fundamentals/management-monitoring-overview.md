---
title: Recursos de segurança de gerenciamento e monitoramento-Microsoft Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos e serviços de segurança que o Azure fornece para auxiliar no gerenciamento e monitoramento de máquinas virtuais e serviços de nuvem do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 9ed2d00d6f229d958e0df024c70381051840548c
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727007"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral de monitoramento e gerenciamento de segurança do Azure
Este artigo fornece uma visão geral dos recursos e serviços de segurança que o Azure fornece para auxiliar no gerenciamento e monitoramento de máquinas virtuais e serviços de nuvem do Azure.

## <a name="shared-responsibility"></a>Responsabilidade partilhada

A segurança de seus serviços de nuvem da Microsoft é uma parceria e uma responsabilidade compartilhada entre você e a Microsoft. A Microsoft é responsável pela plataforma Azure e pela segurança física de seus data centers (usando proteções de segurança, como portas de entrada de selo bloqueadas, limites e proteções). O Azure fornece níveis fortes de segurança na nuvem na camada de software que atende às necessidades de segurança, privacidade e conformidade de seus clientes.

Você possui seus dados e identidades, a responsabilidade de protegê-los, a segurança de seus recursos locais e a segurança dos componentes de nuvem sobre os quais você tem controle. A Microsoft oferece recursos e controles de segurança para ajudá-lo a proteger seus dados e aplicativos. Seu grau de responsabilidade pela segurança é baseado no tipo de serviço de nuvem.

O gráfico a seguir resume o equilíbrio de responsabilidade entre a Microsoft e o cliente.

![Responsabilidade partilhada](./media/management-monitoring-overview/shared-responsibility.png)

Para obter mais informações sobre gerenciamento de segurança, consulte [Gerenciamento de segurança no Azure](management.md).

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções

O RBAC (controle de acesso baseado em função) fornece gerenciamento de acesso detalhado para recursos do Azure. Usando o RBAC, você pode conceder às pessoas apenas a quantidade de acesso de que eles precisam para executar seus trabalhos. O RBAC também pode ajudá-lo a garantir que quando as pessoas deixam a organização, elas perdem o acesso aos recursos na nuvem.

Saiba mais:

* [Blog da equipe Active Directory no RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Com o Azure, você pode usar o software antimalware dos principais fornecedores de segurança, como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky. Esse software ajuda a proteger suas máquinas virtuais contra arquivos mal-intencionados, adware e outras ameaças.

O Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais oferece a capacidade de instalar um agente antimalware para ambas as funções de PaaS e máquinas virtuais. Com base no System Center Endpoint Protection, esse recurso traz a tecnologia de segurança local comprovada para a nuvem.

Também oferecemos integração profunda para os produtos de [segurança profunda](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) da tendência na plataforma Azure. A profunda segurança é uma solução antivírus, e SecureCloud é uma solução de criptografia. A segurança profunda é implantada dentro de VMs por meio de um modelo de extensão. Usando a interface do usuário do portal do Azure e o PowerShell, você pode optar por usar a segurança profunda dentro de novas VMs que estão sendo giradas ou VMs existentes que já foram implantadas.

O Symantec Endpoint Protection (SEP) também tem suporte no Azure. Por meio da integração do portal, você pode especificar que pretende usar SEP em uma VM. O SEP pode ser instalado em uma nova VM por meio do portal do Azure, ou pode ser instalado em uma VM existente por meio do PowerShell.

Saiba mais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](antimalware.md)
* [Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Como instalar e configurar o Endpoint Protection da Symantec em uma VM do Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Novas opções de antimalware para proteger as máquinas virtuais do Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A autenticação multifator do Azure é um método de autenticação que requer o uso de mais de um método de verificação. Ele adiciona uma segunda camada crítica de segurança a entradas e transações do usuário.

A autenticação multifator ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda do usuário por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação (chamada telefônica, mensagem de texto ou notificação de aplicativo móvel ou código de verificação) e tokens OATH de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Multi-Factor Authentication do Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Como funciona a autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Você pode usar o Azure ExpressRoute para estender suas redes locais para o Microsoft Cloud por meio de uma conexão privada dedicada que é facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Azure, Office 365 e CRM Online. A conectividade pode ser de:

* Uma rede de qualquer para qualquer (IP VPN).
* Uma rede Ethernet ponto a ponto.
* Uma conexão cruzada virtual por meio de um provedor de conectividade em uma instalação de colocalização.

As conexões do ExpressRoute não passam pela Internet pública. Eles podem oferecer mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança do que as conexões típicas pela Internet.

Saiba mais:

* [Visão geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Os gateways de VPN, também chamados de gateways de rede virtual do Azure, são usados para enviar tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure (rede para rede). Os gateways de VPN fornecem conectividade segura entre locais entre o Azure e sua infraestrutura.

Saiba mais:

* [Sobre gateways de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Visão geral da segurança de rede do Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Às vezes, os usuários precisam executar operações privilegiadas em recursos do Azure ou outros aplicativos SaaS. Isso geralmente significa que as organizações dão a eles acesso com privilégios permanentes no Azure Active Directory (Azure AD).

Esse é um risco crescente de segurança para recursos hospedados na nuvem, pois as organizações não podem monitorar suficientemente o que esses usuários estão fazendo com o acesso privilegiado. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa violação poderá afetar a segurança geral da nuvem de uma organização. Azure AD Privileged Identity Management ajuda a resolver esse risco reduzindo o tempo de exposição dos privilégios e aumentando a visibilidade do uso.  

Privileged Identity Management apresenta o conceito de administrador temporário para uma função ou acesso de administrador "Just-in-time". Esse tipo de administrador é um usuário que precisa concluir um processo de ativação para essa função atribuída. O processo de ativação altera a atribuição do usuário para uma função no Azure AD de inativo para ativo, por um período de tempo especificado.

Saiba mais:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection fornece uma exibição consolidada de atividades de entrada suspeitas e possíveis vulnerabilidades para ajudar a proteger seus negócios. A proteção de identidade detecta atividades suspeitas para usuários e identidades privilegiadas (administrador), com base em sinais como:

* Ataques de força bruta.
* Credenciais vazadas.
* Entradas de locais desconhecidos e dispositivos infectados.

Ao fornecer notificações e correção recomendada, a proteção de identidade ajuda a reduzir os riscos em tempo real. Ele calcula a severidade de risco do usuário. Você pode configurar políticas baseadas em risco para ajudar automaticamente a proteger o acesso do aplicativo contra ameaças futuras.

Saiba mais:

* [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)
* [Canal 9: Azure AD e identidade mostram: Versão prévia da proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de Segurança

A central de segurança do Azure ajuda você a prevenir, detectar e responder a ameaças. A central de segurança oferece maior visibilidade e controle sobre a segurança de seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ele ajuda a detectar ameaças que poderiam passar despercebidas e trabalha com um amplo ecossistema de soluções de segurança.

A central de segurança ajuda a otimizar e monitorar a segurança de seus recursos do Azure:

* Permitindo que você defina políticas para seus recursos de assinatura do Azure de acordo com:
  * As necessidades de segurança de sua empresa.
  * O tipo de aplicativos ou a sensibilidade dos dados em cada assinatura.
* Monitorando o estado de suas máquinas virtuais, rede e aplicativos do Azure.
* Fornecendo uma lista de alertas de segurança priorizados, incluindo alertas de soluções de parceiros integradas. Ele também fornece as informações de que você precisa para investigar rapidamente um ataque e recomendações sobre como corrigi-lo.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-intro.md)
* [Melhorar sua pontuação segura na central de segurança do Azure](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Gráfico de Segurança Inteligente

O Gráfico de Segurança Inteligente fornece proteção contra ameaças em tempo real em produtos e serviços da Microsoft. Ele usa análises avançadas que vinculam uma grande quantidade de inteligência de ameaças e dados de segurança para fornecer informações que podem reforçar a segurança organizacional. A Microsoft usa análise avançada — processando mais de 450.000.000.000 autenticações por mês, verificando 400.000.000.000 emails para malware e phishing e atualizando dispositivos 1.000.000.000 — para fornecer informações mais ricas. Estes insights podem ajudar a sua organização a detetar e responder de forma mais rápidas aos ataques.

* [Gráfico de Segurança Inteligente](https://www.microsoft.com/security/intelligence)

