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
ms.date: 05/04/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: cce0bb9d1a9317396d197d182a424a45c8448f1b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203646"
---
## <a name="register-your-application"></a>Registar a sua aplicação

Para registar a sua aplicação e adicionar as suas informações de registo de aplicação à sua solução, tem duas opções:

### <a name="option-1-express-mode"></a>Opção 1: Modo expresso

Pode registar a sua aplicação rapidamente ao fazer o seguinte:

1. Registar a sua aplicação através da [Portal de registo de aplicação do Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Introduza um nome para o seu e-mail e a sua aplicação.
3. Certifique-se de que a opção para a configuração interativa está marcada.
4. Siga as instruções para adicionar um URL de redirecionamento para seu aplicativo.

### <a name="option-2-advanced-mode"></a>Opção 2: Modo avançado

Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução, faça o seguinte:

1. Aceda ao [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app) para registar uma aplicação.
2. Introduza um nome para o seu e-mail e a sua aplicação.
3. Certifique-se que a opção para a configuração interativa está desmarcada
4. Selecione `Add Platform`e, em seguida, selecione `Web`.
5. Vá para o Visual Studio e, no Explorador de soluções, selecione o projeto e observar a janela de propriedades (se não vir uma janela de propriedades, pressione F4)
6. Alterar o SSL ativado para `True`.
7. Com o botão direito no projeto no Visual Studio, em seguida, escolha **propriedades**e o **Web** separador. Na *servidores* secção alteração a *Url de projeto* para ser o URL do SSL.
8. Copie o URL de SSL e adicionar este URL à lista de URLs de redirecionamento na lista do Portal de registo de URLs de redirecionamento:<br/><br/>![Propriedades do projeto](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Adicione o seguinte na `web.config` localizado na pasta raiz na secção `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Substitua `ClientId` com o ID da aplicação que acabou de registar.
11. Substitua `redirectUri` com o URL de SSL do seu projeto.
