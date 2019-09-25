---
title: Aplicativo de desktop que chama APIs Web (registro de aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de área de trabalho que chama APIs da Web (registro de aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268396"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplicativo de desktop que chama APIs Web-registro de aplicativo

Este artigo contém as especificidades de registro do aplicativo para um aplicativo de área de trabalho.

## <a name="supported-accounts-types"></a>Tipos de contas com suporte

Os tipos de conta com suporte no aplicativo de área de trabalho dependem da experiência que você deseja acender. Devido a essa relação, os tipos de conta com suporte dependem dos fluxos que você deseja usar.

### <a name="audience-for-interactive-token-acquisition"></a>Público-alvo para aquisição de token interativo

Se seu aplicativo de área de trabalho usa a autenticação interativa, você pode conectar usuários de qualquer [tipo de conta](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Público-alvo dos fluxos silenciosos do aplicativo para desktop

- Para usar a autenticação integrada do Windows ou nome de usuário/senha, seu aplicativo precisa conectar usuários em seu próprio locatário (desenvolvedor de LOB) ou em organizações do Azure Active Directory (cenário de ISV). Esses fluxos de autenticação não têm suporte para contas pessoais da Microsoft.
- Se você quiser usar o fluxo de código do dispositivo, não poderá conectar usuários com suas contas pessoais da Microsoft ainda.
- Se você conectar usuários com identidades sociais passando uma política e autoridade B2C, você só poderá usar a autenticação interativa e de senha de nome de usuário.

## <a name="redirect-uris"></a>URIs de Redirecionamento

Os URIs de redirecionamento a serem usados no aplicativo da área de trabalho dependerão do fluxo que você deseja usar.

- Se você estiver usando a **autenticação interativa** ou o **fluxo de código do dispositivo**, você desejará usar `https://login.microsoftonline.com/common/oauth2/nativeclient`o. Você obterá essa configuração clicando na URL correspondente na seção de **autenticação** do seu aplicativo.
  
  > [!IMPORTANT]
  > Hoje, o MSAL.NET usa outro URI de redirecionamento por padrão em aplicativos`urn:ietf:wg:oauth:2.0:oob`de área de trabalho em execução no Windows (). No futuro, vamos querer alterar esse padrão e, portanto, recomendamos que você use`https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se você estiver criando um aplicativo Objective-C ou Swift nativo para macOS, convém registrar o redirectUri com base no identificador de pacote do seu aplicativo no seguinte formato: **msauth. < seu. app. Bundle. id >://auth** (substituir < seu. app. Bundle. ID > com o identificador de pacote do seu aplicativo)
- Se seu aplicativo estiver usando apenas a autenticação integrada do Windows ou nome de usuário/senha, você não precisará registrar um URI de redirecionamento para seu aplicativo. Esses fluxos fazem uma viagem de ida e volta para o ponto de extremidade v 2.0 da plataforma de identidade da Microsoft e seu aplicativo não será chamado novamente em qualquer URI específico.
- Para distinguir o fluxo de código do dispositivo, a autenticação integrada do Windows e o nome de usuário/senha de um fluxo de aplicativo cliente confidencial que não tem URIs de redirecionamento (o fluxo de credencial do cliente usado em aplicativos de daemon), você precisa expressar que o seu o aplicativo é um aplicativo cliente público. Para obter essa configuração, vá para a seção de **autenticação** do seu aplicativo. Em seguida, na subseção **Configurações avançadas** , no **tipo de cliente padrão** parágrafo, escolha **Sim** para o **aplicativo pergunta tratar como um cliente público**.

  ![Permitir cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

Aplicativos de desktop chamam APIs para o usuário conectado. Eles precisam solicitar permissões delegadas. No entanto, eles não podem solicitar permissões de aplicativo, que são manipuladas apenas em [aplicativos daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de desktop – configuração de aplicativo](scenario-desktop-app-configuration.md)
