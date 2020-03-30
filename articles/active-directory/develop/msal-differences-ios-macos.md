---
title: MSAL para iOS & diferenças macOS [ Azure
titleSuffix: Microsoft identity platform
description: Descreve as diferenças de utilização da Microsoft Authentication Library (MSAL) entre iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084974"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Diferenças entre a Biblioteca de Autenticação da Microsoft para iOS e macOS

Este artigo explica as diferenças de funcionalidade entre a Microsoft Authentication Library (MSAL) para iOS e macOS.

> [!NOTE]
> No Mac, o MSAL apenas suporta aplicações macOS.

## <a name="general-differences"></a>Diferenças gerais

MSAL para macOS é um subconjunto da funcionalidade disponível para iOS.

MSAL para macOS não suporta:

- diferentes tipos `ASWebAuthenticationSession`de `SFAuthenticationSession` `SFSafariViewController`navegador, tais como, .
- a autenticação intermediada através da aplicação Microsoft Authenticator não é suportada para o macOS.

A partilha de keychain entre apps da mesma editora é mais limitada no macOS 10.14 e anteriormente. Utilize [listas](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) de controlo de acesso para especificar os caminhos para as aplicações que devem partilhar o porta-chaves. O utilizador pode ver instruções adicionais sobre o porta-chaves.

No macOS 10.15+, o comportamento da MSAL é o mesmo entre iOS e macOS. A MSAL utiliza [grupos](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) de acesso à cadeia de chaves para partilha de porta-chaves. 

### <a name="conditional-access-authentication-differences"></a>Diferenças de autenticação de acesso condicional

Para cenários de Acesso Condicional, haverá menos solicitações de utilizador quando utilizar o MSAL para iOS. Isto porque o iOS utiliza a aplicação de corretagem (Microsoft Authenticator) que nega a necessidade de solicitar ao utilizador em alguns casos.

### <a name="project-setup-differences"></a>Diferenças de configuração do projeto

**macOS**

- Quando configurar o seu projeto no macOS, certifique-se de que a sua candidatura está assinada com um certificado de desenvolvimento ou produção válido. A MSAL ainda funciona no modo não assinado, mas comportar-se-á de forma diferente no que diz respeito à persistência do cache. A aplicação só deve ser executada sem assinatura para fins de depuração. Se distribuir a aplicação sem assinatura, irá:
1. No dia 10.14 e mais cedo, o MSAL irá solicitar ao utilizador uma senha de porta-chaves sempre que reiniciar a aplicação.
2. Em 10.15+, a MSAL irá solicitar ao utilizador credenciais para cada aquisição simbólica. 

- as aplicações macOS não precisam de implementar a chamada do AppDelegate.

**iOS**

- Existem passos adicionais para configurar o seu projeto para suportar o fluxo de corretor de autenticação. Os passos são chamados no tutorial.
- Os projetos iOS precisam de registar esquemas personalizados na lista de info.plist. Isto não é necessário no macOS.
