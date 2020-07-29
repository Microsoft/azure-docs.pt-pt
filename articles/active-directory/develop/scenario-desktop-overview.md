---
title: Construa uma aplicação de desktop que chame APIs web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de desktop que chame APIs web (visão geral)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 92f0909660427e414264442523dba3ed2abe0142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83771864"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicativo de desktop que chama APIs web

Aprenda tudo o que precisa para construir uma aplicação de desktop que chame APIs web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introdução

Se ainda não o fez, crie a sua primeira aplicação seguindo o arranque rápido do desktop .NET, o quickstart da Plataforma Universal windows (UWP) ou o arranque rápido da aplicação nativa macOS:

> [!div class="nextstepaction"]
> [Início rápido: Adquirir um token e chamar a Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação nativa do macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Descrição geral

Escreve uma aplicação para desktop e pretende iniciar sísem os utilizadores na sua aplicação e ligar para apis web como o Microsoft Graph, outras APIs da Microsoft ou a sua própria API web. Tem várias possibilidades:

- Pode utilizar a aquisição de fichas interativas:

  - Se a sua aplicação para desktop suporta controlos gráficos, por exemplo, se for uma aplicação Windows.Form, uma aplicação WPF ou uma aplicação nativa do macOS.
  - Ou, se for uma aplicação .NET Core e concordar em que a interação de autenticação com o Azure Ative Directory (Azure AD) aconteça no navegador do sistema.

- Para as aplicações hospedadas no Windows, também é possível que as aplicações em execução em computadores se unam a um domínio Windows ou AD Azure se juntem para adquirir um símbolo silenciosamente utilizando a Autenticação Integrada do Windows.
- Finalmente, e embora não seja recomendado, pode usar um nome de utilizador e uma palavra-passe em aplicações de clientes públicos. Ainda é necessário em alguns cenários como DevOps. A sua utilização impõe constrangimentos à sua aplicação. Por exemplo, não pode assinar num utilizador que precise de realizar a [autenticação de vários fatores](../authentication/concept-mfa-howitworks.md) (acesso condicional). Além disso, a sua aplicação não beneficiará de uma única sso(SSO).

  É também contra os princípios da autenticação moderna e só é fornecida por razões antigas.

  ![Aplicação para desktop](media/scenarios/desktop-app.svg)

- Se escrever uma ferramenta de linha de comando portátil, provavelmente uma aplicação .NET Core que funciona no Linux ou Mac, e se aceitar que a autenticação será delegada no navegador do sistema, pode utilizar a autenticação interativa. .NET Core não fornece um [navegador web,](https://aka.ms/msal-net-uses-web-browser)por isso a autenticação acontece no navegador do sistema. Caso contrário, a melhor opção nesse caso é utilizar o fluxo de código do dispositivo. Este fluxo também é utilizado para aplicações sem navegador, como aplicações IoT.

  ![Aplicação sem navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificidades

As aplicações para desktop têm várias especificidades. Dependem principalmente da sua aplicação que utilize ou não autenticação interativa.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aplicativo de ambiente de trabalho: Registo de aplicações](scenario-desktop-app-registration.md)
