---
title: Registar uma aplicação web que assina nos utilizadores - Plataforma de identidade da Microsoft Rio Azure
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
ms.openlocfilehash: 29f5d2960a678204387b2bd1dfd6d4acdc4f9c3d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442519"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Aplicação web que assina nos utilizadores: Registo de aplicações

Este artigo explica as especificações de registo de aplicações para uma aplicação web que assina nos utilizadores.

Para registar a sua aplicação, pode utilizar:

- A [aplicação web começa rapidamente.](#register-an-app-by-using-the-quickstarts) Além de ser uma grande primeira experiência com a criação de uma aplicação, os quickstarts no portal Azure contêm um botão chamado **Make this change for me**. Pode utilizar este botão para definir as propriedades de que necessita, mesmo para uma aplicação existente. Você precisará adaptar os valores destas propriedades ao seu próprio caso. Em particular, o URL da API web para a sua aplicação provavelmente será diferente do padrão proposto, o que também afetará o URI de sinalização.
- O portal Azure para registar a [sua aplicação manualmente](#register-an-app-by-using-the-azure-portal).
- Ferramentas powerShell e linha de comando.

## <a name="register-an-app-by-using-the-quickstarts"></a>Registar uma aplicação utilizando os quickstarts

Pode utilizar estes links para a criação da sua aplicação web:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Registar uma aplicação utilizando o portal Azure

> [!NOTE]
> O portal a utilizar é diferente dependendo se a sua aplicação funciona na nuvem pública do Microsoft Azure ou numa nuvem nacional ou soberana. Para mais informações, consulte [as nuvens nacionais.](./authentication-national-cloud.md#app-registration-endpoints)


1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft. Em alternativa, inscreva-se no [portal Azure de eleição](./authentication-national-cloud.md#app-registration-endpoints) para a nuvem nacional.
2. Se a sua conta lhe der acesso a mais de um inquilino, selecione a sua conta no canto superior direito. Em seguida, desa um pouco para o inquilino pretendido do Azure Ative Directory (Azure AD).
3. No painel esquerdo, selecione o serviço **Azure Ative Directory** e, em seguida, selecione **registos de Aplicações**  >  **Novo registo**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver [tipos de conta suportado.)](./v2-supported-account-types.md)
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, **insira AspNetCore-WebApp**.
   1. Para **redirecionamento URI** , adicione o tipo de aplicação e o destino URI que aceitará respostas simbólicas devolvidas após a autenticação bem sucedida. Por exemplo, insira **https://localhost:44321** . Em seguida, **selecione Registar.**
   ![A screenshot mostra ao Registo uma página de aplicação onde pode selecionar Registar.s.](media/scenario-webapp/scenario-webapp-app-registration-1.png)
1. Selecione o menu **autenticação** e, em seguida, adicione as seguintes informações:
   1. Para **URL de resposta** , adicione o tipo **https://localhost:44321/signin-oidc** **Web**.
   1. Na secção **definições Avançadas,** defina **o URL de logout** para **https://localhost:44321/signout-oidc** .
   1. Em **Concessão implícita** , selecione **Tokens de ID**.
   1. Selecione **Guardar**.
  ![A screenshot mostra as opções de autenticação, onde pode es fazer as alterações descritas.](media/scenario-webapp/scenario-webapp-app-registration-2.png)
 
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   1. Escolha os tipos de conta suportados para a sua aplicação. (Ver [tipos de conta suportado.)](./v2-supported-account-types.md)
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, insira **MailApp-openidconnect-v2**.
   1. Na secção **URI de redirecionamento (opcional),** selecione **Web** na caixa de combinação e introduza o seguinte redireccionamento URI: **https://localhost:44326/** .
1. Selecione **Registar** para criar a aplicação.
1. Selecione o menu **autenticação.**
1. Na secção de concessão implícita **de definições avançadas,**  |  **Implicit grant** selecione **fichas de identificação**. Esta amostra requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja ativado para assinar no utilizador.
1. Selecione **Guardar**.

# <a name="java"></a>[Java](#tab/java)

1. Quando aparecer uma página de **inscrição,** introduza um nome de visualização para a aplicação. Por exemplo, insira **java-webapp**.
1. Selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** e, em seguida, selecione **web app / API** para **Application Type**.
1. **Selecione Registar-se** para registar a inscrição.
1. No menu esquerdo, selecione **Autenticação.** Em **Redirecionar URIs** , selecione **Web**.

1. Introduza dois URIs de redirecionamento: um para a página de entrada e outro para a página do gráfico. Para ambos, utilize o mesmo anfitrião e número de porta, seguido de **/msal4jsample/secure/aad** para a página de inscrição e **msal4jsample/graph/me** para a página de informações do utilizador.

   Por predefinição, a amostra utiliza:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Em seguida, **selecione Save**.

1. Selecione **Certificados & segredos** do menu.
1. Na secção **de segredos** do Cliente, selecione **Novo segredo do cliente,** e depois:

   1. Insira uma descrição chave.
   1. Selecione a duração da chave **Em 1 ano**.
   1. Selecione **Adicionar**.
   1. Quando o valor da chave aparecer, copie-o para mais tarde. Este valor não será exibido novamente ou será recuperado por qualquer outro meio.

# <a name="python"></a>[Python](#tab/python)

1. Quando aparecer a **página de inscrição,** insira as informações de registo do seu pedido:
   1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, insira **o python-webapp**.
   1. Alterar **tipos de conta suportadas** para contas em qualquer **diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
   1. Na secção **URI de redirecionamento (opcional),** selecione **Web** na caixa de combinação e introduza o seguinte redireccionamento URI: **http://localhost:5000/getAToken** .
1. Selecione **Registar** para criar a aplicação.
1. Na **página** geral da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde. Vai precisar dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto.
1. No menu à esquerda, selecione **Certificados & segredos**.
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