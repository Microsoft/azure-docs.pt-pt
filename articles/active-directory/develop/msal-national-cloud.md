---
title: Utilizar o Microsoft Authentication Library (MSAL) em clouds nacionais - a plataforma de identidade da Microsoft
description: Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores de aplicativos adquirir tokens para chamar as APIs de web segura. Estas APIs web pode ser Microsoft Graph, outras APIs da Microsoft, as APIs de web de parceiros ou seu próprio web API. A MSAL suporta várias arquiteturas de aplicativos e plataformas.
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
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235274"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Utilizar a MSAL num ambiente em nuvem nacionais

[Clouds nacionais](authentication-national-cloud.md) são instâncias isoladas fisicamente do Azure. Estas regiões do Azure ajudam a tornar-se de que os requisitos de residência, de soberania de dados e de conformidade de dados são honrados dentro dos limites geográficos.

Além de nuvem da Microsoft em todo o mundo, a Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicativos em clouds nacionais adquirir tokens para autenticar e chamar APIs de web segura. Estas APIs web pode ser Microsoft Graph ou outras APIs da Microsoft.

Incluindo na nuvem global, o Azure Active Directory (Azure AD) é implementado nas clouds nacionais do seguintes:  

- Azure Government
- Azure China 21Vianet
- Azure Alemanha

Este guia demonstra como iniciar sessão trabalhar e escolar contas, obter um acesso de token e chamar a API do Microsoft Graph a [cloud do Azure Government](https://azure.microsoft.com/global-infrastructure/government/) ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpre estes pré-requisitos.

### <a name="choose-the-appropriate-identities"></a>Escolha as identidades apropriadas

[O Azure Government](https://docs.microsoft.com/azure/azure-government/) aplicações podem utilizar as identidades do Azure AD Government e identidades pública do Azure AD para autenticar os utilizadores. Como pode usar qualquer um destas identidades, precisa decidir qual ponto de extremidade de autoridade, deve escolher para o seu cenário:

- Azure AD público: Geralmente, utilizada se a sua organização já tiver um inquilino do Azure AD pública para o suporte do Office 365 (público ou GCC) ou outra aplicação.
- Azure AD Government: Geralmente, utilizada se a sua organização já tiver um inquilino do Azure Government de AD para o suporte do Office 365 (GCC alta ou DoD) ou está a criar um novo inquilino do Azure Government do AD.

Depois de decidir, uma consideração especial é onde executar o registo de aplicação. Se optar por identidades pública do Azure AD para a sua aplicação do Azure Government, tem de registar a aplicação no seu inquilino do Azure AD pública.

### <a name="get-an-azure-government-subscription"></a>Obtenha uma subscrição do Azure Government

Para obter uma subscrição do Azure Government, consulte [gerir e ligar à sua subscrição no Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Se não tiver uma subscrição do Azure Government, crie uma [conta gratuita](https://azure.microsoft.com/global-infrastructure/government/request/) antes de começar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.us/).
    
   Para obter pontos finais do portais do Azure para outras clouds nacionais, veja [pontos de extremidade de registo de aplicação](authentication-national-cloud.md#app-registration-endpoints).

1. Se sua dá conta de acesso a mais do que um inquilino, selecione a sua conta no canto superior direito e definir a sua sessão do portal para o Azure AD pretendido de inquilino.
1. Vá para o [registos das aplicações](https://aka.ms/ra/ff) página na plataforma de identidade da Microsoft para desenvolvedores.
1. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
1. Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional**.
1. Na **URI de redirecionamento** secção, selecione a **Web** plataforma e defina o valor para o URL da aplicação com base no seu servidor web. Consulte as secções seguintes para obter instruções sobre como definir e obter o URL de redirecionamento no nó e o Visual Studio.
1. Selecione **Registar**.
1. Na aplicação **descrição geral** página, tome nota da **ID de aplicação (cliente)** valor.
1. Este tutorial requer que ative a [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md). No painel esquerdo do aplicativo registrado, selecione **autenticação**.
1. Na **definições avançadas**, em **concessão implícita**, selecione o **tokens de ID** e **tokens de acesso** caixas de verificação. Tokens de ID e tokens de acesso são necessários porque esta aplicação tem de iniciar sessão dos utilizadores e chamar uma API.
1. Selecione **Guardar**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Passo 2:  Configurar seu servidor web ou projeto

- [Transferir os ficheiros de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para um servidor local web, como o nó.

  ou

- [Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Em seguida, avance para o [configurar seu SPA do JavaScript](#step-4-configure-your-javascript-spa) para configurar o exemplo de código antes de o executar.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Passo 3: Utilize a biblioteca de autenticação da Microsoft para iniciar a sessão do utilizador

Siga os passos no [JavaScript tutorial](tutorial-v2-javascript-spa.md#create-your-project) para criar o seu projeto e integrar com MSAL para iniciar a sessão do utilizador.

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

Nesse código:

- `Enter_the_Application_Id_here` é o **ID da aplicação (cliente)** valor para a aplicação que registou.
- `Enter_the_Tenant_Info_Here` está definido como uma das seguintes opções:
    - Se a sua aplicação suportar **contas neste diretório organizacional**, substitua este valor com o ID do inquilino ou nome (por exemplo, contoso.microsoft.com) do inquilino.
    - Se a sua aplicação suportar **contas em qualquer diretório organizacional**, substitua este valor com `organizations`.
    
    Para obter pontos finais de autenticação para todas as nuvens nacionais, veja [pontos finais de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Contas Microsoft pessoais não são suportadas em clouds nacionais.
  
- `graphEndpoint` é o ponto de final do Microsoft Graph para a cloud da Microsoft para administração pública dos EUA.

   Para localizar pontos de extremidade do Microsoft Graph para todas as nuvens nacionais, veja [pontos de extremidade do Microsoft Graph em clouds nacionais](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Pode utilizar MSAL.NET para iniciar sessão dos utilizadores, adquira tokens e chamar a API do Microsoft Graph em clouds nacionais.

Os tutoriais seguintes demonstram como criar uma aplicação Web de MVC do .NET Core 2.2. A aplicação utiliza o OpenID Connect para iniciar sessão dos utilizadores com uma conta profissional e escolar numa organização que pertence a uma cloud nacional.

- Para iniciar sessão dos utilizadores e adquirir tokens, siga [deste tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Para chamar a API do Microsoft Graph, siga [deste tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [O Azure na China 21Vianet](https://docs.microsoft.com/azure/china/)
- [O Azure Alemanha](https://docs.microsoft.com/azure/germany/)