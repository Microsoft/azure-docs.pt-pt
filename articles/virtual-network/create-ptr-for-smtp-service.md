---
title: Configure zonas de procura inversas para uma verificação de banner SMTP
titlesuffix: Azure Virtual Network
description: Descreve como configurar zonas de procura inversas para uma verificação de banner SMTP em Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201701"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configure zonas de procura inversas para uma verificação de banner SMTP

Este artigo descreve como usar uma zona inversa em DNS Azure e criar um registo DeDNs (PTR) invertido para verificação de banners SMTP.

## <a name="symptom"></a>Sintoma

Se hospedar um servidor SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro quando enviar ou receber uma mensagem de servidores de correio remoto:

**554: Sem registo ptr**

## <a name="solution"></a>Solução

Para um endereço IP virtual em Azure, os registos inversos são criados em zonas de domínio da Microsoft, e não em zonas de domínio personalizados.

Para configurar os registos de PTR em zonas próprias da Microsoft, utilize a propriedade -ReverseFqdn no recurso PublicIpAddress. Para mais informações, consulte [Configure reverter DNS para serviços hospedados em Azure](../dns/dns-reverse-dns-for-azure-services.md).

Quando configurar os registos ptr, certifique-se de que o endereço IP e o FQDN invertido são propriedade da subscrição. Se tentar definir um FQDN invertido que não pertença à subscrição, recebe a seguinte mensagem de erro:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn corresponde a fqdn de qualquer recurso ip público ao abrigo da subscrição;
    2) A ReverseFqdn resolve para o fqdn (através da cadeia de registos CName) qualquer recurso ip público sob a subscrição;
    3) Resolve-se no endereço ip (através da cadeia CName e A records) de um recurso ip público estático ao abrigo da subscrição.

Se alterar manualmente o seu banner SMTP para corresponder ao nosso FQDN invertido predefinido, o servidor de correio remoto ainda pode falhar porque pode esperar que o banner host SMTP corresponda ao registo MX para o domínio.
