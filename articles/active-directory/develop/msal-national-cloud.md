---
title: Use mSAL em uma aplicação nacional de nuvem [ Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações adquiram fichas para ligar para APIs web seguros. Estas APIs web podem ser microsoft graph, outras APIs microsoft, APIs web parceiro, ou sua própria Web API. A MSAL suporta múltiplas arquiteturas e plataformas de aplicações.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533994"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Use mSAL em um ambiente de nuvem nacional

[As nuvens nacionais](authentication-national-cloud.md), também conhecidas como nuvens soberanas, são casos fisicamente isolados de Azure. Estas regiões de Azure ajudam a garantir que os requisitos de residência, soberania e conformidade dos dados sejam honrados dentro das fronteiras geográficas.

Além da nuvem mundial da Microsoft, a Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações em nuvens nacionais adquiram fichas para autenticar e ligar apis web protegidos. Estas APIs web podem ser Microsoft Graph ou outras APIs microsoft.

Incluindo a nuvem global, o Azure Ative Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como iniciar sessão no trabalho e nas contas escolares, obter um sinal de acesso e ligar para a Microsoft Graph API no ambiente de nuvem do [Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpre estes pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

As aplicações [do Governo Azure](https://docs.microsoft.com/azure/azure-government/) podem usar identidades do Governo azure ad e identidades públicas azure ad para autenticar utilizadores. Como pode usar qualquer uma destas identidades, tem de decidir qual o ponto final da autoridade que deve escolher para o seu cenário:

- Azure AD Public: Comumente usado se a sua organização já tem um inquilino público Azure AD para apoiar o Escritório 365 (Público ou GCC) ou outra aplicação.
- Governo da AD Azure: Comumente usado se a sua organização já tem um inquilino do Governo Da AD Azure para apoiar o Office 365 (GCC High ou DoD) ou está a criar um novo inquilino no Governo da AD Azure.

Depois de decidir, uma consideração especial é onde executa o registo da sua aplicação. Se escolher identidades públicas Azure AD para a sua candidatura do Governo Azure, deve registar a candidatura no seu inquilino Público Azure AD.

### <a name="get-an-azure-government-subscription"></a>Obtenha uma assinatura do Governo Azure

Para obter uma subscrição do Governo Azure, consulte [Gerir e ligar-se à sua subscrição no Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Se não tiver uma assinatura do Governo Azure, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

Para mais detalhes sobre a utilização de uma nuvem nacional com uma determinada linguagem de programação, escolha o separador correspondente à sua língua:

## <a name="net"></a>[.NET](#tab/donet)

Pode usar MSAL.NET para assinar em utilizadores, adquirir fichas e ligar para a Microsoft Graph API em nuvens nacionais.

Os seguintes tutoriais demonstram como construir uma aplicação Web .NET Core 2.2 MVC. A aplicação utiliza o OpenID Connect para assinar em utilizadores com uma conta de trabalho e escola numa organização que pertence a uma nuvem nacional.

- Para iniciar sessão nos utilizadores e adquirir fichas, siga este tutorial: [Construa uma ASP.NET utilizadores de sessão de sessão de aplicações Core Web em nuvens soberanas com a plataforma de identidade da Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para ligar para o Microsoft Graph API, siga este tutorial: [Utilizando a plataforma de identidade da Microsoft para ligar para o Microsoft Graph API a partir de uma aplicação web core 2.x ASP.NET, em nome de um utilizador que inscreveu usando o seu trabalho e conta escolar no Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Para permitir a sua aplicação MSAL.js para nuvens soberanas:

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.us/).

   Para encontrar pontos finais do portal Azure para outras nuvens nacionais, consulte [os pontos finais](authentication-national-cloud.md#app-registration-endpoints)do registo da App.

1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito e detete a sua sessão do portal para o desejado inquilino da AD Azure.
1. Vá à página de [registos](https://aka.ms/ra/ff) da App na plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **Registar uma aplicação** for apresentada, introduza um nome para a sua aplicação.
1. Nos tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional**.
1. Na secção **Redirecionamento URI,** selecione a plataforma **Web** e detete o valor para o URL da aplicação com base no seu servidor web. Consulte as seguintes secções para obter instruções sobre como definir e obter o URL de redirecionamento em Estúdio Visual e Nó.
1. Selecione **Registar**.
1. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente).**
1. Este tutorial requer que você permita o fluxo implícito de [subvenção.](v2-oauth2-implicit-grant-flow.md) No painel esquerdo da aplicação registada, **selecione Autenticação**.
1. Em **configurações avançadas**, sob **a subvenção implícita,** selecione as **fichas de identificação** e as **fichas** de acesso caixas de verificação. São necessários tokens de identificação e fichas de acesso porque esta aplicação precisa de iniciar sessão nos utilizadores e chamar uma API.
1. Selecione **Guardar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2: Configurar o seu servidor web ou projeto

- [Descarregue os ficheiros do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor web local, como o Nó.

  ou

- [Descarregue o projeto Estúdio Visual.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Em seguida, salte para [configurar o seu JavaScript SPA](#step-4-configure-your-javascript-spa) para configurar a amostra de código antes de executá-la.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passo 3: Utilize a Biblioteca de Autenticação da Microsoft para assinar no utilizador

Siga os passos no [tutorial JavaScript](tutorial-v2-javascript-spa.md#create-your-project) para criar o seu projeto e integrar-se com a MSAL para assinar no utilizador.

### <a name="step-4-configure-your-javascript-spa"></a>Passo 4: Configure o seu JavaScript SPA

No `index.html` ficheiro criado durante a configuração do projeto, adicione as informações de registo de aplicação. Adicione o seguinte código na `<script></script>` parte superior dentro `index.html` das etiquetas no corpo do seu ficheiro:

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

- `Enter_the_Application_Id_here`é o valor de ID da **Aplicação (cliente)** para a aplicação que registou.
- `Enter_the_Tenant_Info_Here`é definido para uma das seguintes opções:
    - Se a sua candidatura apoiar **Contas neste diretório organizacional,** substitua este valor pelo nome de iD do arrendatário ou inquilino (por exemplo, contoso.microsoft.com).
    - Se a sua aplicação apoiar **Contas em qualquer diretório organizacional,** substitua este valor por `organizations`.

    Para encontrar pontos finais de autenticação para todas as nuvens nacionais, consulte [os pontos finais de autenticação da Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > As contas pessoais da Microsoft não são suportadas nas nuvens nacionais.

- `graphEndpoint`é o ponto final do Microsoft Graph para a nuvem da Microsoft para o governo dos EUA.

   Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte [os pontos finais do Microsoft Graph nas nuvens nacionais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Para permitir a sua aplicação MSAL Python para nuvens soberanas:

- Registe a sua aplicação num portal específico, dependendo da nuvem. Para mais informações sobre como escolher o portal consulte [os pontos finais](authentication-national-cloud.md#app-registration-endpoints) de registo da App
- Utilize qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) do repo com algumas alterações na configuração, dependendo da nuvem, que é mencionada a seguir.
- Utilize uma autoridade específica, dependendo da nuvem em que registou o pedido. Para obter mais informações sobre as autoridades para diferentes nuvens, consulte [os pontos finais de autenticação AD Azure](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Aqui está uma autoridade de exemplo:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Para ligar para o gráfico da Microsoft requer um URL de ponto final de gráfico específico que dependa da nuvem que está a usar. Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte os [pontos finais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)do serviço Microsoft Graph e Graph Explorer .

    Aqui está um exemplo de um ponto final gráfico, com âmbito:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Para permitir o seu pedido de MSAL para java para nuvens soberanas:

- Registe a sua aplicação num portal específico, dependendo da nuvem. Para mais informações sobre como escolher o portal consulte [os pontos finais](authentication-national-cloud.md#app-registration-endpoints) de registo da App
- Utilize qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) do repo com algumas alterações na configuração, dependendo da nuvem, que são mencionadas a seguir.
- Utilize uma autoridade específica, dependendo da nuvem em que registou o pedido. Para obter mais informações sobre as autoridades para diferentes nuvens, consulte [os pontos finais de autenticação AD Azure](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Aqui está uma autoridade de exemplo:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Para ligar para o gráfico da Microsoft requer um URL de ponto final de gráfico específico que dependa da nuvem que está a usar. Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte os [pontos finais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)do serviço Microsoft Graph e Graph Explorer .

Aqui está um exemplo de um ponto final gráfico, com âmbito:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

O MSAL para iOS e macOS pode ser usado para adquirir tokens `MSALPublicClientApplication`em nuvens nacionais, mas requer uma configuração adicional ao criar .

Por exemplo, se quiser que a sua candidatura seja uma aplicação multi-arrendatária numa nuvem nacional (aqui Governo dos EUA), pode escrever:

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

## <a name="swift"></a>[Swift](#tab/swift)

O MSAL para iOS e macOS pode ser usado para adquirir tokens `MSALPublicClientApplication`em nuvens nacionais, mas requer uma configuração adicional ao criar .

Por exemplo, se quiser que a sua candidatura seja uma aplicação multi-arrendatária numa nuvem nacional (aqui Governo dos EUA), pode escrever:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Autenticação em Nuvens Nacionais](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Alemanha](https://docs.microsoft.com/azure/germany/)
