---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498408"
---
## <a name="register-your-application"></a>Registar a sua aplicação

Pode registrar seu aplicativo em qualquer uma das duas formas.

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar a sua aplicação rapidamente ao fazer o seguinte:
1. Vá para o [portal do Azure – registo de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Introduza um nome para a sua aplicação e xelecione **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
1. Selecione **novo registo**.
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `Win-App-calling-MsGraph`.
   - Na secção **Tipos de conta suportados**, selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais (por exemplo, Skype, Xbox, Outlook.com)**.
   - Selecione **Registar** para criar a aplicação.
1. Na lista de páginas da aplicação, selecione **Autenticação**.
1. Na secção **URIs de Redirecionamento**, localize a secção **URIs de Redirecionamento Sugeridos para clientes públicos (móvel, ambiente de trabalho)** e selecione **"urn:ietf:wg:oauth:2.0:oob**.
1. Selecione **Guardar**.
1. Vá para o Visual Studio, abra a *App.xaml.cs* do ficheiro e, em seguida, substitua `Enter_the_Application_Id_here` com o ID da aplicação que acabou de registar e copiados.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
