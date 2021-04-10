---
title: Funcionalidades de segurança Azure que ajudam na gestão de identidade | Microsoft Docs
description: Conheça as principais funcionalidades de segurança do Azure que ajudam na gestão de identidade. Consulte informações sobre tópicos como um único sinal de inscrição e procuração inversa.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
ms.openlocfilehash: 1bb4f22c6ef114a224597ede2b053d076aba5997
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065842"
---
# <a name="azure-identity-management-security-overview"></a>Descrição geral da segurança da gestão de identidades do Azure

 A gestão de identidade é o processo de autenticação e autorização de [princípios de segurança.](/windows/security/identity-protection/access-control/security-principals) Envolve também o controlo da informação sobre esses principais (identidades). Os princípios de segurança (identidades) podem incluir serviços, aplicações, utilizadores, grupos, etc. As soluções de gestão de identidade e acesso da Microsoft ajudam o IT a proteger o acesso a aplicações e recursos em todo o datacenter corporativo e na nuvem. Esta proteção permite níveis adicionais de validação, tais como políticas de autenticação multi-factor e acesso condicional. Monitorizar atividades suspeitas através de relatórios avançados de segurança, auditoria e alerta ajuda a mitigar potenciais problemas de segurança. [O Azure Ative Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) fornece um único sign-on (SSO) a milhares de software na nuvem como um serviço (SaaS) e acesso a aplicações web que você executou no local.
 
Aproveitando os benefícios de segurança do Azure Ative Directory (Azure AD), pode:

* Crie e faça a gestão de uma única identidade para cada utilizador em toda a sua empresa híbrida, ao manter os utilizadores, os grupos e os dispositivos sincronizados. 
* Forneça acesso SSO às suas aplicações, incluindo milhares de aplicações SaaS pré-integradas.
* Ativar a segurança do acesso a aplicações mediante a aplicação da Multi-Factor Authentication baseada em regras, tanto nas aplicações no local, como na cloud.
* Providenciar acesso remoto seguro a aplicações web no local através do Azure AD Application Proxy.

O objetivo deste artigo é fornecer uma visão geral das principais funcionalidades de segurança do Azure que ajudam na gestão da identidade. Também fornecemos links para artigos que dão detalhes de cada recurso para que possa saber mais.  

O artigo centra-se nas seguintes capacidades de gestão de identidade Azure:

* Início de sessão único
* Proxy inverso
* Multi-Factor Authentication
* Controlo de acesso baseado em funções do Azure (RBAC do Azure)
* Relatórios de monitorização de segurança, alertas e aprendizagem automática
* Gestão da identidade e do acesso ao consumidor
* Registo de dispositivo
* Privileged Identity Management
* Proteção de identidade
* Gestão híbrida de identidade/Azure AD conecta
* Revisões de acesso do Azure AD

## <a name="single-sign-on"></a>Início de sessão único

SSO significa poder aceder a todas as aplicações e recursos que precisa para fazer negócios, fazendo a sessão apenas uma vez utilizando uma única conta de utilizador. Uma vez assinado, pode aceder a todas as aplicações de que necessita sem ser obrigado a autenticar (por exemplo, digitar uma palavra-passe) uma segunda vez.

Muitas organizações confiam em aplicações SaaS como Microsoft 365, Box e Salesforce para a produtividade dos utilizadores. Historicamente, o pessoal de TI precisava de criar e atualizar individualmente as contas de utilizador em cada aplicação SaaS, e os utilizadores tinham de se lembrar de uma palavra-passe para cada aplicação SaaS.

O Azure AD estende os ambientes do Ative Directory para a nuvem, permitindo que os utilizadores utilizem a sua conta organizacional primária para assinar não só os seus dispositivos e recursos da empresa ligados ao domínio, mas também a todas as aplicações web e SaaS de que necessitam para os seus empregos.

Não só os utilizadores não têm de gerir vários conjuntos de nomes de utilizador e palavras-passe, como podem providenciar ou desresvisão de acesso à aplicação automaticamente, com base nos seus grupos organizacionais e no seu estado de colaborador. O Azure AD introduz controlos de governação de segurança e acesso com os quais pode gerir centralmente o acesso dos utilizadores através das aplicações SaaS.

Saiba mais:

* [Visão geral sobre SSO](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Vídeo sobre fundamentos de autenticação](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Série quickstart na gestão de aplicações](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Proxy inverso

O Azure AD Application Proxy permite-lhe publicar aplicações no local, tais como sites [SharePoint,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [Outlook Web App,](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)e aplicações baseadas no [IIS](https://www.iis.net/)dentro da sua rede privada e fornece acesso seguro aos utilizadores fora da sua rede. A Application Proxy fornece acesso remoto e SSO para muitos tipos de aplicações web no local com milhares de aplicações SaaS que a Azure AD suporta. Os colaboradores podem iniciar sôments nas suas apps a partir de casa nos seus próprios dispositivos e autenticar através deste proxy baseado na nuvem.

Saiba mais:

* [Ativar o Proxy de Aplicação Ad Azure](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Publicar aplicações com o Proxy da Aplicação do Azure AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Início de sessão único com o Proxy de Aplicações](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Trabalhar com Acesso Condicional](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A Azure AD Multi-Factor Authentication é um método de autenticação que requer a utilização de mais de um método de verificação e adiciona uma segunda camada crítica de segurança às entradas e transações do utilizador. A Autenticação Multi-Factor ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um simples processo de inscrição. Fornece autenticação forte através de uma série de opções de verificação: chamadas telefónicas, mensagens de texto ou notificações de aplicações móveis ou códigos de verificação e fichas OAuth de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é a Multi-Factor Authentication do Azure AD?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Como funciona o Multi-Factor Authentication do Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>RBAC do Azure

O Azure RBAC é um sistema de autorização construído na Azure Resource Manager que fornece uma gestão de acesso de granulado fino de recursos em Azure. O Azure RBAC permite-lhe controlar granulivelmente o nível de acesso que os utilizadores têm. Por exemplo, pode limitar um utilizador a gerir apenas redes virtuais e outro utilizador para gerir todos os recursos num grupo de recursos. O Azure inclui várias funções incorporadas que pode utilizar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

- [Proprietário](../../role-based-access-control/built-in-roles.md#owner) – tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outras pessoas. 
- [Contribuidor](../../role-based-access-control/built-in-roles.md#contributor) – pode criar e gerir todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.
- [Leitor](../../role-based-access-control/built-in-roles.md#reader) - Pode ver os recursos Azure existentes.
- [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator) – permite gerir o acesso dos utilizadores aos recursos do Azure.

Saiba mais:

* [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Relatórios de monitorização de segurança, alertas e aprendizagem automática

Monitorização de segurança, alertas e relatórios baseados em aprendizagem automática que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger o seu negócio. Você pode usar relatórios de acesso e utilização Azure AD para ganhar visibilidade na integridade e segurança do diretório da sua organização. Com esta informação, um administrador de diretório pode determinar melhor onde podem estar os riscos de segurança possíveis para que possam planear adequadamente esses riscos.

No portal Azure, os relatórios enquadram-se nas seguintes categorias:

* **Relatórios de anomalias**: Contenha eventos de entrada que achamos ser anómalos. O nosso objetivo é dar-lhe a conhecer tal atividade e permitir-lhe determinar se um evento é suspeito.
* **Relatórios de aplicações integrados**: Forneça informações sobre como as aplicações em nuvem estão a ser usadas na sua organização. A Azure AD oferece integração com milhares de aplicações em nuvem.
* **Relatórios de erro**: Indique erros que possam ocorrer quando fornece contas a aplicações externas.
* **Relatórios específicos do utilizador**: Mostrar dados de atividade de inscrição do dispositivo para um utilizador específico.
* **Registos** de atividades : Contenha um registo de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias, ou dure 30 dias, e alterações de atividade de grupo e atividade de reset e registo de passwords.

Saiba mais:

* [Ver os relatórios de acesso e utilização](../../active-directory/reports-monitoring/overview-reports.md)
* [Começar com o Azure Ative Directory reportando](../../active-directory/reports-monitoring/overview-reports.md)
* [Guia de relatórios do Azure Ative Directory](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Gestão da identidade e do acesso ao consumidor

O Azure AD B2C é um serviço de gestão de identidades altamente disponível, global e identitário para aplicações viradas para o consumidor que escala para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Os seus consumidores podem iniciar scontabilidade de todas as suas aplicações através de experiências personalizáveis, utilizando as suas contas sociais existentes ou criando novas credenciais.

No passado, os programadores de aplicações que quisessem inscrever clientes e inscrevê-los nas suas aplicações teriam escrito o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure AD B2C oferece à sua organização uma melhor forma de integrar a gestão da identidade do consumidor em aplicações com a ajuda de uma plataforma segura e baseada em padrões e um grande conjunto de políticas extensíveis.

Quando utiliza o Azure AD B2C, os seus consumidores podem inscrever-se nas suas aplicações utilizando as suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de e-mail e palavra-passe, ou nome de utilizador e senha).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Pré-visualização do Azure Active Directory B2C: inscrever-se e iniciar sessão em consumidores nas suas aplicações](../../active-directory-b2c/overview.md)
* [Visualização do Diretório Ativo Azure B2C: Tipos de aplicações](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registo de dispositivo

O registo de dispositivos Azure AD é a base para cenários de [acesso condicional](../../active-directory/devices/device-management-azure-portal.md) baseados no dispositivo. Quando um dispositivo está registado, o registo do dispositivo Azure AD fornece ao dispositivo uma identidade que utiliza para autenticar o dispositivo quando um utilizador assina. O dispositivo autenticado e os atributos do dispositivo podem então ser utilizados para impor políticas de Acesso Condicional para aplicações que estejam hospedadas na nuvem e no local.

Quando combinado com uma solução de gestão de dispositivos móveis, como o Intune, os atributos do dispositivo em Azure AD são atualizados com informações adicionais sobre o dispositivo. Em seguida, pode criar regras de Acesso Condicional que impõem o acesso dos dispositivos para cumprir os seus padrões de segurança e conformidade.

Saiba mais:

* [Começa com o registo do dispositivo AZure AD](../../active-directory/devices/device-management-azure-portal.md)
* [Registo automático de dispositivos com Azure AD para dispositivos ligados ao domínio do Windows](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Configurar o registo automático de dispositivos ligados ao domínio do Windows com Azure AD](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Com a Azure AD Privileged Identity Management, pode gerir, controlar e monitorizar as suas identidades privilegiadas e aceder a recursos em Azure AD, bem como outros serviços online da Microsoft, como o Microsoft 365 e o Microsoft Intune.

Por vezes, os utilizadores precisam de realizar operações privilegiadas em recursos Azure ou Microsoft 365, ou em outras aplicações SaaS. Esta necessidade significa frequentemente que as organizações têm de dar aos utilizadores um acesso privilegiado permanente em Azure AD. Este acesso é um risco crescente de segurança para os recursos hospedados na nuvem, porque as organizações não conseguem monitorizar suficientemente o que os utilizadores estão a fazer com os seus privilégios de administrador. Além disso, se uma conta de utilizador com acesso privilegiado estiver comprometida, essa violação pode afetar a segurança geral da nuvem da organização. A Azure AD Gestão de Identidade Privilegiada ajuda a mitigar este risco.

Com a Azure AD Gestão de Identidade Privilegiada, pode:

* Veja quais os utilizadores que são administradores da AD Azure.
* Permitir o acesso administrativo a pedido, just-in-time (JIT) a serviços da Microsoft, como o Microsoft 365 e o Intune.
* Obtenha relatórios sobre o histórico de acesso do administrador e alterações nas atribuições de administrador.
* Receba alertas sobre o acesso a um papel privilegiado.

Saiba mais:

* [O que é o Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Atribuir funções de diretório Azure AD em PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade

A Azure AD Identity Protection é um serviço de segurança que fornece uma visão consolidada de deteções de riscos e potenciais vulnerabilidades que afetam as identidades da sua organização. A Proteção de Identidade tira partido das capacidades de deteção de anomalias Azure AD existentes, que estão disponíveis através de relatórios de Atividade Anómala AD Azure. A Proteção de Identidade também introduz novos tipos de deteção de riscos que podem detetar anomalias em tempo real.

Saiba mais:

* [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Canal 9: Azure AD e Identity Show: Preview de Proteção de Identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Gestão híbrida de identidade/Azure AD conecta

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Chamamos-lhe identidade híbrida. O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida. Permite-lhe fornecer uma identidade comum aos seus utilizadores no Microsoft 365, Azure e aplicações SaaS integradas no Azure AD. Proporciona as seguintes funcionalidades:

* Sincronização
* AD FS e integração da federação
* Passar pela autenticação
* Monitorização do Estado de Funcionamento

Saiba mais:

* [Papel branco de identidade híbrida](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog da equipa da AZure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Revisões de acesso do Azure AD

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações façam uma gestão eficiente das associações a grupos, acesso às aplicações empresariais e atribuições de funções privilegiadas.

Saiba mais:

* [Revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)