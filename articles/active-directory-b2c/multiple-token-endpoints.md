---
title: Migrar APIs web baseados em OWIN para b2clogin.com ou um domínio personalizado
titleSuffix: Azure AD B2C
description: Saiba como ativar uma API web .NET para suportar fichas emitidas por vários emitentes simbólicos enquanto migra as suas aplicações para b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 860f167913211ee7c511e515937f29ba5bf954cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491574"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom-or-a-custom-domain"></a>Migrar uma API web baseada em OWIN para b2clogin.com ou um domínio personalizado

Este artigo descreve uma técnica para permitir o suporte a múltiplos emitentes simbólicos em APIs web que implementam a [Interface Web Aberta para .NET (OWIN)](http://owin.org/). O suporte a vários pontos finais simbólicos é útil quando está a migrar APIs do Azure Ative Directory B2C (Azure AD B2C) e as suas aplicações de um domínio para outro. Por exemplo, de *login.microsoftonline.com* a *b2clogin.com,* ou a um [domínio personalizado.](custom-domain.md)

Ao adicionar suporte na sua API para aceitar fichas emitidas por b2clogin.com, login.microsoftonline.com ou um domínio personalizado, pode migrar as suas aplicações web de forma faseada antes de remover o suporte para tokens emitidos por login.microsoftonline.com da API.

As secções seguintes apresentam um exemplo de como ativar vários emitentes numa API web que utiliza os componentes de middleware [Microsoft OWIN][katana] (Katana). Embora os exemplos de código sejam específicos do middleware Microsoft OWIN, a técnica geral deve ser aplicável a outras bibliotecas OWIN.

## <a name="prerequisites"></a>Pré-requisitos

Necessita dos seguintes recursos Azure AD B2C antes de continuar com os passos deste artigo:

* [Fluxos de utilizador](tutorial-create-user-flows.md) ou políticas [personalizadas](custom-policy-get-started.md) criadas no seu inquilino

## <a name="get-token-issuer-endpoints"></a>Obtenha pontos finais de emitente simbólico

Primeiro precisa de obter os URIs do ponto final do emitente para cada emitente que pretende apoiar na sua API. Para obter os *b2clogin.com* e *login.microsoftonline.com* pontos finais apoiados pelo seu inquilino Azure AD B2C, utilize o seguinte procedimento no portal Azure.

Comece por selecionar um dos fluxos de utilizador existentes:

1. Navegue até ao seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com)
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**
1. Selecione uma política existente, por *exemplo, B2C_1_signupsignin1,* em seguida, selecione **Executar o fluxo de utilizador**
1. No âmbito do fluxo do **utilizador Run,** perto do topo da página, selecione a hiperligação para navegar até ao ponto final de descoberta OpenID Connect para esse fluxo do utilizador.

    ![Hiperligação URI conhecida na página run now do portal Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na página que abre no seu navegador, registem o `issuer` valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Utilize o ponto de entrega de **domínio Select** para selecionar o outro domínio e, em seguida, execute os dois passos anteriores novamente e grave o seu `issuer` valor.

Deve agora ter dois URIs gravados que são semelhantes a:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Políticas personalizadas

Se tiver políticas personalizadas em vez de fluxos de utilizador, pode utilizar um processo semelhante para obter os URIs emitentes.

1. Navegue até o seu inquilino Azure AD B2C
1. Quadro de **experiência de identidade selecionado**
1. Selecione uma das suas políticas partidárias, por exemplo, *B2C_1A_signup_signin*
1. Utilize o drop-down de **domínio Select** para selecionar um domínio, por exemplo *yourtenant.b2clogin.com*
1. Selecione a hiperligação exibida no **ponto final de descoberta openID Connect**
1. Registar o `issuer` valor
1. Execute os passos 4-6 para o outro domínio, por exemplo *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Agora que tem ambos URIs de ponto final simbólico, precisa atualizar o seu código para especificar que ambos os pontos finais são emitentes válidos. Para passar por um exemplo, faça o download ou clone da aplicação da amostra e, em seguida, atualize a amostra para suportar ambos os pontos finais como emitentes válidos.

Descarregue o arquivo: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Ativar vários emitentes na Web API

Nesta secção, atualiza o código para especificar que ambos os pontos finais do emitente são válidos.

1. Abra a solução **B2C-WebAPI-DotNet.sln** em Estúdio Visual
1. No projeto **TaskService,** abra o ficheiro *TaskService \\ App_Start \\ **Startup.Auth.cs*** no seu editor
1. Adicione a seguinte `using` diretiva ao topo do processo:

    `using System.Collections.Generic;`
1. Adicione a [`ValidIssuers`][validissuers] propriedade à [`TokenValidationParameters`][tokenvalidationparameters] definição e especifique ambos os URIs que registou na secção anterior:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"//,
            //"https://your-custom-domain/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` é fornecido por MSAL.NET e é consumido pelo middleware OWIN na próxima secção de código em *Startup.Auth.cs*. Com vários emitentes válidos especificados, o pipeline de aplicação OWIN é dado conhecimento de que ambos os pontos finais simbólicos são emitentes válidos.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Como mencionado anteriormente, outras bibliotecas OWIN normalmente fornecem uma facilidade semelhante para apoiar vários emitentes. Embora os exemplos para cada biblioteca esteja fora do âmbito deste artigo, você pode usar uma técnica semelhante para a maioria das bibliotecas.

## <a name="switch-endpoints-in-web-app"></a>Mude pontos finais na aplicação web

Com ambos os URIs agora suportados pela sua API web, precisa agora de atualizar a sua aplicação web para que recupere fichas do ponto final b2clogin.com.

Por exemplo, pode configurar a aplicação web da amostra para utilizar o novo ponto final modificando o `ida:AadInstance` valor no *ficheiro TaskWebApp \\ **Web.config** _ do* projeto _ TaskWebApp***.

Altere o `ida:AadInstance` valor na *Web.config* do TaskWebApp para que se `{your-b2c-tenant-name}.b2clogin.com` refira em vez de `login.microsoftonline.com` .

Antes:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Depois (substituir `{your-b2c-tenant}` pelo nome do seu inquilino B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando as cordas do ponto final são construídas durante a execução da aplicação web, os pontos finais baseados em b2clogin.com são utilizados quando solicita tokens.

Ao utilizar o domínio personalizado:

```xml
<!-- Custom domain -->
<add key="ida:AadInstance" value="https://custom-domain/{0}/{1}" />
```

## <a name="next-steps"></a>Passos seguintes

Este artigo apresentou um método de configurar uma API web implementando o middleware Microsoft OWIN (Katana) para aceitar tokens de vários pontos finais de emitentes. Como pode notar, existem várias outras cordas nos ficheiros *Web.Config* tanto do TaskService como do TaskWebApp que teriam de ser alterados se quiser construir e executar estes projetos contra o seu próprio inquilino. Você é bem-vindo a modificar os projetos apropriadamente se você quiser vê-los em ação, no entanto, uma passagem completa de fazê-lo está fora do âmbito deste artigo.

Para obter mais informações sobre os diferentes tipos de fichas de segurança emitidas pela Azure AD B2C, consulte [a visão geral dos tokens no Azure Ative Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
