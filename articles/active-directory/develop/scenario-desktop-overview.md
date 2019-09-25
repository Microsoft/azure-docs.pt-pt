---
title: Aplicativo de desktop que chama APIs Web (visão geral)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de área de trabalho que chama APIs da Web (visão geral)
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d56a07b46c4cd54577bcb3d81f2c45c03433fc31
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268355"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicação de ambiente de trabalho que chama APIs Web

Saiba tudo o que você precisa para criar um aplicativo de desktop que chama APIs da Web

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se você ainda não fez isso, crie seu primeiro aplicativo seguindo o guia de início rápido da área de trabalho do .NET, o início rápido do UWP ou o início rápido do aplicativo macOS nativo:

> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo da área de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar Microsoft Graph API de um aplicativo nativo do macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Descrição geral

Você escreve um aplicativo de área de trabalho e deseja conectar usuários ao seu aplicativo e chamar APIs da Web, como o Microsoft Graph, outras APIs da Microsoft ou sua própria API Web. Você tem várias possibilidades:

- Você pode usar a aquisição de token interativo:

  - Se seu aplicativo de área de trabalho oferecer suporte a controles gráficos, por exemplo, se for um aplicativo do Windows. Form, um aplicativo do WPF ou um aplicativo nativo do macOS.
  - De se trata-se de um aplicativo .NET Core e você concorda em ter a interação de autenticação com o AD do Azure ocorre no navegador do sistema

- Para aplicativos hospedados do Windows, também é possível para aplicativos em execução em computadores ingressados em um domínio do Windows ou o AAD ingressado para adquirir um token silenciosamente usando a autenticação integrada do Windows.
- Finalmente, e embora não seja recomendado, você pode usar o nome de usuário/senha em aplicativos cliente públicos. Ainda é necessário em alguns cenários (como DevOps), mas saiba que usá-lo irá impor restrições em seu aplicativo. Por exemplo, ele não pode conectar o usuário que precisa executar a autenticação multifator (acesso condicional). Além disso, seu aplicativo não se beneficiará do SSO (logon único).

  Ele também está relacionado aos princípios da autenticação moderna e é fornecido apenas por motivos herdados.

  ![Aplicativo de desktop](media/scenarios/desktop-app.svg)

- Se você estiver escrevendo uma ferramenta de linha de comando portátil, provavelmente um aplicativo .NET Core em execução no Linux ou Mac, e se aceitar que a autenticação seja delegada ao navegador do sistema, você poderá usar a autenticação interativa. (O .NET Core não fornece ainda um [navegador da Web](https://aka.ms/msal-net-uses-web-browser) e, portanto, a autenticação ocorre no navegador do sistema), caso contrário, a melhor opção nesse caso é usar o fluxo de código do dispositivo. Esse fluxo também é usado para aplicativos sem um navegador, como aplicativos de IoT

  ![Aplicativo com navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificações

Os aplicativos de área de trabalho têm várias especificidades, o que depende principalmente se seu aplicativo usa a autenticação interativa ou não.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de desktop – registro de aplicativo](scenario-desktop-app-registration.md)
