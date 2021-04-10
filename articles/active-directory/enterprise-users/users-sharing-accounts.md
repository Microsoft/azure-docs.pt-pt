---
title: Partilha de contas e credenciais - Azure Ative Directory | Microsoft Docs
description: Descreve como o Azure Ative Directory permite que as organizações partilhem de forma segura contas para aplicações no local e serviços na nuvem de consumidores.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576252"
---
# <a name="sharing-accounts-with-azure-ad"></a>Partilhar contas com a Azure AD

## <a name="overview"></a>Descrição Geral

Por vezes, as organizações precisam de usar um único nome de utilizador e palavra-passe para várias pessoas, o que normalmente acontece em dois casos:

* Ao aceder a aplicações que requerem um sinal único e uma palavra-passe para cada utilizador, seja em aplicações no local ou serviços de nuvem de consumo (por exemplo, contas de redes sociais corporativas).
* Ao criar ambientes multiutilizadores. Você pode ter uma única conta local que tem privilégios elevados e é usado para fazer atividades de configuração, administração e recuperação principais. Por exemplo, a conta local de "administrador global" para a Microsoft 365 ou a conta raiz na Salesforce.

Tradicionalmente, estas contas são partilhadas distribuindo as credenciais (nome de utilizador e palavra-passe) para os indivíduos certos ou armazenando-as num local partilhado onde vários agentes de confiança podem aceder às suas credenciais.

O modelo tradicional de partilha tem vários inconvenientes:

* Permitir o acesso a novas aplicações requer que distribua credenciais a todos os que necessitem de acesso.
* Cada aplicação partilhada pode exigir o seu próprio conjunto único de credenciais partilhadas, exigindo que os utilizadores se lembrem de vários conjuntos de credenciais. Quando os utilizadores têm de se lembrar de muitas credenciais, o risco aumenta e recorre a práticas de risco. (por exemplo, escrevendo palavras-passe).
* Não se sabe quem tem acesso a uma candidatura.
* Não se sabe a quem *acedeu a* uma candidatura.
* Quando pretende remover o acesso a uma aplicação, tem de atualizar as credenciais e redistribuí-las a todos os que necessitem de acesso a essa aplicação.

## <a name="azure-active-directory-account-sharing"></a>Partilha de conta do Azure Ative Directory

A Azure AD fornece uma nova abordagem para usar contas partilhadas que elimina estes inconvenientes.

O administrador AZure AD configura quais as aplicações a que um utilizador pode aceder utilizando o Painel de Acesso e escolhendo o tipo de sinal único mais adequado para essa aplicação. Um desses tipos, *com um único sinal baseado em palavra-passe,* permite que a Azure AD atue como uma espécie de "corretor" durante o processo de inscrição para essa aplicação.

Os utilizadores inscrevem-se uma vez com a sua conta organizacional. Esta conta é a mesma que usam regularmente para aceder ao seu ambiente de trabalho ou e-mail. Podem descobrir e aceder apenas às aplicações a que estão atribuídas. Com contas partilhadas, esta lista de aplicações pode incluir qualquer número de credenciais partilhadas. O utilizador final não precisa de se lembrar ou anotar as várias contas que pode estar a utilizar.

As contas partilhadas não só aumentam a supervisão e melhoram a usabilidade, como também aumentam a sua segurança. Os utilizadores com permissões para usar as credenciais não vêem a palavra-passe partilhada, mas obtêm permissões para usar a palavra-passe como parte de um fluxo de autenticação orquestrado. Além disso, algumas aplicações SSO de palavra-passe dão-lhe a opção de usar Azure AD para reverter periodicamente (atualização) palavras-passe. O sistema utiliza senhas grandes e complexas, o que aumenta a segurança da conta. O administrador pode facilmente conceder ou revogar o acesso a uma aplicação, sabe quem tem acesso à conta e quem a acedeu no passado.

A Azure AD suporta contas partilhadas para qualquer Suíte de Mobilidade Empresarial (EMS) ou plano de licença AD Premium Azure, em todos os tipos de aplicações de assinatura única. Pode partilhar contas de qualquer uma das milhares de aplicações pré-integradas na galeria de aplicações e pode adicionar a sua própria aplicação de autenticação de palavra-passe com [aplicações SSO personalizadas.](../manage-apps/what-is-single-sign-on.md)

As funcionalidades AZURE AD que permitem a partilha de conta incluem:

* [Assinatura única de acesso](../manage-apps/sso-options.md#password-based-sso)
* Agente de inscrição único da palavra-passe
* [Atribuição de grupo](groups-self-service-management.md)
* Aplicativos de senha personalizada
* [Painel/relatórios de utilização de aplicativos](../authentication/howto-sspr-reporting.md)
* Portais de acesso ao utilizador final
* [Procuração de aplicativos](../manage-apps/application-proxy.md)
* [Mercado de Diretório Ativo](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Partilhar uma conta

Para utilizar a Azure AD para partilhar uma conta, tem de:

* Adicione uma [galeria de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) de aplicações ou [aplicação personalizada](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)
* Configure o pedido de palavra-passe Single Sign-On (SSO)
* Use [a atribuição baseada em grupo](groups-saasapps.md) e selecione a opção de introduzir uma credencial partilhada

Também pode tornar a sua conta partilhada mais segura com a Autenticação Multi-Factor (MFA) (saiba mais sobre [a garantia de aplicações com Azure AD)](../authentication/concept-mfa-howitworks.md)e pode delegar a capacidade de gerir quem tem acesso à aplicação utilizando a gestão do grupo [de self-service Azure AD.](groups-self-service-management.md)

## <a name="next-steps"></a>Passos seguintes

* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Proteger aplicativos com acesso condicional](../../active-directory-b2c/overview.md)
* [Gestão de grupos de self-service/SSAA](groups-self-service-management.md)