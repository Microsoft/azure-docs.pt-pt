---
title: Saiba mais sobre a biblioteca de autenticação da Microsoft (MSAL) | Azure
description: Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores de aplicativos adquirir tokens para chamar as APIs da Web protegida. Essas APIs da Web pode ser o Microsoft Graph, outras APIS da Microsoft, as APIs da Web de terceiros ou seu próprio Web API. A MSAL suporta várias arquiteturas de aplicativos e plataformas.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7aaa0de87ffa8280a6db34c5497779a5ded41c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545888"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Descrição geral da biblioteca de autenticação da Microsoft (MSAL)
Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores adquirir [tokens](developer-glossary.md#security-token) da Microsoft o ponto final de plataforma de identidade para poder aceder ao protegido APIs da Web. Essas APIs da Web pode ser o Microsoft Graph, outras APIS da Microsoft, as APIs da Web de terceiros ou seu próprio Web API. A MSAL está disponível para .NET, JavaScript, Android e iOS, que suportam muitas plataformas e arquiteturas de aplicativos diferentes.

A MSAL dá-lhe várias formas de obter os tokens, com uma API consistente para várias plataformas. Através de MSAL fornece as seguintes vantagens:

* Não há necessidade de usar diretamente o código contra o protocolo ou bibliotecas de OAuth em seu aplicativo.
* Adquire tokens em nome de um utilizador ou em nome de um aplicativo (quando aplicável para a plataforma).
* Mantém um cache de tokens e atualiza tokens para quando estão fechar a expirar. Não precisa de lidar com expiração do token por conta própria.
* Ajuda-o a especificar o público-alvo que pretende que o seu aplicativo para iniciar sessão (sua organização, várias organizações, trabalho e instituição de ensino e contas pessoais da Microsoft, identidades das redes sociais com o Azure AD B2C, os utilizadores independentes e nuvens nacionais).
* Ajuda-o a configurar a aplicação a partir de ficheiros de configuração.
* Ajuda-o a resolver problemas da aplicação, expondo acionáveis exceções, registro em log e telemetria.

## <a name="application-types-and-scenarios"></a>Tipos de aplicativos e cenários
Através de MSAL, um token pode ser adquirido de um número de tipos de aplicações: e aplicações web, web APIs, aplicações de página única (JavaScript), aplicativos móveis e nativos e daemons do lado do servidor de aplicativos. 

A MSAL pode ser usada em muitos cenários de aplicação, incluindo o seguinte:

* [Aplicações de página única (JavaScript)](scenario-spa-overview.md) 
* [Aplicação Web a iniciar sessão de utilizadores](scenario-web-app-sign-user-overview.md)
* [Aplicação Web a iniciar sessão um utilizador e chamar uma API web em nome do utilizador](scenario-web-app-call-api-overview.md)
* [Para que somente usuários autenticados podem acessá-lo a proteger uma API web](scenario-protected-web-api-overview.md)
* [Web API chamar outra API web a jusante em nome do utilizador com sessão iniciada](scenario-web-api-call-api-overview.md)
* [Aplicativo de Desktop, chamar uma API web em nome do utilizador com sessão iniciada](scenario-desktop-overview.md)
* [Aplicações móveis, chamar uma API Web em nome de utilizador que é sessão iniciada de forma interativa](scenario-mobile-overview.md).
* [Aplicação/serviço de ambiente de trabalho daemon chamar a web API em nome próprio](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Linguagens e arquiteturas

| Biblioteca | Plataformas suportadas e estruturas|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET framework, .NET Core, Xamarin Android, Xamarin iOS, a plataforma Universal do Windows|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[Msal (pré-visualização)](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Estruturas JavaScript/TypeScript como AngularJS, ember ou Durandal.js|
| ![MSAL para Android](media/sample-v2-code/logo_Android.png) <br/>[MSAL para Android (pré-visualização)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![MSAL para iOS](media/sample-v2-code/logo_iOS.png) <br/>[MSAL. Objective-C (pré-visualização)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Diferenças entre o ADAL e MSAL
Active Directory Authentication Library (ADAL) integra-se com o Azure AD para o ponto final de programadores (v1.0), onde a MSAL integra-se com o ponto final do Microsoft identity platform (v2.0). O ponto de extremidade v1.0 suporta contas de trabalho, mas as contas pessoais não. O ponto final v2.0 é unificação de contas pessoais da Microsoft e contas de trabalho num sistema de autenticação único. Além disso, com MSAL também pode obter as autenticações para o Azure AD B2C.

Para obter informações mais específicas, leia sobre [migrando para MSAL.NET do ADAL.NET](msal-net-migration.md) e [migrar a msal do ADAL.js](msal-compare-msal-js-and-adal-js.md).

            