---
title: Configurar zonas de pesquisa inversa para uma verificação de faixa de SMTP no Azure
titlesuffix: Azure Virtual Network
description: Descreve como configurar zonas de pesquisa inversa para uma verificação de faixa de SMTP no Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 203c3c5f371af7de891f0949a35378294bb50a0e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652387"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurar zonas de pesquisa inversa para uma verificação de faixa de SMTP

Este artigo descreve como utilizar uma zona inversa no DNS do Azure e criar um registo de DNS inverso (PTR) para a verificação de faixa de SMTP.

## <a name="symptom"></a>Sintoma

Se alojar um servidor de SMTP no Microsoft Azure, poderá receber a seguinte mensagem de erro quando enviar ou receber uma mensagem de servidores de correio remoto:

**554: Nenhum registo PTR**

## <a name="solution"></a>Solução

Para um endereço IP virtual no Azure, os inversos registos são criados no Microsoft pertencentes à empresa zonas de domínio, zonas de domínio personalizado não.

Para configurar os registos PTR no Microsoft pertencentes à empresa zonas, utilize a propriedade - ReverseFqdn no recurso PublicIpAddress. Para obter mais informações, consulte [configurar DNS inverso para os serviços alojados no Azure](../dns/dns-reverse-dns-for-azure-services.md).

Ao configurar os registros PTR, certifique-se de que o endereço IP e o FQDN inverso são propriedade da subscrição. Se tentar definir um FQDN inverso que não pertence à subscrição, receberá a seguinte mensagem de erro:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn corresponde ao fqdn de qualquer recurso de ip público na subscrição;
    2) ReverseFqdn é resolvido para o fqdn (através de cadeia de registos CName) de qualquer recurso de ip público na subscrição;
    3) Ele é resolvido para o endereço de ip (através de CName e A cadeia de registos) de um recurso de ip público estático na subscrição.

Se alterar manualmente a faixa de SMTP para corresponder ao nosso padrão inverter FQDN, o servidor de correio remoto ainda pode falhar porque ele poderá esperar o anfitrião de faixa de SMTP de acordo com o registro MX do domínio.
