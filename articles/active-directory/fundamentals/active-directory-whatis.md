---
title: O que é o Azure Active Directory? -O azure Active Directory | Documentos da Microsoft
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
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368029"
---
# <a name="what-is-azure-active-directory"></a>O que é o Azure Active Directory?

O Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, que ajuda os seus colaboradores a inscreverem-se e a acederem a recursos em:

- Recursos externos, como o Microsoft Office 365, o portal do Azure e milhares de outras aplicações SaaS.

- Recursos internos, como as aplicações na sua rede empresarial e a intranet, juntamente com quaisquer aplicações na cloud desenvolvida pela sua organização.

Você pode usar os vários cartazes da [Microsoft Cloud para Enterprise Architects Series](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) para entender melhor os principais serviços de identidade em Azure, Azure AD e Office 365.

## <a name="who-uses-azure-ad"></a>Que utiliza o Azure AD?

Destina-se do Azure AD:

- **Administradores de TI.** Administrador de TI, pode utilizar o Azure AD para controlar o acesso para as suas aplicações e recursos da sua aplicação, com base nos seus requisitos empresariais. Por exemplo, pode utilizar o Azure AD para exigir autenticação multifator ao aceder a recursos organizacionais importantes. Além disso, pode utilizar o Azure AD para automatizar o aprovisionamento de utilizadores entre o AD do Windows Server existentes e as suas aplicações na cloud, incluindo o Office 365. Por fim, o Azure AD fornece ferramentas poderosas para automaticamente ajudar a proteger as identidades de utilizador e as credenciais e para cumprir os requisitos de governação de acesso. Para começar, inscreva-se para um teste gratuito de 30 dias [azure Ative Directory Premium.](https://azure.microsoft.com/trial/get-started-active-directory/)

- **Desenvolvedores de aplicativos.** Como desenvolvedor de aplicações, pode utilizar o Azure AD como uma abordagem baseada em padrões para adicionar um único sinal (SSO) à sua aplicação, permitindo-lhe trabalhar com as credenciais pré-existentes de um utilizador. A Azure AD também fornece APIs que podem ajudá-lo a construir experiências de aplicações personalizadas usando dados organizacionais existentes. Para começar, inscreva-se para um teste gratuito de 30 dias [azure Ative Directory Premium.](https://azure.microsoft.com/trial/get-started-active-directory/) Para mais informações, pode também ver [o Azure Ative Directory para programadores.](../develop/index.yml)

- **Microsoft 365, Office 365, Azure ou Dynamics CRM Online.** Como um assinante, já estiver a utilizar do Azure AD. Cada inquilino do Microsoft 365, Office 365, Azure e Dynamics CRM Online é automaticamente um inquilino do Azure AD. Pode começar a gerir o acesso às suas aplicações de cloud integrados.

## <a name="what-are-the-azure-ad-licenses"></a>Quais são as licenças do Azure AD?

Serviços de negócios Online da Microsoft, como o Office 365 ou o Microsoft Azure, necessitam do Azure AD para início de sessão e para ajudar na proteção de identidade. Se subscrever qualquer serviço de negócios microsoft Online, obtém automaticamente o Azure AD com acesso a todas as funcionalidades gratuitas.

Para melhorar a sua implementação de Anúncios Azure, também pode adicionar capacidades pagas através da atualização para as licenças Azure Ative Directory Premium P1 ou Premium P2. As licenças pagas da Azure AD são construídas em cima do seu diretório gratuito existente, fornecendo self-service, monitorização reforçada, relatórios de segurança e acesso seguro para os seus utilizadores móveis.

>[!Note]
>Para obter as opções de preços destas licenças, consulte [o Preço do Diretório Ativo azure](https://azure.microsoft.com/pricing/details/active-directory/).
>
>O Azure Ative Directory Premium P1 e o Premium P2 não são atualmente apoiados na China. Para mais informações sobre os preços da AD Azure, contacte o [Azure Ative Directory Forum](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Ative Directory Free.** Fornece gestão de utilizador e grupo, sincronização de directóriono no local, relatórios básicos, mudança de senha de autosserviço para utilizadores de nuvem, e inscrição única em Azure, Office 365, e muitas aplicações populares do SaaS.

- **Azure Active Directory Premium P1.** Além das funcionalidades Gratuitas, o P1 também permite aos seus utilizadores híbridos aceder em recursos tanto no local como na nuvem. Também suporta administração avançadas, tais como grupos dinâmicos, a gestão de grupos self-service, o Microsoft Identity Manager (no local acesso e identidade management suite) e recursos de repetição de escrita de nuvem, que permitem a senhas de auto-atendimento repor para os utilizadores no local.

- **Azure Active Directory Premium P2.** Além das funcionalidades Free e P1, o P2 também oferece proteção de identidade de [diretório ativo Azure](../identity-protection/overview-identity-protection.md) para ajudar a fornecer acesso condicional baseado em risco às suas apps e dados críticos da empresa e [Gestão de Identidade Privilegiada](../privileged-identity-management/pim-getting-started.md) para ajudar a descobrir, restringir e monitorizar os administradores e o seu acesso aos recursos e fornecer acesso justo no tempo quando necessário.

- **"Pague à medida que for" as licenças de recurso.** Também pode obter licenças de recursos adicionais, como o Azure Active Directory Business-cliente (B2C). B2C pode ajudar a fornecer a identidade e aceder às soluções de gestão para as suas aplicações do lado do cliente. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2C](../../active-directory-b2c/index.yml).

Para obter mais informações sobre a associação de uma subscrição Azure ao Azure AD, consulte [Como: Associar ou adicionar uma subscrição Azure ao Azure Ative Directory](active-directory-how-subscriptions-associated-directory.md) e para mais informações sobre a atribuição de licenças aos seus utilizadores, consulte [Como: Atribuir ou remover licenças de Diretório Ativo Azure.](license-users-groups.md)

## <a name="terminology"></a>Terminologia

Para melhor compreender a Azure AD e a sua documentação, recomendamos rever os seguintes termos.

|Termo ou conceito|Descrição|
|---------------|-----------|
|Identidade| Uma coisa que pode ser autenticada. Uma identidade pode ser um utilizador com um nome de utilizador e senha. As identidades também incluem aplicações ou outros servidores que possam exigir autenticação através de chaves ou certificados secretos.|
|Conta| Uma identidade que tem dados associados. Não pode ter uma conta sem identidade.|
|Conta do Azure AD| Uma identidade criada através do Azure AD ou outro serviço cloud da Microsoft, como o Office 365. As identidades são armazenados no Azure AD e acessível para subscrições de serviços cloud da sua organização. Esta conta também, por vezes, é chamada um trabalho conta escolar ou profissional.|
|Subscrição do Azure| Utilizado para pagar pelos serviços cloud do Azure. Pode ter o número de subscrições e estão ligadas a um cartão de crédito.|
|Inquilino do Azure| Uma instância dedicada e fidedigna do Azure AD que é criado automaticamente quando a sua organização se inscreve numa subscrição de serviço na nuvem do Microsoft, como o Microsoft Azure, Microsoft Intune ou do Office 365. Um inquilino do Azure representa uma única organização.|
|Inquilino único| Inquilinos do Azure que aceder a outros serviços num ambiente dedicado são considerados único inquilino.|
|Multi-inquilino| Inquilinos do Azure que aceder a outros serviços num ambiente compartilhado, entre várias organizações, são considerados multi-inquilinos.|
|Diretório do Azure AD|Cada inquilino do Azure tem um Azure dedicado e fidedigno diretório AD. Diretório do Azure AD inclui os utilizadores, grupos e aplicações do inquilino e é utilizado para efetuar a identidade e acesso a funções de gerenciamento para os recursos de inquilino.|
|Domínio personalizado|Cada novo Azure diretório AD vem com um nome de domínio inicial, domainname.onmicrosoft.com. Além disso ou nome inicial, também pode adicionar nomes, que incluem os nomes de que usar para fazer negócios e os utilizadores utilizam para aceder aos recursos da sua organização, para a lista de domínio de sua organização. Adicionar nomes de domínio personalizados ajuda-o a criar nomes de utilizadores que sejam familiares aos seus utilizadores, como alain@contoso.com.|
|Administrador de Conta|Esta função de administrador de subscrição clássica é conceitualmente o proprietário de faturação de uma subscrição. Esta função tem acesso ao Centro de [Contas Azure](https://account.azure.com/Subscriptions) e permite-lhe gerir todas as subscrições numa conta. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções de controlo de acesso baseado em Papel Azure (RBAC) e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da Azure AD .|
|Administrador de Serviços|Esta função de administrador de subscrição clássica permite-lhe gerir todos os recursos do Azure, incluindo o acesso. Esta função tem o acesso equivalente de um utilizador que é atribuído a função de proprietário no âmbito da subscrição. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.|
|Proprietário|Esta função de ajuda-o a gerir todos os recursos do Azure, incluindo o acesso. Esta função baseia-se um sistema de autorização mais recente, chamado de controlo de acesso de base de função (RBAC) que fornece gestão de acessos detalhada para recursos do Azure. Para mais informações, consulte as funções de administrador de [subscrição Classic, funções Azure RBAC e funções](../../role-based-access-control/rbac-and-directory-admin-roles.md)de administrador da AD Azure.|
|Administrador global do Azure AD|Esta função de administrador é automaticamente atribuído a quem criou o inquilino do Azure AD. Os administradores globais podem fazer todas as funções administrativas para a Azure AD e quaisquer serviços que federam para a Azure AD, tais como Exchange Online, SharePoint Online e Skype para Business Online. Pode ter vários administradores globais, mas apenas os administradores globais podem atribuir funções de administrador (incluindo a atribuição de outros administradores globais) aos utilizadores.<br><br>**Nota**<br>Esta função de administrador chama-se Administrador Global no portal Azure, mas chama-se administrador da **Empresa** na Microsoft Graph API e Azure AD PowerShell.<br><br>Para obter mais informações sobre as várias funções de administrador, consulte [permissões de funções de administrador no Diretório Ativo do Azure](../users-groups-roles/directory-assign-admin-roles.md).|
|Conta Microsoft (também denominada, MSA)|Contas pessoais que fornecem acesso a seus produtos da Microsoft orientados ao consumidor e serviços em nuvem, como o Outlook, OneDrive, Xbox LIVE ou do Office 365. Sua conta Microsoft é criada e armazenada no sistema de contas de identidades de consumidor de Microsoft que é executado pela Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Que apresenta trabalho em Azure AD?

Depois de escolher a sua licença Azure AD, terá acesso a algumas ou todas as seguintes funcionalidades para a sua organização:

|Categoria|Descrição|
|-------|-----------|
|Gestão de aplicações|Gerir as suas aplicações na cloud e no local com o Proxy de aplicações, único início de sessão, o portal das minhas aplicações (também conhecida como painel de acesso) e Software como aplicações de serviço (SaaS). Para mais informações, consulte [Como fornecer acesso remoto seguro a aplicações no local](../manage-apps/application-proxy.md) e documentação de Gestão de [Aplicações.](../manage-apps/index.yml)|
|Autenticação|Gerir o Azure Active Directory de reposição de palavra-passe self-service, multi-factor Authentication, a lista de palavras-passe banidas personalizado e bloqueio inteligente. Para mais informações, consulte a documentação de autenticação da [AD Azure.](../authentication/index.yml)|
|Empresa-Empresa (B2B)|Gerir os seus utilizadores convidados e parceiros externos, e manter o controlo sobre os seus próprios dados empresariais. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2B](../b2b/index.yml).|
|Empresa-Consumidor (B2C)|Personalizar e controlar como os utilizadores iniciam sessão, inicie sessão e gerem os respetivos perfis quando utilizar as suas aplicações. Para mais informações, consulte a [documentação Do Diretório Ativo Azure B2C](../../active-directory-b2c/index.yml).|
|Acesso Condicional|Gerir o acesso às suas aplicações na cloud. Para mais informações, consulte [a documentação de Acesso Condicional azure AD.](../conditional-access/index.yml)|
|Azure Active Directory para programadores|Crie aplicações que inicie sessão em todas as identidades da Microsoft, obtenha tokens para chamar o Microsoft Graph, outras APIs da Microsoft ou APIs personalizadas. Para mais informações, consulte a [plataforma de identidade da Microsoft (Diretório Ativo Azure para desenvolvedores)](../develop/index.yml).|
|Gestão de Dispositivos|Gerir como os dispositivos na cloud ou no local aceder aos dados da sua empresa. Para mais informações, consulte a documentação de [Gestão de Dispositivos Da Azure AD.](../devices/index.yml)|
|Serviços de domínio|Associe máquinas virtuais do Azure a um domínio sem utilizar controladores de domínio. Para mais informações, consulte a documentação dos Serviços de [Domínio da AD Azure.](../../active-directory-domain-services/index.yml)|
|Utilizadores empresariais|Gerir a atribuição de licença, acesso a aplicações e configure a delegados através de grupos e funções de administrador. Para mais informações, consulte a documentação de [gestão de utilizadores do Azure Ative Directory.](../users-groups-roles/index.yml)|
|Identidade híbrida|Utilize o Azure Active Directory Connect e Connect Health para proporcionar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização (na cloud ou no local). Para mais informações, consulte [documentação de identidade híbrida.](../hybrid/index.yml)|
|Governação de identidade|Gerir a identidade da sua organização por meio dos funcionários, parceiros de negócios, fornecedor, serviço e controlos de acesso da aplicação. Também pode executar as revisões de acesso. Para mais informações, consulte a documentação de [governação da identidade da Azure AD](../governance/identity-governance-overview.md) e as avaliações de acesso à [AD Azure.](../governance/access-reviews-overview.md)|
|Proteção de identidade|Detete potenciais vulnerabilidades que afetam as identidades da sua organização, configurar políticas para responder a ações suspeitas e, em seguida, execute as ações apropriadas para resolvê-los. Para mais informações, consulte [AD Protection](../identity-protection/index.yml).|
|Identidades geridas para os recursos do Azure|Fornece os serviços do Azure com uma identidade gerida automaticamente no Azure AD que pode autenticar qualquer serviço de autenticação do Azure AD com suporte, incluindo o Key Vault. Para mais informações, veja [o que é gerida identidades para os recursos do Azure?](../managed-identities-azure-resources/overview.md)|
|Privileged Identity Management (PIM)|Gerir, controlar e monitorizar o acesso dentro da sua organização. Esta funcionalidade inclui acesso a recursos em Azure AD e Azure, e outros Serviços Online da Microsoft, como o Office 365 ou O Intune. Para mais informações, consulte [AD AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Relatórios e monitorização|Obter informações sobre os padrões de segurança e utilização no seu ambiente. Para mais informações, consulte os [relatórios e monitorização do Diretório Ativo do Azure.](../reports-monitoring/index.yml)|

## <a name="next-steps"></a>Passos seguintes

- [Inscreva-se no Azure Ative Directory Premium](active-directory-get-started-premium.md)

- [Associe uma subscrição Azure ao seu Diretório Ativo Azure](active-directory-how-subscriptions-associated-directory.md)

- [Access Azure Ative Diretório e criar um novo inquilino](active-directory-access-create-new-tenant.md)

- [Lista de verificação de funcionalidades azure Ative Directory Premium P2](active-directory-deployment-checklist-p2.md)
