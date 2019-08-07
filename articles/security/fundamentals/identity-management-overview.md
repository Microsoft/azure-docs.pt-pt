---
title: Recursos de segurança do Azure que ajudam com o gerenciamento de identidade | Microsoft Docs
description: " Este artigo fornece uma visão geral dos principais recursos de segurança do Azure que ajudam com o gerenciamento de identidades. As soluções de gerenciamento de identidade e acesso da Microsoft ajudam a ti a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem, permitindo níveis adicionais de validação, como a autenticação multifator e o acesso condicional Policie. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: 1081fa8c9c7cc64418515aabbb755ecf056196ca
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826285"
---
# <a name="azure-identity-management-security-overview"></a>Visão geral da segurança do gerenciamento de identidades do Azure

 O gerenciamento de identidades é o processo de autenticação e autorização de [entidades de segurança](/windows/security/identity-protection/access-control/security-principals). Ele também envolve o controle de informações sobre essas entidades (identidades). As entidades de segurança (identidades) podem incluir serviços, aplicativos, usuários, grupos, etc. As soluções de gerenciamento de acesso e identidade da Microsoft ajudam a ti a proteger o acesso a aplicativos e recursos no datacenter corporativo e na nuvem. Essa proteção permite níveis adicionais de validação, como a autenticação multifator e políticas de acesso condicional. O monitoramento de atividades suspeitas por meio de relatórios de segurança avançados, auditoria e alertas ajuda a reduzir possíveis problemas de segurança. O [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) fornece SSO (logon único) para milhares de aplicativos de SaaS (software como serviço) em nuvem e acesso a aplicativos Web que você executa localmente.
 
Aproveitando os benefícios de segurança do Azure Active Directory (AD do Azure), você pode:

* Crie e gerencie uma única identidade para cada usuário em sua empresa híbrida, mantendo os usuários, grupos e dispositivos sincronizados. 
* Forneça acesso de SSO aos seus aplicativos, incluindo milhares de aplicativos SaaS previamente integrados.
* Habilite a segurança de acesso do aplicativo impondo a autenticação multifator baseada em regras para aplicativos locais e na nuvem.
* Provisione o acesso remoto seguro a aplicativos Web locais por meio do Azure Proxy de Aplicativo do AD.

O objetivo deste artigo é fornecer uma visão geral dos principais recursos de segurança do Azure que ajudam com o gerenciamento de identidades. Também fornecemos links para artigos que fornecem detalhes de cada recurso para que você possa saber mais.  

O artigo se concentra nos seguintes recursos básicos de gerenciamento de identidade do Azure:

* Início de sessão único
* Proxy inverso
* Multi-Factor Authentication
* RBAC (controle de acesso baseado em função)
* Monitoramento de segurança, alertas e relatórios baseados em Machine Learning
* Gestão de acesso e identidade do consumidor
* Registo de dispositivo
* Privileged Identity Management
* Proteção de identidade
* Gerenciamento de identidade híbrida/Azure AD Connect
* Revisões de acesso do Azure AD

## <a name="single-sign-on"></a>Início de sessão único

O SSO significa poder acessar todos os aplicativos e recursos de que você precisa para fazer negócios, entrando apenas uma vez usando uma única conta de usuário. Depois de conectado, você pode acessar todos os aplicativos necessários sem a necessidade de autenticação (por exemplo, digite uma senha) uma segunda vez.

Muitas organizações contam com aplicativos SaaS como Office 365, Box e Salesforce para produtividade do usuário. Historicamente, a equipe de ti precisava criar e atualizar individualmente as contas de usuário em cada aplicativo SaaS, e os usuários precisavam lembrar uma senha para cada aplicativo SaaS.

O Azure AD estende os ambientes de Active Directory locais para a nuvem, permitindo que os usuários usem sua conta organizacional principal para entrar não apenas em seus dispositivos ingressados no domínio e recursos da empresa, mas também em todos os aplicativos Web e SaaS de que precisam para seus trabalhos.

Não apenas os usuários não precisam gerenciar vários conjuntos de nomes de usuário e senhas, você pode provisionar ou desprovisionar o acesso ao aplicativo automaticamente, com base em seus grupos organizacionais e no status de seus funcionários. O Azure AD introduz controles de governança de segurança e acesso com os quais você pode gerenciar centralmente o acesso dos usuários em aplicativos SaaS.

Saiba mais:

* [Visão geral do logon único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [What is application access and single sign-on with Azure Active Directory?](../../active-directory/manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Integrar o logon único do Azure Active Directory com aplicativos SaaS](../../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Proxy inverso

O Azure Proxy de Aplicativo do AD permite que você publique aplicativos locais, como sites [do SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , o [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)e aplicativos baseados no [IIS](https://www.iis.net/)dentro de sua rede privada e fornece acesso seguro aos usuários fora da rede. O proxy de aplicativo fornece acesso remoto e SSO para muitos tipos de aplicativos Web locais com milhares de aplicativos SaaS com suporte do Azure AD. Os funcionários podem entrar em seus aplicativos de casa em seus próprios dispositivos e autenticar por meio desse proxy baseado em nuvem.

Saiba mais:

* [Habilitando o Azure Proxy de Aplicativo do AD](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publicar aplicações com o Proxy de aplicações do Azure AD](/azure/active-directory/active-directory-application-proxy-publish)
* [Logon único com o proxy de aplicativo](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Trabalhando com acesso condicional](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A autenticação multifator do Azure é um método de autenticação que requer o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança a entradas e transações do usuário. A autenticação multifator ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda do usuário por um processo de entrada simples. Ele fornece autenticação forte por meio de uma variedade de opções de verificação: chamadas telefônicas, mensagens de texto ou notificações de aplicativo móvel ou códigos de verificação e tokens OAuth de terceiros.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Multi-Factor Authentication do Azure?](/azure/active-directory/authentication/multi-factor-authentication)
* [Como funciona a autenticação multifator do Azure](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

O RBAC é um sistema de autorização criado em Azure Resource Manager que fornece gerenciamento de acesso refinado de recursos no Azure. O RBAC permite controlar de forma granular o nível de acesso que os usuários têm. Por exemplo, você pode limitar um usuário a gerenciar apenas redes virtuais e outro usuário para gerenciar todos os recursos em um grupo de recursos. O Azure inclui várias funções internas que você pode usar. São apresentadas em seguida quatro funções incorporadas fundamentais. As três primeiras aplicam-se a todos os tipos de recursos.

Saiba mais:

* [O que é o controlo de acesso baseado em funções (RBAC)?](/azure/role-based-access-control/overview)
* [Built-in roles for Azure resources](/azure/role-based-access-control/built-in-roles) (Funções incorporadas para recursos do Azure)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitoramento de segurança, alertas e relatórios baseados em Machine Learning

Monitoramento de segurança, alertas e relatórios baseados em Machine Learning que identificam padrões de acesso inconsistentes podem ajudá-lo a proteger seus negócios. Você pode usar os relatórios de uso e acesso do Azure AD para obter visibilidade da integridade e da segurança do diretório da sua organização. Com essas informações, um administrador de diretório pode determinar melhor onde possíveis riscos de segurança podem se situar para que eles possam planejar adequadamente a atenuação desses riscos.

Na portal do Azure, os relatórios se enquadram nas seguintes categorias:

* **Relatórios**de anomalias: Conter eventos de entrada que descobrimos ser anormais. Nossa meta é fazer você conhecer essa atividade e permitir que você determine se um evento é suspeito.
* **Relatórios de aplicativos integrados**: Forneça informações sobre como os aplicativos em nuvem estão sendo usados na sua organização. O Azure AD oferece integração com milhares de aplicativos em nuvem.
* **Relatórios de erros**: Indique os erros que podem ocorrer quando você provisiona contas para aplicativos externos.
* **Relatórios específicos do usuário**: Exibir dados de atividade de entrada do dispositivo para um usuário específico.
* **Logs de atividade**: Conter um registro de todos os eventos auditados nas últimas 24 horas, nos últimos 7 dias ou nos últimos 30 dias, e alterações de atividade de grupo e atividade de registro e redefinição de senha.

Saiba mais:

* [Ver os relatórios de acesso e utilização](/azure/active-directory/active-directory-view-access-usage-reports)
* [Introdução aos relatórios de Azure Active Directory](/azure/active-directory/active-directory-reporting-getting-started)
* [Guia de relatórios do Azure Active Directory](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Gestão de acesso e identidade do consumidor

O Azure AD B2C é um serviço de gerenciamento de identidade global e altamente disponível para aplicativos voltados para o consumidor que pode ser dimensionado para centenas de milhões de identidades. Pode ser integrado entre plataformas móveis e Web. Seus consumidores podem entrar em todos os seus aplicativos por meio de experiências personalizáveis usando suas contas sociais existentes ou criando novas credenciais.

No passado, os desenvolvedores de aplicativos que desejavam inscrever clientes e conectá-los a seus aplicativos teriam escrito seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. A Azure AD B2C oferece à sua organização uma maneira melhor de integrar o gerenciamento de identidade do consumidor em aplicativos com a ajuda de uma plataforma segura baseada em padrões e um grande conjunto de políticas extensível.

Quando você usa Azure AD B2C, seus consumidores podem se inscrever para seus aplicativos usando suas contas sociais existentes (Facebook, Google, Amazon, LinkedIn) ou criando novas credenciais (endereço de email e senha, ou nome de usuário e senha).

Saiba mais:

* [O que é o Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Visualização de Azure Active Directory B2C: Inscrever-se e entrar em consumidores em seus aplicativos](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Visualização de Azure Active Directory B2C: Tipos de aplicativos](../../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registo de dispositivo

O registro de dispositivos do Azure AD é a base para cenários de [acesso condicional](/azure/active-directory/active-directory-conditional-access-device-registration-overview) com base em dispositivo. Quando um dispositivo é registrado, o registro de dispositivo do Azure AD fornece ao dispositivo uma identidade que ele usa para autenticar o dispositivo quando um usuário faz logon. O dispositivo autenticado e os atributos do dispositivo podem ser usados para impor políticas de acesso condicional para aplicativos hospedados na nuvem e localmente.

Quando combinado com uma solução de gerenciamento de dispositivo móvel, como o Intune, os atributos de dispositivo no Azure AD são atualizados com informações adicionais sobre o dispositivo. Você pode criar regras de acesso condicional que impõem o acesso de dispositivos para atender aos seus padrões de segurança e conformidade.

Saiba mais:

* [Introdução ao registro de dispositivos do Azure AD](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Registro de dispositivo automático com o Azure AD para dispositivos ingressados no domínio do Windows](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure AD](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Com o Azure AD Privileged Identity Management, você pode gerenciar, controlar e monitorar suas identidades com privilégios e o acesso aos recursos no Azure AD, bem como a outros serviços online da Microsoft, como o Office 365 e Microsoft Intune.

Às vezes, os usuários precisam executar operações privilegiadas no Azure ou nos recursos do Office 365 ou em outros aplicativos SaaS. Geralmente, essa necessidade significa que as organizações precisam fornecer acesso privilegiado permanente aos usuários no Azure AD. Esse acesso é um crescente risco de segurança para recursos hospedados na nuvem, pois as organizações não podem monitorar suficientemente o que os usuários estão fazendo com seus privilégios de administrador. Além disso, se uma conta de usuário com acesso privilegiado for comprometida, essa violação poderá afetar a segurança geral da nuvem da organização. Azure AD Privileged Identity Management ajuda a mitigar esse risco.

Com Azure AD Privileged Identity Management, você pode:

* Veja quais usuários são administradores do Azure AD.
* Habilite o acesso administrativo just-in-time (JIT) sob demanda aos serviços da Microsoft, como o Office 365 e o Intune.
* Obtenha relatórios sobre o histórico de acesso de administrador e as alterações nas atribuições de administrador.
* Obtenha alertas sobre o acesso a uma função com privilégios.

Saiba mais:

* [O que é Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Atribuir funções de diretório do Azure AD no PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Proteção de identidade

O Azure AD Identity Protection é um serviço de segurança que fornece uma visão consolidada dos eventos de risco e possíveis vulnerabilidades que afetam as identidades da sua organização. A proteção de identidade aproveita os recursos existentes de detecção de anomalias do Azure AD, que estão disponíveis por meio de relatórios de atividade anômala do Azure AD. A proteção de identidade também introduz novos tipos de evento de risco que podem detectar anomalias em tempo real.

Saiba mais:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Canal 9: Azure AD e identidade mostram: Versão prévia da proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Gerenciamento de identidade híbrida/Azure AD Connect

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Chamamos-lhe identidade híbrida. O Azure AD Connect é a ferramenta da Microsoft concebida para satisfazer e atingir os seus objetivos de identidade híbrida. Isto permite-lhe fornecer uma identidade comum para o utilizadores das aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD. Proporciona as seguintes funcionalidades:

* Sincronização
* Integração de AD FS e Federação
* Autenticação de passagem
* Monitorização do Estado de Funcionamento

Saiba mais:

* [white paper de identidade híbrida](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog da equipe do Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Revisões de acesso do Azure AD

As revisões de acesso do Azure Active Directory (Azure AD) permitem que as organizações façam uma gestão eficiente das associações a grupos, acesso às aplicações empresariais e atribuições de funções privilegiadas.

Saiba mais:

* [Revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Gerir o acesso de utilizador com as revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)
