---
title: Configurar zonas de pesquisa inversa para uma verificação de faixa SMTP no Azure
titlesuffix: Azure Virtual Network
description: Descreve como configurar zonas de pesquisa inversa para uma verificação de faixa SMTP no Azure
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
ms.openlocfilehash: 084fdb7f850f3819738a982127fa98efab114197
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059023"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurar zonas de pesquisa inversa para uma verificação de faixa SMTP

Este artigo descreve como usar uma zona inversa no DNS do Azure e criar um registro DNS (PTR) reverso para verificação de faixa de SMTP.

## <a name="symptom"></a>Sintoma

Se você hospedar um servidor SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro ao enviar ou receber uma mensagem de servidores de email remotos:

**554: Nenhum registro PTR**

## <a name="solution"></a>Solução

Para um endereço IP virtual no Azure, os registros inversos são criados em zonas de domínio pertencentes à Microsoft, não zonas de domínio personalizadas.

Para configurar os registros PTR em zonas corporativas da Microsoft, use a propriedade-ReverseFqdn no recurso PublicIpAddress. Para obter mais informações, consulte [Configurar o DNS reverso para serviços hospedados no Azure](../dns/dns-reverse-dns-for-azure-services.md).

Ao configurar os registros PTR, certifique-se de que o endereço IP e o FQDN reverso sejam de propriedade da assinatura. Se você tentar definir um FQDN reverso que não pertença à assinatura, você receberá a seguinte mensagem de erro:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn corresponde ao FQDN de qualquer recurso de IP público na assinatura;
    2) ReverseFqdn resolve para o FQDN (por meio da cadeia de registros CName) de qualquer recurso de IP público na assinatura;
    3) Ele resolve para o endereço IP (por meio de CName e uma cadeia de registros) de um recurso IP público estático na assinatura.

Se você alterar manualmente sua faixa SMTP para corresponder ao nosso FQDN padrão reverso, o servidor de email remoto ainda poderá falhar porque pode esperar que o host de faixa SMTP corresponda ao registro MX do domínio.
