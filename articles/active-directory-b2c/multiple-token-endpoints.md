---
title: Suporte a vários emissores de token em um aplicativo Web baseado em OWIN-Azure Active Directory B2C
description: Saiba como habilitar um aplicativo Web .NET para dar suporte a tokens emitidos por vários domínios.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 31ab19b8b3adbef1f0ea573af13b98750d278db8
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716729"
---
# <a name="support-multiple-token-issuers-in-an-owin-based-web-application"></a>Suporte a vários emissores de token em um aplicativo Web baseado em OWIN

Este artigo descreve uma técnica para habilitar o suporte a vários emissores de token em aplicativos Web e APIs que implementam a [interface da Web aberta para .net (OWIN)](http://owin.org/). O suporte a vários pontos de extremidade de token é útil quando você está migrando aplicativos do Azure Active Directory (Azure AD) B2C de *login.microsoftonline.com* para *b2clogin.com*.

As seções a seguir apresentam um exemplo de como habilitar vários emissores em um aplicativo Web e a API Web correspondente que usam os componentes de middleware do [Microsoft OWIN][katana] (Katana). Embora os exemplos de código sejam específicos para o middleware Microsoft OWIN, a técnica geral deve ser aplicável a outras bibliotecas OWIN.

> [!NOTE]
> Este artigo destina-se a Azure ad B2C clientes com aplicativos atualmente implantados que fazem referência `login.microsoftonline.com` e que desejam migrar para o ponto de extremidade recomendado. `b2clogin.com` Se você estiver configurando um novo aplicativo, use [b2clogin.com](b2clogin.md) como indicado.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos de Azure AD B2C em vigor antes de continuar com as etapas neste artigo:

* [Fluxos de usuário](tutorial-create-user-flows.md) ou [políticas personalizadas](active-directory-b2c-get-started-custom.md) criadas em seu locatário

## <a name="get-token-issuer-endpoints"></a>Obter pontos de extremidade do emissor do token

Primeiro, você precisa obter os URIs do ponto de extremidade do emissor do token para cada emissor ao qual deseja dar suporte em seu aplicativo. Para obter os pontos de extremidade *b2clogin.com* e *login.microsoftonline.com* com suporte pelo seu locatário Azure ad B2C, use o procedimento a seguir no portal do Azure.

Comece selecionando um dos fluxos de usuário existentes:

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com)
1. Em **políticas**, selecione **fluxos de usuário (políticas)**
1. Selecione uma política existente, por exemplo, *B2C_1_signupsignin1*, em seguida, selecione **executar fluxo de usuário**
1. No cabeçalho **executar fluxo de usuário** próximo à parte superior da página, selecione o hiperlink para navegar até o ponto de extremidade de descoberta do OpenID Connect para esse fluxo de usuário.

    ![Hiperlink de URI conhecido na página executar agora do portal do Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na página que é aberta no navegador, registre o `issuer` valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Use a lista suspensa **selecionar domínio** para selecionar o outro domínio e, em seguida, execute as duas etapas anteriores novamente e registre `issuer` seu valor.

Agora você deve ter dois URIs registrados que são semelhantes a:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Políticas personalizadas

Se você tiver políticas personalizadas em vez de fluxos de usuário, poderá usar um processo semelhante para obter os URIs do emissor.

1. Navegue até seu locatário do Azure AD B2C
1. Selecionar **estrutura de experiência de identidade**
1. Selecione uma das políticas de terceira parte confiável, por exemplo, *B2C_1A_signup_signin*
1. Use a lista suspensa **selecionar domínio** para selecionar um domínio, por exemplo, *yourtenant.b2clogin.com*
1. Selecione o hiperlink exibido em **ponto de extremidade de descoberta do OpenID Connect**
1. Registrar o `issuer` valor
1. Execute as etapas 4-6 para o outro domínio, por exemplo, *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Agora que você tem os dois URIs de ponto de extremidade de token, você precisa atualizar seu código para especificar que ambos os pontos de extremidades sejam emissores válidos. Para percorrer um exemplo, baixe ou clone o aplicativo de exemplo e, em seguida, atualize o exemplo para dar suporte a ambos os pontos de extremidade como emissores válidos.

Baixe o arquivo: [Active-Directory-B2C-dotnet-webapp-and-webAPI-Master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Habilitar vários emissores na API Web

Nesta seção, você atualiza o código para especificar que ambos os pontos de extremidade do emissor do token sejam válidos.

1. Abra a solução **B2C-WebAPI-dotnet. sln** no Visual Studio
1. No projeto **TaskService** , abra o arquivo *TaskService\\App_Start\\* * Startup.auth.cs** * em seu editor
1. Adicione a seguinte `using` diretiva à parte superior do arquivo:

    `using System.Collections.Generic;`
1. Adicione a [`ValidIssuers`][validissuers] propriedade [`TokenValidationParameters`][tokenvalidationparameters] à definição e especifique os URIs que você registrou na seção anterior:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`é fornecido pelo MSAL.NET e é consumido pelo middleware OWIN na próxima seção do código em *Startup.auth.cs*. Com vários emissores válidos especificados, o pipeline de aplicativo do OWIN é informado de que os dois pontos de extremidade do token são emissores válidos.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Como mencionado anteriormente, outras bibliotecas OWIN geralmente fornecem um recurso semelhante para dar suporte a vários emissores. Embora fornecer exemplos para cada biblioteca esteja fora do escopo deste artigo, você pode usar uma técnica semelhante para a maioria das bibliotecas.

## <a name="switch-endpoints-in-web-app"></a>Alternar pontos de extremidade no aplicativo Web

Agora, com os dois URIs compatíveis com sua API Web, você precisa atualizar seu aplicativo Web para que ele recupere tokens do ponto de extremidade b2clogin.com.

Por exemplo, você pode configurar o aplicativo Web de exemplo para usar o novo ponto de extremidade `ida:AadInstance` modificando o valor no arquivo *TaskWebApp\\* * Web. config** * do projeto **TaskWebApp** .

Altere o `ida:AadInstance` valor no *Web. config* de TaskWebApp para que ele faça referência `{your-b2c-tenant-name}.b2clogin.com` em vez `login.microsoftonline.com`de.

Antes:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Depois (substitua `{your-b2c-tenant}` pelo nome do seu locatário B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando as cadeias de caracteres de ponto de extremidade são construídas durante a execução do aplicativo Web, os pontos de extremidades baseados em b2clogin.com são usados ao solicitar tokens.

## <a name="next-steps"></a>Passos Seguintes

Este artigo apresentou um método de configuração de uma API Web que implementa o Katana (Microsoft OWIN middleware) para aceitar tokens de vários pontos de extremidade do emissor. Como você pode observar, há várias outras cadeias de caracteres nos arquivos *Web. config* dos projetos TaskService e TaskWebApp que precisariam ser alterados se você quiser compilar e executar esses projetos em seu próprio locatário. Você é bem-vindo a modificar os projetos adequadamente se quiser vê-los em ação. no entanto, um passo a passo completo de fazer isso está fora do escopo deste artigo.

Para obter mais informações sobre os diferentes tipos de tokens de segurança emitidos por Azure AD B2C, consulte [visão geral de tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
