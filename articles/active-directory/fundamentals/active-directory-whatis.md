---
title: O que é o Azure Active Directory? - Diretório Ativo Azure / Microsoft Docs
description: Visão geral e informação conceptual sobre o Azure Ative Directory, incluindo terminologia, quais as licenças disponíveis, e uma lista de funcionalidades associadas com links para mais informações.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1709bd42fb77bd4f18b8a5e9d1af6e07be67b6c4
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043081"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?

O Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a iniciar e aceder a recursos em:

- Recursos externos, como o Microsoft 365, o portal Azure, e milhares de outras aplicações SaaS.

- Recursos internos, como aplicações na sua rede corporativa e intranet, juntamente com quaisquer aplicações em nuvem desenvolvidas pela sua própria organização. Para obter mais informações sobre a criação de um inquilino para a sua organização, consulte [Quickstart: Crie um novo inquilino no Azure Ative Directory](active-directory-access-create-new-tenant.md).

Para saber a diferença entre Azure AD e Ative Directory Domain Services, consulte [Compare Ative Directory com Azure Ative Directory](active-directory-compare-azure-ad-to-ad.md). Também pode utilizar os vários cartazes da [Microsoft Cloud for Enterprise Architects Series](/microsoft-365/solutions/cloud-architecture-models?view=o365-worldwide) para entender melhor os serviços de identidade nuclear em Azure, Azure AD e Microsoft 365.

## <a name="who-uses-azure-ad"></a>Quem utiliza o Azure AD?

A Azure AD destina-se a:

- **Administradores de TI.** Como administrador de TI, pode utilizar o Azure AD para controlar o acesso às suas apps e aos recursos da sua aplicação, com base nos requisitos do seu negócio. Por exemplo, pode utilizar a Azure AD para exigir a autenticação de vários fatores ao aceder a importantes recursos organizacionais. Além disso, pode utilizar o Azure AD para automatizar o fornecimento de utilizadores entre o atual Windows Server AD e as suas aplicações na nuvem, incluindo o Microsoft 365. Por fim, o Azure AD dá-lhe ferramentas poderosas para ajudar automaticamente a proteger as identidades e credenciais dos utilizadores e a satisfazer os seus requisitos de governação de acesso. Para começar, inscreva-se para um [teste gratuito de 30 dias do Azure Ative Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Desenvolvedores de aplicativos.** Como desenvolvedor de aplicações, pode utilizar o Azure AD como uma abordagem baseada em padrões para adicionar um único sign-on (SSO) à sua aplicação, permitindo-lhe trabalhar com as credenciais pré-existentes de um utilizador. O Azure AD também fornece APIs que podem ajudá-lo a construir experiências de aplicações personalizadas usando dados organizacionais existentes. Para começar, inscreva-se para um [teste gratuito de 30 dias do Azure Ative Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Para mais informações, também pode ver [o Azure Ative Directory para programadores.](../develop/index.yml)

- **Microsoft 365, Office 365, Azure ou Dynamics CRM Online.** Como assinante, já está a utilizar o Azure AD. Cada inquilino do Microsoft 365, Office 365, Azure e Dynamics CRM Online é automaticamente um inquilino do Azure AD. Pode começar imediatamente a gerir o acesso às suas aplicações integradas na nuvem.

## <a name="what-are-the-azure-ad-licenses"></a>Quais são as licenças da AD Azure?

Os serviços empresariais da Microsoft Online, como o Microsoft 365 ou o Microsoft Azure, requerem a Azure AD para iniciar sismo e para ajudar na proteção de identidade. Se subscrever qualquer serviço de negócios do Microsoft Online, obtém automaticamente o AD Azure com acesso a todas as funcionalidades gratuitas.

Para melhorar a sua implementação Azure AD, também pode adicionar capacidades pagas através da atualização das licenças Azure Ative Directory Premium P1 ou Premium P2. As licenças pagas Azure AD são construídas em cima do seu diretório gratuito existente, fornecendo self-service, monitorização melhorada, relatórios de segurança e acesso seguro para os seus utilizadores móveis.

>[!Note]
>Para as opções de preços destas licenças, consulte [o Azure Ative Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/).
>
>O Azure Ative Directory Premium P1 e o Premium P2 não são atualmente apoiados na China. Para mais informações sobre os preços da AD Azure, contacte o [Azure Ative Directory Forum](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Ative Directy Free.** Fornece gestão de utilizadores e grupos, sincronização de diretórios no local, relatórios básicos, alteração de senha de autosserviço para utilizadores na nuvem, e um único sinal de entrada em Azure, Microsoft 365 e muitas aplicações populares do SaaS.

- **Azure Ative Directory Premium P1.** Além das funcionalidades Free, o P1 também permite aos seus utilizadores híbridos acederem tanto ao local como aos recursos na nuvem. Também suporta administração avançada, como grupos dinâmicos, gestão de grupos de self-service, Microsoft Identity Manager (uma suite de gestão de identidade e acesso no local) e capacidades de back-back de cloud, que permitem o reset da palavra-passe de autosserviço para os seus utilizadores no local.

- **Azure Ative Directory Premium P2.** Além das funcionalidades Free e P1, o P2 também oferece proteção de [identidade do Diretório Ativo Azure](../identity-protection/overview-identity-protection.md) para ajudar a fornecer acesso condicional baseado no risco às suas apps e dados críticos da empresa e [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-getting-started.md) para ajudar a descobrir, restringir e monitorizar os administradores e o seu acesso aos recursos e fornecer acesso just-in-time quando necessário.

- **Licenças de recurso "Pay as you go".** Também pode obter licenças de recursos adicionais, tais como Azure Ative Directory Business-to-Customer (B2C). O B2C pode ajudá-lo a fornecer soluções de gestão de identidade e acesso para as suas aplicações viradas para o cliente. Para mais informações, consulte [a documentação do Azure Ative Directory B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre a associação de uma subscrição da Azure à Azure AD, consulte [o Associado ou adicione uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md) e para obter mais informações sobre a atribuição de licenças aos seus utilizadores, consulte [Como: Atribuir ou remover licenças de Diretório Ativo Azure](license-users-groups.md).

## <a name="which-features-work-in-azure-ad"></a>Que recursos funcionam no Azure AD?

Depois de escolher a sua licença Azure AD, terá acesso a algumas ou todas as seguintes funcionalidades para a sua organização:

|Categoria|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerencie as suas aplicações de nuvem e no local utilizando aplicações Proxy de aplicação, um único sinal, o portal My Apps (também conhecido como painel de Acesso) e o Software como um Serviço (SaaS). Para obter mais informações, consulte [Como fornecer acesso remoto seguro a aplicações no local](../manage-apps/application-proxy.md) e documentação de Gestão de [Aplicações.](../manage-apps/index.yml)|
|Autenticação|Gerir o reposição da palavra-passe de autosserviço do Azure Ative Directory, autenticação multi-factor, lista de palavras-passe proibidas personalizadas e bloqueio inteligente. Para mais informações, consulte [a documentação de autenticação AD AD Azure](../authentication/index.yml).|
|Azure Active Directory para programadores|Construa aplicativos que assinem todas as identidades da Microsoft, obtenha fichas para ligar para o Microsoft Graph, outras APIs da Microsoft ou APIs personalizados. Para obter mais informações, consulte [a plataforma de identidade da Microsoft (Azure Ative Directory para programadores)](../develop/index.yml).|
|Empresa-Empresa (B2B)|Gerencie os seus utilizadores convidados e parceiros externos, mantendo o controlo sobre os seus próprios dados corporativos. Para mais informações, consulte [a documentação do Azure Ative Directory B2B](../external-identities/index.yml).|
|Empresa-Consumidor (B2C)|Personalize e controle como os utilizadores se inscrevem, se inscrevem e gerem os seus perfis ao utilizarem as suas apps. Para mais informações, consulte [a documentação do Azure Ative Directory B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerencie o acesso às suas aplicações na nuvem. Para mais informações, consulte [a documentação de Acesso Condicionado AD Azure.](../conditional-access/index.yml)|
|Gestão de Dispositivos|Faça a gestão da forma como os seus dispositivos na cloud ou no local acedem aos seus dados empresariais. Para mais informações, consulte [a documentação de Gestão de Dispositivos AD Azure.](../devices/index.yml)|
|Serviços de domínio|Junte as máquinas virtuais Azure a um domínio sem utilizar controladores de domínio. Para mais informações, consulte [a documentação dos Serviços de Domínio AZure AD](../../active-directory-domain-services/index.yml).|
|Utilizadores empresariais|Gerir a atribuição de licenças, aceder a apps e criar delegados usando papéis de grupos e administradores. Para obter mais informações, consulte [a documentação de gestão de utilizadores do Azure Ative Directory](../enterprise-users/index.yml).|
|Identidade híbrida|Utilize o Azure Ative Directory Connect and Connect Health para fornecer uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização (nuvem ou no local). Para mais informações, consulte [a documentação de identidade híbrida.](../hybrid/index.yml)|
|Governação de identidade|Gerencie a identidade da sua organização através de controlos de acesso a colaboradores, parceiros de negócios, fornecedores, serviços e acesso a aplicações. Também pode efetuar comentários de acesso. Para obter mais informações, consulte a [documentação de governação de identidade da Azure AD](../governance/identity-governance-overview.md) e [as avaliações de acesso a Ad da Azure.](../governance/access-reviews-overview.md)|
|Proteção de identidade|Detetar potenciais vulnerabilidades que afetam as identidades da sua organização, configurar políticas para responder a ações suspeitas e, em seguida, tomar as medidas adequadas para resolvê-las. Para mais informações, consulte [a Azure AD Identity Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece aos seus serviços Azure uma identidade gerida automaticamente em Azure AD que pode autenticar qualquer serviço de autenticação suportado pela Azure, incluindo o Key Vault. Para mais informações, veja [o que são identidades geridas para os recursos da Azure?](../managed-identities-azure-resources/overview.md)|
|Privileged Identity Management (PIM)|Gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos em AD e Azure, e outros Serviços Microsoft Online, como o Microsoft 365 ou o Intune. Para mais informações, consulte [Azure AD Gestão de Identidade Privilegiada.](../privileged-identity-management/index.yml)|
|Relatórios e monitorização|Obtenha informações sobre os padrões de segurança e utilização no seu ambiente. Para obter mais informações, consulte [os relatórios e monitorização do Azure Ative Directory](../reports-monitoring/index.yml).|

## <a name="terminology"></a>Terminologia

Para melhor entender a Azure AD e a sua documentação, recomendamos a revisão dos seguintes termos.

|Termo ou conceito|Descrição|
|---------------|-----------|
|Identidade| Uma coisa que pode ser autenticada. Uma identidade pode ser um utilizador com um nome de utilizador e senha. As identidades também incluem aplicações ou outros servidores que podem exigir autenticação através de chaves ou certificados secretos.|
|Conta| Uma identidade que tem dados associados a ela. Não se pode ter uma conta sem identidade.|
|Conta do Azure AD| Uma identidade criada através do Azure AD ou de outro serviço de cloud da Microsoft, como o Microsoft 365. As identidades são armazenadas em Azure AD e acessíveis às subscrições de serviços em nuvem da sua organização. Esta conta também é por vezes chamada de conta de trabalho ou escola.|
|Administrador de Conta|Esta função clássica de administrador de subscrição é conceptualmente a proprietária de uma subscrição. Esta função tem acesso ao [Centro de Conta Azure](https://account.azure.com/Subscriptions) e permite-lhe gerir todas as subscrições numa conta. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador de Serviços|Esta função clássica de administrador de subscrição permite-lhe gerir todos os recursos da Azure, incluindo o acesso. Esta função tem o acesso equivalente a um utilizador a quem é atribuída a função De proprietário no âmbito de subscrição. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Proprietário|Este papel ajuda-o a gerir todos os recursos da Azure, incluindo o acesso. Esta função baseia-se num novo sistema de autorização chamado Azure role-base access control (Azure RBAC) que fornece uma gestão de acesso de grãos finos aos recursos Azure. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Administrador global do Azure AD|Esta função de administrador é automaticamente atribuída a quem criou o inquilino Azure AD. Os administradores globais podem fazer todas as funções administrativas do Azure AD e quaisquer serviços que federam para a Azure AD, tais como Exchange Online, SharePoint Online e Skype for Business Online. Pode ter vários administradores globais, mas apenas os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos utilizadores. Note que esta função de administrador é chamada de administrador global no portal Azure, mas é chamado **de administrador** da Empresa na Microsoft Graph API e Azure AD PowerShell. Para obter mais informações sobre as várias funções de administrador, consulte [permissões de função de administrador no Diretório Ativo Azure](../roles/permissions-reference.md).|
|Subscrição do Azure| Costumava pagar pelos serviços de nuvem Azure. Pode ter muitas subscrições e estão ligadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e fidedigna do Azure AD que é automaticamente criada quando a sua organização se inscreve para uma subscrição de serviço na nuvem da Microsoft, como Microsoft Azure, Microsoft Intune ou Microsoft 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Os inquilinos da Azure que acedem a outros serviços num ambiente dedicado são considerados inquilinos únicos.|
|Multi-inquilino| Os inquilinos do Azure que acedem a outros serviços num ambiente partilhado, através de várias organizações, são considerados multi-inquilinos.|
|Diretório do Azure AD|Cada inquilino da Azure tem um diretório dedicado e confiável da Azure AD. O diretório AZURE AD inclui os utilizadores, grupos e aplicações do arrendatário e é utilizado para executar funções de gestão de identidade e acesso para recursos de inquilinos.|
|Domínio personalizado|Cada novo diretório AD Azure vem com um nome de domínio inicial, domainname.onmicrosoft.com. Além desse nome inicial, também pode adicionar os nomes de domínio da sua organização, que incluem os nomes que usa para fazer negócios e os seus utilizadores usam para aceder aos recursos da sua organização, à lista. Adicionar nomes de domínio personalizados ajuda-o a criar nomes de utilizadores que sejam familiares aos seus utilizadores, tais como alain@contoso.com .|
|Conta Microsoft (também chamada, MSA)|Contas pessoais que fornecem acesso aos seus produtos e serviços na nuvem orientados para o consumidor, tais como Outlook, OneDrive, Xbox LIVE ou Microsoft 365. A sua conta Microsoft é criada e armazenada no sistema de contas de identidade de consumo da Microsoft que é gerido pela Microsoft.|

## <a name="next-steps"></a>Passos seguintes

- [Inscrever-se no Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associe uma assinatura Azure ao seu Diretório Ativo Azure](active-directory-how-subscriptions-associated-directory.md)

- [Lista de verificação de implementação de funcionalidades Azure Ative Directory Premium P2](active-directory-deployment-checklist-p2.md)
