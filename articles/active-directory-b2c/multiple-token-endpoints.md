---
title: Migrar apis web baseados em OWIN para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como permitir que uma API web .NET suporte tokens emitidos por vários emitentes de token enquanto migra as suas aplicações para b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184099"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrar uma API web baseada em OWIN para b2clogin.com

Este artigo descreve uma técnica para permitir o suporte a múltiplos emitentes simbólicos em APIs web que implementam a [Interface Web Aberta para .NET (OWIN)](http://owin.org/). Apoiar vários pontos finais simbólicos é útil quando está a migrar o Azure Ative Directory B2C (Azure AD B2C) APIs e as suas aplicações de *login.microsoftonline.com* a *b2clogin.com*.

Adicionando suporte na sua API para aceitar fichas emitidas tanto por b2clogin.com como por login.microsoftonline.com, pode migrar as suas aplicações web de forma faseada antes de remover o suporte para fichas emitidas login.microsoftonline.com da API.

As seguintes secções apresentam um exemplo de como ativar vários emitentes numa API web que utiliza os componentes do middleware [Microsoft OWIN][katana] (Katana). Embora os exemplos de código sejam específicos do middleware Microsoft OWIN, a técnica geral deve ser aplicável a outras bibliotecas OWIN.

> [!NOTE]
> Este artigo destina-se a clientes Azure AD B2C com APIs e aplicações atualmente implantadas que referenciam `login.microsoftonline.com` e que queiram migrar para o ponto final recomendado. `b2clogin.com` Se estiver a configurar uma nova aplicação, utilize [b2clogin.com](b2clogin.md) conforme direcionado.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com os passos deste artigo:

* [Fluxos de utilizador](tutorial-create-user-flows.md) ou [políticas personalizadas](custom-policy-get-started.md) criadas no seu inquilino

## <a name="get-token-issuer-endpoints"></a>Obtenha pontos finais de emitente simbólico

Primeiro, é necessário obter os URIs finais do emitente simbólico para cada emitente que deseja apoiar na sua API. Para obter os *b2clogin.com* e *login.microsoftonline.com* pontos finais suportados pelo seu inquilino Azure AD B2C, utilize o seguinte procedimento no portal Azure.

Comece por selecionar um dos fluxos de utilizador existentes:

1. Navegue para o seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com)
1. Em **Termos de Políticas**, selecione **fluxos de utilizador (políticas)**
1. Selecione uma política existente, por *exemplo, B2C_1_signupsignin1,* em seguida, selecione **executar o fluxo de utilizador**
1. Sob o fluxo do **utilizador Run** que se aproxime do topo da página, selecione a hiperligação para navegar para o ponto final de descoberta OpenID Connect para esse fluxo de utilizador.

    ![Hiperligação URI bem conhecida na página Run agora do portal Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na página que abre no seu `issuer` navegador, registe o valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Utilize a queda de **domínio Select** para selecionar o outro domínio e, em seguida, execute os dois passos anteriores mais uma vez e grave o seu `issuer` valor.

Deve agora ter dois URIs gravados que são semelhantes a:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Políticas personalizadas

Se tiver políticas personalizadas em vez de fluxos de utilizador, pode utilizar um processo semelhante para obter os URIs emitentes.

1. Navegue para o seu inquilino Azure AD B2C
1. Selecione **Quadro de Experiência de Identidade**
1. Selecione uma das suas políticas partidárias, por exemplo, *B2C_1A_signup_signin*
1. Utilize a gota de **domínio Select** para selecionar um domínio, por *exemplo, yourtenant.b2clogin.com*
1. Selecione a hiperligação exibida sob o ponto final da **descoberta OpenID Connect**
1. Grave `issuer` o valor
1. Execute os passos 4-6 para o outro domínio, por *exemplo, login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Agora que tem ambos os URIs de ponto final simbólico, precisa de atualizar o seu código para especificar que ambos os pontos finais são emitentes válidos. Para passar por um exemplo, faça o download ou clone a aplicação da amostra e, em seguida, atualize a amostra para suportar ambos os pontos finais como emitentes válidos.

Descarregue o arquivo: [active-directy-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Ativar vários emitentes na Web API

Nesta secção, atualiza o código para especificar que ambos os pontos finais do emitente simbólico são válidos.

1. Abra a solução **B2C-WebAPI-DotNet.sln** no Estúdio Visual
1. No projeto **TaskService,** abra o ficheiro *\\TaskService App_Start\\**Startup.Auth.cs*** no seu editor
1. Adicione a `using` seguinte diretiva ao topo do ficheiro:

    `using System.Collections.Generic;`
1. Adicione [`ValidIssuers`][validissuers] a propriedade [`TokenValidationParameters`][tokenvalidationparameters] à definição e especifique ambas as URIs que registou na secção anterior:

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

`TokenValidationParameters`é fornecido por MSAL.NET e é consumido pelo middleware OWIN na próxima secção de código em *Startup.Auth.cs*. Com vários emitentes válidos especificados, o pipeline de aplicação OWIN é informado de que ambos os pontos finais simbólicos são emitentes válidos.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Como mencionado anteriormente, outras bibliotecas OWIN normalmente fornecem uma facilidade semelhante para apoiar vários emitentes. Embora fornecer exemplos para cada biblioteca esteja fora do âmbito deste artigo, você pode usar uma técnica semelhante para a maioria das bibliotecas.

## <a name="switch-endpoints-in-web-app"></a>Switch pontos finais na aplicação web

Com ambas as URIs agora suportadas pela sua Web API, agora precisa de atualizar a sua aplicação web para que recupere tokens do b2clogin.com ponto final.

Por exemplo, pode configurar a aplicação web da amostra `ida:AadInstance` para utilizar o novo ponto final modificando o valor no ficheiro *TaskWebApp\\**Web.config*** do projeto **TaskWebApp.**

Altere `ida:AadInstance` o valor no *Web.config* do TaskWebApp `{your-b2c-tenant-name}.b2clogin.com` de `login.microsoftonline.com`modo a que se refira em vez de .

Antes:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Depois (substitua-o `{your-b2c-tenant}` pelo nome do seu inquilino B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando as cordas de ponto final são construídas durante a execução da aplicação web, os pontos finais baseados na b2clogin.com são usados quando solicitatos.

## <a name="next-steps"></a>Passos seguintes

Este artigo apresentou um método de configurar um API web implementando o middleware Microsoft OWIN (Katana) para aceitar tokens de vários pontos finais de emitente. Como pode notar, existem várias outras cordas nos ficheiros *Web.Config* dos projetos TaskService e TaskWebApp que teriam de ser alterados se quiser construir e executar estes projetos contra o seu próprio inquilino. Você é bem-vindo a modificar os projetos apropriadamente se você quiser vê-los em ação, no entanto, um walk-through completo de fazê-lo está fora do âmbito deste artigo.

Para obter mais informações sobre os diferentes tipos de fichas de segurança emitidas pelo Azure AD B2C, consulte a [visão geral das fichas no Diretório Ativo Azure B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
