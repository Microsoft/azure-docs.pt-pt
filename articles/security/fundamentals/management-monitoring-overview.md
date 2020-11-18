---
title: Funcionalidades de segurança de gestão e monitorização - Microsoft Azure Microsoft Docs
description: Este artigo fornece uma visão geral das funcionalidades e serviços de segurança que a Azure fornece para ajudar na gestão e monitorização dos serviços de nuvem Azure e máquinas virtuais.
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
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 3520cbafc0e9eebfd6057fe92579a45873b25734
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698765"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral da gestão e monitorização da segurança da Azure
Este artigo fornece uma visão geral das funcionalidades e serviços de segurança que a Azure fornece para ajudar na gestão e monitorização dos serviços de nuvem Azure e máquinas virtuais.

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure

O controlo de acesso baseado em funções Azure (Azure RBAC) fornece uma gestão detalhada do acesso aos recursos da Azure. Ao utilizar o Azure RBAC, pode conceder às pessoas apenas a quantidade de acesso que precisam para desempenharem os seus trabalhos. O Azure RBAC também pode ajudá-lo a garantir que quando as pessoas deixam a organização, perdem o acesso aos recursos na nuvem.

Saiba mais:

* [Blog de equipe de diretório ativo no Azure RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar software antimalware de grandes fornecedores de segurança como Microsoft, Symantec, Trend Micro, McAfee e Kaspersky. Este software ajuda a proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Azure Cloud Services e Máquinas Virtuais oferece-lhe a capacidade de instalar um agente antimalware tanto para funções PaaS como para máquinas virtuais. Com base na Proteção de Pontos De ponta do System Center, esta funcionalidade traz tecnologia de segurança comprovada no local para a nuvem.

Também oferecemos uma integração profunda para os produtos [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) da Trend na plataforma Azure. Deep Security é uma solução antivírus, e o SecureCloud é uma solução de encriptação. A Deep Security é implantada dentro de VMs através de um modelo de extensão. Ao utilizar o portal Azure UI e PowerShell, pode optar por utilizar a Deep Security dentro de novos VMs que estão a ser girados, ou VMs existentes que já estão implantados.

A Symantec Endpoint Protection (SEP) também é apoiada no Azure. Através da integração do portal, pode especificar que pretende utilizar o SEP num VM. O SEP pode ser instalado num novo VM através do portal Azure, ou pode ser instalado num VM existente via PowerShell.

Saiba mais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware para Azure Cloud Services e Máquinas Virtuais](antimalware.md)
* [Como instalar e configurar a Trend Micro Deep Security como um serviço num VM do Windows](../../virtual-machines/extensions/trend.md)
* [Como instalar e configurar a proteção de ponto final Symantec num VM do Windows](../../virtual-machines/extensions/symantec.md)
* [Novas opções de antimalware para proteger máquinas virtuais do Azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A autenticação multi-factor Azure é um método de autenticação que requer a utilização de mais do que um método de verificação. Adiciona uma segunda camada crítica de segurança às entradas e transações do utilizador.

A Autenticação Multi-Factor ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um simples processo de inscrição. Fornece autenticação forte através de uma gama de opções de verificação (chamada telefónica, mensagem de texto ou código de notificação ou verificação de aplicações móveis) e fichas de OATH de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Multi-Factor Authentication do Azure?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Como funciona o Multi-Factor Authentication do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Pode utilizar o Azure ExpressRoute para estender as suas redes no local para a Microsoft Cloud através de uma ligação privada dedicada que é facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços na nuvem da Microsoft como a Azure, Microsoft 365 e CRM Online. A conectividade pode ser a partir de:

* Uma rede any-to-any (IP VPN).
* Uma rede Ethernet ponto a ponto.
* Uma ligação cruzada virtual através de um fornecedor de conectividade numa instalação de co-localização.

As ligações ExpressRoute não passam pela internet pública. Podem oferecer mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da internet.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Gateways VPN, também chamados gateways de rede virtual Azure, são usados para enviar tráfego de rede entre redes virtuais e locais no local. Também são usados para enviar tráfego entre várias redes virtuais dentro do Azure (rede para rede). Gateways VPN fornecem conectividade transversal segura entre Azure e sua infraestrutura.

Saiba mais:

* [Sobre gateways VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Visão geral da segurança da rede Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou outras aplicações SaaS. Isto significa frequentemente que as organizações lhes dão acesso privilegiado permanente no Azure Ative Directory (Azure AD).

Este é um risco crescente de segurança para os recursos hospedados na nuvem porque as organizações não conseguem monitorizar suficientemente o que esses utilizadores estão a fazer com o seu acesso privilegiado. Além disso, se uma conta de utilizador com acesso privilegiado estiver comprometida, essa violação pode afetar a segurança global da nuvem de uma organização. A Azure AD Gestão de Identidade Privilegiada ajuda a resolver este risco reduzindo o tempo de exposição dos privilégios e aumentando a visibilidade para o uso.  

Gestão de Identidade Privilegiada introduz o conceito de administrador temporário para um papel ou acesso de administrador "just in time". Este tipo de administração é um utilizador que precisa de completar um processo de ativação para o papel atribuído. O processo de ativação altera a atribuição do utilizador a uma função no AD Azure de inativo para ativo, por um período de tempo especificado.

Saiba mais:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

A Azure AD Identity Protection fornece uma visão consolidada de atividades suspeitas de inscrição e potenciais vulnerabilidades para ajudar a proteger o seu negócio. A Proteção de Identidade deteta atividades suspeitas para utilizadores e identidades privilegiadas (administração), com base em sinais como:

* Ataques à força bruta.
* Credenciais vazadas.
* Entradas de locais desconhecidos e dispositivos infetados.

Ao fornecer notificações e remediação recomendada, a Proteção de Identidade ajuda a mitigar os riscos em tempo real. Calcula a gravidade do risco do utilizador. Pode configurar políticas baseadas no risco para ajudar automaticamente a salvaguardar o acesso à aplicação de ameaças futuras.

Saiba mais:

* [Proteção de Identidade do Diretório Ativo Azure](../../active-directory/identity-protection/overview-identity-protection.md)
* [Canal 9: Azure AD e Identity Show: Preview de Proteção de Identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de Segurança

O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças. O Centro de Segurança dá-lhe uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições Azure. Ajuda a detetar ameaças que de outra forma poderiam passar despercebidas, e trabalha com um amplo ecossistema de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança dos seus recursos Azure através de:

* Permitindo-lhe definir políticas para os seus recursos de subscrição Azure de acordo com:
  * As necessidades de segurança da sua empresa.
  * O tipo de aplicações ou sensibilidade dos dados em cada subscrição.
* Monitorização do estado das suas máquinas virtuais Azure, networking e aplicações.
* Fornecendo uma lista de alertas de segurança prioritários, incluindo alertas de soluções parceiras integradas. Também fornece a informação de que precisa para investigar rapidamente um ataque e recomendações sobre como remediar.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-introduction.md)
* [Melhore a sua pontuação segura no Azure Security Center](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Gráfico de segurança inteligente

O Smart Security Graph fornece proteção contra ameaças em tempo real em produtos e serviços da Microsoft. Usa análises avançadas que ligam uma quantidade massiva de dados de inteligência e segurança para fornecer insights que podem fortalecer a segurança organizacional. A Microsoft usa análises avançadas — processando mais de 450 mil milhões de autenticações por mês, digitalizando 400 mil milhões de e-mails para malware e phishing, e atualizando mil milhões de dispositivos — para fornecer insights mais ricos. Estas informações podem ajudar a sua organização a detetar e responder rapidamente a ataques.

* [Gráfico de segurança inteligente](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Passos Seguintes
Conheça o [modelo de responsabilidade partilhada](shared-responsibility.md) e quais as tarefas de segurança que a Microsoft trata e quais as tarefas que são tratadas por si.

Para obter mais informações sobre a gestão de segurança, consulte [a gestão de segurança em Azure.](management.md)