---
title: Saiba mais sobre o MSAL Rio Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações adquiram fichas de forma a chamar APIs web seguras. Estas APIs web podem ser o Microsoft Graph, outros APIs da Microsoft, APIs web de terceiros ou a sua própria API web. A MSAL suporta múltiplas arquiteturas e plataformas de aplicações.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 8fbcacf4e3c26f117be4ab5b64b35f670435e6b6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092754"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Visão geral da Biblioteca de Autenticação da Microsoft (MSAL)
A Microsoft Authentication Library (MSAL) permite que os desenvolvedores adquiram [fichas](developer-glossary.md#security-token) a partir do ponto final da plataforma de identidade da Microsoft, de forma a autenticar os utilizadores e aceder a APIs web seguras. Pode ser usado para fornecer acesso seguro ao Microsoft Graph, outras APIs da Microsoft, APIs web de terceiros ou a sua própria API web. A MSAL suporta muitas arquiteturas e plataformas de aplicações diferentes, incluindo .NET, JavaScript, Java, Python, Android e iOS.

A MSAL dá-lhe muitas formas de obter fichas, com uma API consistente para várias plataformas. A utilização da MSAL proporciona os seguintes benefícios:

* Não é necessário utilizar diretamente as bibliotecas ou códigos OAuth contra o protocolo da sua aplicação.
* Adquire fichas em nome de um utilizador ou em nome de uma aplicação (quando aplicável à plataforma).
* Mantém uma cache simbólica e refresca fichas para si quando estão perto de expirar. Não precisas de lidar com a expiração por conta própria.
* Ajuda-o a especificar em que público pretende que a sua aplicação entre (o seu orgs, várias orgs, trabalho e contas pessoais da escola e da Microsoft, identidades sociais com Azure AD B2C, utilizadores em nuvens soberanas e nacionais).
* Ajuda a configurar a sua aplicação a partir de ficheiros de configuração.
* Ajuda-o a resolver problemas com a sua aplicação expondo exceções acccionáveis, registos e telemetria.

## <a name="application-types-and-scenarios"></a>Tipos de aplicação e cenários
Utilizando o MSAL, um token pode ser adquirido a partir de uma série de tipos de aplicações: aplicações web, APIs web, aplicações de página única (JavaScript), aplicações móveis e nativas, e daemons e aplicações do lado do servidor.

A MSAL pode ser utilizada em muitos cenários de aplicação, incluindo os seguintes:

* [Aplicações de página única (JavaScript)](scenario-spa-overview.md)
* [Web app assinando em utilizadores](scenario-web-app-sign-user-overview.md)
* [Assinatura de aplicação web em um utilizador e chamando uma API web em nome do utilizador](scenario-web-app-call-api-overview.md)
* [Proteger uma API web para que apenas utilizadores autenticados possam acessá-lo](scenario-protected-web-api-overview.md)
* [Web API chamando outra API web a jusante em nome do utilizador inscrito](scenario-web-api-call-api-overview.md)
* [Aplicação de ambiente de trabalho chamando uma API web em nome do utilizador inscrito](scenario-desktop-overview.md)
* [Aplicação móvel chamando uma API web em nome do utilizador que se inscreveu interativamente](scenario-mobile-overview.md).
* [Aplicação de ambiente de trabalho/serviço da daemon chamando a API web em nome de si mesma](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Línguas e quadros

| Biblioteca | Plataformas e quadros apoiados|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universal Windows Platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Quadros JavaScript/TypeScript tais como AngularJS, Ember.js ou Durandal.js|
| [MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS e macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Diferenças entre ADAL e MSAL

A Ative Directory Authentication Library (ADAL) integra-se com o AD AZure para programadores (v1.0), onde o MSAL se integra com a plataforma de identidade da Microsoft (v2.0). O ponto final v1.0 suporta contas de trabalho, mas não contas pessoais. O ponto final v2.0 é a unificação das contas pessoais da Microsoft e contas de trabalho num único sistema de autenticação. Além disso, com o MSAL também pode obter autenticações para Azure AD B2C.

Para obter informações mais específicas, leia sobre [migração para MSAL.NET a partir de ADAL.NET](msal-net-migration.md) e [migrar para MSAL.js a partir de ADAL.js](msal-compare-msal-js-and-adal-js.md).
