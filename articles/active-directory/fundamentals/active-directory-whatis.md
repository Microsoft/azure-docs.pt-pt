---
title: O que é o Azure Active Directory? -Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 8e0e028be6b4a507ba97f9126c791ee77c764c70
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880502"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?

O Azure Active Directory (AD do Azure) é o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft, que ajuda seus funcionários a entrar e acessar recursos no:

- Recursos externos, como Microsoft Office 365, o portal do Azure e milhares de outros aplicativos SaaS.

- Recursos internos, como aplicativos em sua rede corporativa e intranet, juntamente com qualquer aplicativo de nuvem desenvolvido por sua própria organização.

Você pode usar os vários [Microsoft Cloud para os cartazes da série Enterprise Architects](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) para entender melhor os principais serviços de identidade no Azure, no Azure AD e no Office 365.

## <a name="who-uses-azure-ad"></a>Quem usa o Azure AD?

O Azure AD destina-se a:

- **Administradores de TI.** Como administrador de ti, você pode usar o Azure AD para controlar o acesso aos seus aplicativos e aos recursos do seu aplicativo, com base em seus requisitos de negócios. Por exemplo, você pode usar o Azure AD para exigir a autenticação multifator ao acessar recursos organizacionais importantes. Além disso, você pode usar o Azure AD para automatizar o provisionamento de usuário entre o AD do Windows Server existente e seus aplicativos de nuvem, incluindo o Office 365. Por fim, o Azure AD oferece ferramentas poderosas para ajudar automaticamente a proteger identidades e credenciais de usuário e para atender aos requisitos de governança de acesso. Para começar, Inscreva-se para uma [avaliação de Azure Active Directory Premium de 30 dias gratuita](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Desenvolvedores de aplicativos.** Como desenvolvedor de aplicativos, você pode usar o Azure AD como uma abordagem baseada em padrões para adicionar SSO (logon único) ao seu aplicativo, permitindo que ele trabalhe com as credenciais preexistentes de um usuário. O AD do Azure também fornece APIs que podem ajudá-lo a criar experiências de aplicativo personalizadas usando dados organizacionais existentes. Para começar, Inscreva-se para uma [avaliação de Azure Active Directory Premium de 30 dias gratuita](https://azure.microsoft.com/trial/get-started-active-directory/). Para obter mais informações, você também pode ver [Azure Active Directory para desenvolvedores](../develop/index.yml).

- **Assinantes do Microsoft 365, Office 365, Azure ou Dynamics CRM Online.** Como assinante, você já está usando o Azure AD. Cada Microsoft 365, Office 365, Azure e locatário do Dynamics CRM Online é automaticamente um locatário do Azure AD. Você pode começar imediatamente a gerenciar o acesso aos seus aplicativos de nuvem integrados.

## <a name="what-are-the-azure-ad-licenses"></a>Quais são as licenças do Azure AD?

O Microsoft Online Business Services, como o Office 365 ou Microsoft Azure, requer o Azure AD para entrar e ajudar com a proteção de identidade. Se você se inscrever em qualquer serviço comercial da Microsoft Online, você obtém automaticamente o Azure AD com acesso a todos os recursos gratuitos.

Para aprimorar a implementação do Azure AD, você também pode adicionar recursos pagos Atualizando para as licenças Azure Active Directory Premium P1 ou Premium P2. As licenças pagas do Azure AD são criadas sobre seu diretório livre existente, fornecendo autoatendimento, monitoramento avançado, relatórios de segurança e acesso seguro para seus usuários móveis.

>[!Note]
>Para obter as opções de preço dessas licenças, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 e Premium P2 não têm suporte no momento na China. Para obter mais informações sobre preços do Azure AD, contate o [Fórum de Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Gratuito.** Fornece gerenciamento de usuário e grupo, sincronização de diretório local, relatórios básicos, alteração de senha de autoatendimento para usuários de nuvem e logon único no Azure, Office 365 e muitos aplicativos SaaS populares.

- **Azure Active Directory Premium P1.** Além dos recursos gratuitos, P1 também permite que seus usuários híbridos acessem recursos locais e na nuvem. Ele também dá suporte à administração avançada, como grupos dinâmicos, gerenciamento de grupo de autoatendimento Microsoft Identity Manager (um pacote de gerenciamento de identidade e acesso local) e recursos de write-back de nuvem, que permitem a redefinição de senha de autoatendimento para seus usuários locais.

- **Azure Active Directory Premium P2.** Além dos recursos gratuitos e P1, o P2 também oferece [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) para ajudar a fornecer acesso condicional baseado em risco aos seus aplicativos e dados críticos da empresa e [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) para ajudar a descobrir, Restrinja e monitore os administradores e seus acessos a recursos e forneça acesso just-in-time quando necessário.

- **Licenças de recurso "pré-pago".** Você também pode obter licenças de recursos adicionais, como Azure Active Directory empresa a cliente (B2C). O B2C pode ajudá-lo a fornecer soluções de gerenciamento de acesso e identidade para seus aplicativos voltados para o cliente. Para obter mais informações, consulte a [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre como associar uma assinatura do Azure ao Azure AD, consulte [como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) e para obter mais informações sobre como atribuir licenças a seus usuários, consulte [como adicionar ou remover o Azure Active Directory licenças](license-users-groups.md).

## <a name="terminology"></a>Terminologia

Para entender melhor o Azure AD e sua documentação, é recomendável revisar os termos a seguir.

|Termo ou conceito|Descrição|
|---------------|-----------|
|Identidade| Algo que pode ser autenticado. Uma identidade pode ser um usuário com um nome de usuários e senha. As identidades também incluem aplicativos ou outros servidores que podem exigir autenticação por meio de certificados ou chaves secretas.|
|Conta| Uma identidade que tem dados associados a ela. Você não pode ter uma conta sem uma identidade.|
|Conta do Azure AD| Uma identidade criada por meio do Azure AD ou de outro serviço de nuvem da Microsoft, como o Office 365. As identidades são armazenadas no Azure AD e podem ser acessadas pelas assinaturas de serviço de nuvem da sua organização. Essa conta às vezes também é chamada de conta corporativa ou de estudante.|
|Subscrição do Azure| Usado para pagar pelos serviços de nuvem do Azure. Você pode ter muitas assinaturas e elas estão vinculadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e confiável do Azure AD criada automaticamente quando sua organização se inscreve para uma assinatura do serviço de nuvem da Microsoft, como Microsoft Azure, Microsoft Intune ou Office 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Os locatários do Azure que acessam outros serviços em um ambiente dedicado são considerados locatário único.|
|Multi-inquilino| Os locatários do Azure que acessam outros serviços em um ambiente compartilhado, em várias organizações, são considerados multilocatários.|
|Diretório do Azure AD|Cada locatário do Azure tem um diretório do Azure AD dedicado e confiável. O diretório do AD do Azure inclui os usuários, grupos e aplicativos do locatário e é usado para executar funções de gerenciamento de acesso e identidade para recursos de locatário.|
|Domínio personalizado|Cada novo diretório do Azure AD é fornecido com um nome de domínio inicial, domainname.onmicrosoft.com. Além desse nome inicial, você também pode adicionar os nomes de domínio da sua organização, que incluem os nomes que você usa para fazer negócios e que os usuários usam para acessar os recursos da sua organização, na lista. Adicionar nomes de domínio personalizados ajuda a criar nomes de usuário que são familiares para seus usuários, como alain@contoso.com.|
|Administrador de Conta|Essa função de administrador de assinatura clássica é conceitualmente o proprietário de cobrança de uma assinatura. Essa função tem acesso ao [centro de contas do Azure](https://account.azure.com/Subscriptions) e permite que você gerencie todas as assinaturas em uma conta. Para obter mais informações, consulte funções de [administrador de assinatura clássica, funções de RBAC (controle de acesso baseado em função) do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador de Serviços|Essa função de administrador de assinatura clássica permite que você gerencie todos os recursos do Azure, incluindo o acesso. Essa função tem o acesso equivalente de um usuário que recebe a função de proprietário no escopo da assinatura. Para obter mais informações, consulte [funções de administrador de assinatura clássica, funções de RBAC do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietário|Essa função ajuda você a gerenciar todos os recursos do Azure, incluindo o acesso. Essa função é criada em um sistema de autorização mais recente chamado de RBAC (controle de acesso de base de função) que fornece gerenciamento de acesso refinado aos recursos do Azure. Para obter mais informações, consulte [funções de administrador de assinatura clássica, funções de RBAC do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador global do Azure AD|Essa função de administrador é atribuída automaticamente ao quem criado pelo locatário do Azure AD. Os administradores globais podem fazer todas as funções administrativas do Azure AD e de todos os serviços que se federam ao Azure AD, como o Exchange Online, o SharePoint Online e o Skype for Business online. Você pode ter vários administradores globais, mas somente os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos usuários.<br><br>**Nota**<br>Essa função de administrador é chamada de administrador global na portal do Azure, mas é chamada de **administrador da empresa** na API Microsoft Graph, no azure ad API do Graph e no Azure ad PowerShell.<br><br>Para obter mais informações sobre as várias funções de administrador, consulte [permissões de função de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta Microsoft (também chamado de MSA)|Contas pessoais que fornecem acesso aos seus produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook, OneDrive, Xbox LIVE ou Office 365. Seu conta Microsoft é criado e armazenado no sistema de conta de identidade do consumidor da Microsoft que é executado pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Quais recursos funcionam no Azure AD?

Depois de escolher sua licença do Azure AD, você terá acesso a alguns ou a todos os recursos a seguir para sua organização:

|Categoria|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerencie seus aplicativos de nuvem e locais usando o proxy de aplicativo, logon único, o portal meus aplicativos (também conhecido como painel de acesso) e aplicativos SaaS (software como serviço). Para obter mais informações, consulte [como fornecer acesso remoto seguro a aplicativos locais](../manage-apps/application-proxy.md) e documentação de [Gerenciamento de aplicativos](../manage-apps/index.yml).|
|Autenticação|Gerencie Azure Active Directory redefinição de senha de autoatendimento, autenticação multifator, lista de senhas excluídas personalizada e bloqueio inteligente. Para obter mais informações, consulte a [documentação de autenticação do Azure ad](../authentication/index.yml).|
|Empresa-Empresa (B2B)|Gerencie seus usuários convidados e parceiros externos, mantendo o controle sobre seus próprios dados corporativos. Para obter mais informações, consulte a [documentação B2B do Azure Active Directory](../b2b/index.yml).|
|Empresa-Consumidor (B2C)|Personalize e controle como os usuários se inscrevem, entram e gerenciam seus perfis ao usar seus aplicativos. Para obter mais informações, consulte a [documentação do Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerencie o acesso aos seus aplicativos de nuvem. Para obter mais informações, consulte [documentação de acesso condicional do Azure ad](../conditional-access/index.yml).|
|Azure Active Directory para programadores|Crie aplicativos que entram em todas as identidades da Microsoft, obtenha tokens para chamar Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para obter mais informações, consulte [plataforma de identidade da Microsoft (Azure Active Directory para desenvolvedores)](../develop/index.yml).|
|Gestão de Dispositivos|Gerencie como seus dispositivos de nuvem ou locais acessam seus dados corporativos. Para obter mais informações, consulte a [documentação de gerenciamento de dispositivos do Azure ad](../devices/index.yml).|
|Serviços de domínio|Ingresse máquinas virtuais do Azure em um domínio sem usar controladores de domínio. Para obter mais informações, consulte a [documentação do Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Utilizadores empresariais|Gerencie a atribuição de licenças, o acesso a aplicativos e configure delegados usando grupos e funções de administrador. Para obter mais informações, consulte [Azure Active Directory documentação de gerenciamento de usuário](../users-groups-roles/index.yml).|
|Identidade híbrida|Use Azure Active Directory Connect e conecte a integridade para fornecer uma única identidade de usuário para autenticação e autorização para todos os recursos, independentemente do local (nuvem ou local). Para obter mais informações, consulte [documentação de identidade híbrida](../hybrid/index.yml).|
|Governação de identidades|Gerencie a identidade da sua organização por meio de controles de acesso de funcionários, parceiros comerciais, fornecedores, serviços e aplicativos. Você também pode executar revisões de acesso. Para obter mais informações, consulte [documentação do Azure ad Identity Governance](../governance/identity-governance-overview.md) e [revisões de acesso do Azure ad](../governance/access-reviews-overview.md).|
|Proteção de identidade|Detecte possíveis vulnerabilidades que afetam as identidades da sua organização, configure políticas para responder a ações suspeitas e, em seguida, execute a ação apropriada para resolvê-las. Para obter mais informações, consulte [Azure ad Identity Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD que pode autenticar qualquer serviço de autenticação com suporte do Azure AD, incluindo Key Vault. Para obter mais informações, consulte [o que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Gerencie, controle e monitore o acesso em sua organização. Esse recurso inclui acesso a recursos no Azure AD e no Azure, além de outros serviços online da Microsoft, como o Office 365 ou o Intune. Para obter mais informações, consulte [Azure ad Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitorização|Obter informações sobre os padrões de segurança e uso em seu ambiente. Para obter mais informações, consulte [Azure Active Directory relatórios e monitoramento](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Passos seguintes

- [Inscreva-se para Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associar uma assinatura do Azure ao seu Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Acessar Azure Active Directory e criar um novo locatário](active-directory-access-create-new-tenant.md)

- [Lista de verificação de implantação de recursos do Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
