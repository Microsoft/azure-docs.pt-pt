---
title: MSAL para iOS & macOS | Azure
titleSuffix: Microsoft identity platform
description: Descreve as diferenças de uso da MSAL (biblioteca de autenticação da Microsoft) entre o iOS e o macOS.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d1a985d1dfbae9ad0dbaa252512c868dcb199eb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916848"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Diferenças entre a Biblioteca de Autenticação da Microsoft para iOS e macOS

Este artigo explica as diferenças na funcionalidade entre a MSAL (biblioteca de autenticação da Microsoft) para iOS e macOS.

> [!NOTE]
> No Mac, o MSAL só dá suporte a aplicativos macOS.

## <a name="general-differences"></a>Diferenças gerais

O MSAL para macOS é um subconjunto da funcionalidade disponível para iOS.

O MSAL para macOS não dá suporte a:

- diferentes tipos de navegador, como `ASWebAuthenticationSession`, `SFAuthenticationSession``SFSafariViewController`.
- Não há suporte para a autenticação orientada por meio do aplicativo Microsoft Authenticator no macOS.

O compartilhamento de conjunto de chaves entre aplicativos do mesmo editor é mais limitado no macOS 10,14 e anterior. Use [listas de controle de acesso](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) para especificar os caminhos para os aplicativos que devem compartilhar o conjunto de chaves. O usuário pode ver os prompts adicionais do conjunto de chaves.

No macOS 10.15 +, o comportamento do MSAL é o mesmo entre o iOS e o macOS. O MSAL usa [grupos de acesso](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) do conjunto de chaves para compartilhamento de conjunto de chaves. 

### <a name="conditional-access-authentication-differences"></a>Diferenças de autenticação de acesso condicional

Para cenários de acesso condicional, haverá menos prompts de usuário quando você usar o MSAL para iOS. Isso ocorre porque o iOS usa o aplicativo agente (Microsoft Authenticator), que nega a necessidade de solicitar o usuário em alguns casos.

### <a name="project-setup-differences"></a>Diferenças de configuração do projeto

**macOS**

- Ao configurar seu projeto no macOS, verifique se seu aplicativo está assinado com um certificado de produção ou de desenvolvimento válido. O MSAL ainda funciona no modo não assinado, mas se comportará de forma diferente com relação à persistência do cache. O aplicativo só deve ser executado sem assinatura para fins de depuração. Se você distribuir o aplicativo sem assinatura, ele irá:
1. Em 10,14 e anterior, o MSAL solicitará ao usuário uma senha de conjunto de chaves toda vez que reiniciar o aplicativo.
2. No 10.15 +, o MSAL solicitará ao usuário as credenciais para cada aquisição de token. 

- aplicativos macOS não precisam implementar a chamada AppDelegate.

**iOS**

- Há etapas adicionais para configurar seu projeto para dar suporte ao fluxo do agente de autenticação. As etapas são chamadas no tutorial.
- os projetos do iOS precisam registrar esquemas personalizados no info. plist. Isso não é necessário no macOS.
