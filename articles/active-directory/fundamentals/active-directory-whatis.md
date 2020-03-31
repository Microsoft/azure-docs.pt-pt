---
title: O que é o Azure Active Directory? - Diretório Ativo Azure / Microsoft Docs
description: Visão geral e informação conceptual sobre o Diretório Ativo azure, incluindo terminologia, que licenças estão disponíveis, e uma lista de funcionalidades associadas com links para mais informações.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd7b412e99526935738c2494d31a16fded7101a6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240787"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?

O Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a inscreverem-se e a acederem a recursos em:

- Recursos externos, como o Microsoft Office 365, o portal Azure e milhares de outras aplicações SaaS.

- Recursos internos, como aplicações na sua rede corporativa e intranet, juntamente com quaisquer aplicações em nuvem desenvolvidas pela sua própria organização.

Você pode usar os vários cartazes da [Microsoft Cloud para Enterprise Architects Series](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) para entender melhor os principais serviços de identidade em Azure, Azure AD e Office 365.

## <a name="who-uses-azure-ad"></a>Quem usa o Anúncio Azure?

A Azure AD destina-se a:

- **Administradores de TI.** Como administrador de TI, pode utilizar o Azure AD para controlar o acesso às suas apps e recursos da sua aplicação, com base nos seus requisitos de negócio. Por exemplo, pode utilizar o Azure AD para exigir a autenticação de vários fatores ao aceder a importantes recursos organizacionais. Além disso, pode utilizar o Azure AD para automatizar o fornecimento de utilizadores entre o seu AD Windows Server existente e as suas aplicações na nuvem, incluindo o Office 365. Finalmente, a Azure AD dá-lhe ferramentas poderosas para ajudar automaticamente a proteger as identidades e credenciais dos utilizadores e a satisfazer os seus requisitos de governação de acesso. Para começar, inscreva-se para um teste gratuito de 30 dias [azure Ative Directory Premium.](https://azure.microsoft.com/trial/get-started-active-directory/)

- **Desenvolvedores de aplicativos.** Como desenvolvedor de aplicações, pode utilizar o Azure AD como uma abordagem baseada em padrões para adicionar um único sinal (SSO) à sua aplicação, permitindo-lhe trabalhar com as credenciais pré-existentes de um utilizador. A Azure AD também fornece APIs que podem ajudá-lo a construir experiências de aplicações personalizadas usando dados organizacionais existentes. Para começar, inscreva-se para um teste gratuito de 30 dias [azure Ative Directory Premium.](https://azure.microsoft.com/trial/get-started-active-directory/) Para mais informações, pode também ver [o Azure Ative Directory para programadores.](../develop/index.yml)

- **Microsoft 365, Office 365, Azure ou Dynamics CRM Online.** Como assinante, já está a usar o Azure AD. Cada Microsoft 365, Office 365, Azure e Dynamics CRM Inquilino online é automaticamente um inquilino Azure AD. Pode começar imediatamente a gerir o acesso às suas aplicações integradas na nuvem.

## <a name="what-are-the-azure-ad-licenses"></a>O que são as licenças da AD Azure?

Os serviços empresariais microsoft Online, como o Office 365 ou o Microsoft Azure, exigem a Azure AD para iniciar sessão e ajudar na proteção de identidade. Se subscrever qualquer serviço de negócios microsoft Online, obtém automaticamente o Azure AD com acesso a todas as funcionalidades gratuitas.

Para melhorar a sua implementação de Anúncios Azure, também pode adicionar capacidades pagas através da atualização para as licenças Azure Ative Directory Premium P1 ou Premium P2. As licenças pagas da Azure AD são construídas em cima do seu diretório gratuito existente, fornecendo self-service, monitorização reforçada, relatórios de segurança e acesso seguro para os seus utilizadores móveis.

>[!Note]
>Para obter as opções de preços destas licenças, consulte [o Preço do Diretório Ativo azure](https://azure.microsoft.com/pricing/details/active-directory/).
>
>O Azure Ative Directory Premium P1 e o Premium P2 não são atualmente apoiados na China. Para mais informações sobre os preços da AD Azure, contacte o [Azure Ative Directory Forum](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Ative Directory Free.** Fornece gestão de utilizador e grupo, sincronização de directóriono no local, relatórios básicos, mudança de senha de autosserviço para utilizadores de nuvem, e inscrição única em Azure, Office 365, e muitas aplicações populares do SaaS.

- **Azure Active Directory Premium P1.** Além das funcionalidades Gratuitas, o P1 também permite aos seus utilizadores híbridos aceder em recursos tanto no local como na nuvem. Também suporta administração avançada, tais como grupos dinâmicos, gestão de grupos de self-service, Microsoft Identity Manager (um suite de gestão de identidade e acesso no local) e capacidades de redisagem em nuvem, que permitem o reset de senha de autosserviço para seus utilizadores no local.

- **Azure Active Directory Premium P2.** Além das funcionalidades Free e P1, o P2 também oferece proteção de identidade de [diretório ativo Azure](../identity-protection/overview-identity-protection.md) para ajudar a fornecer acesso condicional baseado em risco às suas apps e dados críticos da empresa e [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-getting-started.md) para ajudar a descobrir, restringir e monitorizar os administradores e o seu acesso aos recursos e fornecer acesso justo no tempo quando necessário.

- **"Pague à medida que for" as licenças de recurso.** Também pode obter licenças adicionais de funcionalidades, como o Azure Ative Directory Business-to-Customer (B2C). O B2C pode ajudá-lo a fornecer soluções de gestão de identidade e acesso para as suas aplicações voltadas para o cliente. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre a associação de uma subscrição Azure ao Azure AD, consulte [Como: Associar ou adicionar uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md) e para mais informações sobre a atribuição de licenças aos seus utilizadores, consulte [Como: Atribuir ou remover licenças de Diretório Ativo Azure.](license-users-groups.md)

## <a name="terminology"></a>Terminologia

Para melhor compreender a Azure AD e a sua documentação, recomendamos rever os seguintes termos.

|Termo ou conceito|Descrição|
|---------------|-----------|
|Identidade| Uma coisa que pode ser autenticada. Uma identidade pode ser um utilizador com um nome de utilizador e senha. As identidades também incluem aplicações ou outros servidores que possam exigir autenticação através de chaves ou certificados secretos.|
|Conta| Uma identidade que tem dados associados. Não pode ter uma conta sem identidade.|
|Conta do Azure AD| Uma identidade criada através do Azure AD ou de outro serviço de nuvem da Microsoft, como o Office 365. As identidades são armazenadas em Azure AD e acessíveis às subscrições de serviço sinuoso da sua organização. Esta conta também é por vezes chamada de trabalho ou conta escolar.|
|Subscrição do Azure| Costumava pagar os serviços de nuvem Azure. Pode ter muitas subscrições e estão ligadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e de confiança do Azure AD que é automaticamente criada quando a sua organização se inscreve para uma subscrição de serviço na nuvem da Microsoft, como microsoft Azure, Microsoft Intune ou Office 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Os inquilinos azure que acedem a outros serviços em ambiente dedicado são considerados inquilinos únicos.|
|Multi-inquilino| Os inquilinos azure que acedem a outros serviços em ambiente compartilhado, em várias organizações, são considerados multi-inquilinos.|
|Diretório do Azure AD|Cada inquilino azure tem um diretório azure dedicado e confiável. O diretório Azure AD inclui utilizadores, grupos e aplicações do arrendatário e é utilizado para executar funções de gestão de identidade e acesso para recursos de inquilinos.|
|Domínio personalizado|Cada novo diretório da AD Azure vem com um nome inicial de domínio, domainname.onmicrosoft.com. Além desse nome inicial, também pode adicionar à lista os nomes de domínio da sua organização, que incluem os nomes que utiliza para fazer negócios e os seus utilizadores para aceder aos recursos da sua organização. Adicionar nomes de domínio personalizados ajuda-o a criar alain@contoso.comnomes de utilizadores que sejam familiares aos seus utilizadores, tais como .|
|Administrador de Conta|Este papel clássico de administrador de subscrição é conceptualmente o proprietário de faturação de uma subscrição. Esta função tem acesso ao Centro de [Contas Azure](https://account.azure.com/Subscriptions) e permite-lhe gerir todas as subscrições numa conta. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções de controlo de acesso baseado em Papel Azure (RBAC) e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da Azure AD .|
|Administrador de Serviços|Esta função clássica de administrador de subscrição permite-lhe gerir todos os recursos do Azure, incluindo o acesso. Esta função tem o acesso equivalente de um utilizador a quem é atribuída a função Proprietário no âmbito de subscrição. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.|
|Proprietário|Este papel ajuda-o a gerir todos os recursos do Azure, incluindo o acesso. Esta função baseia-se num novo sistema de autorização chamado controlo de acesso de base de papel (RBAC) que fornece uma gestão de acesso de grãos finos aos recursos do Azure. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.|
|Administrador global do Azure AD|Esta função de administrador é automaticamente atribuída a quem quer que tenha criado o inquilino da AD Azure. Os administradores globais podem fazer todas as funções administrativas para a Azure AD e quaisquer serviços que federam para a Azure AD, tais como Exchange Online, SharePoint Online e Skype para Business Online. Pode ter vários administradores Globais, mas apenas os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos utilizadores.<br><br>**Nota**<br>Esta função de administrador chama-se Administrador Global no portal Azure, mas chama-se administrador da **Empresa** na Microsoft Graph API e Azure AD PowerShell.<br><br>Para obter mais informações sobre as várias funções de administrador, consulte [permissões de funções de administrador no Diretório Ativo do Azure](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta Microsoft (também chamada, MSA)|Contas pessoais que fornecem acesso aos seus produtos microsoft orientados para o consumidor e serviços na nuvem, tais como Outlook, OneDrive, Xbox LIVE ou Office 365. A sua conta Microsoft é criada e armazenada no sistema de conta de identidade de consumo da Microsoft que é gerido pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Que apresenta trabalho em Azure AD?

Depois de escolher a sua licença Azure AD, terá acesso a algumas ou todas as seguintes funcionalidades para a sua organização:

|Categoria|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerencie as suas aplicações de cloud e on-in-local utilizando application Proxy, um único sign-on, o portal My Apps (também conhecido como painel de acesso) e aplicações de Software como Serviço (SaaS). Para mais informações, consulte [Como fornecer acesso remoto seguro a aplicações no local](../manage-apps/application-proxy.md) e documentação de Gestão de [Aplicações.](../manage-apps/index.yml)|
|Autenticação|Gerencie o reset de palavra-passe self-service do Azure Ative Diretório, A autenticação multi-factor, lista de senhas proibida suster personalizada e bloqueio inteligente. Para mais informações, consulte a documentação de autenticação da [AD Azure.](../authentication/index.yml)|
|Empresa-Empresa (B2B)|Gerencie os seus utilizadores convidados e parceiros externos, mantendo ao mesmo tempo o controlo sobre os seus próprios dados corporativos. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2B](../b2b/index.yml).|
|Empresa-Consumidor (B2C)|Personalize e controle a forma como os utilizadores se inscrevem, fazem o check-in e gerem os seus perfis ao utilizarem as suas apps. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerencie o acesso às suas aplicações na nuvem. Para mais informações, consulte [a documentação de Acesso Condicional azure AD.](../conditional-access/index.yml)|
|Azure Active Directory para programadores|Construa aplicações que assinem todas as identidades da Microsoft, ligue para o Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para mais informações, consulte a [plataforma de identidade da Microsoft (Diretório Ativo Azure para desenvolvedores)](../develop/index.yml).|
|Gestão de Dispositivos|Faça a gestão da forma como os seus dispositivos na cloud ou no local acedem aos seus dados empresariais. Para mais informações, consulte a documentação de [Gestão de Dispositivos Da Azure AD.](../devices/index.yml)|
|Serviços de domínio|Junte as máquinas virtuais Azure a um domínio sem utilizar controladores de domínio. Para mais informações, consulte a documentação dos Serviços de [Domínio da AD Azure.](../../active-directory-domain-services/index.yml)|
|Utilizadores empresariais|Gerir a atribuição de licenças, aceder a apps e configurar delegados usando grupos e funções de administrador. Para mais informações, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)|
|Identidade híbrida|Utilize o Azure Ative Directory Connect and Connect Health para fornecer uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização (cloud ou no local). Para mais informações, consulte [documentação de identidade híbrida.](../hybrid/index.yml)|
|Governação de identidade|Gerencie a identidade da sua organização através de controlos de acesso a funcionários, parceiros de negócios, fornecedores, serviços e acesso sinuosos. Também pode realizar avaliações de acesso. Para mais informações, consulte a documentação de [governação da identidade da Azure AD](../governance/identity-governance-overview.md) e as avaliações de acesso à [AD Azure.](../governance/access-reviews-overview.md)|
|Proteção de identidade|Detete potenciais vulnerabilidades que afetem as identidades da sua organização, configure políticas para responder a ações suspeitas e, em seguida, tome as medidas adequadas para resolvê-las. Para mais informações, consulte [AD Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece aos seus serviços Azure uma identidade gerida automaticamente em Azure AD que pode autenticar qualquer serviço de autenticação apoiado por AD Azure, incluindo o Key Vault. Para mais informações, veja [o que é gerida identidades para os recursos do Azure?](../managed-identities-azure-resources/overview.md)|
|Privileged Identity Management (PIM)|Gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos em Azure AD e Azure, e outros Serviços Online da Microsoft, como o Office 365 ou O Intune. Para mais informações, consulte [AD AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitorização|Obtenha informações sobre os padrões de segurança e utilização no seu ambiente. Para mais informações, consulte os [relatórios e monitorização do Diretório Ativo do Azure.](../reports-monitoring/index.yml)|

## <a name="next-steps"></a>Passos seguintes

- [Inscrever-se no Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Associe uma subscrição Azure ao seu Diretório Ativo Azure](active-directory-how-subscriptions-associated-directory.md)

- [Access Azure Ative Diretório e criar um novo inquilino](active-directory-access-create-new-tenant.md)

- [Lista de verificação de funcionalidades azure Ative Directory Premium P2](active-directory-deployment-checklist-p2.md)
