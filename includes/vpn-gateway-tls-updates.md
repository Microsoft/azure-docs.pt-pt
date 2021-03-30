---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "67184083"
---
>[!NOTE]
>A partir de 1 de julho de 2018, o suporte será removido para o TLS 1.0 e 1.1 do Gateway de VPN do Azure. O Gateway de VPN irá suportar apenas o TLS 1.2. Para manter o suporte, consulte as [atualizações para permitir o suporte para o TLS1.2](#tls1).

Além disso, os seguintes algoritmos legados também serão depreciados para TLS em 1 de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de Encriptação de Dados)
* 3DES (Algoritmo Triplo de Encriptação de Dados)
* MD5 (Resumo de Mensagens 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Como posso permitir o suporte para TLS 1.2 no Windows 7 e Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
