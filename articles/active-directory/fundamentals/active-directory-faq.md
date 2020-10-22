---
title: Perguntas frequentes (FAQ) - Azure Ative Directory Microsoft Docs
description: Perguntas e respostas comuns sobre O Diretório Ativo Azure e Azure, gestão de passwords e acesso a aplicações.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8774d6868d55c7d040b1894c4a14d989cb2702
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371873"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Perguntas frequentes sobre O Diretório Ativo Azure
O Azure Active Directory (Azure AD) é uma solução identidade como um Serviço (IDaaS) completa que abrange todos os aspetos relativos à identidade, gestão de acesso e segurança.

Para obter mais informações, consulte [What is Azure Active Directory? (O que é o Azure Active Directory?)](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Aceder ao Azure e Azure Active Directory
**P: Por que recebo "Nenhuma subscrição encontrada" quando tento aceder ao Azure AD no portal Azure?**

**R:** Para aceder ao portal do Azure, cada utilizador precisa de permissões com uma subscrição do Azure. Se não tiver uma subscrição paga do Microsoft 365 ou AD Azure, terá de ativar uma [conta Azure](https://azure.microsoft.com/free/
) gratuita ou uma subscrição paga.

Para obter mais informações, consulte:

* [Como as subscrições do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**P: Qual é a relação entre Azure AD, Microsoft 365 e Azure?**

**R:** O Azure AD fornece-lhe capacidades de identidade e de acesso comuns a todos os serviços da Web. Quer esteja a utilizar o Microsoft 365, Microsoft Azure, Intune ou outros, já está a utilizar o AZure AD para ajudar a ligar a gestão de acesso e acesso a todos estes serviços.

Todos os utilizadores que estão configurados para utilizar serviços da web estão definidos como contas de utilizador numa ou mais instâncias do Azure AD. Pode configurar estas contas para obter gratuitamente capacidades do Azure AD, como o acesso a aplicações na cloud.

A Azure AD pagou serviços como Enterprise Mobility + Security complementam outros serviços web como o Microsoft 365 e o Microsoft Azure com soluções abrangentes de gestão e segurança em escala empresarial.

---

**P: Quais são as diferenças entre Proprietário e Administrador Global?**

**A:** Por predefinição, a pessoa que se inscreve para uma subscrição Azure é atribuída a função proprietário para recursos Azure. Um Proprietário pode usar uma conta Microsoft ou uma conta de trabalho ou escola a partir do diretório a que a subscrição do Azure está associada.  Esta função está autorizada a gerir serviços no portal do Azure.

Se outros precisarem de iniciar sessão e aceder aos serviços utilizando a mesma subscrição, pode [atribuir-lhes](../../role-based-access-control/built-in-roles.md)a função incorporada adequada . Para obter informações adicionais, consulte [Adicionar ou remover atribuições de funções Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

Por padrão, a pessoa que se inscreve para uma subscrição Azure é atribuída a função de Administrador Global para o diretório. O Administrador Global tem acesso a todas as funcionalidades do diretório AZure. A Azure AD tem um conjunto diferente de funções de administrador para gerir as características relacionadas com o diretório e identidade. Estes administradores terão acesso a várias funcionalidades no portal Azure. A função do administrador determina o que podem fazer, como criar ou editar utilizadores, atribuir funções administrativas a outros, redefinir palavras-passe do utilizador, gerir licenças de utilizador ou gerir domínios.  Para obter informações adicionais sobre os administradores de diretórios Adu da Azure e as suas funções, consulte [atribuir um utilizador às funções de administrador em Azure Ative Directory](active-directory-users-assign-role-azure-portal.md) e atribuir [funções de administrador em Azure Ative Directory](../roles/permissions-reference.md).

Adicionalmente, a Azure AD pagou serviços como Enterprise Mobility + Security complementam outros serviços web, como o Microsoft 365 e o Microsoft Azure, com soluções abrangentes de gestão e segurança em escala empresarial.

---
**P: Existe um relatório que mostra quando as minhas licenças de utilizador do Azure AD expiram?**

**R:** Não.  Esta configuração não está disponível no momento.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Introdução ao Azure AD Híbrido


**P: Como deixo um inquilino quando estou adicionado como um colaborador?**

**R:** Quando é adicionado ao inquilino de outra organização como um colaborador, pode utilizar o "comutador do inquilino" na parte superior direita para alternar entre inquilinos.  Atualmente, não existe nenhuma forma de deixar a organização que o convidou. A Microsoft está a trabalhar para dispor desta funcionalidade.  Até que esta funcionalidade esteja disponível, pode pedir à organização que o convidou para o remover do seu inquilino.

---
**P: Como ligar o meu diretório no local ao Azure AD?**

**R:** Pode ligar o diretório no local ao Azure AD com o Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../hybrid/whatis-hybrid-identity.md).

---
**P: Como posso configurar o SSO entre o meu diretório no local e as minhas aplicações na nuvem?**

**R:** Só tem de configurar o início de sessão único (SSO) entre o seu diretório no local e o Azure AD. Enquanto aceder às suas aplicações na nuvem através do Azure AD, o serviço orienta automaticamente os utilizadores para uma autenticação correta com as suas credenciais no local.

A implementação de SSO a partir de instalações pode ser facilmente alcançada com soluções da federação, tais como Serviços da Federação de Diretórios Ativos (AD FS), ou configurando a sincronização de haxixe de palavra-passe. Pode implementar facilmente ambas as opções utilizando o assistente de configuração Azure AD Connect.

Para obter mais informações, consulte [Integrating your on-premises identities with Azure Active Directory (Integrar as identidades no local ao Azure Active Directory)](../hybrid/whatis-hybrid-identity.md).

---
**P: O Azure AD fornece um portal personalizado aos utilizadores da minha organização?**

**R:** Sim, o Azure AD fornece o acesso ao [Painel de Acesso do Azure AD](https://myapps.microsoft.com) para acesso a utilizadores pessoalizados e a aplicações. Se for cliente Microsoft 365, pode encontrar muitas das mesmas capacidades no [portal do Office 365.](https://portal.office.com)

Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](../user-help/my-apps-portal-end-user-access.md).

---
**P: O Azure AD ajuda-me a gerir a minha infraestrutura no local?**

**R:** Sim. A edição Premium do Azure AD fornece o Azure AD Connect Health. O Azure AD Connect Health ajuda a monitorizar e obter informações sobre a sua infraestrutura de identidade no local, bem como sobre os serviços de sincronização.  

Para obter mais informações, consulte [Monitor your on-premises identity infrastructure and synchronization services in the cloud (Monitorizar os serviços de infraestrutura de identidade no local e sincronização na cloud)](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Gestão de palavras-passe
**P: Posso usar a palavra-passe AD do Azure sem sincronização de palavra-passe? (Neste cenário, é possível utilizar a palavra-passe de autosserviço AD do Azure (SSPR) com a palavra-passe e não armazenar palavras-passe na nuvem?)**

**R:** Não tem de sincronizar as palavras-passe do Active Directory no Azure AD para ativar a repetição de escrita de palavras-passe. Num ambiente federado, o início de sessão único (SSO) do Azure AD depende do diretório no local para autenticar o utilizador. Este cenário não requer que a palavra-passe no local seja controlada no Azure AD.

---
**P: Quanto tempo demora a repetição de escrita de uma palavra-passe no Active Directory no local?**

**R:** a repetição de palavras-passe é feita em tempo real.

Para mais informações, consulte [Introdução à gestão de palavras-passe](../authentication/tutorial-enable-sspr.md)

---
**P: Posso utilizar a repetição de palavras-passe com palavras-passe geridas por um administrador?**

**R:** Sim, se tiver ativado a repetição de palavras-passe, as operações de palavras-passe feitas por um administrador são repetidas no ambiente local.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Para obter mais respostas a perguntas relacionadas com palavras-passe, consulte [Password management frequently asked questions (Perguntas mais frequentes da gestão de palavras-passe)](../authentication/active-directory-passwords-faq.md).
---
**P: O que posso fazer se não me lembrar da minha senha de AD Microsoft 365/Azure enquanto tento alterar a minha palavra-passe?**

**A:** Para este tipo de situação, há algumas opções.  Utilize a reposição de palavra-self-service (SSPR) se estiver disponível.  A SSPR funciona consoante a forma como está configurada.  Para obter mais informações, consulte [como funciona o portal de reset da palavra-passe](../authentication/howto-sspr-deployment.md).

Para os utilizadores da Microsoft 365, o seu administrador pode redefinir a palavra-passe utilizando os passos descritos nas [palavras-passe do utilizador Reset](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Para contas do Azure AD, os administradores podem repor as palavras-passe através de um dos seguintes procedimentos:

- [Repor contas no portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Utilizar o PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Segurança
**P: As contas são bloqueadas ao fim de um determinado número de tentativas falhadas ou é utilizada outra estratégia mais sofisticada?**

Utilizamos uma estratégia mais sofisticada para bloquear as contas.  Baseia-se no IP do pedido e nas palavras-passe introduzidas. A duração do bloqueio também aumenta com base na probabilidade de se tratar de um ataque.  

**P: Certas palavras-passe (comuns) são rejeitadas com as mensagens "esta palavra-passe foi usada muitas vezes", refere-se a palavras-passe utilizadas no diretório ativo atual?**

Isto refere-se a palavras-passe que são globalmente comuns, como quaisquer variantes de "Password" e "123456".

**P: Os pedidos de início de sessão de origens duvidosas (botnets, pontos finais de tor) vão ser bloqueados em inquilinos B2C ou é necessário para tal um inquilino da edição Básica ou Premium?**

Temos um gateway que filtra os pedidos e que oferece alguma proteção contra botnets, que é aplicado a todos os inquilinos B2C.

## <a name="application-access"></a>Acesso à aplicação

**P: Onde posso encontrar uma lista das aplicações previamente integradas ao Azure AD e as respetivas funcionalidades?**

**R:** O Azure AD tem mais de 2.600 aplicações previamente integradas da Microsoft, de fornecedores de serviços de aplicações e parceiros. Todas as aplicações previamente integradas suportam o início de sessão único (SSO). O SSO permite-lhe utilizar as credenciais organizacionais para aceder às suas aplicações. Algumas das aplicações também suportam o aprovisionamento e cancelamento de aprovisionamento automatizados.

Para obter a lista completa das aplicações previamente integradas, consulte [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**P: E se a aplicação de que preciso não fizer parte do Azure AD Marketplace?**

**R:** Com o Azure AD Premium, pode adicionar e configurar qualquer aplicação que desejar. Dependendo das capacidades da sua aplicação e das suas preferências, pode configurar sSO e provisões automatizadas.  

Para obter mais informações, consulte:

* [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](../manage-apps/configure-saml-single-sign-on.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**P: Como os utilizadores iniciam sessão em aplicações com o Azure AD?**

**R:** O Azure AD oferece aos utilizadores várias formas de visualizarem e acederem às respetivas aplicações, tais como:

* O painel de acesso do Azure AD
* O lançador de aplicações Microsoft 365
* Início de sessão direto em aplicações federadas
* Ligações avançadas para aplicações federadas, baseadas em palavras-passe ou existentes

Para obter mais informações, consulte [experiências do utilizador final para aplicações.](../manage-apps/end-user-experiences.md)

---
**P: Quais são as várias formas de autenticação e início de sessão único do Azure AD das aplicações?**

**R:** O Azure AD suporta vários protocolos normalizados para a autenticação e autorização, tais como SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. O Azure AD também suporta cofres de palavras-passe e as capacidades de início de sessão automatizado para aplicações que apenas suportam autenticação baseada em formulários.  

Para obter mais informações, consulte:

* [Cenários de autenticação do Azure AD](../develop/authentication-vs-authorization.md)
* [Protocolos de autenticação de diretório ativo](/previous-versions/azure/dn151124(v=azure.100))
* [Inscrição única para aplicações no Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**P: Posso adicionar aplicações que estou a executar no local?**

**R:** O Proxy da Aplicação do Azure AD fornece um acesso fácil e seguro às Web Apps no local que escolher. Pode aceder a estas aplicações da mesma forma a que acede às aplicações de software como um serviço (SaaS) no Azure AD. Não é necessário nenhuma VPN ou alterar a infraestrutura de rede.  

Para mais informações, consulte [How to provide secure remote access to on-premises applications (Como fornecer acesso remoto seguro a aplicações no local)](../manage-apps/application-proxy.md).

---
**P: Como posso exigir a autenticação multifator para utilizadores que acedam a uma determinada aplicação?**

**A:** Com o Acesso Condicional AZURE AD, pode atribuir uma política de acesso única para cada aplicação. Na sua política, pode exigir a autenticação multifator sempre ou quando os utilizadores não estiverem ligados à rede local.  

Para obter mais informações, consulte [garantir o acesso ao Microsoft 365 e outras aplicações ligadas ao Azure Ative Directory](../conditional-access/overview.md).

---
**P: O que é o fornecimento automatizado de utilizadores para aplicações SaaS?**

**R:** Utilize o Azure AD para automatizar a criação, a manutenção e a remoção de identidades de utilizadores em diversas aplicações SaaS na cloud populares.

Para obter mais informações, consulte [automatizar o fornecimento e desprovisionamento de utilizadores para aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).

---
**P: Posso configurar uma ligação LDAP segura ao Azure AD? **

**A:**  Não. A Azure AD não suporta diretamente o protocolo de Protocolo de Acesso ao Diretório Leve (LDAP) nem o LDAP Seguro. No entanto, é possível permitir a instância dos Serviços de Domínio AZure AD (Azure AD DS) no seu inquilino AZure AD com grupos de segurança de rede devidamente configurados através do Azure Networking para alcançar a conectividade LDAP. Para obter mais informações, consulte [Configure secure LDAP para um domínio gerido por Serviços de Domínio do Diretório Ativo Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md)