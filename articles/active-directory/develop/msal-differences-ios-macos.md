---
title: MSAL para iOS & diferenças de macOS | Rio Azure
titleSuffix: Microsoft identity platform
description: Descreve as diferenças de utilização da Microsoft Authentication Library (MSAL) entre iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064920"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Diferenças entre a Biblioteca de Autenticação da Microsoft para iOS e macOS

Este artigo explica as diferenças de funcionalidade entre a Microsoft Authentication Library (MSAL) para iOS e macOS.

> [!NOTE]
> No Mac, o MSAL suporta apenas aplicações macOS.

## <a name="general-differences"></a>Diferenças gerais

MSAL para macOS é um subconjunto da funcionalidade disponível para iOS.

MSAL para macOS não suporta:

- diferentes tipos de navegador, tais `ASWebAuthenticationSession` `SFAuthenticationSession` como, . `SFSafariViewController` . .
- a autenticação mediada através da aplicação Microsoft Authenticator não é suportada para o macOS.

A partilha de keychain entre apps da mesma editora é mais limitada no macOS 10.14 e mais cedo. Utilize [listas de controlo de acesso](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) para especificar os caminhos das aplicações que devem partilhar o chaveiro. O utilizador pode ver pedidos adicionais de chave.

No macOS 10.15+, o comportamento da MSAL é o mesmo entre iOS e macOS. A MSAL utiliza [grupos de acesso a chaveiros](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) para a partilha do chaveiro. 

### <a name="conditional-access-authentication-differences"></a>Diferenças de autenticação de acesso condicional

Para cenários de Acesso Condicional, haverá menos solicitações de utilizador quando utilizar o MSAL para iOS. Isto porque o iOS utiliza a aplicação de corretagem (Microsoft Authenticator) que nega a necessidade de incitar o utilizador em alguns casos.

### <a name="project-setup-differences"></a>Diferenças de configuração do projeto

**macOS**

- Quando configurar o seu projeto no macOS, certifique-se de que a sua aplicação é assinada com um certificado de desenvolvimento ou produção válido. A MSAL ainda funciona no modo não assinado, mas comportar-se-á de forma diferente no que diz respeito à persistência da cache. A aplicação só deve ser executada sem assinatura para fins de depuragem. Se distribuir a aplicação não assinada, será:
1. No dia 10.14 e mais cedo, o MSAL irá solicitar ao utilizador uma palavra-passe de chave sempre que reiniciar a aplicação.
2. Em 10.15+, a MSAL solicitará ao utilizador credenciais para cada aquisição simbólica. 

- as aplicações para macOS não precisam de implementar a chamada do AppDeegate.

**iOS**

- Existem passos adicionais para configurar o seu projeto para suportar o fluxo de corretor de autenticação. Os degraus são chamados no tutorial.
- Os projetos iOS precisam de registar esquemas personalizados na info.plist. Isto não é necessário no macOS.
