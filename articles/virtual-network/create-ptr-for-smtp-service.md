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
ms.openlocfilehash: 0264ad93eb53e27d1dc76f2b20ad175a6ee2f8de
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84688692"
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

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn corresponde a Fqdn de qualquer recurso ip público ao abrigo da subscrição;
    2) ReverseFqdn resolve a fqdn (através da cadeia de registos CName) de qualquer recurso ip público ao abrigo da subscrição;
    3) Resolve-se para o endereço IP (através da cadeia de registos CName e A) de um recurso ip público estático sob a subscrição.

Se alterar manualmente o seu banner SMTP para corresponder ao nosso FQDN invertido padrão, o servidor de correio remoto ainda pode falhar porque pode esperar que o anfitrião do banner SMTP corresponda ao registo MX do domínio.
