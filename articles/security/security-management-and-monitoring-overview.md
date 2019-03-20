---
title: Gestão de segurança do Azure e a descrição geral da monitorização | Documentos da Microsoft
description: Este artigo fornece uma descrição geral dos recursos de segurança e serviços fornecidos pelo Azure para ajudar no gerenciamento e monitoramento de serviços cloud do Azure e máquinas virtuais.
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: f79f94c277b02a9f377b90bf74763ac617f65c16
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892903"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Gestão de segurança do Azure e a descrição geral da monitorização

O Azure oferece mecanismos de segurança para facilitar a gestão e monitorização de serviços cloud do Azure e máquinas virtuais (VMs). Este artigo fornece uma visão geral desses principais recursos de segurança e serviços. São fornecidas hiperligações para artigos que fornecem detalhes de cada, para que possa saber mais.

A segurança dos seus serviços cloud da Microsoft é uma parceria e uma responsabilidade partilhada entre e a Microsoft. A Microsoft é responsável pela plataforma do Azure e a segurança física dos seus datacenters (ao utilizar proteções de segurança como portas de entrada de destaque bloqueado, limites e proteção). O Azure fornece fortes níveis de segurança de cloud na camada de software que satisfaça as necessidades de segurança, privacidade e conformidade dos seus clientes.

Possui seus dados e identidades, a responsabilidade de protegê-los, a segurança dos seus recursos no local e a segurança dos componentes da nuvem através do qual tem controle. Microsoft dá-lhe capacidades para ajudar a proteger os seus dados e aplicações e controlos de segurança. O grau de responsabilidade de segurança baseia-se no tipo de serviço em nuvem.

A tabela a seguir resume o saldo de responsabilidade entre a Microsoft e o cliente.

![Responsabilidade partilhada][1]

Para obter mais informações sobre a gestão de segurança, consulte [gestão de segurança no Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções

Controlo de acesso baseado em funções (RBAC) proporciona gestão de acessos detalhada para recursos do Azure. Ao utilizar o RBAC, pode conceder as pessoas apenas a quantidade de acesso que necessitam para desempenhar as suas funções. RBAC pode também ajudar a garantir que quando as pessoas deixam a organização, eles perderem o acesso a recursos na cloud.

Saiba mais:

* [Blogue da equipa do Active Directory em RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controlo de acesso baseado em função do Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar o software antimalware dos maiores fornecedores de segurança, tais como Microsoft, Symantec, Trend Micro, McAfee e a Kaspersky. Este software ajuda a proteger as suas máquinas virtuais contra ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para serviços Cloud do Azure e máquinas virtuais oferece a capacidade de instalar um agente de antimalware para as funções de PaaS e máquinas virtuais. Com base no System Center Endpoint Protection, esta funcionalidade permite locais comprovadas tecnologia de segurança na cloud.

Também oferecemos uma integração profunda da tendência [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produtos na plataforma do Azure. Segurança avançada é uma solução de antivírus e SecureCloud é uma solução de encriptação. Deep Security é implementado dentro de VMs por meio de um modelo de extensão. Ao utilizar o portal do Azure da interface do Usuário e o PowerShell, pode optar por utilizar o Deep Security em novas VMs que estão a ser rotacionadas ou em VMs existentes que já estão implementadas.

Symantec Endpoint Protection (SEP) também é suportado no Azure. Por meio da integração do portal, pode especificar que pretende usar SEP numa VM. Setembro de pode ser instalado numa nova VM através do portal do Azure ou pode ser instalado numa VM existente através do PowerShell.

Saiba mais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [O Microsoft Antimalware para serviços Cloud do Azure e máquinas virtuais](azure-security-antimalware.md)
* [Como instalar e configurar o Trend Micro Deep Security como serviço numa VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Novas opções de Antimalware para a proteção de máquinas virtuais do Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

O Azure multi-factor Authentication é um método de autenticação que requer a utilização de mais do que um método de verificação. Ele adiciona uma segunda camada crítica de segurança para inícios de sessão de utilizador e transações. 

Multi-factor Authentication ajuda a salvaguardar o acesso a dados e aplicações, satisfazendo um processo de início de sessão simples. Proporciona uma autenticação segura através de um conjunto de opções de verificação (chamada telefónica, mensagem de texto ou notificação ou verificação de código da aplicação móvel) e tokens OATH de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Multi-Factor Authentication do Azure?](../active-directory/authentication/multi-factor-authentication.md)
* [Como funciona o Azure multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Pode utilizar o Azure ExpressRoute para expandir as redes no local para a Cloud da Microsoft através de uma ligação privada dedicada, que é facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços cloud da Microsoft, como o Azure, Office 365 e CRM Online. A conectividade pode ser a partir de:

* Uma rede de qualquer a qualquer (VPN de IP).
* Uma rede de Ethernet ponto a ponto.
* Uma ligação cruzada virtual através de um fornecedor de conectividade numa instalação de colocalização. 

As ligações ExpressRoute não passam pela internet pública. Eles podem oferecem mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações normais pela internet.

Saiba mais:

* [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Gateways de VPN, também denominadas gateways de rede virtual do Azure, são utilizados para enviar tráfego de rede entre redes virtuais e localizações no local. Também são utilizados para enviar tráfego entre várias redes virtuais no Azure (rede de rede). Gateways de VPN fornecem conectividade segura em vários locais entre o Azure e a sua infraestrutura.

Saiba mais:

* [Sobre os gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Descrição geral da segurança de rede do Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Por vezes, os utilizadores precisam realizar operações privilegiadas em recursos do Azure ou outras aplicações SaaS. Isso geralmente significa que as organizações lhes dar acesso com privilégios permanente no Azure Active Directory (Azure AD). 

Este é um risco de segurança de cada vez maior para recursos alojados na cloud, uma vez que as organizações suficientemente não podem monitorizar o que os utilizadores estão a fazer com o acesso privilegiado. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, que uma violação pode afetar a segurança de cloud global da organização. O Azure AD Privileged Identity Management ajuda a resolver este risco ao reduzir o tempo de exposição dos privilégios e aumentar a visibilidade na utilização.  

Privileged Identity Management introduz o conceito de um administrador temporário para uma função ou "just-in-time" acesso de administrador. Esse tipo de administrador é um utilizador que precisa para concluir um processo de ativação para que função atribuída. O processo de ativação é alterado a atribuição do utilizador a uma função no Azure AD de período inativo para o Active Directory, para um período de tempo especificado.

Saiba mais:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

O Azure AD Identity Protection fornece uma vista consolidada de atividades de início de sessão suspeitas e potenciais vulnerabilidades para o ajudar a proteger a sua empresa. Proteção de identidade Deteta atividades suspeitas para utilizadores e identidades com privilégios (administrador), com base em sinais como:

* Ataques de força bruta.
* Fuga de credenciais.
* Inícios de sessão de localizações desconhecidas e em dispositivos infetados.

Ao fornecer notificações e a remediação recomendada, Identity Protection ajuda a reduzir os riscos em tempo real. Ele calcula a gravidade de risco do utilizador. Pode configurar políticas baseadas no risco para o ajudar automaticamente a aplicação de salvaguardar contra ameaças futuras de acesso.

Saiba mais:

* [O Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: O Azure AD e mostrar de identidade: Pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de Segurança

Centro de segurança do Azure ajuda-o a prevenir, detetar e responder a ameaças. Fornece o Centro de segurança maior visibilidade e controlo sobre a segurança dos seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. Ele ajuda a detetar ameaças que caso contrário podem passar despercebidas e funcionam com um ecossistema abrangente de soluções de segurança.

Centro de segurança ajuda-o a otimizar e monitorizar a segurança dos seus recursos do Azure por:

* Permitindo-lhe definir políticas para os seus recursos de subscrição do Azure de acordo com:
  * Segurança da sua empresa precisa.
  * O tipo de aplicações ou sensibilidade dos dados em cada subscrição.
* Monitorizar o estado dos seus aplicativos, redes e máquinas virtuais do Azure.
* Fornecer uma lista de alertas de segurança prioritários, incluindo alertas de soluções de parceiros integradas. Ele também fornece as informações necessárias para investigar rapidamente e recomendações sobre como resolvê-lo de um ataque.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../security-center/security-center-intro.md)
* [Melhorar a sua pontuação segura no Centro de segurança do Azure](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Gráfico de segurança inteligente

Gráfico de segurança inteligente oferece proteção de ameaças em tempo real em produtos e serviços Microsoft. Utiliza análise avançada com ligação uma grande quantidade de dados de segurança e de inteligência de ameaças para fornecer informações que podem reforçar a segurança organizacional. A Microsoft utiliza análise avançada — mais de 450 milhares de milhões de autenticações por mês de processamento, análise de e-mails de 400 milhares de milhões de software maligno e phishing e atualizar mil milhões de dispositivos — para fornecer informações mais ricas. Estes insights podem ajudar a sua organização a detetar e responder de forma mais rápidas aos ataques.

* [Gráfico de segurança inteligente](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

