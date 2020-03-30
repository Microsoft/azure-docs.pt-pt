---
title: Partilhar contas e credenciais - Diretório Ativo Azure / Microsoft Docs
description: Descreve como o Azure Ative Directory permite que as organizações partilhem de forma segura as contas de aplicações no local e serviços de nuvem de consumo.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 987c3ee7c90eb0bb793b96eb2771efbb258f16a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565507"
---
# <a name="sharing-accounts-with-azure-ad"></a>Partilhar contas com a Azure AD

## <a name="overview"></a>Descrição geral

Por vezes, as organizações precisam de usar um único nome de utilizador e senha para várias pessoas, o que normalmente acontece em dois casos:

* Ao aceder a aplicações que exijam um registo único e senha para cada utilizador, seja aplicações no local ou serviços de nuvem de consumo (por exemplo, contas de redes sociais corporativas).
* Ao criar ambientes multiutilizadores. Você pode ter uma única conta local que tem privilégios elevados e é usado para fazer atividades de configuração, administração e recuperação. Por exemplo, o "administrador global" local conta para o Office 365 ou a conta raiz no Salesforce.

Tradicionalmente, estas contas são partilhadas distribuindo as credenciais (nome de utilizador e palavra-passe) aos indivíduos certos ou armazenando-as num local partilhado onde vários agentes de confiança podem acessá-las.

O modelo de partilha tradicional tem vários inconvenientes:

* Permitir o acesso a novas aplicações requer que distribua credenciais a todos os que precisam de acesso.
* Cada aplicação partilhada pode exigir o seu próprio conjunto único de credenciais partilhadas, exigindo que os utilizadores se lembrem de vários conjuntos de credenciais. Quando os utilizadores têm de se lembrar de muitas credenciais, o risco aumenta que recorrem a práticas de risco. (por exemplo, anotar senhas).
* Não se pode dizer quem tem acesso a uma aplicação.
* Não se pode dizer quem *acedeu a* uma candidatura.
* Quando pretende remover o acesso a uma aplicação, tem de atualizar as credenciais e redistribuí-las a todos os que precisam de acesso a essa aplicação.

## <a name="azure-active-directory-account-sharing"></a>Partilha de conta de Diretório Ativo Azure

A Azure AD fornece uma nova abordagem para usar contas partilhadas que elimina estes inconvenientes.

O administrador da AD Azure configura quais as aplicações a que um utilizador pode aceder através do Painel de Acesso e escolhendo o tipo de inscrição única mais adequada para essa aplicação. Um desses tipos, *com um único sinal baseado em palavra-passe,* permite que o Azure AD atue como uma espécie de "corretor" durante o processo de início de sessão dessa aplicação.

Os utilizadores assinam uma vez com a sua conta organizacional. Esta conta é a mesma que usam regularmente para aceder ao seu ambiente de trabalho ou e-mail. Podem descobrir e aceder apenas às aplicações a que estão atribuídas. Com contas partilhadas, esta lista de aplicações pode incluir qualquer número de credenciais partilhadas. O utilizador final não precisa de se lembrar ou escrever as várias contas que podem estar a usar.

As contas partilhadas não só aumentam a supervisão e melhoram a usabilidade, como também aumentam a sua segurança. Os utilizadores com permissões para usar as credenciais não vêem a palavra-passe partilhada, mas obtêm permissões para usar a palavra-passe como parte de um fluxo de autenticação orquestrado. Além disso, algumas aplicações sSO de senha dão-lhe a opção de usar o Azure AD para reverter periodicamente (atualizar) palavras-passe. O sistema utiliza senhas grandes e complexas, o que aumenta a segurança da conta. O administrador pode facilmente conceder ou revogar o acesso a uma aplicação, sabe quem tem acesso à conta e quem acedeu no passado.

A Azure AD suporta contas partilhadas de qualquer Suite de Mobilidade Empresarial (EMS) ou plano de licença Azure AD Premium, em todos os tipos de aplicações de assinatura única de senha. Pode partilhar contas de qualquer uma das milhares de aplicações pré-integradas na galeria de aplicações e pode adicionar a sua própria aplicação de autenticação de palavra-passe com [aplicações SSO personalizadas](../manage-apps/configure-single-sign-on-non-gallery-applications.md).

As funcionalidades da Azure AD que permitem a partilha de conta incluem:

* [Inscrição única de senha](../manage-apps/what-is-single-sign-on.md#password-based-sso)
* Agente de inscrição único de senha
* [Atribuição de grupo](groups-self-service-management.md)
* Aplicativos de senha personalizados
* [Painel de instrumentos/relatórios de utilização de aplicativos](../active-directory-passwords-get-insights.md)
* Portais de acesso ao utilizador final
* [Procuração de aplicativos](../manage-apps/application-proxy.md)
* [Mercado de Diretório ativo](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Partilhar uma conta

Para utilizar o Azure AD para partilhar uma conta, é necessário:

* Adicione uma galeria de [aplicações](https://azure.microsoft.com/marketplace/active-directory/) de aplicações ou [aplicação personalizada](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configure a aplicação para senha Single Sign-On (SSO)
* Utilize [a atribuição baseada em grupo](groups-saasapps.md) e selecione a opção de introduzir uma credencial partilhada

Também pode tornar a sua conta partilhada mais segura com a Autenticação Multi-Factor (MFA) (saiba mais sobre a obtenção de aplicações com a [AD Azure)](../authentication/concept-mfa-whichversion.md)e pode delegar a capacidade de gerir quem tem acesso à aplicação utilizando a gestão do grupo [de self-service Azure AD.](groups-self-service-management.md)

## <a name="next-steps"></a>Passos seguintes

* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Proteger aplicativos com acesso condicional](../active-directory-conditional-access-azure-portal.md)
* [Gestão de grupos de self-service/SSAA](groups-self-service-management.md)
