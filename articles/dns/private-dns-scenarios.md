---
title: Cenários para Zonas Privadas - Azure DNS
description: Neste artigo, conheça os cenários comuns para a utilização de Zonas Privadas Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76939331"
---
# <a name="azure-dns-private-zones-scenarios"></a>Cenários de zonas Privadas de DNS do Azure

As Zonas Privadas Azure DNS fornecem resolução de nomes dentro de uma rede virtual, bem como entre redes virtuais. Neste artigo, olhamos para alguns cenários comuns que podem ser realizados usando esta funcionalidade.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Cenário: Resolução de nomes a uma única rede virtual
Neste cenário, tem uma rede virtual em Azure que tem uma série de recursos Azure nele, incluindo máquinas virtuais (VMs). Pretende resolver os recursos a partir da rede virtual através de um nome de domínio específico (zona DNS), e precisa que a resolução do nome seja privada e não acessível a partir da internet. Além disso, para os VMs dentro do VNET, você precisa que a Azure os registe automaticamente na zona DNS. 

Este cenário é retratado abaixo. A Rede Virtual denominada "A" contém dois VMs (VNETA-VM1 e VNETA-VM2). Cada um destes tem IPs privados associados. Assim que criar uma Zona Privada denominada contoso.com e ligar esta rede virtual como uma rede virtual de Registo, o Azure DNS criará automaticamente dois registos A na zona como descrito. Agora, as consultas dns da VNETA-VM1 para resolver VNETA-VM2.contoso.com receberão uma resposta DNS que contenha o IP privado de VNETA-VM2. Além disso, uma consulta de DNS reverso (PTR) para o IP privado de VNETA-VM1 (10.0.0.1) emitida pela VNETA-VM2 receberá uma resposta DNS que contenha o nome de VNETA-VM1, como esperado. 

![Resolução de rede virtual única](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Cenário: Resolução de nomes em redes virtuais

Este cenário é o caso mais comum em que é necessário associar uma Zona Privada a várias redes virtuais. Este cenário pode encaixar-se em arquiteturas como o modelo Hub-and-Spoke onde existe uma rede virtual central do Hub à qual várias outras redes virtuais spoke estão ligadas. A rede virtual central do Hub pode ser ligada como a rede virtual de registo a uma zona privada, e as redes virtuais Spoke podem ser ligadas como redes virtuais de Resolução. 

O diagrama seguinte mostra uma versão simples deste cenário onde existem apenas duas redes virtuais - A e B. A é designada como rede virtual de registo e B é designada como uma rede virtual de Resolução. A intenção é que ambas as redes virtuais partilhem uma zona comum contoso.com. Quando a zona é criada e as redes virtuais de Resolução e Registo estão ligadas à zona, o Azure registará automaticamente registos DNS para os VMs (VNETA-VM1 e VNETA-VM2) da rede virtual A. Também pode adicionar manualmente registos DNS na zona de VMs na rede virtual de Resolução B. Com esta configuração, observará o seguinte comportamento para consultas de DNS para a frente e para reverter:
* Uma consulta de DNS da VNETB-VM1 na rede virtual de resolução B, para VNETA-VM1.contoso.com, receberá uma resposta DNS contendo o IP privado de VNETA-VM1.
* Uma consulta de DNS reverso (PTR) da VNETB-VM2 na rede virtual de resolução B, para 10.1.0.1, receberá uma resposta DNS contendo o VNETB-VM1.contoso.com FQDN.  
* Uma consulta reversa de DNS (PTR) da VNETB-VM3 na rede virtual de resolução B, para 10.0.0.1, receberá NXDOMAIN. A razão é que as consultas de DNS reversos são apenas scoped para a mesma rede virtual. 


![Múltiplas resoluções de rede virtual](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Cenário: funcionalidade Split-Horizon

Neste cenário, tem um caso de uso em que pretende realizar diferentes comportamentos de resolução de DNS dependendo de onde o cliente se encontra (dentro do Azure ou fora na internet), para a mesma zona de DNS. Por exemplo, pode ter uma versão privada e pública da sua aplicação que tenha diferentes funcionalidades ou comportamentos, mas pretende utilizar o mesmo nome de domínio para ambas as versões. Este cenário pode ser realizado com o Azure DNS através da criação de uma zona de DNS Público, bem como de uma Zona Privada, com o mesmo nome.

O diagrama que se segue retrata este cenário. Tem uma rede virtual A que tem dois VMs (VNETA-VM1 e VNETA-VM2) que têm iPs privados e IPs públicos atribuídos. Cria-se uma zona pública de DNS chamada contoso.com e regista os IPs públicos para estes VMs como registos DNS dentro da zona. Também cria uma zona privada de DNS também chamada contoso.com especificando A como rede virtual de registo. O Azure regista automaticamente os VMs como registos A na Zona Privada, apontando para os seus IPs Privados.

Agora, quando um cliente da Internet emite uma consulta de DNS para procurar VNETA-VM1.contoso.com, o Azure devolverá o registo de IP público da zona pública. Se a mesma consulta de DNS for emitida a partir de outro VM (por exemplo: VNETA-VM2) na mesma rede virtual A, a Azure devolverá o registo IP privado da zona privada. 

![Resolução de Brian dividida](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

Saiba como [criar uma zona privada de DNS](./private-dns-getstarted-powershell.md) em Azure DNS.

Saiba mais sobre as zonas e registos DNS visitando: [zonas DNS e visão geral dos registos](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

