---
title: Construa uma aplicação de desktop que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como construir uma aplicação de desktop que chama APIs web (visão geral)
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
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702152"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicação de ambiente de trabalho que chama APIs web

Aprenda tudo o que precisa para construir uma aplicação de desktop que chama APIs web.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introdução

Se ainda não o fez, crie a sua primeira aplicação seguindo o arranque rápido do ambiente de trabalho .NET, o quickstart da Plataforma Universal Windows (UWP) ou a aplicação nativa macOS:

> [!div class="nextstepaction"]
> [Início rápido: Adquirir um token e chamar a Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API de uma aplicação UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API a partir de uma aplicação nativa macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Descrição geral

Escreve uma aplicação para desktop e pretende inscrever os utilizadores na sua aplicação e ligar para apis web como o Microsoft Graph, outras APIs da Microsoft ou a sua própria API web. Tem várias possibilidades:

- Pode utilizar a aquisição interativa de fichas:

  - Se a sua aplicação de ambiente de trabalho suportar controlos gráficos, por exemplo, se for uma aplicação Windows.Form, uma aplicação WPF ou uma aplicação nativa macOS.
  - Ou, se for uma aplicação .NET Core e concordar em que a interação de autenticação com o Azure Ative Directory (Azure AD) aconteça no navegador do sistema.

- Para aplicações hospedadas pelo Windows, também é possível que as aplicações em execução em computadores unidos a um domínio Windows ou Azure AD se juntem para adquirir um símbolo silenciosamente utilizando a Autenticação Integrada do Windows.
- Finalmente, e embora não seja recomendado, pode usar um nome de utilizador e uma palavra-passe em aplicações de clientes públicos. Ainda é necessário em alguns cenários como DevOps. Usá-lo impõe restrições à sua aplicação. Por exemplo, não pode assinar num utilizador que precisa de realizar a autenticação multifactor (acesso condicional). Além disso, a sua aplicação não beneficiará de um único sign-on (SSO).

  É também contra os princípios da autenticação moderna e só é fornecido por razões antigas.

  ![Aplicação de ambiente de trabalho](media/scenarios/desktop-app.svg)

- Se escrever uma ferramenta portátil de linha de comando, provavelmente uma aplicação .NET Core que funciona no Linux ou Mac, e se aceitar que a autenticação será delegada no navegador do sistema, pode utilizar a autenticação interativa. .NET Core não fornece um [navegador web](https://aka.ms/msal-net-uses-web-browser), por isso a autenticação acontece no navegador do sistema. Caso contrário, a melhor opção neste caso é utilizar o fluxo de código do dispositivo. Este fluxo também é utilizado para aplicações sem browser, como aplicações IoT.

  ![Aplicação sem navegador](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Especificidades

As aplicações para desktop têm uma série de especificidades. Dependem principalmente da utilização da sua aplicação ou não.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aplicativo de ambiente de trabalho: Registo de aplicativos](scenario-desktop-app-registration.md)
