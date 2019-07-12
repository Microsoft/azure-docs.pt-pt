---
title: Aplicativo de Desktop que chamadas de web APIs (descrição geral) - a plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chamadas de web APIs (descrição geral)
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
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785637"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicação de ambiente de trabalho que chama APIs Web

Saiba tudo o que precisa para criar uma aplicação de ambiente de trabalho que chama a APIs web

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introdução

Se ainda não o fez, crie seu primeiro aplicativo seguindo o guia de introdução de área de trabalho .NET ou o guia de introdução do UWP:

> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Quickstart: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Descrição geral

Escrever um aplicativo de desktop e que pretende iniciar sessão dos utilizadores ao seu aplicativo e chamar APIs, como o Microsoft Graph, outras APIs da Microsoft ou o seu próprio web API web. Tem várias possibilidades:

- Pode usar a aquisição de token interativa:

  - Se o seu aplicativo para Desktops suporta controlos de gráficos, por exemplo se se trata de um aplicativo de Windows.Form ou um aplicativo do WPF.
  - De se é uma aplicação .NET Core e concorda em efetuar a interação de autenticação com o Azure AD acontecer no navegador de sistema

- Para aplicativos do Windows alojados, também é possível para aplicativos executados em computadores associados a um domínio Windows ou AAD associado ao adquirir um token silenciosamente usando autenticação integrada do Windows.
- Por fim, e apesar de não ser recomendado, pode utilizar o nome de utilizador/palavra-passe em aplicativos cliente público. Ainda é necessário em alguns cenários (como o DevOps), mas lembre-se de que a usá-lo irá impor restrições em seu aplicativo. Por exemplo, ele não é possível iniciar sessão de utilizador que precisa de realizar a autenticação multifator (acesso condicional). Seu aplicativo não beneficiar de início de sessão único (SSO).

  Ele também é contra os princípios da autenticação moderna e só é fornecido por motivos de herança.

  ![Aplicativo de Desktop](media/scenarios/desktop-app.svg)

- Se estiver escrevendo uma ferramenta de linha de comando - provavelmente uma aplicação .NET Core em execução no Linux ou Mac - portátil, e se aceita que a autenticação ser designado como o navegador de sistema, será capaz de usar a autenticação interativa. (.NET core não fornece ainda [navegador da Web](https://aka.ms/msal-net-uses-web-browser) e, portanto, a autenticação ocorre no system browser), caso contrário, nesse caso é a melhor opção utilizar o fluxo de código de dispositivo. Este fluxo também é utilizado para as aplicações sem um browser, tais como aplicações de IoT

  ![Aplicação browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Informações específicas

Aplicativos de Desktop tem um número de specificities, que depende principalmente se a sua aplicação utiliza a autenticação interativa ou não.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicativo de Desktop - registo de aplicações](scenario-desktop-app-registration.md)
