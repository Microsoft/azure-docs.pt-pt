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
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076949"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Cenário: Aplicativo de Desktop que chamadas de APIs web

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

- Se o seu aplicativo para Desktops suporta controlos de gráficos, por exemplo se for um aplicativo de Windows.Form ou um aplicativo do WPF, pode usar a aquisição de token interativa.
- Para aplicativos do Windows alojados, também é possível para aplicativos executados em computadores associados a um domínio Windows ou AAD associado ao adquirir um token silenciosamente usando autenticação integrada do Windows.
- Por fim, e apesar de não ser recomendado, pode utilizar o nome de utilizador/palavra-passe em aplicativos cliente público. Ainda é necessário em alguns cenários (como o DevOps), mas lembre-se de que a usá-lo irá impor restrições em seu aplicativo. Por exemplo, ele não é possível iniciar sessão de utilizador que precisa de realizar a autenticação multifator (acesso condicional). Seu aplicativo não beneficiar de início de sessão único (SSO).

  Ele também é contra os princípios da autenticação moderna e só é fornecido por motivos de herança.

  ![Aplicativo de Desktop](media/scenarios/desktop-app.svg)

- Se estiver escrevendo uma ferramenta de linha de comando portátil - provavelmente uma aplicação .NET Core em execução no Linux ou Mac, não vai conseguir utilizar nenhum da autenticação interativa (como o .NET Core não fornece um [navegador da Web](https://aka.ms/msal-net-uses-web-browser)), nem integrado Autenticação do Windows. Nesse caso é a melhor opção utilizar o fluxo de código de dispositivo. Este fluxo também é utilizado para as aplicações sem um browser, tais como aplicações de iOT

  ![Aplicação browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Informações específicas

Aplicativos de Desktop tem um número de specificities, que depende principalmente se a sua aplicação utiliza a autenticação interativa ou não.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aplicativo de Desktop - registo de aplicações](scenario-desktop-app-registration.md)
