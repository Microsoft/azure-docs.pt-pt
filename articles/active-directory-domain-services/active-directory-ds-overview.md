---
title: Descrição geral dos serviços de domínio do Active Directory do Azure | Documentos da Microsoft
description: Descrição geral dos serviços de domínio do Active Directory do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: ergreenl
ms.openlocfilehash: c9833ac1dd6f5b046a89a1c2d9007e2cd5c9f813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416709"
---
# <a name="azure-active-directory-ad-domain-services"></a>Serviços de domínio do Azure Active Directory (AD)
## <a name="overview"></a>Descrição geral
Serviços de infraestrutura do Azure permitem-lhe implementar uma vasta gama de soluções de computação de forma ágil. Com máquinas de virtuais do Azure, pode implementar quase instantânea e só paga ao minuto. Usando o suporte para Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, pode implementar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operativo. Esses benefícios permitem-lhe migrar aplicativos herdados, implementados no local para o Azure, para poupar nos custos operacionais.

Um aspecto fundamental de migração dos aplicativos no local para o Azure está a processar as necessidades de identidade desses aplicativos. Aplicações compatíveis com diretórios podem dependem de LDAP para leitura ou acesso de escrita para o diretório empresarial ou dependem da autenticação integrada do Windows (autenticação Kerberos ou NTLM) para autenticar os utilizadores finais. Linha de negócio (LOB) em execução no Windows Server é normalmente implantado em computadores associados a um domínio, para que possam ser geridos com segurança usando a diretiva de grupo. Para "lift-and-shift" aplicações no local para a cloud, estas dependências na infraestrutura de identidade empresarial tem de ser resolvidos.

Freqüentemente, os administradores para uma das seguintes soluções para satisfazer as necessidades de identidade das suas aplicações implementadas no Azure:

* Implemente uma ligação de VPN de site a site entre cargas de trabalho em execução nos serviços de infraestrutura do Azure e o diretório empresarial no local.
* Amplie a infraestrutura de domínio/floresta de AD empresarial ao configurar os controladores de domínio de réplica com máquinas virtuais do Azure.
* Implemente um domínio autónomo no Azure através de controladores de domínio implementados como máquinas virtuais do Azure.

Todas essas abordagens sofrem de alto custo e a sobrecarga administrativa. Os administradores são necessários para implementar controladores de domínio utilizando as máquinas virtuais no Azure. Além disso, eles precisam gerenciar, proteger, patch, monitorizar, de cópia de segurança, e estas máquinas virtuais de resolução de problemas. A dependência em ligações de VPN para o diretório no local faz com que as cargas de trabalho implementadas no Azure a ser vulneráveis a falhas de rede transitórios ou falhas. Estas falhas de rede por sua vez resultam em menor tempo de atividade e fiabilidade reduzida para estas aplicações.

Projetamos o Azure AD Domain Services para fornecer uma alternativa mais fácil.

### <a name="watch-an-introductory-video"></a>Ver um vídeo introdutório

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Introdução ao Azure AD Domain Services

O Azure AD Domain Services fornece serviços de domínio geridos, tais como associação a um domínio, autenticação de Kerberos/NTLM de política, LDAP, do grupo que são totalmente compatíveis com o Windows Server Active Directory. Pode consumir estes serviços de domínio sem a necessidade de implementar, gerir e corrigir os controladores de domínio na cloud. O Azure AD Domain Services integra-se com o seu inquilino do Azure AD existente, que a torna possível que os utilizadores iniciem sessão com as credenciais da empresa. Além disso, pode utilizar grupos existentes e contas de utilizador para proteger o acesso a recursos, que garante um mais suave "migração lift-and-shift" de recursos no local para serviços de infraestrutura do Azure.

Funcionalidade de serviços de domínio do AD do Azure funciona perfeitamente, independentemente se o seu inquilino do Azure AD é apenas na cloud ou sincronizado com o Active Directory no local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure AD Domain Services para as organizações apenas na cloud

Cloud só de inquilino do Azure AD (muitas vezes referido como "geridos inquilinos") não tem quaisquer requisitos de espaço de identidade no local. Em outras palavras, contas de usuário, senhas e associações de grupo são todos nativas para a cloud - ou seja, criados e geridos no Azure AD. Considere por um momento que a Contoso é uma cloud apenas inquilino do Azure AD. Conforme mostrado na ilustração seguinte, o administrador da Contoso tiver configurado uma rede virtual nos serviços de infraestrutura do Azure. Aplicações e cargas de trabalho do servidor são implementadas nesta rede virtual em máquinas virtuais do Azure. Uma vez que a Contoso é um inquilino apenas na cloud, todas as identidades de utilizador, as credenciais e associações a grupos são criadas e geridas no Azure AD.

![Descrição geral dos serviços de domínio do Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Do contoso administrador de TI pode ativar o Azure AD Domain Services para o respetivo inquilino do Azure AD e optar por tornar os serviços de domínio disponíveis nesta rede virtual. Por esse motivo, o Azure AD Domain Services aprovisiona um domínio gerido e disponibiliza-o na rede virtual. Todas as contas de utilizador, associações a grupos e credenciais de utilizador disponíveis no inquilino do Azure AD da Contoso também estão disponíveis neste domínio recém-criado. Esta funcionalidade permite aos utilizadores na organização iniciar sessão no domínio utilizando as credenciais da empresa - por exemplo, ao ligar remotamente a computadores associados a um domínio através do ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso a recursos no domínio utilizando as associações de grupo existente. Aplicações implementadas em máquinas virtuais na rede virtual podem utilizar funcionalidades como a associação a um domínio, LDAP leitura, LDAP bind, a autenticação NTLM e Kerberos e diretiva de grupo.

Seguem-se alguns aspetos salientes o domínio gerido aprovisionado pelo Azure AD Domain Services:

* Do contoso administrador de TI não precisa de gerir, corrigir ou monitorizar este domínio ou quaisquer controladores de domínio para este domínio gerido.
* Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, associações a grupos e credenciais do inquilino do Azure AD da Contoso estão automaticamente disponíveis neste domínio gerido.
* Uma vez que o domínio é gerido pelo Azure AD Domain Services, a Contoso administrador de TI não tem privilégios de administrador Corporativo ou de administrador de domínio neste domínio.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Serviços de domínio do Azure AD para organizações híbridas
As organizações com infraestrutura de TI híbrida consumam uma combinação de recursos de nuvem e recursos no local. Tais organizações sincronizam informações de identidade do seu diretório no local seu inquilino do Azure AD. Conforme organizações híbridas olha para migrar mais das suas aplicações no local para a nuvem, particularmente compatíveis com diretórios aplicativos herdados, o Azure AD Domain Services podem ser úteis aos mesmos.

Litware Corporation implantou [do Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md)para sincronizar informações de identidade do seu diretório no local seu inquilino do Azure AD. As informações de identidade estão sincronizadas incluem contas de utilizador, os hashes de credenciais para autenticação (sincronização de hash de palavra-passe) e as associações de grupo.

> [!NOTE]
> **Sincronização de hash de palavra-passe é obrigatória para organizações híbridas utilizar o Azure AD Domain Services**. Este requisito é porque são necessárias credenciais de usuários no domínio gerido fornecido pelos serviços de domínio do Azure AD, para autenticar estes utilizadores através de métodos de autenticação NTLM ou Kerberos.
>
>

![Para Litware Corporation dos serviços de domínio do Azure AD](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A ilustração anterior mostra como as organizações com um híbrido infra-estrutura de TI, como Litware Corporation, podem utilizar o Azure AD Domain Services. Aplicações e cargas de trabalho do servidor que requerem os serviços de domínio da litware são implementadas numa rede virtual nos serviços de infraestrutura do Azure. Da litware administrador de TI pode ativar o Azure AD Domain Services para o respetivo inquilino do Azure AD e optar por tornar disponível nesta rede virtual um domínio gerido. Como Litware é uma organização com a infraestrutura de TI híbrida, contas de utilizador, grupos e as credenciais são sincronizadas com o respetivo inquilino do Azure AD do seu diretório no local. Esta funcionalidade permite aos utilizadores iniciar sessão domínio utilizando as credenciais da empresa - por exemplo, quando ligar remotamente a computadores associados ao domínio através do ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso a recursos no domínio utilizando as associações de grupo existente. Aplicações implementadas em máquinas virtuais na rede virtual podem utilizar funcionalidades como a associação a um domínio, LDAP leitura, LDAP bind, a autenticação NTLM e Kerberos e diretiva de grupo.

Seguem-se alguns aspetos salientes o domínio gerido aprovisionado pelo Azure AD Domain Services:

* O domínio gerido é um domínio autónomo. Não é uma extensão de domínio no local da Litware.
* Da litware administrador de TI não precisa de gerir, patch, ou monitorizar controladores de domínio para este domínio gerido.
* Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, associações a grupos e credenciais a partir do diretório no local da Litware são sincronizadas com o Azure AD através do Azure AD Connect. Estas contas de utilizador, associações a grupos e credenciais estão automaticamente disponíveis no domínio gerido.
* Uma vez que o domínio é gerenciado pelo Azure AD Domain Services, Litware administrador de TI não tem privilégios de administrador Corporativo ou de administrador de domínio neste domínio.

## <a name="benefits"></a>Benefícios
Com os serviços de domínio do Azure AD, pode desfrutar das vantagens seguintes:

* **Simples** –, pode atender as necessidades de identidade das máquinas virtuais implementadas nos serviços de infraestrutura do Azure com apenas alguns cliques. Não é necessário implementar e gerir a infraestrutura de identidade no Azure ou a configuração de conectividade para a infraestrutura de identidade no local.
* **Integrada** – Azure AD Domain Services está profundamente integrado com o inquilino do Azure AD. Agora, pode utilizar o Azure AD como um diretório de integrada empresarial baseada na nuvem que atende às necessidades dos seus aplicativos modernos e de aplicativos compatíveis com diretórios tradicionais.
* **Compatível** – Azure AD Domain Services baseia-se a infraestrutura de nível empresarial comprovado do Windows Server Active Directory. Portanto, seus aplicativos podem contar com um maior grau de compatibilidade com funcionalidades do Windows Server Active Directory. Nem todas as funcionalidades disponíveis no Windows Server AD estão atualmente disponíveis no Azure AD Domain Services. No entanto, as funcionalidades disponíveis são compatíveis com os recursos do Windows Server AD correspondentes que confia na sua infraestrutura no local. As capacidades de associação LDAP, Kerberos, NTLM, diretiva de grupo e domínio constituem uma oferta de madura que foi testada e refinada sobre várias versões do Windows Server.
* **Económico** – com o Azure AD Domain Services, pode evitar o fardo de infraestrutura e gestão que estão associado com a gestão da infraestrutura de identidade para oferecer suporte a aplicativos compatíveis com diretórios tradicionais. Pode mover esses aplicativos para serviços de infraestrutura do Azure e beneficiar de mais poupança nos custos operacionais.


## <a name="next-steps"></a>Passos Seguintes
### <a name="learn-more-about-azure-ad-domain-services"></a>Saiba mais sobre o Azure AD Domain Services
* [Funcionalidades](active-directory-ds-features.md)
* [Cenários de implementação](active-directory-ds-scenarios.md)
* [Descubra se o Azure AD Domain Services se adequa aos seus casos de uso](active-directory-ds-comparison.md)
* [Compreender como o Azure AD Domain Services sincroniza com o seu diretório do Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introdução ao Azure AD Domain Services
* [Ativar os serviços de domínio do Azure AD no portal do Azure](active-directory-ds-getting-started.md)
