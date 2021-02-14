---
title: Registe uma aplicação web que assina nos utilizadores | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como registar uma aplicação web que assina nos utilizadores
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 96fbe676d4b929d2c1ee2efce0d10f3b5dc7c6ab
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103197"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplicação web que assina nos utilizadores: Registo de aplicações

Este artigo explica as etapas de registo de aplicações para uma aplicação web que assina nos utilizadores.

Para registar a sua aplicação, pode utilizar:

- A [aplicação web começa rapidamente.](#register-an-app-by-using-the-quickstarts) Além de ser uma grande primeira experiência com a criação de uma aplicação, os quickstarts no portal Azure contêm um botão chamado **Make this change for me**. Pode utilizar este botão para definir as propriedades de que necessita, mesmo para uma aplicação existente. Adapte os valores destas propriedades ao seu próprio caso. Em particular, o URL da API web para a sua aplicação provavelmente será diferente do padrão proposto, o que também afetará o URI de sinalização.
- O portal Azure para registar a [sua aplicação manualmente](#register-an-app-by-using-the-azure-portal).
- Ferramentas powerShell e linha de comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registar uma aplicação utilizando os quickstarts

Pode utilizar estes links para a criação da sua aplicação web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registar uma aplicação utilizando o portal Azure

> [!NOTE]
> O portal a utilizar é diferente dependendo se a sua aplicação funciona na nuvem pública do Microsoft Azure ou numa nuvem nacional ou soberana. Para mais informações, consulte [as nuvens nacionais.](./authentication-national-cloud.md#app-registration-endpoints)


1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>. 
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Introduza um **Nome** para a sua aplicação, por `AspNetCore-WebApp` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver [tipos de conta suportado.)](./v2-supported-account-types.md)
   1. Para **redirecionamento URI**, adicione o tipo de aplicação e o destino URI que aceitará respostas simbólicas devolvidas após a autenticação bem sucedida. Por exemplo, introduza `https://localhost:44321`.
   1. Selecione **Registar**.
1. Em **Gestão**, selecione **Autenticação** e adicione as seguintes informações:
   1. Na secção **Web,** adicione `https://localhost:44321/signin-oidc` como **URI de redirecionamento**.
   1. No **URL de logout do canal** frontal, insira `https://localhost:44321/signout-oidc` .
   1. Sob **subvenção implícita e fluxos híbridos,** selecione **fichas de identificação**.
   1. Selecione **Guardar**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   1. Introduza um **Nome** para a sua aplicação, por `MailApp-openidconnect-v2` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver [tipos de conta suportado.)](./v2-supported-account-types.md)
   1. Na secção **URI de redirecionamento (opcional),** selecione **Web** na caixa de combinação e introduza um **URI de redirecionamento** de `https://localhost:44326/` .
   1. Selecione **Registar** para criar a aplicação.
1. Em **Gestão**, **selecione Autenticação**.
1. Na secção **de fluxos implícitos e híbridos,** selecione **fichas de identificação**. Esta amostra requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja ativado para assinar no utilizador.
1. Selecione **Guardar**.

# <a name="java"></a>[Java](#tab/java)

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido: 
    1. Introduza um **Nome** para a sua aplicação, por `java-webapp` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde. 
    1. Selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
    1. **Selecione Registar-se** para registar a inscrição.
1. Em **Gestão**, **selecione Autenticação**  >  **Adicione uma plataforma**.
1. Selecione **Web**.
1. Para **redirecionar uri**, introduza o mesmo anfitrião e número de porta, seguido `/msal4jsample/secure/aad` da página de entrada. 
1. Selecione **Configurar**.
1. Na secção **Web,** utilize o anfitrião e o número de porta, seguido `/msal4jsample/graph/me` de um **URI de redirecionamento** para a página de informações do utilizador.
Por predefinição, a amostra utiliza:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Selecione **Guardar**.
1. Em **Gerir**, selecione **Certificados e segredos**.
1. Na secção **de segredos** do Cliente, selecione **Novo segredo do cliente,** e depois:

   1. Insira uma descrição chave.
   1. Selecione a duração da chave **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor da chave aparecer, copie-o para mais tarde. Este valor não será exibido novamente ou será recuperado por qualquer outro meio.

# <a name="python"></a>[Python](#tab/python)

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   1. Introduza um **Nome** para a sua aplicação, por `python-webapp` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
   1. Alterar **tipos de conta suportadas** para contas em qualquer **diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
   1. Na secção **URI de redirecionamento (opcional),** selecione **Web** na caixa de combinação e introduza o seguinte redireccionamento URI: `http://localhost:5000/getAToken` .
   1. Selecione **Registar** para criar a aplicação.
1. Na **página** geral da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde. Vai precisar dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto.
1. Em **Gerir**, selecione **Certificados e segredos**.
1. Na secção **Segredos** de Cliente, selecione **Novo segredo do cliente,** e depois:
   1. Insira uma descrição chave.
   1. Selecione uma duração chave de **In 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor da chave aparecer, copie-o. Precisará dela mais tarde.
---

## <a name="register-an-app-by-using-powershell"></a>Registar uma aplicação utilizando o PowerShell

> [!NOTE]
> Atualmente, a Azure AD PowerShell cria aplicações apenas com os seguintes tipos de conta suportados:
>
> - MyOrg (contas apenas neste diretório organizacional)
> - AnyOrg (contas em qualquer diretório organizacional)
>
> Pode criar uma aplicação que assina nos utilizadores com as suas contas pessoais da Microsoft (por exemplo, Skype, Xbox ou Outlook.com). Primeiro, criar uma aplicação multitenant. Os tipos de conta suportado são contas em qualquer diretório organizacional. Em seguida, altere o [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) imóvel para **2** e o [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) imóvel para `AzureADandPersonalMicrosoftAccount` no manifesto de [aplicação](./reference-app-manifest.md) a partir do portal Azure. Para mais informações, consulte o [passo 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) no tutorial do Núcleo ASP.NET. Pode generalizar este passo para aplicações web em qualquer idioma.

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [a configuração de código da App](scenario-web-app-sign-user-app-configuration.md).
