---
title: Utilizar o Microsoft Authentication Library (MSAL) em Clouds nacionais - a plataforma de identidade da Microsoft
description: Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores de aplicativos adquirir tokens para chamar as APIs de web segura. Estas APIs web pode ser o Microsoft Graph, outras APIS da Microsoft, as APIs de web de terceiros ou seu próprio web API. A MSAL suporta várias arquiteturas de aplicativos e plataformas.
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
ms.openlocfilehash: 18eccd6b6d29a43cc3fa13d133d449bf0b9be657
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075794"
---
# <a name="use-msal-in-national-cloud-environment"></a>Utilizar a MSAL no ambiente em nuvem nacionais

[Clouds nacionais](authentication-national-cloud.md) são instâncias isoladas fisicamente do Azure. Estas regiões do Azure foram concebidos para se certificar de que os requisitos de residência, de soberania de dados e de conformidade de dados são honrados dentro dos limites geográficos.

Além de nuvem da Microsoft em todo o mundo, a Microsoft Authentication Library (MSAL) também permite aos programadores de aplicações em clouds nacionais adquira tokens para autenticar e chamar APIs de web segura. Estas APIs web pode ser o Microsoft Graph ou outras APIS da Microsoft.

Incluindo nuvem global, o Azure Active Directory (Azure AD) é implementado nas clouds nacionais do seguintes:  

- Azure US Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como iniciar sessão nas contas profissionais e escolares, obter um acesso de token e chamar a API do Microsoft Graph [Microsoft cloud para administração pública dos EUA](https://azure.microsoft.com/global-infrastructure/government/) ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpre estes pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

[O Azure Government](https://docs.microsoft.com/azure/azure-government/) aplicações podem utilizar as identidades do Azure AD Government, bem como identidades públicas do Azure AD para autenticar os utilizadores. Uma vez que pode utilizar qualquer um destas identidades, tem de compreender e decidir qual ponto de extremidade de autoridade, deve escolher para o seu cenário:

- Azure AD público: Geralmente, utilizada se a sua organização já tiver um inquilino do Azure AD pública para o suporte do Office 365 (público ou GCC) ou outra aplicação.
- Azure AD Government: Geralmente, utilizada se a sua organização já tiver um inquilino do Azure Government de AD para o suporte do Office 365 (GCC alta ou DoD) ou está a criar um novo inquilino do Azure Government do AD.

Depois de decidir, a consideração especial é onde executar o registo de aplicação. Se optar por identidades pública do Azure AD para a sua aplicação do Azure Government, tem de registar a aplicação no seu inquilino do Azure AD pública.

### <a name="get-an-azure-government-subscription"></a>Obtenha uma subscrição do Azure Government

- Para obter uma subscrição do Azure Government, consulte [gerir e ligar a sua subscrição no Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).
- Se não tiver uma subscrição do Azure Government, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação

1. Inicie sessão para o [portal do Azure](https://portal.azure.us/) registar uma aplicação.
    1. Para obter pontos finais do portais do Azure para outras clouds nacionais, veja [pontos finais de registo de aplicação](authentication-national-cloud.md#app-registration-endpoints)

1. Se sua dá conta de acesso a mais do que um inquilino, selecione a sua conta no canto superior direito e definir a sua sessão do portal para o Azure AD pretendido de inquilino.
1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://aka.ms/ra/ff) página.
1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional**.
1. Sob o **URI de redirecionamento** secção, selecione a **Web** plataforma e defina o valor para o URL da aplicação com base no seu servidor web. Consulte as secções abaixo para obter instruções sobre como definir e obter o URL de redirecionamento no nó e o Visual Studio.
1. Quando terminar, selecione **Registar**.
1. Na aplicação **descrição geral** página, tome nota da **ID de aplicação (cliente)** valor.
1. Este tutorial requer o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação do lado esquerdo do aplicativo registrado, selecione **autenticação**.
1. Na **definições avançadas**, em **concessão implícita**, permita as duas opções **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários, uma vez que esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
1. Selecione **Guardar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2:  Configurar seu servidor web ou projeto

- [Transferir os ficheiros de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor local web, como o nó.

  ou

- [Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

E, em seguida, avance para o ["Configurar seu SPA do JavaScript"](#step-4-configure-your-javascript-spa) para configurar o exemplo de código antes de executá-la.

### <a name="step-3-use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Passo 3: Utilizar o Microsoft Authentication Library (MSAL) para iniciar a sessão do utilizador

Siga os passos em [Javascript tutorial](tutorial-v2-javascript-spa.md#create-your-project) para criar o seu projeto e integrar com o Microsoft Authentication Library (MSAL) para iniciar a sessão do utilizador.

### <a name="step-4-configure-your-javascript-spa"></a>Passo 4: Configurar seu SPA do JavaScript

Na `index.html` ficheiro criado durante a configuração do projeto, adicione as informações de registo de aplicação. Adicione o seguinte código na parte superior dentro do `<script></script>` etiquetas no corpo do seu `index.html` ficheiro:

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

Em que:

- `Enter_the_Application_Id_here` - é o **ID da Aplicação (cliente)** que registou.
- `Enter_the_Tenant_Info_Here` - está definido para uma das seguintes opções:
    - Se a sua aplicação suportar **contas neste diretório organizacional**, substitua este valor com o **ID do inquilino** ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
    - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
    -  Para obter pontos finais de autenticação para todas as nuvens nacionais, veja [pontos finais de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)

     > [!NOTE]
     > Cenários de contas (MSA) da Microsoft pessoais não são suportados em clouds nacionais.
  
-   `graphEndpoint` -é o ponto de final do Microsoft Graph para a Microsoft cloud para administração pública dos EUA.
    -  Para localizar pontos de extremidade do Microsoft Graph para todas as nuvens nacionais, veja [pontos de extremidade do Microsoft Graph na cloud nacional](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

## <a name="net"></a>.NET

.NET de MSAL permite-lhe iniciar sessão dos utilizadores, obter o token e chamar o Microsoft Graph API em Clouds nacionais.

O tutorial seguinte demonstra como criar uma aplicação Web de MVC do .NET Core 2.2 que usa o OpenID Connect para iniciar sessão dos utilizadores com as suas contas "profissionais e escolares" na sua organização que pertencem a nuvens nacionais.

- Para iniciar sessão dos utilizadores e obter o token, siga [deste tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para chamar a API do Microsoft Graph, siga [deste tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [O Azure na China 21Vianet](https://docs.microsoft.com/azure/china/)
- [O Azure Alemanha](https://docs.microsoft.com/azure/germany/)