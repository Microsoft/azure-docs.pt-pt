---
title: Cenários para Zonas Privadas - DNS Azure
description: Neste artigo, conheça cenários comuns para a utilização de Zonas Privadas Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939331"
---
# <a name="azure-dns-private-zones-scenarios"></a>Cenários de zonas privadas Azure DNS

As Zonas Privadas Azure DNS fornecem resolução de nomes dentro de uma rede virtual, bem como entre redes virtuais. Neste artigo, olhamos para alguns cenários comuns que podem ser realizados usando esta funcionalidade.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Cenário: Resolução de nomes traçada para uma única rede virtual
Neste cenário, tem uma rede virtual em Azure que tem uma série de recursos Azure na seleção, incluindo máquinas virtuais (VMs). Você quer resolver os recursos de dentro da rede virtual através de um nome de domínio específico (zona DNS), e você precisa que a resolução de nome sê-lo seja privada e não acessível a partir da internet. Além disso, para os VMs dentro do VNET, precisa do Azure para os registar automaticamente na zona DNS. 

Este cenário é retratado abaixo. A Rede Virtual denominada "A" contém dois VMs (VNETA-VM1 e VNETA-VM2). Cada um deles tem IPs privados associados. Assim que criar uma Zona Privada chamada contoso.com e ligar esta rede virtual como uma rede virtual de registo, o Azure DNS criará automaticamente dois registos A na zona, tal como descrito. Agora, as consultas dNS da VNETA-VM1 para resolver VNETA-VM2.contoso.com receberão uma resposta DNS que contenha o IP privado da VNETA-VM2. Além disso, uma consulta dNS inversa (PTR) para o IP privado de VNETA-VM1 (10.0.0.1) emitida a partir da VNETA-VM2 receberá uma resposta DNS que contenha o nome de VNETA-VM1, como esperado. 

![Resolução única da rede virtual](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Cenário: Resolução de nomes através de redes virtuais

Este cenário é o caso mais comum onde você precisa associar uma Zona Privada a várias redes virtuais. Este cenário pode encaixar em arquiteturas como o modelo Hub-and-Spoke onde existe uma rede virtual central do Hub à qual várias outras redes virtuais faladas estão ligadas. A rede virtual central do Hub pode ser ligada como a rede virtual registration a uma zona privada, e as redes virtuais spoke podem ser ligadas como redes virtuais de Resolução. 

O diagrama seguinte mostra uma versão simples deste cenário onde existem apenas duas redes virtuais - A e B. A é designada como uma rede virtual de registo e B é designada como uma rede virtual de Resolução. A intenção é que ambas as redes virtuais partilhem uma zona comum contoso.com. Quando a zona é criada e as redes virtuais de Resolução e Registo estão ligadas à zona, o Azure registará automaticamente os registos dNS para os VMs (VNETA-VM1 e VNETA-VM2) da rede virtual A. Também pode adicionar registos DNS manualmente na zona para VMs na rede virtual Resolução B. Com esta configuração, observará o seguinte comportamento para consultas dNS inversas e inversas:
* Uma consulta dNS da VNETB-VM1 na rede virtual resolução B, por VNETA-VM1.contoso.com, receberá uma resposta DNS contendo o IP privado da VNETA-VM1.
* Uma consulta de DNS inversa (PTR) da VNETB-VM2 na rede virtual resolução B, para 10.1.0.1, receberá uma resposta DNS contendo o VNETB-VM1.contoso.com FQDN.  
* Uma consulta DeDNS (PTR) inversa da VNETB-VM3 na rede virtual resolução B, para 10.0.0.1, receberá o NXDOMAIN. A razão é que as consultas de DNS inversas são apenas consultadas para a mesma rede virtual. 


![Múltiplas resoluções de rede virtual](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Cenário: Funcionalidade Split-Horizon

Neste cenário, tem um caso de uso onde pretende realizar diferentes comportamentos de resolução de DNS dependendo de onde o cliente se senta (dentro de Azure ou fora da internet), para a mesma zona DNS. Por exemplo, pode ter uma versão privada e pública da sua aplicação que tem funcionalidade ou comportamento diferentes, mas pretende usar o mesmo nome de domínio para ambas as versões. Este cenário pode ser realizado com o Azure DNS através da criação de uma zona DeNs Pública, bem como uma Zona Privada, com o mesmo nome.

O diagrama seguinte retrata este cenário. Possui uma rede virtual A que tem dois VMs (VNETA-VM1 e VNETA-VM2) que têm iPs privados e IPs públicos atribuídos. Cria-se uma zona pública de DNS chamada contoso.com e regista os IPs públicos para estes VMs como registos dNS dentro da zona. Também cria uma zona Privada de DNS também chamada contoso.com especificando A como rede virtual registration. O Azure regista automaticamente os VMs como registos A na Zona Privada, apontando para os seus IPs Privados.

Agora, quando um cliente da Internet emite uma consulta de DNS para procurar VNETA-VM1.contoso.com, o Azure devolverá o registo de IP público da zona pública. Se a mesma consulta dNS for emitida a partir de outro VM (por exemplo: VNETA-VM2) na mesma rede virtual A, o Azure devolverá o registo IP privado da zona privada. 

![Resolução de Brian dividida](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

Aprenda a [criar uma zona privada de DNS](./private-dns-getstarted-powershell.md) em Azure DNS.

Conheça as zonas e registos dNS visitando: [Zonas DNS e monitorizações globais](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

