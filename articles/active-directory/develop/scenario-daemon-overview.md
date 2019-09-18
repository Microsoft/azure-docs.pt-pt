---
title: Aplicativo de daemon chamando APIs Web (visão geral)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae0912203f2427694d2a9b8611966a55e1e6889e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056387"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Cenário: Aplicativo daemon que chama APIs da Web

Saiba tudo o que você precisa para criar um aplicativo daemon que chama APIs da Web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Descrição geral

Seu aplicativo pode adquirir um token para chamar uma API da Web em nome de si mesmo (não em nome de um usuário). Esse cenário é útil para aplicativos de daemon. Ele está usando a concessão de [credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 padrão.

![Aplicações daemon](./media/scenario-daemon-app/daemon-app.svg)

Aqui estão alguns exemplos de casos de uso para aplicativos de daemon:

- Aplicativos Web que são usados para provisionar ou administrar usuários, ou processos em lote em um diretório
- Aplicativos de área de trabalho (como serviços do Windows no Windows ou processos de daemons no Linux) que executam trabalhos em lotes ou um serviço de sistema operacional em execução em segundo plano
- APIs Web que precisam manipular diretórios, não usuários específicos

Há outro caso comum em que aplicativos não daemon usam credenciais de cliente: mesmo quando eles atuam em nome dos usuários, eles precisam acessar uma API da Web ou um recurso com sua identidade por motivos técnicos. Um exemplo é o acesso a segredos no keyvault ou um banco de dados SQL do Azure para um cache.

Aplicativos que adquirem um token para suas próprias identidades:

- São aplicativos cliente confidenciais. Esses aplicativos, Considerando que acessam recursos independentemente de um usuário, precisam provar sua identidade. Eles também são aplicativos confidenciais, que precisam ser aprovados pelos administradores de locatário do Azure Active Directory (AD do Azure).
- Registrou um segredo (senha de aplicativo ou certificado) com o Azure AD. Esse segredo é passado durante a chamada para o Azure AD para obter um token.

## <a name="specifics"></a>Especificações

> [!IMPORTANT]
>
> - A interação do usuário não é possível com um aplicativo daemon. Um aplicativo daemon requer sua própria identidade. Esse tipo de aplicativo solicita um token de acesso usando sua identidade de aplicativo e apresentando sua ID de aplicativo, credencial (senha ou certificado) e URI de ID do aplicativo para o Azure AD. Após a autenticação bem-sucedida, o daemon recebe um token de acesso (e um token de atualização) do ponto de extremidade da plataforma de identidade da Microsoft, que é usado para chamar a API da Web (e é atualizado conforme necessário).
> - Como a interação do usuário não é possível, o consentimento incremental não será possível. Todas as permissões de API necessárias precisam ser configuradas no registro do aplicativo, e o código do aplicativo apenas solicita permissões definidas estaticamente. Isso também significa que os aplicativos daemon não oferecerão suporte a consentimento incremental.

Para os desenvolvedores, a experiência de ponta a ponta para esse cenário tem os seguintes aspectos:

- Os aplicativos daemon só podem funcionar em locatários do Azure AD. Não faz sentido criar um aplicativo daemon que tente manipular contas pessoais da Microsoft. Se você for um desenvolvedor de aplicativos LOB (linha de negócios), criará seu aplicativo daemon em seu locatário. Se você for um ISV, talvez queira criar um aplicativo de daemon multilocatário. Ele precisará ser consentido por cada administrador de locatários.
- Durante o [registro do aplicativo](./scenario-daemon-app-registration.md), o **URI de resposta** não é necessário. Você precisa compartilhar segredos ou certificados ou declarações assinadas com o Azure AD, e você precisa solicitar permissões de aplicativos e conceder consentimento de administrador para usar essas permissões de aplicativo.
- A [configuração do aplicativo](./scenario-daemon-app-configuration.md) precisa fornecer credenciais de cliente como compartilhadas com o Azure ad durante o registro do aplicativo.
- O [escopo](scenario-daemon-acquire-token.md#scopes-to-request) usado para adquirir um token com o fluxo de credenciais do cliente precisa ser um escopo estático.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo daemon-registro de aplicativo](./scenario-daemon-app-registration.md)
