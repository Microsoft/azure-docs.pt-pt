---
title: Aplicativo Web que conecta usuários (registro de aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que entra em usuários (registro de aplicativo)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086707"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplicativo Web que entra em usuários – registro de aplicativo

Esta página explica as especificações de registro do aplicativo para um aplicativo Web que faz logon de usuários.

Para registrar seu aplicativo, você pode usar:

- Os [guias de início rápido do aplicativo Web](#register-an-app-using-the-quickstarts) -além de ser uma ótima experiência com a criação de um aplicativo, os guias de início rápido no portal do Azure contêm um botão chamado **fazer essa alteração para mim**. Você pode usar esse botão para definir as propriedades necessárias, mesmo para um aplicativo existente. Você precisará adaptar os valores dessas propriedades ao seu próprio caso. Em particular, a URL da API Web para seu aplicativo provavelmente será diferente do padrão proposto, o que também afetará o URI de saída.
- O portal do Azure para [registrar seu aplicativo manualmente](#register-an-app-using-azure-portal)
- PowerShell e ferramentas de linha de comando

## <a name="register-an-app-using-the-quickstarts"></a>Registrar um aplicativo usando os guias de início rápido

Se você navegar até esse link, poderá criar a inicialização da criação de seu aplicativo Web:

- [Núcleo do ASP.NET](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registrar um aplicativo usando portal do Azure

> [!NOTE]
> o portal a ser usado é diferente dependendo de se seu aplicativo é executado na nuvem pública Microsoft Azure ou em uma nuvem nacional ou soberanas. Para obter mais informações, consulte [nuvens nacionais](./authentication-national-cloud.md#app-registration-endpoints)

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft. Como alternativa, entre na nuvem nacional portal do Azure de escolha.
1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, selecione **registros de aplicativo** > **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. escolha os tipos de conta com suporte para seu aplicativo (consulte [tipos de conta com suporte](./v2-supported-account-types.md))
   1. Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AspNetCore-WebApp`.
   1. Em **URI de redirecionamento**, adicione o tipo de aplicativo e o destino do URI que aceitará respostas de token retornadas após a autenticação bem-sucedida. Por exemplo, `https://localhost:44321/`.  Selecione **Registar**.
1. Selecione o menu **autenticação** e, em seguida, adicione as seguintes informações:
   1. Em **URL de resposta**, `https://localhost:44321/signin-oidc`adicione.
   1. Na seção **Configurações avançadas** , defina a **URL** de logout `https://localhost:44321/signout-oidc`para.
   1. Em **concessão implícita**, verifique os **tokens de ID**.
   1. Selecione **Guardar**.

### <a name="register-an-app-using-powershell"></a>Registrar um aplicativo usando o PowerShell

> [!NOTE]
> Atualmente, o PowerShell do Azure AD cria apenas aplicativos com os seguintes tipos de conta com suporte:
>
> - MyOrg (contas neste diretório organizacional somente)
> - AnyOrg (contas em qualquer diretório organizacional).
>
> Se você quiser criar um aplicativo que faz logon de usuários com suas contas pessoais da Microsoft (por exemplo, Skype, XBox, Outlook.com), você pode primeiro criar um aplicativo multilocatário (tipos de conta com suporte = contas em qualquer diretório organizacional) e, em seguida, alterar a `signInAudience` Propriedade no manifesto do aplicativo do portal do Azure. Isso é explicado em detalhes na etapa [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) do tutorial de ASP.NET Core (e pode ser generalizado para aplicativos Web em qualquer idioma).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código do aplicativo](scenario-web-app-sign-user-app-configuration.md)
