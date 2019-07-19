---
title: Compartilhando contas e credenciais-Azure Active Directory | Microsoft Docs
description: Descreve como Azure Active Directory permite que as organizações compartilhem com segurança contas para aplicativos locais e serviços de nuvem do consumidor.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5638710c4c01d361d108cfef69869fad1a7dad1a
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304379"
---
# <a name="sharing-accounts-with-azure-ad"></a>Compartilhando contas com o Azure AD
## <a name="overview"></a>Descrição geral
Às vezes, as organizações precisam usar um único nome de usuário e senha para várias pessoas, o que geralmente acontece em dois casos:

* Ao acessar aplicativos que exigem uma entrada e senha exclusivas para cada usuário, seja aplicativos locais ou serviços de nuvem do consumidor (por exemplo, contas de mídia social corporativa).
* Ao criar ambientes de vários usuários. Você pode ter uma única conta local que tem privilégios elevados e é usada para fazer a configuração principal, a administração e as atividades de recuperação. Por exemplo, a conta "administrador global" local para o Office 365 ou a conta raiz no Salesforce.

Tradicionalmente, essas contas são compartilhadas distribuindo as credenciais (nome de usuário e senha) para os indivíduos certos ou armazenando-as em um local compartilhado onde vários agentes confiáveis podem acessá-las.

O modelo de compartilhamento tradicional tem várias desvantagens:

* A habilitação do acesso a novos aplicativos exige que você distribua credenciais para todos que precisam de acesso.
* Cada aplicativo compartilhado pode exigir seu próprio conjunto exclusivo de credenciais compartilhadas, exigindo que os usuários se comentem com vários conjuntos de credenciais. Quando os usuários precisam se lembrar de muitas credenciais, o risco aumenta a recorrer a práticas arriscadas. (por exemplo, gravando senhas).
* Você não pode dizer quem tem acesso a um aplicativo.
* Você não pode dizer quem *acessou* um aplicativo.
* Quando desejar remover o acesso a um aplicativo, você precisará atualizar as credenciais e redistribuí-las a todos que precisem de acesso a esse aplicativo.

## <a name="azure-active-directory-account-sharing"></a>Compartilhamento de conta Azure Active Directory
O Azure AD fornece uma nova abordagem para o uso de contas compartilhadas que eliminam essas desvantagens.

O administrador do Azure AD Configura quais aplicativos um usuário pode acessar usando o painel de acesso e escolhendo o tipo de logon único mais adequado para esse aplicativo. Um desses tipos, *logon único baseado em senha*, permite que o Azure ad atue como um tipo de "agente" durante o processo de logon para esse aplicativo.

Os usuários fazem logon uma vez com sua conta institucional. Essa conta é a mesma usada regularmente para acessar seus desktops ou emails. Eles podem descobrir e acessar somente os aplicativos aos quais eles estão atribuídos. Com contas compartilhadas, essa lista de aplicativos pode incluir qualquer número de credenciais compartilhadas. O usuário final não precisa se lembrar ou anotar as várias contas que podem estar usando.

As contas compartilhadas não só aumentam a supervisão e melhoram a usabilidade, elas também aprimoram sua segurança. Os usuários com permissões para usar as credenciais não veem a senha compartilhada, mas, em vez disso, obtêm permissões para usar a senha como parte de um fluxo de autenticação orquestrada. Além disso, alguns aplicativos de SSO de senha oferecem a opção de usar o Azure AD para substituir periodicamente (atualizar) as senhas. O sistema usa senhas grandes e complexas, o que aumenta a segurança da conta. O administrador pode conceder ou revogar facilmente o acesso a um aplicativo, sabe quem tem acesso à conta e quem a acessou no passado.

O Azure AD dá suporte a contas compartilhadas para qualquer usuário licenciado do Enterprise Mobility Suite (EMS), Premium ou Basic, em todos os tipos de aplicativos de logon único com senha. Você pode compartilhar contas para qualquer um dos milhares de aplicativos previamente integrados na Galeria de aplicativos e pode adicionar seu próprio aplicativo de autenticação de senha com [aplicativos de SSO personalizados](../manage-apps/configure-single-sign-on-portal.md).

Os recursos do Azure AD que habilitam o compartilhamento de conta incluem:

* [Logon único com senha](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agente de logon único com senha
* [Atribuição de grupo](groups-self-service-management.md)
* Aplicativos de senha personalizada
* [Painel/relatórios de uso do aplicativo](../active-directory-passwords-get-insights.md)
* Portais de acesso do usuário final
* [Proxy de aplicativo](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Compartilhando uma conta
Para usar o Azure AD para compartilhar uma conta, você precisa:

* Adicionar uma [Galeria](https://azure.microsoft.com/marketplace/active-directory/) de aplicativos de aplicativo ou um [aplicativo personalizado](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configurar o aplicativo para logon único com senha (SSO)
* Use a [atribuição baseada em grupo](groups-saasapps.md) e selecione a opção para inserir uma credencial compartilhada

Você também pode tornar sua conta compartilhada mais segura com a MFA (autenticação multifator) (Saiba mais sobre como [proteger aplicativos com o Azure ad](../authentication/concept-mfa-whichversion.md)) e pode delegar a capacidade de gerenciar quem tem acesso ao aplicativo usando o autoatendimento [do Azure AD ](groups-self-service-management.md)gerenciamento de grupo.

## <a name="related-articles"></a>Artigos relacionados
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Protegendo aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gerenciamento de grupo de autoatendimento/SSAA](groups-self-service-management.md)
