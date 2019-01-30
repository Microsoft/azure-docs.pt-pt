---
title: Noções básicas sobre iDNS no Azure Stack | Documentos da Microsoft
description: Noções básicas sobre iDNS funcionalidades e capacidades no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b313772fb2737c015a02cfc36cec87797c95f619
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251967"
---
# <a name="introducing-idns-for-azure-stack"></a>Introdução ao iDNS para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

iDNS é uma funcionalidade de rede do Azure Stack que lhe permite resolver nomes DNS externos (por exemplo, http://www.bing.com.) ele também permite-lhe registar nomes de rede virtual interna. Ao fazer isso, pode resolver VMs na mesma rede virtual ao nome em vez de endereço IP. Essa abordagem remove a necessidade de fornecer as entradas de servidor DNS personalizadas. Para obter mais informações sobre o DNS, consulte a [descrição geral do DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>O que faz iDNS?

Com iDNS no Azure Stack, tem as seguintes capacidades, sem ter de especificar as entradas de servidor DNS personalizadas:

- Partilhado serviços de resolução de nome DNS para cargas de trabalho de inquilino.
- Serviço DNS autoritativo para resolução de nomes e o registro DNS na rede virtual do inquilino.
- Serviço de DNS recursivo para resolução de nomes de Internet de VMs do inquilino. Os inquilinos já não tem de especificar as entradas de DNS personalizadas para resolver nomes de Internet (por exemplo, www.bing.com.)

Pode ainda traga seu próprio DNS e utilizar servidores DNS personalizados. No entanto, ao utilizar iDNS, que pode resolver nomes DNS de Internet e ligar a outras VMs na mesma rede virtual, não precisa de criar entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>O que não iDNS?

Os iDNS não permite que faça, é criar um registo DNS para um nome que possa ser resolvido a partir de fora da rede virtual.

No Azure, tem a opção de especificar uma etiqueta de nome DNS que está associada um endereço IP público. Pode escolher a etiqueta (prefixo), mas Azure escolhe o sufixo, que se baseia na região em que criar o endereço IP público.

![Exemplo de uma etiqueta de nome DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como mostra a imagem anterior, Azure irá criar um registo "A" no DNS para a etiqueta de nome DNS especificada na zona **westus.cloudapp.azure.com**. O prefixo e o sufixo são combinados para compor um [nome de domínio completamente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que pode ser resolvido a partir em qualquer lugar na Internet pública.

O Azure Stack apenas suporta iDNS para o registo de nome interno, para que ele não pode fazer o seguinte:

- Criar um registo DNS numa zona DNS alojado existente (por exemplo, local.azurestack.external.)
- Criar uma zona DNS (por exemplo, Contoso.com).
- Crie um registo em sua própria zona DNS personalizado.
- Suporta a compra de nomes de domínio.

## <a name="next-steps"></a>Passos Seguintes

[Através de DNS no Azure Stack](azure-stack-dns.md)
