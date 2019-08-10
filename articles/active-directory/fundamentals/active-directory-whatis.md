---
title: O que é o Azure Active Directory? -O azure Active Directory | Documentos da Microsoft
description: Visão geral e informações conceituais sobre Azure Active Directory, incluindo terminologia, quais licenças estão disponíveis e uma lista de recursos associados com links para obter mais informações.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a52b9ee26aac20d95356db6a280c5feb40023fed
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942796"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?

O Azure Active Directory (AD do Azure) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft, que ajuda seus funcionários a entrar e acessar recursos no:

- Recursos externos, como o Microsoft Office 365, o portal do Azure e milhares de outras aplicações SaaS.

- Recursos internos, como as aplicações na sua rede empresarial e a intranet, juntamente com quaisquer aplicações na cloud desenvolvida pela sua organização.

Pode usar as várias [da Microsoft Cloud for Enterprise Architects série](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) cartazes para compreender melhor os serviços de identidade principais no Azure, Azure AD e o Office 365.

## <a name="who-uses-azure-ad"></a>Que utiliza o Azure AD?

Destina-se do Azure AD:

- **Administradores de TI.** Administrador de TI, pode utilizar o Azure AD para controlar o acesso para as suas aplicações e recursos da sua aplicação, com base nos seus requisitos empresariais. Por exemplo, pode utilizar o Azure AD para exigir autenticação multifator ao aceder a recursos organizacionais importantes. Além disso, pode utilizar o Azure AD para automatizar o aprovisionamento de utilizadores entre o AD do Windows Server existentes e as suas aplicações na cloud, incluindo o Office 365. Por fim, o Azure AD fornece ferramentas poderosas para automaticamente ajudar a proteger as identidades de utilizador e as credenciais e para cumprir os requisitos de governação de acesso. Para começar, inscreva-se para uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Programadores de aplicações.** Como desenvolvedor de aplicativos, você pode usar o Azure AD como uma abordagem baseada em padrões para adicionar SSO (logon único) ao seu aplicativo, permitindo que ele trabalhe com as credenciais preexistentes de um usuário. O AD do Azure também fornece APIs que podem ajudá-lo a criar experiências de aplicativo personalizadas usando dados organizacionais existentes. Para começar, inscreva-se para uma [avaliação gratuita de 30 dias do Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Para obter mais informações, também pode consultar [do Azure Active Directory para programadores](../develop/index.yml).

- **Subscritores do Microsoft 365, Office 365, Azure ou do Dynamics CRM Online.** Como um assinante, já estiver a utilizar do Azure AD. Cada inquilino do Microsoft 365, Office 365, Azure e Dynamics CRM Online é automaticamente um inquilino do Azure AD. Pode começar a gerir o acesso às suas aplicações de cloud integrados.

## <a name="what-are-the-azure-ad-licenses"></a>Quais são as licenças do Azure AD?

Serviços de negócios Online da Microsoft, como o Office 365 ou o Microsoft Azure, necessitam do Azure AD para início de sessão e para ajudar na proteção de identidade. Se você se inscrever em qualquer serviço comercial da Microsoft Online, você obtém automaticamente o Azure AD com acesso a todos os recursos gratuitos.

Para aprimorar a implementação do Azure AD, você também pode adicionar recursos pagos Atualizando para as licenças Azure Active Directory Premium P1 ou Premium P2. As licenças pagas do Azure AD são criadas sobre seu diretório livre existente, fornecendo autoatendimento, monitoramento avançado, relatórios de segurança e acesso seguro para seus usuários móveis.

>[!Note]
>Para as opções de preços destas licenças, consulte [preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 e Premium P2 não têm suporte no momento na China. Para obter mais informações sobre preços do Azure AD, contate o [Fórum de Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **O Azure Active Directory gratuito.** Fornece gerenciamento de usuário e grupo, sincronização de diretório local, relatórios básicos, alteração de senha de autoatendimento para usuários de nuvem e logon único no Azure, Office 365 e muitos aplicativos SaaS populares.

- **Azure Active Directory Premium P1.** Além dos recursos gratuitos, P1 também permite que seus usuários híbridos acessem recursos locais e na nuvem. Também suporta administração avançadas, tais como grupos dinâmicos, a gestão de grupos self-service, o Microsoft Identity Manager (no local acesso e identidade management suite) e recursos de repetição de escrita de nuvem, que permitem a senhas de auto-atendimento repor para os utilizadores no local.

- **Azure Active Directory Premium P2.** Além dos recursos gratuitos e P1, o P2 também oferece [Azure Active Directory Identity Protection](../identity-protection/enable.md) para ajudar a fornecer acesso condicional baseado em risco aos seus aplicativos e dados críticos da empresa e [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) para ajudar a descobrir, Restrinja e monitore os administradores e seus acessos a recursos e forneça acesso just-in-time quando necessário.

- **Licenças de recursos de "Pay as you go".** Também pode obter licenças de recursos adicionais, como o Azure Active Directory Business-cliente (B2C). B2C pode ajudar a fornecer a identidade e aceder às soluções de gestão para as suas aplicações do lado do cliente. Para obter mais informações, consulte [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre como associar uma assinatura do Azure ao Azure ad [, consulte Como: Associe ou adicione uma assinatura do Azure ao](active-directory-how-subscriptions-associated-directory.md) Azure Active Directory e para obter mais informações sobre como atribuir licenças a seus usuários, [consulte Como: Atribuir ou remover licenças](license-users-groups.md)Azure Active Directory.

## <a name="terminology"></a>Terminologia

Para entender melhor o Azure AD e sua documentação, é recomendável revisar os termos a seguir.

|Termo ou conceito|Descrição|
|---------------|-----------|
|identidade| Algo que pode ser autenticado. Uma identidade pode ser um usuário com um nome de usuários e senha. As identidades também incluem aplicativos ou outros servidores que podem exigir autenticação por meio de certificados ou chaves secretas.|
|Conta| Uma identidade que tem dados associados a ela. Você não pode ter uma conta sem uma identidade.|
|Conta do Azure AD| Uma identidade criada através do Azure AD ou outro serviço cloud da Microsoft, como o Office 365. As identidades são armazenados no Azure AD e acessível para subscrições de serviços cloud da sua organização. Esta conta também, por vezes, é chamada um trabalho conta escolar ou profissional.|
|Subscrição do Azure| Utilizado para pagar pelos serviços cloud do Azure. Pode ter o número de subscrições e estão ligadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e fidedigna do Azure AD que é criado automaticamente quando a sua organização se inscreve numa subscrição de serviço na nuvem do Microsoft, como o Microsoft Azure, Microsoft Intune ou do Office 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Inquilinos do Azure que aceder a outros serviços num ambiente dedicado são considerados único inquilino.|
|Multi-inquilino| Inquilinos do Azure que aceder a outros serviços num ambiente compartilhado, entre várias organizações, são considerados multi-inquilinos.|
|Diretório do Azure AD|Cada inquilino do Azure tem um Azure dedicado e fidedigno diretório AD. Diretório do Azure AD inclui os utilizadores, grupos e aplicações do inquilino e é utilizado para efetuar a identidade e acesso a funções de gerenciamento para os recursos de inquilino.|
|Domínio personalizado|Cada novo Azure diretório AD vem com um nome de domínio inicial, domainname.onmicrosoft.com. Além disso ou nome inicial, também pode adicionar nomes, que incluem os nomes de que usar para fazer negócios e os utilizadores utilizam para aceder aos recursos da sua organização, para a lista de domínio de sua organização. Adicionar nomes de domínio personalizados ajuda-o a criar nomes de utilizador que estão familiarizados aos seus utilizadores, tais como alain@contoso.com.|
|Administrador de Conta|Esta função de administrador de subscrição clássica é conceitualmente o proprietário de faturação de uma subscrição. Esta função tem acesso para o [Centro de contas do Azure](https://account.azure.com/Subscriptions) e permite-lhe gerir todas as subscrições numa conta. Para obter mais informações, consulte funções de [administrador de assinatura clássica, funções de RBAC (controle de acesso baseado em função) do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador de Serviços|Esta função de administrador de subscrição clássica permite-lhe gerir todos os recursos do Azure, incluindo o acesso. Esta função tem o acesso equivalente de um utilizador que é atribuído a função de proprietário no âmbito da subscrição. Para obter mais informações, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietário|Esta função de ajuda-o a gerir todos os recursos do Azure, incluindo o acesso. Esta função baseia-se um sistema de autorização mais recente, chamado de controlo de acesso de base de função (RBAC) que fornece gestão de acessos detalhada para recursos do Azure. Para obter mais informações, consulte [funções de administrador de subscrição clássico, funções RBAC do Azure e funções de administrador do Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador global do Azure AD|Esta função de administrador é automaticamente atribuído a quem criou o inquilino do Azure AD. Os administradores globais podem fazer todas as funções administrativas do Azure AD e de todos os serviços que se federam ao Azure AD, como o Exchange Online, o SharePoint Online e o Skype for Business online. Pode ter vários administradores globais, mas apenas os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos utilizadores.<br><br>**Nota**<br>Esta função de administrador é chamada de Administrador Global no portal do Azure, mas ele é chamado **administrador da empresa** no Microsoft Graph API, o Azure AD Graph API e o Azure AD PowerShell.<br><br>Para obter mais informações sobre as várias funções de administrador, consulte [permissões da função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta Microsoft (também denominada, MSA)|Contas pessoais que fornecem acesso a seus produtos da Microsoft orientados ao consumidor e serviços em nuvem, como o Outlook, OneDrive, Xbox LIVE ou do Office 365. Sua conta Microsoft é criada e armazenada no sistema de contas de identidades de consumidor de Microsoft que é executado pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Quais recursos funcionam no Azure AD?

Depois de escolher sua licença do Azure AD, você terá acesso a alguns ou a todos os recursos a seguir para sua organização:

|Category|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerir as suas aplicações na cloud e no local com o Proxy de aplicações, único início de sessão, o portal das minhas aplicações (também conhecida como painel de acesso) e Software como aplicações de serviço (SaaS). Para obter mais informações, consulte [como fornecer acesso remoto seguro a aplicações no local](../manage-apps/application-proxy.md) e [documentação de gestão de aplicações](../manage-apps/index.yml).|
|Autenticação|Gerir o Azure Active Directory de reposição de palavra-passe self-service, multi-factor Authentication, a lista de palavras-passe banidas personalizado e bloqueio inteligente. Para obter mais informações, consulte [documentação de autenticação do Azure AD](../authentication/index.yml).|
|Empresa-empresa (B2B)|Gerir os seus utilizadores convidados e parceiros externos, e manter o controlo sobre os seus próprios dados empresariais. Para obter mais informações, consulte [documentação do Azure Active Directory B2B](../b2b/index.yml).|
|Empresa-cliente (B2C)|Personalizar e controlar como os utilizadores iniciam sessão, inicie sessão e gerem os respetivos perfis quando utilizar as suas aplicações. Para obter mais informações, consulte [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerir o acesso às suas aplicações na cloud. Para obter mais informações, consulte [documentação de acesso condicional do Azure AD](../conditional-access/index.yml).|
|Azure Active Directory para programadores|Crie aplicações que inicie sessão em todas as identidades da Microsoft, obtenha tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para obter mais informações, consulte [plataforma de identidade (Azure Active Directory para programadores) da Microsoft](../develop/index.yml).|
|Gestão de Dispositivos|Gerir como os dispositivos na cloud ou no local aceder aos dados da sua empresa. Para obter mais informações, consulte [documentação da gestão de dispositivos do Azure AD](../devices/index.yml).|
|Serviços de domínio|Associe máquinas virtuais do Azure a um domínio sem utilizar controladores de domínio. Para obter mais informações, consulte [documentação do Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Utilizadores empresariais|Gerir a atribuição de licença, acesso a aplicações e configure a delegados através de grupos e funções de administrador. Para obter mais informações, consulte [documentação de gestão de utilizador do Azure Active Directory](../users-groups-roles/index.yml).|
|Identidade híbrida|Utilize o Azure Active Directory Connect e Connect Health para proporcionar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização (na cloud ou no local). Para obter mais informações, consulte [documentação de identidade híbrida](../hybrid/index.yml).|
|Governação de identidade|Gerir a identidade da sua organização por meio dos funcionários, parceiros de negócios, fornecedor, serviço e controlos de acesso da aplicação. Também pode executar as revisões de acesso. Para obter mais informações, consulte [documentação de governação de identidade do Azure AD](../governance/identity-governance-overview.md) e [revisões de acesso do Azure AD](../governance/access-reviews-overview.md).|
|Proteção de identidade|Detete potenciais vulnerabilidades que afetam as identidades da sua organização, configurar políticas para responder a ações suspeitas e, em seguida, execute as ações apropriadas para resolvê-los. Para obter mais informações, consulte [do Azure AD Identity Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece os serviços do Azure com uma identidade gerida automaticamente no Azure AD que pode autenticar qualquer serviço de autenticação do Azure AD com suporte, incluindo o Key Vault. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).|
|Gestão de identidades privilegiadas (PIM)|Gerir, controlar e monitorizar o acesso dentro da sua organização. Esse recurso inclui acesso a recursos no Azure AD e no Azure, além de outros serviços online da Microsoft, como o Office 365 ou o Intune. Para obter mais informações, consulte [do Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitorização|Obter informações sobre os padrões de segurança e utilização no seu ambiente. Para obter mais informações, consulte [monitorização e relatórios do Azure Active Directory](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Passos Seguintes

- [Inscrever-se no Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associar uma subscrição do Azure para o Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Aceder ao Azure Active Directory e criar um novo inquilino](active-directory-access-create-new-tenant.md)

- [O Azure Active Directory Premium P2 funcionalidade implementação lista de verificação](active-directory-deployment-checklist-p2.md)
