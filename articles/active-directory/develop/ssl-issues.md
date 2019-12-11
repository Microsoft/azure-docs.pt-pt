---
title: Solucionar problemas de SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba o que fazer sobre vários problemas usando certificados SSL com o MSAL. Biblioteca Objective-C.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cfb530deab0cce12247c2ec87e3efb3ce61810b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964723"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>Como: solucionar problemas de SSL do MSAL para iOS e macOS

Este artigo fornece informações para ajudá-lo a solucionar problemas que podem surgir durante o uso da [MSAL (biblioteca de autenticação da Microsoft) para IOS e MacOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de rede

**Erro-1200**: "ocorreu um erro de SSL e uma conexão segura com o servidor não pode ser feita".

Esse erro significa que a conexão não é segura. Ocorre quando um certificado é inválido. Para obter mais informações, incluindo qual servidor está falhando na verificação de SSL, consulte `NSURLErrorFailingURLErrorKey` no dicionário de `userInfo` do objeto de erro.

Esse erro é da biblioteca de rede da Apple. Uma lista completa de códigos de erro NSURL está em NSURLError. h nos SDKs macOS e iOS. Para obter mais detalhes sobre esse erro, consulte [códigos de erro do sistema de carregamento de URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problemas de certificado

Se a URL que fornece um certificado inválido se conectar ao servidor que você pretende usar como parte do fluxo de autenticação, um bom começo para diagnosticar o problema é testar a URL com um serviço de validação SSL, como o [Qualys SSL Labs Analyzer](https://www.ssllabs.com/ssltest/analyze.html). Ele testa o servidor em uma ampla gama de cenários e navegadores e verifica se há muitas vulnerabilidades conhecidas.

Por padrão, o novo recurso de [ATS (segurança de transporte de aplicativo)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple aplica políticas de segurança mais rigorosas a aplicativos que usam certificados SSL. Alguns sistemas operacionais e navegadores da Web começaram a impor algumas dessas políticas por padrão. Por motivos de segurança, recomendamos que você não desabilite o ATS.

Os certificados que usam hashes SHA-1 têm vulnerabilidades conhecidas. Os navegadores da Web mais modernos não permitem certificados com hashes SHA-1.

## <a name="captive-portals"></a>Portais prisioneiros

Um portal cativo apresenta uma página da Web para um usuário ao acessar pela primeira vez uma rede Wi-Fi e ainda não recebeu acesso a essa rede. Ele intercepta seu tráfego de Internet até que o usuário atenda aos requisitos do Portal. Erros de rede porque o usuário não pode se conectar aos recursos de rede é esperado até que o usuário se conecte por meio do Portal.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [portais prisioneiros](https://en.wikipedia.org/wiki/Captive_portal) e o novo recurso de [ATS (segurança de transporte de aplicativo)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple.
