---
title: Problemas de resolução de problemas TLS/SSL (MSAL iOS/macOS) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba o que fazer sobre vários problemas utilizando certificados TLS/SSL com o MSAL. Biblioteca objective-C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881082"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Como: Resolução de problemas MSAL para problemas iOS e macOS TLS/SSL

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar durante a utilização da [Microsoft Authentication Library (MSAL) para iOS e macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de rede

**Error -1200**: "Ocorreu um erro SSL e não é possível errar uma ligação segura ao servidor."

Este erro significa que a ligação não é segura. Ocorre quando um certificado é inválido. Para obter mais informações, incluindo qual o servidor que está a falhar na verificação de TLS, consulte `NSURLErrorFailingURLErrorKey` no dicionário do objeto de `userInfo` erro.

Este erro é da biblioteca de redes da Apple. Uma lista completa de códigos de erro NSURL está em NSURLError.h nos SDKs macOS e iOS. Para obter mais detalhes sobre este erro, consulte [códigos de erro do sistema de carregamento de URL](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Emissões de certificados

Se o URL que fornece um certificado inválido ligar-se ao servidor que pretende utilizar como parte do fluxo de autenticação, um bom começo para diagnosticar o problema é testar o URL com um serviço de validação SSL, como [o SSL Server Test](https://www.ssllabs.com/ssltest/analyze.html). Testa o servidor contra uma grande variedade de cenários e navegadores e verifica muitas vulnerabilidades conhecidas.

Por padrão, a nova funcionalidade de [Segurança de Transporte de Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple aplica políticas de segurança mais rigorosas às aplicações que utilizam certificados TLS/SSL. Alguns sistemas operativos e navegadores web começaram a aplicar algumas destas políticas por padrão. Por razões de segurança, recomendamos que não desative a ATS.

Os certificados que usam hashes SHA-1 têm vulnerabilidades conhecidas. A maioria dos navegadores web modernos não permitem certificados com hashes SHA-1.

## <a name="captive-portals"></a>Portais em cativeiro

Um portal cativo apresenta uma página web a um utilizador quando acede a uma rede Wi-Fi e ainda não teve acesso a essa rede. Interceta o tráfego de internet até que o utilizador satisfaça os requisitos do portal. Os erros de rede porque o utilizador não consegue ligar-se aos recursos de rede são esperados até que o utilizador se conecte através do portal.

## <a name="next-steps"></a>Passos seguintes

Conheça [os portais em cativeiro](https://en.wikipedia.org/wiki/Captive_portal) e a nova funcionalidade [de Segurança de Transporte de Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple.
