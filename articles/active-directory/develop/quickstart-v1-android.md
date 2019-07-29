---
title: Iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android | Microsoft Docs
description: Saiba como iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android.
services: active-directory
documentationcenter: android
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1ebfce2b96da51b9c33646c1626495d280d7b8a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592240"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo Android

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Se estiver a desenvolver uma aplicação Android, a Microsoft torna o processo de início de sessão dos utilizadores no Azure Active Directory (Azure AD) simples e direto. O Azure AD permite que a sua aplicação obtenha acesso a dados de utilizador através do Microsoft Graph ou da sua própria API WEB protegida.

A biblioteca Android da ADAL (biblioteca de autenticação do Azure AD) fornece ao seu aplicativo a capacidade de começar a usar o [Microsoft Azure nuvem](https://azure.microsoft.com/free/cloud-services/) e a [API de Microsoft Graph](https://developer.microsoft.com/graph) ao dar suporte a [contas de Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usando o padrão do setor OAuth 2,0 e OpenID Connect.

Neste início rápido, vai aprender a:

* Obter um token para o Microsoft Graph
* Atualizar um token
* Chamar o Microsoft Graph
* Terminar a sessão do utilizador

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de um inquilino do Azure AD no qual possa criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Cenário: Conectar usuários e chamar o Microsoft Graph

![Mostra a topologia do Azure AD e do Android](./media/quickstart-v1-android/active-directory-android-topology.png)

Pode utilizar esta aplicação para todas as contas do Azure AD. Suporta cenários de inquilino único e multi-inquilino (discutidos nos passos). Demonstra como pode criar aplicações para ligar a utilizadores empresariais e aceder aos seus dados do Azure + O365 através do Microsoft Graph. Durante o fluxo de autenticação, os utilizadores finais precisarão de iniciar sessão e consentir as permissões da aplicação e, em alguns casos, pode ser preciso que um administrador dê o consentimento para a aplicação. A maioria da lógica neste exemplo mostra como autenticar um utilizador final e fazer uma chamada básica ao Microsoft Graph.

## <a name="sample-code"></a>Código de exemplo

Você pode encontrar o código de exemplo completo [no GitHub](https://github.com/Azure-Samples/active-directory-android).

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this,
        AUTHORITY,
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(),
    RESOURCE_ID,
    CLIENT_ID,
    REDIRECT_URI,
    PromptBehavior.Auto,
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="step-1-register-and-configure-your-app"></a>Passo 1: Registrar e configurar seu aplicativo

Terá de ter uma aplicação cliente nativa registada na Microsoft com o [portal do Azure](https://portal.azure.com).

1. Aceder ao registo de aplicações
    - Navegue para o [portal do Azure](https://aad.portal.azure.com).
    - Selecione ***Azure Active Directory*** > ***Registos das Aplicações***.

2. Criar a aplicação
    - Selecione **novo registro**.
    - Introduza um nome de aplicação no campo **Nome**.
    - Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
    - Em **URI**de redirecionamento, selecione **cliente público (móvel e área de trabalho)** na lista suspensa e insira `http://localhost`.
    - Clique em **registrar**.

3. Configurar o Microsoft Graph
    - Selecione **permissões API**.
    - Selecione **Adicionar uma permissão**, dentro **de selecionar uma API** , selecione ***Microsoft Graph***.
    - Em **permissões delegadas**, selecione a permissão **usuário. ler**e, em seguida, clique em **Adicionar** para salvar.        
    
4. Parabéns! A sua aplicação está corretamente configurada. Na secção seguinte, terá de:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>Passo 2: Obter o código de exemplo

1. Clone o código.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Abra o exemplo no Android Studio.
    - Selecione **Open an existing Android Studio project** (Abrir um projeto existente do Android Studio).

## <a name="step-3-configure-your-code"></a>Passo 3: Configurar seu código

Pode encontrar toda a configuração para este exemplo de código no ficheiro ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***.

1. Substitua a constante `CLIENT_ID` pelo `ApplicationID`.
2. Substitua a constante `REDIRECT URI` pelo `Redirect URI` que configurou anteriormente (`http://localhost`).

## <a name="step-4-run-the-sample"></a>Passo 4: Executar o exemplo

1. Selecione **Compilar > Limpar Projeto**.
2. Selecione **Executar > Executar aplicação**.
3. A aplicação deve criar e mostrar alguma UX básica. Quando clica no botão `Call Graph API`, irá pedir um início de sessão e, em seguida, chamar silenciosamente a Microsoft Graph API com o novo token.

## <a name="next-steps"></a>Passos Seguintes

1. Consulte o [Wiki ADAL Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) para obter mais informações sobre a mecânica da biblioteca e como configurar novos cenários e capacidades.
2. Em cenários Nativos, a aplicação vai utilizar uma Webview incorporada e não deixará a aplicação. O `Redirect URI` pode ser arbitrário.
3. Deparou-se com problemas ou tem pedidos? Você pode criar um problema ou postar em Stack Overflow com a marca `azure-active-directory`.

### <a name="cross-app-sso"></a>SSO entre várias aplicações

Saiba [como ativar o SSO entre várias aplicações em Android com a ADAL](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Telemetria de autenticação

A biblioteca ADAL expõe telemetria de autenticação para ajudar os programadores de aplicações a compreenderem o comportamento das suas aplicações e criar melhores experiências. Isto permite-lhe capturar o êxito do início de sessão, os utilizadores ativos e várias outras informações interessantes. A utilização da telemetria de autenticação requer que os programadores de aplicações estabeleçam um serviço de telemetria para agregar e armazenar eventos.

Para saber mais sobre a telemetria de autenticação, veja [Telemetria de autenticação da ADAL Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry).
