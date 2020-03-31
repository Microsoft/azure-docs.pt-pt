---
title: Saiba mais sobre o MSAL Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações adquiram fichas para ligar para APIs web seguros. Estas APIs web podem ser o Microsoft Graph, outros APIS microsoft, APIs web de terceiros ou a sua própria Web API. A MSAL suporta múltiplas arquiteturas e plataformas de aplicações.
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
ms.openlocfilehash: c20d93c70484dc7ea800898da4309af2699c718e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085730"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Visão geral da Biblioteca de Autenticação da Microsoft (MSAL)
A Microsoft Authentication Library (MSAL) permite que os desenvolvedores adquiram [fichas](developer-glossary.md#security-token) do ponto final da plataforma de identidade da Microsoft para aceder a APIs web seguras. Estas APIs web podem ser o Microsoft Graph, outros APIS microsoft, APIs web de terceiros ou a sua própria Web API. O MSAL está disponível para .NET, JavaScript, Android e iOS, que suportam muitas arquiteturas e plataformas de aplicações diferentes.

A MSAL dá-lhe muitas formas de obter fichas, com uma API consistente para várias plataformas. A utilização do MSAL proporciona os seguintes benefícios:

* Não é necessário utilizar diretamente as bibliotecas oAuth ou o código contra o protocolo na sua aplicação.
* Adquire fichas em nome de um utilizador ou em nome de uma aplicação (quando aplicável à plataforma).
* Mantém uma cache simbólica e atualiza tokens para si quando estão perto de expirar. Não precisas de lidar com a expiração do token sozinho.
* Ajuda-o a especificar em que público pretende que a sua aplicação entre (o seu org, vários orgs, trabalho e contas pessoais escolares e da Microsoft, identidades sociais com O Azure AD B2C, utilizadores em nuvens soberanas e nacionais).
* Ajuda-o a configurar a sua aplicação a partir de ficheiros de configuração.
* Ajuda-o a resolver problemas com a aplicação expondo exceções atoladas, exploração madeireira e telemetria.

## <a name="application-types-and-scenarios"></a>Tipos de aplicação e cenários
Utilizando o MSAL, um símbolo pode ser adquirido a partir de vários tipos de aplicações: aplicações web, APIs web, aplicações de página única (JavaScript), aplicações móveis e nativas, e daemons e aplicações do lado do servidor.

O MSAL pode ser utilizado em muitos cenários de aplicação, incluindo o seguinte:

* [Aplicações de página única (JavaScript)](scenario-spa-overview.md)
* [Sessão de aplicações web nos utilizadores](scenario-web-app-sign-user-overview.md)
* [Sessão de aplicação web num utilizador e chamada de API web em nome do utilizador](scenario-web-app-call-api-overview.md)
* [Proteger uma API web para que apenas os utilizadores autenticados possam aceder a ela](scenario-protected-web-api-overview.md)
* [Web API chamando outra API web a jusante em nome do utilizador inscrito](scenario-web-api-call-api-overview.md)
* [Aplicação de ambiente de trabalho chamando uma API web em nome do utilizador inscrito](scenario-desktop-overview.md)
* [Aplicação móvel chamando uma API Web em nome do utilizador que assinou interactivamente](scenario-mobile-overview.md).
* [Aplicação de daemon de desktop/serviço chamando web API em nome de si mesma](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Línguas e quadros

| Biblioteca | Plataformas e quadros suportados|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Universal Windows Platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Quadros JavaScript/TypeScript tais como AngularJS, Ember.js ou Durandal.js|
| [MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS e macOS|
| [MSAL Java (pré-visualização)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (pré-visualização)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Diferenças entre a ADAL e a MSAL

A Ative Directory Authentication Library (ADAL) integra-se com o Ponto final do Azure AD para programadores (v1.0), onde a MSAL se integra com a plataforma de identidade da Microsoft (v2.0) endpoint. O ponto final v1.0 suporta contas de trabalho, mas não contas pessoais. O ponto final v2.0 é a unificação de contas pessoais da Microsoft e contas de trabalho num único sistema de autenticação. Além disso, com mSAL também pode obter autenticações para Azure AD B2C.

Para obter informações mais específicas, leia sobre [a migração para MSAL.NET de ADAL.NET](msal-net-migration.md) e migrar para [MSAL.js da ADAL.js](msal-compare-msal-js-and-adal-js.md).
