---
title: Registrar aplicativos da área de trabalho que chamam APIs da Web-plataforma Microsoft Identity | Azure
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
ms.openlocfilehash: dabc96ef669f0c0c61a7bca4a16828294cf404df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423841"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplicativo de desktop que chama APIs Web-registro de aplicativo

Este artigo aborda as especificações de registro do aplicativo para um aplicativo de área de trabalho.

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

- Se você estiver usando a **autenticação interativa** ou o **fluxo de código do dispositivo**, convém usar `https://login.microsoftonline.com/common/oauth2/nativeclient`. Você obterá essa configuração clicando na URL correspondente na seção de **autenticação** do seu aplicativo.
  
  > [!IMPORTANT]
  > Hoje, o MSAL.NET usa outro URI de redirecionamento por padrão em aplicativos de área de trabalho em execução no Windows (`urn:ietf:wg:oauth:2.0:oob`). No futuro, vamos querer alterar esse padrão e, portanto, recomendamos que você use `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se você estiver criando um aplicativo Objective-C ou Swift nativo para macOS, convém registrar o redirectUri com base no identificador de pacote do seu aplicativo no seguinte formato: **msauth. < seu. app. Bundle. id >://auth** (substitua < seu. app. Bundle. ID > pelo identificador de pacote do seu aplicativo)
- Se seu aplicativo estiver usando apenas a autenticação integrada do Windows ou nome de usuário/senha, você não precisará registrar um URI de redirecionamento para seu aplicativo. Esses fluxos fazem uma viagem de ida e volta para o ponto de extremidade v 2.0 da plataforma de identidade da Microsoft e seu aplicativo não será chamado novamente em qualquer URI específico.
- Para distinguir o fluxo de código do dispositivo, a autenticação integrada do Windows e o nome de usuário/senha de um fluxo de aplicativo cliente confidencial que não tem URIs de redirecionamento (o fluxo de credencial do cliente usado em aplicativos de daemon), você precisa expressar que seu aplicativo é um aplicativo cliente público. Para obter essa configuração, vá para a seção de **autenticação** do seu aplicativo. Em seguida, na subseção **Configurações avançadas** , no **tipo de cliente padrão** parágrafo, escolha **Sim** para o **aplicativo pergunta tratar como um cliente público**.

  ![Permitir cliente público](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Permissões de API

Aplicativos de desktop chamam APIs para o usuário conectado. Eles precisam solicitar permissões delegadas. No entanto, eles não podem solicitar permissões de aplicativo, que são manipuladas apenas em [aplicativos daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de desktop – configuração de aplicativo](scenario-desktop-app-configuration.md)
