---
title: Problemas TLS/SSL (MSAL iOS/macOS) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba o que fazer sobre vários problemas utilizando certificados TLS/SSL com o MSAL. Biblioteca Objectiva-C.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369406"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Como: Resolução de problemas MSAL para problemas iOS e macOS TLS/SSL

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar ao utilizar a [Microsoft Authentication Library (MSAL) para iOS e macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de rede

**Erro -1200**: "Ocorreu um erro SSL e não é possível então uma ligação segura ao servidor."

Este erro significa que a ligação não é segura. Ocorre quando um certificado é inválido. Para mais informações, incluindo qual servidor está `NSURLErrorFailingURLErrorKey` falhando `userInfo` a verificação TLS, consulte no dicionário do objeto de erro.

Este erro é da biblioteca de networking da Apple. Uma lista completa de códigos de erro NSURL está em NSURLError.h nos sDKs macOS e iOS. Para mais detalhes sobre este erro, consulte [códigos](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)de erro do sistema de carregamento de URL .

## <a name="certificate-issues"></a>Emissões de certificados

Se o URL que fornece um certificado inválido se ligar ao servidor que pretende utilizar como parte do fluxo de autenticação, um bom começo para diagnosticar o problema é testar o URL com um serviço de validação SSL, como o Teste do [Servidor SSL](https://www.ssllabs.com/ssltest/analyze.html). Testa o servidor contra uma grande variedade de cenários e navegadores e verifica muitas vulnerabilidades conhecidas.

Por padrão, a nova funcionalidade de Segurança do Transporte de [Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple aplica políticas de segurança mais rigorosas a apps que utilizam certificados TLS/SSL. Alguns sistemas operativos e navegadores web começaram a aplicar algumas destas políticas por padrão. Por razões de segurança, recomendamos que não desative a ATS.

Os certificados que usam hashes SHA-1 têm vulnerabilidades conhecidas. A maioria dos navegadores web modernos não permitem certificados com hashes SHA-1.

## <a name="captive-portals"></a>Portais cativos

Um portal cativo apresenta uma página web a um utilizador quando acede pela primeira vez a uma rede Wi-Fi e ainda não teve acesso a essa rede. Interceta o seu tráfego de internet até que o utilizador satisfaça os requisitos do portal. Erros de rede porque o utilizador não pode ligar-se aos recursos de rede são esperados até que o utilizador se conecte através do portal.

## <a name="next-steps"></a>Passos seguintes

Conheça os [portais cativos](https://en.wikipedia.org/wiki/Captive_portal) e a nova funcionalidade de Segurança do Transporte de [Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple.
