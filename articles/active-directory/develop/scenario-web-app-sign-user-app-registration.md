---
title: Aplicação Web que inicia sessão dos utilizadores (registo da aplicação) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação web que inicia sessão dos utilizadores (registo da aplicação)
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
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150233"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Aplicação Web que inicia sessão dos utilizadores - registo de aplicações

Esta página explica as especificações de registo de aplicação para uma aplicação web que os utilizadores inicia sessão.

Para registar a sua aplicação, pode utilizar:

- O [web inícios rápidos de aplicação](#register-an-app-using-the-quickstarts) -além de ser uma excelente experiência de primeira com a criação de um aplicativo, guias de introdução no portal do Azure contém um botão chamado **efetuar esta alteração para mim**. Pode utilizar este botão para definir as propriedades que precisa, mesmo para uma aplicação existente. Terá de se adaptar os valores dessas propriedades para o seu caso. Em particular, o URL da API web para a sua aplicação é provavelmente será diferente da predefinição proposta, que também irá afetar a fim de sessão URI.
- Portal do Azure para [registar a sua aplicação manualmente](#register-an-app-using-azure-portal)
- PowerShell e ferramentas de linha de comandos

## <a name="register-an-app-using-the-quickstarts"></a>Registar uma aplicação com os inícios rápidos

Se navegar para esta ligação, pode criar bootstrap a criação da sua aplicação web:

- [Núcleo do ASP.NET](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Registar uma aplicação com o portal do Azure

> [!NOTE]
> o portal para utilizar é diferente, dependendo se a sua aplicação é executada na cloud pública do Microsoft Azure ou numa cloud nacional ou soberana. Para obter mais informações, consulte [Clouds nacionais](./authentication-national-cloud.md#app-registration-endpoints)

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft. Em alternativa, inicie sessão para a cloud nacional portal do Azure à escolha.
1. Se sua dá conta de acesso a mais do que um inquilino, selecione a sua conta no canto superior direito e definir a sua sessão do portal para o Azure AD pretendido de inquilino.
1. No painel de navegação do lado esquerdo, selecione o **do Azure Active Directory** serviço e, em seguida, selecione **registos das aplicações** > **novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Escolha os tipos de conta suportado para a sua aplicação (veja [tipos de conta suportado](./v2-supported-account-types.md))
   1. Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AspNetCore-WebApp`.
   1. Na **URI de redirecionamento**, adicione o tipo de aplicação e o destino de URI que aceitará devolvido respostas token após a autenticação bem-sucedida. Por exemplo, `https://localhost:44321/`.  Selecione **Registar**.
1. Selecione o **autenticação** menu e, em seguida, adicione as seguintes informações:
   1. Na **URL de resposta**, adicione `https://localhost:44321/signin-oidc`.
   1. Na **definições avançadas** secção, defina **URL de fim de sessão** para `https://localhost:44321/signout-oidc`.
   1. Sob **concessão implícita**, verifique **tokens de ID**.
   1. Selecione **Guardar**.

### <a name="register-an-app-using-powershell"></a>Registar uma aplicação com o PowerShell

> [!NOTE]
> Atualmente o Azure AD PowerShell cria apenas a aplicações com os seguintes tipos de conta suportado:
>
> - MyOrg (contas neste diretório organizacional apenas)
> - AnyOrg (contas em qualquer diretório organizacional).
>
> Se quiser criar uma aplicação que os utilizadores inicia sessão com as suas Accounts Microsoft pessoais (por exemplo, o Skype, XBox, Outlook.com), primeiro pode criar uma aplicação multi-inquilino (tipos de conta suportados = contas em qualquer diretório organizacional) e, em seguida, altere o `signInAudience` propriedade no manifesto da aplicação do portal do Azure. Isso é explicado em detalhes no passo [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) do tutorial de ASP.NET Core (e pode ser generalizada para aplicações web em qualquer linguagem).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Configuração de código da aplicação](scenario-web-app-sign-user-app-configuration.md)
