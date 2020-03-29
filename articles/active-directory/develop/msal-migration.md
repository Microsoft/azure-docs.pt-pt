---
title: Migrar para a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a Microsoft Authentication Library (MSAL) e a Azure AD Authentication Library (ADAL) e como migrar para o MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164935"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrar aplicações para a Microsoft Authentication Library (MSAL)

Tanto a Microsoft Authentication Library (MSAL) como a Azure AD Authentication Library (ADAL) são usadas para autenticar entidades da AD Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades azure AD (contas de trabalho e escola) solicitando fichas usando a Biblioteca de Autenticação AD Azure (ADAL). Utilizando a MSAL:

- Pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades AD Azure e contas microsoft, e contas sociais e locais através do Azure AD B2C) uma vez que utiliza o ponto final da plataforma de identidade microsoft.
- Os seus utilizadores terão a melhor experiência de inscrição individual.
- A sua aplicação pode permitir o consentimento incremental e apoiar o Acesso Condicional é mais fácil.
- Beneficia-se da inovação.

**MSAL é agora a biblioteca auth recomendada para usar com a plataforma de identidade Microsoft**. Não serão implementadas novas funcionalidades no ADAL. Os esforços centram-se na melhoria do MSAL.

Os seguintes artigos descrevem as diferenças entre as bibliotecas MSAL e ADAL e ajudam-no a migrar para a MSAL:
- [Migrar para MSAL.NET](msal-net-migration.md)
- [Migrar para MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrar para MSAL.Android](migrate-android-adal-msal.md)
- [Migrar para MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Migrar para MSAL Python](migrate-python-adal-msal.md)
- [Migrar para MSAL para Java](migrate-adal-msal-java.md)
- [Migrar aplicações Xamarin com mediadores para o MSAL.NET](msal-net-migration-ios-broker.md)
