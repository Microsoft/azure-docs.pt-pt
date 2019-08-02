---
title: Perguntas mais frequentes sobre (FAQ) - Azure Active Directory | Documentos da Microsoft
description: Perguntas e respostas sobre o Azure e Azure Active Directory, gestão de palavra-passe e acesso a aplicações comuns.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c1ee5e849d8004f828a2d92d728ad7925fc05c4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693955"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Perguntas mais frequentes sobre o Azure Active Directory
O Azure Active Directory (Azure AD) é uma solução identidade como um Serviço (IDaaS) completa que abrange todos os aspetos relativos à identidade, gestão de acesso e segurança.

Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Aceder ao Azure e Azure Active Directory
**P: Por que obtenho "nenhuma assinatura encontrada" quando tento acessar o Azure AD na portal do Azure?**

**R:** Para acessar o portal do Azure, cada usuário precisa de permissões com uma assinatura do Azure. Se tiver uma subscrição paga do Office 365 ou do Azure AD, aceda a [https://aka.ms/accessAAD](https://aka.ms/accessAAD) com apenas um passo único de ativação. Caso contrário, terá de ativar uma [conta do Azure](https://azure.microsoft.com/pricing/free-trial/) gratuita ou uma subscrição paga.

Para obter mais informações, consulte:

* [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**P: Qual é a relação entre o Azure AD, o Office 365 e o Azure?**

**R:** O Azure AD oferece recursos comuns de identidade e acesso a todos os serviços da Web. Se estiver a utilizar o Office 365, o Microsoft Azure, o Intune ou outros, já está a utilizar o Azure AD para ajudar a ativar o início de sessão e gestão de acesso a todos estes serviços.

Todos os utilizadores que estão configurados para utilizar serviços da web estão definidos como contas de utilizador numa ou mais instâncias do Azure AD. Pode configurar estas contas para obter gratuitamente capacidades do Azure AD, como o acesso a aplicações na cloud.

Os serviços pagos do Azure AD, como o Enterprise Mobility + Security complementam outros serviços da Web, como o Office 365 e o Microsoft Azure com soluções abrangentes de gestão empresarial e de segurança.

---

**P:  Quais são as diferenças entre o proprietário e o administrador global?**

**R:** Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de proprietário para recursos do Azure. Um proprietário pode utilizar uma conta Microsoft ou uma conta profissional ou escolar do diretório que está associada à subscrição do Azure.  Esta função está autorizada a gerir serviços no portal do Azure.

Se outras pessoas tiverem de iniciar sessão e aceder aos serviços com a mesma subscrição, pode atribuí-los apropriado [função incorporada](../../role-based-access-control/built-in-roles.md). Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

Por predefinição, a pessoa que se inscreve para uma subscrição do Azure é atribuída a função de Administrador Global do diretório. O Administrador Global tenha acesso a todas as funcionalidades de diretório do Azure AD. O Azure AD tem um conjunto diferente de funções de administrador para gerir o diretório e as funcionalidades relacionadas com a identidade. Estes administradores terão acesso às várias funcionalidades no portal do Azure. Função do administrador determina o que eles podem fazer, como criar ou editar utilizadores, atribuir funções administrativas a outras pessoas, repor palavras-passe do utilizador, gerir licenças de utilizador ou gerir domínios.  Para obter informações adicionais sobre os administradores de diretórios do Azure AD e as respetivas funções, consulte [atribuir um utilizador a funções de administrador no Azure Active Directory](active-directory-users-assign-role-azure-portal.md) e [atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

Além disso, os serviços pagos do Azure AD, como o Enterprise Mobility + Security complementam outros serviços da Web, como o Office 365 e o Microsoft Azure com soluções abrangentes de gestão empresarial e de segurança.

---
**P: Há um relatório que mostra quando minhas licenças de usuário do Azure AD expirarão?**

**R:** Não.  Esta configuração não está disponível no momento.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Introdução ao Azure AD Híbrido


**P: Como fazer deixar um locatário quando eu sou adicionado como um colaborador?**

**R:** Quando você é adicionado ao locatário de outra organização como um colaborador, você pode usar o "seletor de locatário" no canto superior direito para alternar entre locatários.  Atualmente, não existe nenhuma forma de deixar a organização que o convidou. A Microsoft está a trabalhar para dispor desta funcionalidade.  Até que esta funcionalidade esteja disponível, pode pedir à organização que o convidou para o remover do seu inquilino.

---
**P: Como eu posso conectar meu diretório local ao Azure AD?**

**R:** Você pode conectar seu diretório local ao Azure AD usando Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../hybrid/whatis-hybrid-identity.md).

---
**P: Como fazer configurar o SSO entre meu diretório local e meus aplicativos de nuvem?**

**R:** Você só precisa configurar o SSO (logon único) entre seu diretório local e o Azure AD. Enquanto aceder às suas aplicações na nuvem através do Azure AD, o serviço orienta automaticamente os utilizadores para uma autenticação correta com as suas credenciais no local.

A implementação do SSO no local poderá ser feita facilmente com soluções de federação, tais como o Active Directory Federation Services (AD FS) ou ao configurar a sincronização hash de palavras-passe. Pode facilmente implementar ambas as opções utilizando o assistente de configuração do Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../hybrid/whatis-hybrid-identity.md).

---
**P: O Azure AD fornece um portal de autoatendimento para usuários em minha organização?**

**R:** Sim, o Azure AD fornece o [painel de acesso do Azure ad](https://myapps.microsoft.com) para o autoatendimento do usuário e o acesso ao aplicativo. Se você for um cliente do Office 365, poderá encontrar muitos dos mesmos recursos no portal do [office 365](https://portal.office.com).

Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](../user-help/active-directory-saas-access-panel-introduction.md).

---
**P: O Azure AD me ajuda a gerenciar minha infraestrutura local?**

**R:** Sim. A edição Premium do Azure AD fornece o Azure AD Connect Health. O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização.  

Para obter mais informações, consulte [Monitor your on-premises identity infrastructure and synchronization services in the cloud (Monitorizar os serviços de infraestrutura de identidade no local e sincronização na cloud)](../hybrid/whatis-hybrid-identity-health.md).  

---
## <a name="password-management"></a>Gestão de palavras-passe
**P: Posso usar o Write-back de senha do Azure AD sem sincronização de senha? (Neste cenário, é possível utilizar a reposição de palavra-passe self-service do Azure AD (SSPR) com a repetição de escrita de palavras-passe e não armazenar palavras-passe na cloud?)**

**R:** Você não precisa sincronizar suas senhas do Active Directory com o Azure AD para habilitar o Write-back. Num ambiente federado, o início de sessão único (SSO) do Azure AD depende do diretório no local para autenticar o utilizador. Este cenário não requer que a palavra-passe no local seja controlada no Azure AD.

---
**P: Quanto tempo leva para que uma senha seja gravada de volta para Active Directory local?**

**R:** O Write-back de senha funciona em tempo real.

Para mais informações, consulte [Introdução à gestão de palavras-passe](../authentication/quickstart-sspr.md)

---
**P: Posso usar write-back de senha com senhas que são gerenciadas por um administrador?**

**R:** Sim, se você tiver o Write-back de senha habilitado, as operações de senha executadas por um administrador serão gravadas de volta em seu ambiente local.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questionsauthenticationactive-directory-passwords-faqmd"></a>Para obter mais respostas a perguntas relacionadas com palavras-passe, consulte [Password management frequently asked questions (Perguntas mais frequentes da gestão de palavras-passe)](../authentication/active-directory-passwords-faq.md).
---
**P:  O que posso fazer se não me lembrar da minha senha existente do Office 365/Azure AD ao tentar alterar minha senha?**

**R:** Para esse tipo de situação, há algumas opções.  Utilize a reposição de palavra-self-service (SSPR) se estiver disponível.  A SSPR funciona consoante a forma como está configurada.  Para mais informações, veja [How does the password reset portal work (Como funciona o portal de reposição de palavras-passe)](../authentication/howto-sspr-deployment.md).

Para os utilizadores do Office 365, o administrador pode repor a palavra-passe através dos passos descritos em [Reset user passwords (Repor palavras-passe de utilizador)](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Para contas do Azure AD, os administradores podem repor as palavras-passe através de um dos seguintes procedimentos:

- [Repor contas no portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Utilizar o PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Segurança
**P: As contas são bloqueadas após um número específico de tentativas com falha ou há uma estratégia mais sofisticada usada?**

Utilizamos uma estratégia mais sofisticada para bloquear as contas.  Baseia-se no IP do pedido e nas palavras-passe introduzidas. A duração do bloqueio também aumenta com base na probabilidade de se tratar de um ataque.  

**P:  Determinadas senhas (comuns) são rejeitadas com as mensagens ' esta senha foi usada para muitas vezes ', isso se refere a senhas usadas no Active Directory atual?**

Refere-se a palavras-passe que são globalmente comuns, como as variantes de “Palavra-passe” e “123456”.

**P: Uma solicitação de entrada de fontes duvidosa (botnets, ponto de extremidade Tor) será bloqueada em um locatário B2C ou isso exigirá um locatário de edição básico ou Premium?**

Temos um gateway que filtra os pedidos e que oferece alguma proteção contra botnets, que é aplicado a todos os inquilinos B2C.

## <a name="application-access"></a>Acesso à aplicação

**P: Onde posso encontrar uma lista de aplicativos que são previamente integrados ao Azure AD e seus recursos?**

**R:** O Azure AD tem mais de 2.600 aplicativos previamente integrados da Microsoft, de provedores de serviços de aplicativos e de parceiros. Todas as aplicações previamente integradas suportam o início de sessão único (SSO). O SSO permite-lhe utilizar as credenciais organizacionais para aceder às suas aplicações. Algumas das aplicações também suportam o aprovisionamento e cancelamento de aprovisionamento automatizados.

Para obter a lista completa das aplicações previamente integradas, consulte [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

---
**P: E se o aplicativo necessário não estiver no Azure AD Marketplace?**

**R:** Com Azure AD Premium, você pode adicionar e configurar qualquer aplicativo desejado. Consoante as funcionalidades da aplicação e das suas preferências, pode configurar o SSO e o aprovisionamento automatizado.  

Para obter mais informações, consulte:

* [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](../manage-apps/use-scim-to-provision-users-and-groups.md)

---
**P: Como os usuários entram em aplicativos usando o Azure AD?**

**R:** O Azure AD fornece várias maneiras para os usuários exibirem e acessarem seus aplicativos, como:

* O painel de acesso do Azure AD
* O iniciador de aplicações do Office 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Para obter mais informações, consulte [experiências de utilizador final para aplicações](../manage-apps/end-user-experiences.md).

---
**P: Quais são as diferentes maneiras como o Azure AD permite autenticação e logon único para aplicativos?**

**R:** O Azure AD dá suporte a vários protocolos padronizados para autenticação e autorização, como SAML 2,0, OpenID Connect, OAuth 2,0 e WS-Federation. O Azure AD também suporta cofres de palavras-passe e as capacidades de início de sessão automatizado para aplicações que apenas suportam autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](../develop/authentication-scenarios.md)
* [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Início de sessão único para aplicações no Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**P: Posso adicionar aplicativos que estou executando localmente?**

**R:** O Azure Proxy de Aplicativo do AD fornece acesso fácil e seguro a aplicativos Web locais que você escolhe. Pode aceder a estas aplicações da mesma forma a que acede às aplicações de software como um serviço (SaaS) no Azure AD. Não é necessário nenhuma VPN ou alterar a infraestrutura de rede.  

Para mais informações, consulte [How to provide secure remote access to on-premises applications (Como fornecer acesso remoto seguro a aplicações no local)](../manage-apps/application-proxy.md).

---
**P: Como fazer exigir autenticação multifator para usuários que acessam um aplicativo específico?**

**R:** Com o acesso condicional do Azure AD, você pode atribuir uma política de acesso exclusiva para cada aplicativo. Na sua política, pode exigir a autenticação multifator sempre ou quando os utilizadores não estiverem ligados à rede local.  

Para mais informações, consulte [Securing access to Office 365 and other apps connected to Azure Active Directory (Proteger o acesso ao Office 365 e a outras aplicações ligadas ao Azure Active Directory)](../active-directory-conditional-access-azure-portal.md).

---
**P: O que é o provisionamento automatizado de usuários para aplicativos SaaS?**

**R:** Use o Azure AD para automatizar a criação, a manutenção e a remoção de identidades de usuário em muitos aplicativos SaaS de nuvem populares.

Para obter mais informações, consulte [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory (Automatizar o aprovisionamento do utilizador e cancelar o aprovisionamento a aplicações SaaS com o Azure Active Directory)](../manage-apps/user-provisioning.md).

---
**P:  Posso configurar uma conexão LDAP segura com o Azure AD?**

**R:**  Não. O Azure AD não dá suporte ao protocolo LDAP ou LDAP Seguro diretamente. No entanto, é possível habilitar a instância Azure AD Domain Services (Azure AD DS) em seu locatário do Azure AD com grupos de segurança de rede configurados corretamente por meio da rede do Azure para obter conectividade LDAP. Para obter mais informações, https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap consulte.
