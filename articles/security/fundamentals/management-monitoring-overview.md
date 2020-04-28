---
title: Funcionalidades de gestão e monitorização de segurança - Microsoft Azure / Microsoft Docs
description: Este artigo fornece uma visão geral das funcionalidades e serviços de segurança que a Azure fornece para ajudar na gestão e monitorização dos serviços de nuvem Azure e das máquinas virtuais.
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
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73162742"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Visão geral da gestão e monitorização da segurança azure
Este artigo fornece uma visão geral das funcionalidades e serviços de segurança que a Azure fornece para ajudar na gestão e monitorização dos serviços de nuvem Azure e das máquinas virtuais.

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções

O Controlo de Acesso baseado em funções (RBAC) fornece uma gestão detalhada do acesso aos recursos do Azure. Ao utilizar o RBAC, só pode conceder às pessoas a quantidade de acesso de que necessitam para desempenharem os seus trabalhos. O RBAC também pode ajudá-lo a garantir que quando as pessoas saem da organização, perdem o acesso aos recursos na nuvem.

Saiba mais:

* [Blog da equipa de Diretório Ativo no RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Controlo de Acesso Baseado em Funções do Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Com o Azure, pode utilizar software antimalware de grandes fornecedores de segurança como microsoft, Symantec, Trend Micro, McAfee e Kaspersky. Este software ajuda a proteger as suas máquinas virtuais de ficheiros maliciosos, adware e outras ameaças.

O Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais oferece-lhe a capacidade de instalar um agente antimalware tanto para papéis PaaS como para máquinas virtuais. Com base na Proteção de Endpoint do System Center, esta funcionalidade traz tecnologia de segurança comprovada no local para a nuvem.

Também oferecemos uma integração profunda para os produtos [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) e [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) da Trend na plataforma Azure. Deep Security é uma solução antivírus, e o SecureCloud é uma solução de encriptação. A Deep Security é implantada dentro de VMs através de um modelo de extensão. Ao utilizar o portal Azure UI e PowerShell, pode optar por utilizar a Deep Security dentro de novos VMs que estão a ser fiados ou vMs existentes que já estão implantados.

A Symantec Endpoint Protection (SEP) também é apoiada no Azure. Através da integração do portal, pode especificar que pretende utilizar o SEP num VM. O SEP pode ser instalado num novo VM através do portal Azure, ou pode ser instalado num VM existente via PowerShell.

Saiba mais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais](antimalware.md)
* [Como instalar e configurar a Trend Micro Deep Security como um serviço num VM windows](/azure/virtual-machines/windows/classic/install-trend)
* [Como instalar e configurar a Proteção de Endpoint Symantec num VM do Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Novas opções antimalware para proteger máquinas virtuais azure](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A autenticação multi-factor Azure é um método de autenticação que requer a utilização de mais de um método de verificação. Adiciona uma segunda camada crítica de segurança aos insines e transações dos utilizadores.

A Autenticação Multi-Factor ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um processo simples de iniciar sessão. Fornece autenticação forte através de um leque de opções de verificação (chamada telefónica, mensagem de texto ou código de notificação ou verificação de aplicações móveis) e fichas de JURAMENTO de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Multi-Factor Authentication do Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Como funciona o Multi-Factor Authentication do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Pode utilizar o Azure ExpressRoute para estender as suas redes no local ao Microsoft Cloud através de uma ligação privada dedicada que é facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços de cloud da Microsoft, tais como Azure, Office 365 e CRM Online. A conectividade pode ser de:

* Uma rede qualquer para qualquer (VPN IP).
* Uma rede Ethernet ponto a ponto.
* Uma ligação cruzada virtual através de um fornecedor de conectividade numa instalação de co-localização.

As ligações ExpressRoute não passam pela internet pública. Podem oferecer mais fiabilidade, velocidades mais rápidas, latências mais baixas e maior segurança do que as ligações típicas através da internet.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual

Os gateways VPN, também chamados gateways de rede virtual Azure, são usados para enviar tráfego de rede entre redes virtuais e locais no local. São também utilizados para enviar tráfego entre várias redes virtuais dentro do Azure (rede para rede). Os gateways VPN proporcionam uma conectividade transversal segura entre o Azure e a sua infraestrutura.

Saiba mais:

* [Sobre gateways VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Visão geral da segurança da rede Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou outras aplicações SaaS. Isto muitas vezes significa que as organizações lhes dão acesso privilegiado permanente no Azure Ative Directory (Azure AD).

Este é um risco crescente de segurança para os recursos hospedados na nuvem porque as organizações não conseguem monitorizar suficientemente o que esses utilizadores estão a fazer com o seu acesso privilegiado. Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, essa violação pode afetar a segurança total da nuvem de uma organização. A Azure AD Privileged Identity Management ajuda a resolver este risco, reduzindo o tempo de exposição dos privilégios e aumentando a visibilidade no uso.  

A Gestão de Identidade Privilegiada introduz o conceito de administrador temporário para um papel ou acesso "a tempo" do administrador. Este tipo de administração é um utilizador que precisa de concluir um processo de ativação para esse papel atribuído. O processo de ativação altera a atribuição do utilizador a uma função em Azure AD de inativo para ativo, durante um determinado período de tempo.

Saiba mais:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

A Azure AD Identity Protection proporciona uma visão consolidada de atividades suspeitas de entrada e potenciais vulnerabilidades para ajudar a proteger o seu negócio. A Proteção de Identidade deteta atividades suspeitas para utilizadores e identidades privilegiadas (administração), com base em sinais como:

* Ataques à força bruta.
* Credenciais vazadas.
* Inscrições em locais desconhecidos e dispositivos infetados.

Ao fornecer notificações e remediação recomendada, a Proteção de Identidade ajuda a mitigar riscos em tempo real. Calcula a gravidade do risco do utilizador. Pode configurar políticas baseadas em riscos para ajudar automaticamente a salvaguardar o acesso à aplicação de ameaças futuras.

Saiba mais:

* [Proteção de Identidade do Diretório Ativo Azure](/azure/active-directory/active-directory-identityprotection)
* [Canal 9: Azure AD e Mostra de Identidade: Pré-visualização da Proteção de Identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de Segurança

O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças. O Centro de Segurança dá-lhe maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure. Ajuda a detetar ameaças que de outra forma podem passar despercebidas e trabalha com um vasto ecossistema de soluções de segurança.

O Security Center ajuda-o a otimizar e monitorizar a segurança dos seus recursos Azure:

* Permitindo-lhe definir políticas para os seus recursos de subscrição Azure de acordo com:
  * As necessidades de segurança da sua empresa.
  * O tipo de aplicações ou sensibilidade dos dados em cada subscrição.
* Monitorizando o estado das suas máquinas virtuais Azure, networking e aplicações.
* Fornecendo uma lista de alertas de segurança prioritários, incluindo alertas de soluções parceiras integradas. Também fornece a informação de que precisa para investigar rapidamente um ataque e recomendações sobre como corrigi-lo.

Saiba mais:

* [Introdução ao Centro de Segurança do Azure](../../security-center/security-center-intro.md)
* [Melhore a sua pontuação segura no Centro de Segurança Azure](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Gráfico de segurança inteligente

O Smart Security Graph fornece proteção contra ameaças em tempo real em produtos e serviços da Microsoft. Usa análises avançadas que ligam uma quantidade massiva de dados de inteligência e segurança de ameaças para fornecer insights que podem fortalecer a segurança organizacional. A Microsoft usa análises avançadas — processando mais de 450 mil milhões de autenticações por mês, digitalizando 400 mil milhões de e-mails para malware e phishing, e atualizando mil milhões de dispositivos — para fornecer informações mais ricas. Estas informações podem ajudar a sua organização a detetar e responder rapidamente a ataques.

* [Gráfico de segurança inteligente](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o modelo de [responsabilidade partilhada](shared-responsibility.md) e quais as tarefas de segurança que são tratadas pela Microsoft e quais as tarefas que são tratadas por si.

Para obter mais informações sobre gestão de segurança, consulte [a gestão de segurança no Azure.](management.md)
