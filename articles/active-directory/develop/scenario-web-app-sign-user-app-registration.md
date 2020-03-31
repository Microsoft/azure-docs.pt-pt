---
title: Registe uma aplicação web que assina nos utilizadores - plataforma de identidade da Microsoft Azure
description: Saiba como registar uma aplicação web que assina nos utilizadores
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f2513b1fee022199f040ba0dcf83110c0b3b1365
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701574"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplicação web que assina nos utilizadores: Registo de aplicativos

Este artigo explica as especificidades do registo da aplicação para uma aplicação web que assina nos utilizadores.

Para registar a sua candidatura, pode utilizar:

- A [aplicação web começa rapidamente.](#register-an-app-by-using-the-quickstarts) Além de ser uma excelente primeira experiência na criação de uma aplicação, os quickstarts no portal Azure contêm um botão chamado **Faça esta mudança para mim.** Pode utilizar este botão para definir as propriedades de que necessita, mesmo para uma aplicação existente. Terá de adaptar os valores destas propriedades ao seu próprio caso. Em particular, o URL API web para a sua aplicação provavelmente será diferente do padrão proposto, que também afetará o URI de inscrição.
- O portal Azure para [registar a sua aplicação manualmente](#register-an-app-by-using-the-azure-portal).
- PowerShell e ferramentas de linha de comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registe uma aplicação utilizando os quickstarts

Pode utilizar estes links para a criação da sua aplicação web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registe uma aplicação utilizando o portal Azure

> [!NOTE]
> O portal a utilizar é diferente dependendo se a sua aplicação funciona na nuvem pública do Microsoft Azure ou numa nuvem nacional ou soberana. Para mais informações, consulte [as nuvens nacionais.](./authentication-national-cloud.md#app-registration-endpoints)


1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft. Em alternativa, inscreva-se no portal azure de eleição para a nuvem nacional.
1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Em seguida, detete a sua sessão do portal para o desejado inquilino azure ative directory (Azure AD).
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure** e, em seguida, selecione registos de **aplicações** > **Nova inscrição**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver tipos de [conta suportada](./v2-supported-account-types.md).)
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, introduza **AspNetCore-WebApp**.
   1. Para **Redirecionar URI,** adicione o tipo de aplicação e o destino URI que aceitará respostas simbólicas devolvidas após autenticação bem sucedida. Por exemplo, **https://localhost:44321**introduza . Em seguida, selecione **Register**.
1. Selecione o menu **de Autenticação** e, em seguida, adicione as seguintes informações:
   1. Para url de **https://localhost:44321/signin-oidc** **resposta,** adicione do tipo **Web**.
   1. Na secção **de definições avançadas,** coloque **o URL de logout** para **https://localhost:44321/signout-oidc**.
   1. Em **Concessão implícita**, selecione **Tokens de ID**.
   1. Selecione **Guardar**.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura:
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver tipos de [conta suportada](./v2-supported-account-types.md).)
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, introduza **mailApp-openidconnect-v2**.
   1. Na secção **Redirecionamento URI (opcional),** selecione **Web** na caixa **https://localhost:44326/** de combinação e introduza o seguinte redirecionamento URI: .
1. Selecione **Registar** para criar a aplicação.
1. Selecione o menu **autenticação.**
1. Nas **definições avançadas** | Secção**de subvenção implícita,** selecione **fichas de ID**. Esta amostra requer que o fluxo implícito de [subvenção](v2-oauth2-implicit-grant-flow.md) seja ativado para assinar no utilizador.
1. Selecione **Guardar**.

# <a name="java"></a>[Java](#tab/java)

1. Quando aparecer uma página de **inscrição,** introduza um nome de exibição para a aplicação. Por exemplo, **introduza java-webapp**.
1. Selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** e, em seguida, selecione **Web app / API** para Tipo de **Aplicação**.
1. Selecione **Registar** para registar o pedido.
1. No menu esquerdo, **selecione Autenticação**. Em **Redirecionamento de URIs,** selecione **Web**.

1. Introduza duas URIs redirecionais: uma para a página de entrada e outra para a página do gráfico. Para ambos, utilize o mesmo número de hospedeiro e porta, seguido de **/msal4jsample/secure/aad** para a página de inscrição e **msal4jsample/graph/me** para a página de informação do utilizador.

   Por predefinição, a amostra utiliza:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Em seguida, selecione **Guardar**.

1. Selecione **Certificados & segredos** do menu.
1. Na secção **de segredos** do Cliente, selecione **novo segredo do cliente,** e depois:

   1. Introduza uma descrição chave.
   1. Selecione a duração da chave **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor-chave aparecer, copie-o para mais tarde. Este valor não será mostrado novamente ou será recuperado por qualquer outro meio.

# <a name="python"></a>[Pitão](#tab/python)

1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura:
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, **introduza python-webapp**.
   1. Alterar os tipos de **conta suportados** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
   1. Na secção **Redirecionamento URI (opcional),** selecione **Web** na caixa **http://localhost:5000/getAToken**de combinação e introduza o seguinte redirecionamento URI: .
1. Selecione **Registar** para criar a aplicação.
1. Na página **de Visão Geral** da aplicação, encontre o valor de ID da **Aplicação (cliente)** e grave-o para mais tarde. Vai precisar dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto.
1. No menu esquerdo, selecione **Certificados & segredos.**
1. Na secção **Segredos** de Cliente, selecione **novo segredo de cliente,** e depois:

   1. Introduza uma descrição chave.
   1. Selecione uma duração chave de **1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor-chave aparecer, copie-o. Precisará dela mais tarde.
---

## <a name="register-an-app-by-using-powershell"></a>Registe uma aplicação utilizando o PowerShell

> [!NOTE]
> Atualmente, a Azure AD PowerShell cria aplicações com apenas os seguintes tipos de conta suportados:
>
> - MyOrg (contas apenas neste diretório organizacional)
> - AnyOrg (contas em qualquer diretório organizacional)
>
> Pode criar uma aplicação que instique nos utilizadores com as suas contas pessoais da Microsoft (por exemplo, Skype, Xbox ou Outlook.com). Primeiro, crie uma aplicação multiarrendatária. Os tipos de conta suportados são contas em qualquer diretório organizacional. Em seguida, `signInAudience` altere o imóvel no manifesto de aplicação do portal Azure. Para mais informações, consulte o [passo 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) no tutorial ASP.NET Core. Pode generalizar este passo para aplicações web em qualquer idioma.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-web-app-sign-user-app-configuration.md)
