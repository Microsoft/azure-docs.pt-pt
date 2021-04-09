---
title: Use o MSAL numa aplicação nacional de nuvem | Rio Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações adquiram fichas de forma a chamar APIs web seguras. Estas APIs web podem ser Microsoft Graph, outras APIs da Microsoft, APIs de web parceiros ou a sua própria API web. A MSAL suporta múltiplas arquiteturas e plataformas de aplicações.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 09c4dadd7a6560bd5163d623dd8a7f247b57860e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102500"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Use o MSAL num ambiente de nuvem nacional

[Nuvens nacionais](authentication-national-cloud.md), também conhecidas como nuvens soberanas, são casos fisicamente isolados de Azure. Estas regiões do Azure ajudam a garantir que os requisitos de residência, soberania e conformidade sejam respeitados dentro dos limites geográficos.

Além da nuvem mundial da Microsoft, a Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações em nuvens nacionais adquiram fichas para autenticar e chamar APIs web seguras. Estas APIs web podem ser Microsoft Graph ou outras APIs da Microsoft.

Incluindo a nuvem global, o Azure Ative Directory (Azure AD) é implantado nas seguintes nuvens nacionais:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como iniciar seduções nas contas do trabalho e da escola, obter um token de acesso e ligar para a Microsoft Graph API no ambiente [de nuvem do Governo Azure.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de cumprir estes pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

As aplicações [do Governo Azure](../../azure-government/index.yml) podem usar identidades do Governo AD Azure e identidades públicas da Azure AD para autenticar os utilizadores. Como pode usar qualquer uma destas identidades, decida qual o ponto final da autoridade que deve escolher para o seu cenário:

- Azure AD Public: Normalmente usado se a sua organização já tem um inquilino público Azure AD para apoiar a Microsoft 365 (Público ou GCC) ou outra aplicação.
- Governo Azure AD: Normalmente usado se a sua organização já tem um inquilino do Governo Azure AD para apoiar o Office 365 (GCC High ou DoD) ou está a criar um novo inquilino no Governo Azure AD.

Depois de decidir, uma consideração especial é onde realiza o registo da sua aplicação. Se escolher identidades públicas da Azure AD para a sua candidatura ao Governo de Azure, deve registar o pedido no seu inquilino público Azure AD.

### <a name="get-an-azure-government-subscription"></a>Obtenha uma assinatura do Governo Azure

Para obter uma assinatura do Governo Azure, consulte [Managing e connecting à sua subscrição no Governo Azure](../../azure-government/compare-azure-government-global-azure.md).

Se não tiver uma assinatura do Governo Azure, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

Para mais detalhes sobre a utilização de uma nuvem nacional com uma determinada linguagem de programação, escolha o separador correspondente ao seu idioma:

## <a name="net"></a>[.NET](#tab/dotnet)

Pode utilizar MSAL.NET para iniciar síssição nos utilizadores, adquirir fichas e ligar para a API do Microsoft Graph em nuvens nacionais.

Os seguintes tutoriais demonstram como construir uma aplicação Web .NET Core 2.2 MVC. A aplicação usa o OpenID Connect para assinar em utilizadores com uma conta de trabalho e escola numa organização que pertence a uma nuvem nacional.

- Para iniciar sessão nos utilizadores e adquirir fichas, siga este tutorial: [Construa uma ASP.NET utilizadores de aplicações Core Web em nuvens soberanas com a plataforma de identidade da Microsoft.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- Para ligar para a Microsoft Graph API, siga este tutorial: [Utilizando a plataforma de identidade da Microsoft para ligar para a API do Microsoft Graph a partir de uma aplicação web Core 2.x ASP.NET, em nome de um utilizador que se inscreve utilizando o seu trabalho e conta escolar na Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Para ativar a sua aplicação MSAL.js para nuvens soberanas:

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

1. Inicie sessão no <a href="https://portal.azure.us/" target="_blank">portal do Azure</a>.

   Para encontrar pontos finais do portal Azure para outras nuvens nacionais, consulte [os pontos finais de registo da App.](authentication-national-cloud.md#app-registration-endpoints)

1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional**.
1. Na secção **Redirecionar o URI,** selecione a plataforma **Web** e desaver o valor para o URL da aplicação com base no seu servidor web. Consulte as próximas secções para obter instruções sobre como definir e obter o URL de redirecionamento em Estúdio Visual e Nó.
1. Selecione **Registar**.
1. Na página **'Visão Geral',** anote o valor **de ID da Aplicação (cliente)** para utilização posterior.
    Este tutorial requer que você permita o [fluxo de subvenção implícita](v2-oauth2-implicit-grant-flow.md). 
1. Em **Gestão**, **selecione Autenticação**.
1. Sob **subvenção implícita e fluxos híbridos,** selecione **fichas de identificação** e **fichas de acesso.** São necessários tokens de ID e fichas de acesso porque esta aplicação precisa de assinar nos utilizadores e chamar uma API.
1. Selecione **Guardar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2: Configurar o seu servidor web ou projeto

- [Descarregue os ficheiros do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor web local, como o Node.

  ou

- [Descarregue o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Em seguida, salte para [configurar o seu JAVAScript SPA](#step-4-configure-your-javascript-spa) para configurar a amostra de código antes de executá-la.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passo 3: Utilize a Biblioteca de Autenticação da Microsoft para iniciar súblio no utilizador

Siga os passos no [tutorial do JavaScript](tutorial-v2-javascript-spa.md#create-your-project) para criar o seu projeto e integrar-se com o MSAL para iniciar sessão no utilizador.

### <a name="step-4-configure-your-javascript-spa"></a>Passo 4: Configurar o seu JavaScript SPA

No ficheiro criado durante a `index.html` configuração do projeto, adicione as informações de registo de pedidos. Adicione o seguinte código na parte superior dentro das `<script></script>` etiquetas no corpo do seu `index.html` ficheiro:

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

Neste código:

- `Enter_the_Application_Id_here` é o valor **de ID de aplicação (cliente)** para a aplicação que registou.
- `Enter_the_Tenant_Info_Here` é definida como uma das seguintes opções:
    - Se a sua candidatura apoiar **contas neste diretório organizacional,** substitua este valor pelo ID do inquilino ou nome do inquilino (por exemplo, contoso.microsoft.com).
    - Se a sua aplicação suportar **contas em qualquer diretório organizacional,** substitua este valor por `organizations` .

    Para encontrar pontos finais de autenticação para todas as nuvens nacionais, consulte [os pontos finais de autenticação AD AZure](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > As contas pessoais da Microsoft não são suportadas nas nuvens nacionais.

- `graphEndpoint` é o ponto final do Microsoft Graph para a nuvem da Microsoft para o governo dos EUA.

   Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte os [pontos finais do Microsoft Graph em nuvens nacionais.](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="python"></a>[Python](#tab/python)

Para ativar a sua aplicação MSAL Python para nuvens soberanas:

- Registe a sua aplicação num portal específico, dependendo da nuvem. Para mais informações sobre como escolher o portal consulte os [pontos finais de registo da App](authentication-national-cloud.md#app-registration-endpoints)
- Utilize qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) do repo com algumas alterações na configuração, dependendo da nuvem, que é mencionada em seguida.
- Utilize uma autoridade específica, dependendo da nuvem em que registou a aplicação. Para obter mais informações sobre as autoridades para diferentes nuvens, consulte [os pontos finais de autenticação Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Aqui está uma autoridade exemplo:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Ligar para a API do Gráfico microsoft requer um URL de ponto final específico da nuvem que está a usar. Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte os [pontos finais de raiz do microsoft Graph e Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Aqui está um exemplo de um ponto final do Microsoft Graph, com âmbito:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Para ativar a sua aplicação MSAL para Java para nuvens soberanas:

- Registe a sua aplicação num portal específico, dependendo da nuvem. Para mais informações sobre como escolher o portal consulte os [pontos finais de registo da App](authentication-national-cloud.md#app-registration-endpoints)
- Utilize qualquer uma das [amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) do repo com algumas alterações na configuração, dependendo da nuvem, que são mencionadas em seguida.
- Utilize uma autoridade específica, dependendo da nuvem em que registou a aplicação. Para obter mais informações sobre as autoridades para diferentes nuvens, consulte [os pontos finais de autenticação Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Aqui está uma autoridade exemplo:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Ligar para a API do Gráfico microsoft requer um URL de ponto final específico da nuvem que está a usar. Para encontrar pontos finais do Microsoft Graph para todas as nuvens nacionais, consulte os [pontos finais de raiz do microsoft Graph e Graph Explorer](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Aqui está um exemplo de um ponto final gráfico, com âmbito:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

O MSAL para iOS e macOS pode ser usado para adquirir fichas em nuvens nacionais, mas requer configuração adicional ao criar `MSALPublicClientApplication` .

Por exemplo, se quiser que a sua candidatura seja uma aplicação multi-inquilina numa nuvem nacional (aqui governo dos EUA), pode escrever:

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

O MSAL para iOS e macOS pode ser usado para adquirir fichas em nuvens nacionais, mas requer configuração adicional ao criar `MSALPublicClientApplication` .

Por exemplo, se quiser que a sua candidatura seja uma aplicação multi-inquilina numa nuvem nacional (aqui governo dos EUA), pode escrever:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Passos seguintes

Consulte [os pontos finais](authentication-national-cloud.md) de autenticação em nuvem nacional para obter uma lista dos URLs do portal Azure e pontos finais simbólicos para cada nuvem.

Documentação nacional em nuvem:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Alemanha](../../germany/index.yml)