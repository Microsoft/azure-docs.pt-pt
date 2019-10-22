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
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "67184083"
---
>[!NOTE]
>A partir de 1 de julho de 2018, o suporte será removido para o TLS 1.0 e 1.1 do Gateway de VPN do Azure. O Gateway de VPN irá suportar apenas o TLS 1.2. Para manter o suporte, consulte as [atualizações para habilitar o suporte para TLS 1.2](#tls1).

Além disso, os seguintes algoritmos herdados também serão preteridos para o TLS em 1º de julho de 2018:

* RC4 (Rivest Cipher 4)
* DES (Algoritmo de Encriptação de Dados)
* 3DES (Algoritmo Triplo de Encriptação de Dados)
* MD5 (Resumo de Mensagens 5)

### <a name="tls1"></a>Como fazer habilitar o suporte para TLS 1,2 no Windows 7 e Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
