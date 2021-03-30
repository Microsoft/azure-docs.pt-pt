---
title: Configurar zonas de procura inversa para uma verificação de banner SMTP
titlesuffix: Azure Virtual Network
description: Descreve como configurar zonas de procura inversa para uma verificação de banner SMTP em Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c8ffadb8d54db0c2a99dc12e45b5990155a0505e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135061"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurar zonas de procura inversa para uma verificação de banner SMTP

Este artigo descreve como utilizar uma zona inversa em Azure DNS e criar um registo de DNS reverso (PTR) para verificação de banner smtp.

## <a name="symptom"></a>Sintoma

Se hospedar um servidor SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro quando enviar ou receber uma mensagem a partir de servidores de correio remoto:

**554: Sem Registo PTR**

## <a name="solution"></a>Solução

Para um endereço IP virtual em Azure, os registos inversos são criados em zonas de domínio da Microsoft, e não em zonas de domínio personalizadas.

Para configurar os registos PTR em zonas proprietárias da Microsoft, utilize a propriedade -ReverseFqdn no recurso PublicIpAddress. Para mais informações, consulte [o Configure reverse DNS para serviços hospedados no Azure](../dns/dns-reverse-dns-for-azure-services.md).

Quando configurar os registos PTR, certifique-se de que o endereço IP e o FQDN invertido são propriedade da subscrição. Se tentar definir um FQDN invertido que não pertença à subscrição, recebe a seguinte mensagem de erro:

```output
Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                    
1) ReverseFqdn matches fqdn of any public ip resource under the subscription;
2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subscription;
3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription.
```

Se alterar manualmente o seu banner SMTP para corresponder ao nosso FQDN invertido padrão, o servidor de correio remoto ainda pode falhar porque pode esperar que o anfitrião do banner SMTP corresponda ao registo MX do domínio.
