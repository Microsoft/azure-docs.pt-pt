---
title: Saiba mais sobre a MSAL (biblioteca de autenticação da Microsoft) | Azure
description: A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos adquiram tokens para chamar APIs da Web protegidas. Essas APIs da Web podem ser as Microsoft Graph, outras APIS da Microsoft, APIs Web de terceiros ou sua própria API Web. O MSAL dá suporte a várias arquiteturas e plataformas de aplicativos.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/4/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a53de919960902fcc89a53293f31a07baa88cf32
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268557"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Visão geral da biblioteca de autenticação da Microsoft (MSAL)
A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores adquiram [tokens](developer-glossary.md#security-token) do ponto de extremidade da plataforma Microsoft Identity para acessar APIs da Web protegidas. Essas APIs da Web podem ser as Microsoft Graph, outras APIS da Microsoft, APIs Web de terceiros ou sua própria API Web. O MSAL está disponível para .NET, JavaScript, Android e iOS, que oferece suporte a várias arquiteturas e plataformas de aplicativos diferentes.

O MSAL oferece várias maneiras de obter tokens, com uma API consistente para várias plataformas. O uso do MSAL oferece os seguintes benefícios:

* Não é necessário usar diretamente as bibliotecas OAuth ou o código em relação ao protocolo em seu aplicativo.
* Adquire tokens em nome de um usuário ou em nome de um aplicativo (quando aplicável à plataforma).
* Mantém um cache de token e atualiza os tokens para você quando eles estiverem próximos de expirar. Você não precisa lidar com a expiração do token por conta própria.
* Ajuda a especificar qual público-alvo você deseja que seu aplicativo entre (sua organização, várias organizações, trabalho e escola e contas pessoais da Microsoft, identidades sociais com Azure AD B2C, usuários em soberanas e nuvens nacionais).
* Ajuda a configurar seu aplicativo a partir de arquivos de configuração.
* Ajuda a solucionar problemas de seu aplicativo expondo exceções, registro em log e telemetria acionáveis.

## <a name="application-types-and-scenarios"></a>Tipos de aplicativos e cenários
Usando o MSAL, um token pode ser adquirido de vários tipos de aplicativos: aplicativos Web, APIs da Web, aplicativos de página única (JavaScript), aplicativos móveis e nativos, e daemons e aplicativos do lado do servidor. 

O MSAL pode ser usado em muitos cenários de aplicativos, incluindo o seguinte:

* [Aplicativos de página única (JavaScript)](scenario-spa-overview.md) 
* [Assinatura de aplicativo Web em usuários](scenario-web-app-sign-user-overview.md)
* [Assinatura de aplicativo Web em um usuário e chamada de uma API Web em nome do usuário](scenario-web-app-call-api-overview.md)
* [Protegendo uma API Web para que somente usuários autenticados possam acessá-la](scenario-protected-web-api-overview.md)
* [API Web chamando outra API Web downstream em nome do usuário conectado](scenario-web-api-call-api-overview.md)
* [Aplicativo de desktop chamando uma API da Web em nome do usuário conectado](scenario-desktop-overview.md)
* [Aplicativo móvel chamando uma API da Web em nome do usuário que está conectado interativamente](scenario-mobile-overview.md).
* [Aplicativo de daemon de área de trabalho/serviço chamando API da Web em nome de si mesmo](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Linguagens e estruturas

| Biblioteca | Plataformas e estruturas com suporte|
| --- | --- | 
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Plataforma Universal do Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Estruturas de JavaScript/TypeScript, como AngularJS, Ember. js ou Durandal. js|
| [MSAL para Android (versão prévia)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS e macOS|
| [Visualização do MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|

## <a name="differences-between-adal-and-msal"></a>Diferenças entre ADAL e MSAL

O Biblioteca de Autenticação do Active Directory (ADAL) integra-se com o ponto de extremidade do Azure AD para desenvolvedores (v 1.0), em que MSAL se integra com o ponto de extremidade da plataforma Microsoft Identity (v 2.0). O ponto de extremidade v 1.0 dá suporte a contas de trabalho, mas não a contas pessoais. O ponto de extremidade v 2.0 é a unificação das contas pessoais da Microsoft e contas corporativas em um único sistema de autenticação. Além disso, com o MSAL, você também pode obter autenticações para Azure AD B2C.

Para obter informações mais específicas, leia sobre como [migrar para o MSAL.net do Adal.net](msal-net-migration.md) e [migrando para MSAL. js do Adal. js](msal-compare-msal-js-and-adal-js.md).
