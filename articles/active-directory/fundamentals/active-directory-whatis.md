---
title: O que é o Azure Active Directory? -O azure Active Directory | Documentos da Microsoft
description: Saiba mais sobre o Azure Active Directory, incluindo a terminologia necessária, o público-alvo, licenciamento Noções básicas e recursos associados.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 11/13/2018
ms.author: lizross
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d3b3cd7e759892c2215f190a682f5f69818958d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442373"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory? 
Azure Active Directory (Azure AD) é baseado na nuvem acesso e identidade do serviço de gestão da Microsoft. O Azure AD ajuda os funcionários de iniciar sessão e aceder a recursos no:

- Recursos externos, como o Microsoft Office 365, o portal do Azure e milhares de outras aplicações SaaS.

- Recursos internos, como as aplicações na sua rede empresarial e a intranet, juntamente com quaisquer aplicações na cloud desenvolvida pela sua organização.

Pode usar as várias [da Microsoft Cloud for Enterprise Architects série](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) cartazes para compreender melhor os serviços de identidade principais no Azure, Azure AD e o Office 365.

## <a name="who-uses-azure-ad"></a>Que utiliza o Azure AD?
Destina-se do Azure AD:

- **Administradores de TI.** Administrador de TI, pode utilizar o Azure AD para controlar o acesso para as suas aplicações e recursos da sua aplicação, com base nos seus requisitos empresariais. Por exemplo, pode utilizar o Azure AD para exigir autenticação multifator ao aceder a recursos organizacionais importantes. Além disso, pode utilizar o Azure AD para automatizar o aprovisionamento de utilizadores entre o AD do Windows Server existentes e as suas aplicações na cloud, incluindo o Office 365. Por fim, o Azure AD fornece ferramentas poderosas para automaticamente ajudar a proteger as identidades de utilizador e as credenciais e para cumprir os requisitos de governação de acesso. Para começar, inscreva-se para uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Programadores de aplicações.** Como um programador de aplicações do Azure AD oferece uma abordagem baseada em normas para adicionar início de sessão único (SSO) à sua aplicação, que lhe permite trabalhar com as credenciais já existente de um utilizador. O Azure AD também fornece APIs que podem ajudá-lo a criar experiências de aplicação personalizada tirar partido dos dados organizacionais existentes. Para começar, inscreva-se para uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Para obter mais informações, também pode consultar [do Azure Active Directory para programadores](../develop/index.yml).

- **Subscritores do Microsoft 365, Office 365, Azure ou do Dynamics CRM Online.** Como um assinante, já estiver a utilizar do Azure AD. Cada inquilino do Microsoft 365, Office 365, Azure e Dynamics CRM Online é automaticamente um inquilino do Azure AD. Pode começar a gerir o acesso às suas aplicações de cloud integrados.

## <a name="what-are-the-azure-ad-licenses"></a>Quais são as licenças do Azure AD?
Serviços de negócios Online da Microsoft, como o Office 365 ou o Microsoft Azure, necessitam do Azure AD para início de sessão e para ajudar na proteção de identidade. Por conseguinte, se se inscrever a qualquer serviço de negócios Online da Microsoft, obtém automaticamente do Azure AD com o acesso a todos os recursos gratuitos.

Para melhorar a sua implementação do Azure AD, também pode adicionar funcionalidades pagas atualizando para o Azure Active Directory básico, Premium P1 ou Premium P2 licenças. Paga licenças do Azure AD são criadas com base no seu diretório livre existente, fornecer monitorização avançada, self-service, relatórios de segurança e acesso seguro para a sua força de trabalho móvel.

>[!Note]
>Para as opções de preços destas licenças, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).<br><br>O Azure Active Directory Premium P1 e Premium P2 e o Azure Active Directory Básico não são suportados na China, atualmente. Para obter mais informações sobre os preços do Azure AD, pode contactar o [fórum do Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **O Azure Active Directory gratuito.** Fornece gestão de utilizadores e de grupo, a sincronização de diretórios no local, relatórios básicos e início de sessão único no Azure, Office 365 e muitas aplicações SaaS populares.

- **Azure Active Directory Básico.** Além das funcionalidades gratuitas, Basic também fornece acesso a aplicações voltada para a cloud, gestão de acesso baseado em grupo, senhas de auto-atendimento repor para aplicações na cloud e o Proxy de aplicações do Azure AD, que permite publicar aplicações no local web com o Azure AD.

- **Azure Active Directory Premium P1.** Além das funcionalidades básicas e gratuito, P1 também permite que os utilizadores de híbrida no local de acesso e recursos na cloud. Também suporta administração avançadas, tais como grupos dinâmicos, a gestão de grupos self-service, o Microsoft Identity Manager (no local acesso e identidade management suite) e recursos de repetição de escrita de nuvem, que permitem a senhas de auto-atendimento repor para os utilizadores no local.

- **Azure Active Directory Premium P2.** Além das funcionalidades gratuito, Basic e P1, P2 também oferece [do Azure Active Directory Identity Protection](../identity-protection/enable.md) para ajudar a fornecer acesso condicional com base em riscos às suas aplicações e dados críticos da empresa e [privilegiada de identidades Gestão](../privileged-identity-management/pim-getting-started.md) para ajudar a detetar, restringir e monitorizar os administradores e o respetivo acesso a recursos e fornecer acesso just-in-time quando necessário.

- **Licenças de recursos de "Pay as you go".** Também pode obter licenças de recursos adicionais, como o Azure Active Directory Business-cliente (B2C). B2C pode ajudar a fornecer a identidade e aceder às soluções de gestão para as suas aplicações do lado do cliente. Para obter mais informações, consulte [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre a associação de uma subscrição do Azure para o Azure AD, consulte [como: Associar ou adicionar uma subscrição do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) e para obter mais informações sobre a atribuição de licenças aos seus utilizadores, consulte [como: Atribuir ou remover licenças do Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologia
Para compreender melhor do Azure AD e a documentação, deve rever os termos seguintes.

|Termo ou conceito|Descrição|
|---------------|-----------|
|Subscrição do Azure| Utilizado para pagar pelos serviços cloud do Azure. Pode ter o número de subscrições e estão ligadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e fidedigna do Azure AD que é criado automaticamente quando a sua organização se inscreve numa subscrição de serviço na nuvem do Microsoft, como o Microsoft Azure, Microsoft Intune ou do Office 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Inquilinos do Azure que aceder a outros serviços num ambiente dedicado são considerados único inquilino.|
|Multi-inquilino| Inquilinos do Azure que aceder a outros serviços num ambiente compartilhado, entre várias organizações, são considerados multi-inquilinos.|
|Diretório do Azure AD|Cada inquilino do Azure tem um Azure dedicado e fidedigno diretório AD. Diretório do Azure AD inclui os utilizadores, grupos e aplicações do inquilino e é utilizado para efetuar a identidade e acesso a funções de gerenciamento para os recursos de inquilino.|
|Conta do Azure AD | Uma identidade criada através do Azure AD ou outro serviço cloud da Microsoft, como o Office 365. As identidades são armazenados no Azure AD e acessível para subscrições de serviços cloud da sua organização. Esta conta também, por vezes, é chamada um trabalho conta escolar ou profissional.|
|Domínio personalizado|Cada novo Azure diretório AD vem com um nome de domínio inicial, domainname.onmicrosoft.com. Além disso ou nome inicial, também pode adicionar nomes, que incluem os nomes de que usar para fazer negócios e os utilizadores utilizam para aceder aos recursos da sua organização, para a lista de domínio de sua organização. Adicionar nomes de domínio personalizados ajuda-o a criar nomes de utilizador que estão familiarizados aos seus utilizadores, tais como alain@contoso.com.|
|Administrador de Conta|Esta função de administrador de subscrição clássica é conceitualmente o proprietário de faturação de uma subscrição. Esta função tem acesso para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) e permite-lhe gerir todas as subscrições numa conta. Para obter mais informações, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador de Serviços|Esta função de administrador de subscrição clássica permite-lhe gerir todos os recursos do Azure, incluindo o acesso. Esta função tem o acesso equivalente de um utilizador que é atribuído a função de proprietário no âmbito da subscrição. Para obter mais informações, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietário|Esta função de ajuda-o a gerir todos os recursos do Azure, incluindo o acesso. Esta função baseia-se um sistema de autorização mais recente, chamado de controlo de acesso de base de função (RBAC) que fornece gestão de acessos detalhada para recursos do Azure. Para obter mais informações, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador global do Azure AD|Esta função de administrador é automaticamente atribuído a quem criou o inquilino do Azure AD. Os administradores globais podem efetuar todas as funções administrativas para o Azure AD e todos os serviços que federar com o Azure AD, como o Exchange Online, SharePoint Online e Skype para empresas Online. Pode ter vários administradores globais, mas apenas os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos utilizadores.<br><br>**Nota**<br>Esta função de administrador é chamada de Administrador Global no portal do Azure, mas ele é chamado **administrador da empresa** no Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell.<br><br>Para obter mais informações sobre as várias funções de administrador, consulte [permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta Microsoft (também denominada, MSA)|Contas pessoais que fornecem acesso a seus produtos da Microsoft orientados ao consumidor e serviços em nuvem, como o Outlook, OneDrive, Xbox LIVE ou do Office 365. Sua conta Microsoft é criada e armazenada no sistema de contas de identidades de consumidor de Microsoft que é executado pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Quais recursos de trabalho no Azure AD?
Depois de escolher sua licença do Azure AD, obterá acesso a alguns ou todos os recursos seguintes para a sua organização:

|Categoria|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerir as suas aplicações na cloud e no local com o Proxy de aplicações, único início de sessão, o portal das minhas aplicações (também conhecida como painel de acesso) e Software como aplicações de serviço (SaaS). Para obter mais informações, consulte [como fornecer acesso remoto seguro a aplicações no local](../manage-apps/application-proxy.md) e [documentação de gestão de aplicações](../manage-apps/index.yml).|
|Autenticação|Gerir o Azure Active Directory de reposição de palavra-passe self-service, multi-factor Authentication, a lista de palavras-passe banidas personalizado e bloqueio inteligente. Para obter mais informações, consulte [documentação de autenticação do Azure AD](../authentication/index.yml).|
|Empresa-empresa (B2B)|Gerir os seus utilizadores convidados e parceiros externos, e manter o controlo sobre os seus próprios dados empresariais. Para obter mais informações, consulte [documentação do Azure Active Directory B2B](../b2b/index.yml).|
|Empresa-cliente (B2C)|Personalizar e controlar como os utilizadores iniciam sessão, inicie sessão e gerem os respetivos perfis quando utilizar as suas aplicações. Para obter mais informações, consulte [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Acesso condicional|Gerir o acesso às suas aplicações na cloud. Para obter mais informações, consulte [documentação de acesso condicional do Azure AD](../conditional-access/index.yml).|
|Azure Active Directory para programadores|Crie aplicações que inicie sessão em todas as identidades da Microsoft, obtenha tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para obter mais informações, consulte [plataforma de identidade (Azure Active Directory para programadores) da Microsoft](../develop/index.yml).|
|Gestão de Dispositivos|Gerir como os dispositivos na cloud ou no local aceder aos dados da sua empresa. Para obter mais informações, consulte [documentação da gestão de dispositivos do Azure AD](../devices/index.yml).|
|Serviços de domínio|Associe máquinas virtuais do Azure a um domínio sem utilizar controladores de domínio. Para obter mais informações, consulte [documentação do Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Utilizadores empresariais|Gerir a atribuição de licença, acesso a aplicações e configure a delegados através de grupos e funções de administrador. Para obter mais informações, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).|
|Identidade híbrida|Utilize o Azure Active Directory Connect e Connect Health para proporcionar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização (na cloud ou no local). Para obter mais informações, consulte [documentação de identidade híbrida](../hybrid/index.yml).|
|Governação de identidade|Gerir a identidade da sua organização por meio dos funcionários, parceiros de negócios, fornecedor, serviço e controlos de acesso da aplicação. Também pode executar as revisões de acesso. Para obter mais informações, consulte [documentação de governação de identidade do Azure AD](../governance/identity-governance-overview.md) e [revisões de acesso do Azure AD](../governance/access-reviews-overview.md).|
|Proteção de identidade|Detete potenciais vulnerabilidades que afetam as identidades da sua organização, configurar políticas para responder a ações suspeitas e, em seguida, execute as ações apropriadas para resolvê-los. Para obter mais informações, consulte [do Azure AD Identity Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece os serviços do Azure com uma identidade gerida automaticamente no Azure AD que pode autenticar qualquer serviço de autenticação do Azure AD com suporte, incluindo o Key Vault. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).|
|Gestão de identidades privilegiadas (PIM)|Gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos no Azure AD, recursos do Azure e outros serviços Online da Microsoft, como o Office 365 ou o Intune. Para obter mais informações, consulte [do Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitorização|Obter informações sobre os padrões de segurança e utilização no seu ambiente. Para obter mais informações, consulte [monitorização e relatórios do Azure Active Directory](../reports-monitoring/index.yml).|


## <a name="next-steps"></a>Passos Seguintes
- [Inscrever-se no Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associar uma subscrição do Azure para o Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Aceder ao Azure Active Directory e criar um novo inquilino](active-directory-access-create-new-tenant.md)

- [O Azure Active Directory Premium P2 funcionalidade implementação lista de verificação](active-directory-deployment-checklist-p2.md)
