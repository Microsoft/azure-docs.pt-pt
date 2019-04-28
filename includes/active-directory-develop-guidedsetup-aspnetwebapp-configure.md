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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298160"
---
## <a name="register-your-application"></a>Registar a sua aplicação

Para registar a sua aplicação e adicionar as suas informações de registo de aplicação à sua solução, tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar a sua aplicação rapidamente ao fazer o seguinte:

1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) painel.
1. Introduza um nome para a sua aplicação e clique em **Registar**.
1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:

1. Vá para o Visual Studio e:
   1. no Solution Explorer, selecione o projeto e ver a janela de propriedades (se não vir uma janela de propriedades, pressione F4).
   1. Alterar o SSL ativado para `True`.
   1. Com o botão direito no projeto no Visual Studio, em seguida, escolha **propriedades**e o **Web** separador. Na *servidores* secção alteração a *Url de projeto* para ser o URL do SSL.
   1. Copie o URL do SSL. Irá adicionar este URL à lista de URLs de redirecionamento na lista do Portal de registo de URLs de redirecionamento no próximo passo:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
1. Selecione **novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   1. Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `ASPNET-Tutorial`.
   1. Adicionar o URL do SSL tinha copiou a partir do Visual Studio no passo 1 (por exemplo `https://localhost:44368/`) no **URL de resposta**e clique em **registar**.
1. Selecione o menu **Autenticação**, defina os **tokens de ID** em **Concessão Implícita** e, em seguida, selecione **Guardar**.
1. Adicione o seguinte na `web.config` localizado na pasta raiz na secção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Substitua `ClientId` com o ID da aplicação que acabou de registar.
1. Substitua `redirectUri` com o URL de SSL do seu projeto.
