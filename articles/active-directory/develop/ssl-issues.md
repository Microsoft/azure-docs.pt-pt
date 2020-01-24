---
title: Problemas problemas ssl (MSAL iOS/macOS)  Azure
titleSuffix: Microsoft identity platform
description: Saiba o que fazer sobre vários problemas usando certificados SSL com o MSAL. Biblioteca Objectiva-C.
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
ms.openlocfilehash: fef1e1df15fed8452066f06a351452a83f73d89b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701353"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>Como: Resolução de problemas mSAL para problemas iOS e macOS SSL

Este artigo fornece informações para ajudá-lo a resolver problemas que pode encontrar ao utilizar a [Microsoft Authentication Library (MSAL) para iOS e macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemas de rede

**Erro -1200**: "Ocorreu um erro SSL e não é possível então uma ligação segura ao servidor."

Este erro significa que a ligação não é segura. Ocorre quando um certificado é inválido. Para obter mais informações, incluindo qual o servidor que está falhando na verificação SSL, consulte `NSURLErrorFailingURLErrorKey` no dicionário `userInfo` do objeto de erro.

Este erro é da biblioteca de networking da Apple. Uma lista completa de códigos de erro NSURL está em NSURLError.h nos sDKs macOS e iOS. Para mais detalhes sobre este erro, consulte [códigos](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)de erro do sistema de carregamento de URL .

## <a name="certificate-issues"></a>Emissões de certificados

Se o URL que fornece um certificado inválido ligar ao servidor que pretende utilizar como parte do fluxo de autenticação, um bom começo para diagnosticar o problema é testar o URL com um serviço de validação SSL, como o [Qualys SSL Labs Analyzer.](https://www.ssllabs.com/ssltest/analyze.html) Testa o servidor contra uma grande variedade de cenários e navegadores e verifica muitas vulnerabilidades conhecidas.

Por padrão, a nova funcionalidade de Segurança do Transporte de [Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple aplica políticas de segurança mais rigorosas a apps que utilizam certificados SSL. Alguns sistemas operativos e navegadores web começaram a aplicar algumas destas políticas por padrão. Por razões de segurança, recomendamos que não desative a ATS.

Os certificados que usam hashes SHA-1 têm vulnerabilidades conhecidas. A maioria dos navegadores web modernos não permitem certificados com hashes SHA-1.

## <a name="captive-portals"></a>Portais cativos

Um portal cativo apresenta uma página web a um utilizador quando acede pela primeira vez a uma rede Wi-Fi e ainda não teve acesso a essa rede. Interceta o seu tráfego de internet até que o utilizador satisfaça os requisitos do portal. Erros de rede porque o utilizador não pode ligar-se aos recursos de rede são esperados até que o utilizador se conecte através do portal.

## <a name="next-steps"></a>Passos seguintes

Conheça os [portais cativos](https://en.wikipedia.org/wiki/Captive_portal) e a nova funcionalidade de Segurança do Transporte de [Aplicações (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) da Apple.
