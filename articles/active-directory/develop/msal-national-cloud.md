---
title: Usar a MSAL (biblioteca de autenticação da Microsoft) em nuvens nacionais – plataforma de identidade da Microsoft
description: A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos adquiram tokens para chamar APIs da Web protegidas. Essas APIs da Web podem ser Microsoft Graph, outras APIs da Microsoft, APIs Web de parceiros ou sua própria API Web. O MSAL dá suporte a várias arquiteturas e plataformas de aplicativos.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268570"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Usar o MSAL em um ambiente de nuvem nacional

As [nuvens nacionais](authentication-national-cloud.md) são instâncias fisicamente isoladas do Azure. Essas regiões do Azure ajudam a garantir que os requisitos de residência de dados, soberania e conformidade sejam respeitados dentro dos limites geográficos.

Além da nuvem Mundial da Microsoft, a MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos em nuvens nacionais adquiram tokens para autenticar e chamar APIs da Web protegidas. Essas APIs da Web podem ser Microsoft Graph ou outras APIs da Microsoft.

Incluindo a nuvem global, o Azure Active Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como entrar em contas corporativas e de estudante, obter um token de acesso e chamar a API de Microsoft Graph no ambiente de [nuvem do Azure governamental](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de atender a esses pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

Os aplicativos [do Azure governamental](https://docs.microsoft.com/azure/azure-government/) podem usar identidades do Azure ad governamental e identidades públicas do Azure ad para autenticar usuários. Como você pode usar qualquer uma dessas identidades, você precisa decidir qual ponto de extremidade de autoridade deve escolher para seu cenário:

- Público do Azure AD: Normalmente usado se sua organização já tiver um locatário público do Azure AD para dar suporte ao Office 365 (Public ou GCC) ou a outro aplicativo.
- Azure AD governamental: Normalmente usado se sua organização já tiver um locatário do Azure AD governamental para dar suporte ao Office 365 (GCC High ou DoD) ou estiver criando um novo locatário no Azure AD governamental.

Depois de decidir, uma consideração especial é onde você executa o registro do aplicativo. Se você escolher identidades públicas do Azure AD para seu aplicativo do Azure governamental, deverá registrar o aplicativo em seu locatário público do Azure AD.

### <a name="get-an-azure-government-subscription"></a>Obter uma assinatura do Azure governamental

Para obter uma assinatura do Azure governamental, consulte [Gerenciando e conectando-se à sua assinatura no Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Se você não tiver uma assinatura do Azure governamental, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.us/).
    
   Para localizar portal do Azure pontos de extremidade para outras nuvens nacionais, consulte [pontos de extremidade de registro de aplicativo](authentication-national-cloud.md#app-registration-endpoints).

1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
1. Vá para a página de [registros de aplicativo](https://aka.ms/ra/ff) na plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **registrar um aplicativo** for exibida, insira um nome para seu aplicativo.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.
1. Na seção **URI de redirecionamento** , selecione a plataforma **da Web** e defina o valor como a URL do aplicativo com base no servidor Web. Consulte as próximas seções para obter instruções sobre como definir e obter a URL de redirecionamento no Visual Studio e no nó.
1. Selecione **Registar**.
1. Na página **visão geral** do aplicativo, anote o valor da **ID do aplicativo (cliente)** .
1. Este tutorial requer que você habilite o [fluxo de concessão implícito](v2-oauth2-implicit-grant-flow.md). No painel esquerdo do aplicativo registrado, selecione **autenticação**.
1. Em **Configurações avançadas**, em **concessão implícita**, marque as caixas de seleção **tokens de ID** e **tokens de acesso** . Tokens de ID e tokens de acesso são necessários porque esse aplicativo precisa conectar usuários e chamar uma API.
1. Selecione **Guardar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2:  Configurar seu servidor Web ou projeto

- [Baixe os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor Web local, como node.

  ou

- [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Em seguida, pule para [Configurar o seu JavaScript Spa](#step-4-configure-your-javascript-spa) para configurar o exemplo de código antes de executá-lo.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passo 3: Usar a biblioteca de autenticação da Microsoft para conectar o usuário

Siga as etapas no [tutorial do JavaScript](tutorial-v2-javascript-spa.md#create-your-project) para criar seu projeto e integrá-lo ao MSAL para conectar o usuário.

### <a name="step-4-configure-your-javascript-spa"></a>Passo 4: Configurar seu JavaScript SPA

`index.html` No arquivo criado durante a configuração do projeto, adicione as informações de registro do aplicativo. Adicione o seguinte código na parte superior dentro das `<script></script>` marcas no corpo `index.html` do arquivo:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Nesse código:

- `Enter_the_Application_Id_here`é o valor da **ID do aplicativo (cliente)** para o aplicativo que você registrou.
- `Enter_the_Tenant_Info_Here`é definido como uma das seguintes opções:
    - Se seu aplicativo der suporte a **contas nesse diretório organizacional**, substitua esse valor pela ID do locatário ou pelo nome do locatário (por exemplo, contoso.Microsoft.com).
    - Se seu aplicativo oferecer suporte a **contas em qualquer diretório organizacional**, substitua esse `organizations`valor por.
    
    Para localizar pontos de extremidade de autenticação para todas as nuvens nacionais, consulte [pontos de extremidade de autenticação do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Não há suporte para contas pessoais da Microsoft em nuvens nacionais.
  
- `graphEndpoint`é o ponto de extremidade Microsoft Graph para a nuvem da Microsoft para o governo dos EUA.

   Para localizar Microsoft Graph pontos de extremidade para todas as nuvens nacionais, confira [Microsoft Graph pontos de extremidade em nuvens nacionais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Você pode usar o MSAL.NET para conectar usuários, adquirir tokens e chamar a API Microsoft Graph em nuvens nacionais.

Os tutoriais a seguir demonstram como criar um aplicativo Web MVC do .NET Core 2,2. O aplicativo usa o OpenID Connect para conectar usuários com uma conta corporativa e de estudante em uma organização que pertence a uma nuvem nacional.

- Para conectar usuários e adquirir tokens, siga [este tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para chamar a API de Microsoft Graph, siga [este tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

O MSAL para iOS e macOS pode ser usado para adquirir tokens em nuvens nacionais, mas requer configuração adicional ao `MSALPublicClientApplication`criar.

Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional (aqui, o governo dos EUA), você poderia escrever:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [21Vianet do Azure na China](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
