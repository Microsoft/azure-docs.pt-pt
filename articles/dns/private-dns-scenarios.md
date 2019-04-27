---
title: Cenários para zonas de privadas do DNS do Azure
description: Descrição geral dos cenários comuns para utilizar zonas privadas do DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 409595febded7b242eae876ebb2cb35ae4999e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686865"
---
# <a name="azure-dns-private-zones-scenarios"></a>Cenários de zonas privadas do DNS do Azure
As zonas privadas do DNS do Azure fornecem resolução de nomes numa rede virtual, bem como entre redes virtuais. Neste artigo, vamos ver alguns cenários comuns que podem ser percebidos utilizar esta funcionalidade. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Cenário: Resolução de nomes no âmbito de uma única rede virtual
Neste cenário, tem uma rede virtual no Azure que tem um número de recursos do Azure, incluindo máquinas virtuais (VMs). Pretende resolver os recursos a partir de dentro da rede virtual através de um nome de domínio específico (zona DNS), e terá da resolução de nomes para ser privado e não está acessível a partir da internet. Além disso, para as VMs dentro da VNET, precisa do Azure para registar automaticamente para a zona DNS. 

Este cenário é descrito abaixo. Rede virtual denominada "A" contém duas VMs (VM1 também e também VM2). Cada um deles tem IPs privados associados. Depois de criar uma zona privada com o nome contoso.com e ligar esta rede virtual como uma rede virtual de registo, DNS do Azure irá criar automaticamente dois registos na zona conforme representado. Agora, as consultas DNS de também-VM1 para resolver VM2.contoso.com também irão receber uma resposta DNS que contém o IP privado da VM2 também. Além disso, uma consulta de DNS inverso (PTR) para o IP privado de também-VM1 (10.0.0.1) emitidos pela VM2 também irão receber uma resposta DNS que contém o nome do também-VM1, conforme o esperado. 

![Resolução de rede Virtual única](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Cenário: Resolução de nomes por várias redes virtuais

Este cenário é o caso mais comum em que precisa de associar uma zona privada com várias redes virtuais. Este cenário pode se encaixar arquiteturas, como o modelo de Hub-and-Spoke onde existe uma rede virtual Hub central ao qual outro vários Spoke estão ligadas a redes virtuais. A rede virtual do concentrador central pode ser ligada como a rede virtual de registo para uma zona privada e as redes virtuais indicadas podem ser ligadas como redes virtuais de resolução. 

O diagrama seguinte mostra uma versão simples desse cenário em que existem apenas duas redes virtuais - A e B. Uma é designada como uma rede virtual de registo e B é designada como uma rede virtual de resolução. A intenção é que ambas as redes virtuais partilhar uma zona contoso.com comuns. Quando a zona é criada e as redes virtuais de resolução e registo são associadas à zona, o Azure irá registar automaticamente registos DNS para as VMs (VM1 também e também VM2) da rede virtual. Também pode adicionar manualmente registos DNS para a zona para as VMs na rede virtual de resolução B. Com esta configuração, observará o seguinte comportamento para progressivas e reversas consultas DNS:
* Uma consulta DNS da VM1 VNETA na resolução rede virtual B, para também-VM1.contoso.com, irá receber uma resposta DNS que contém o IP privado da VM1 também.
* Uma consulta de DNS inverso (PTR) de VNETA VM2 na resolução rede virtual B, para 10.1.0.1, irá receber uma resposta DNS que contém a VNETA-VM1.contoso.com FQDN. O motivo é que as consultas de DNS inversos estão confinadas à mesma rede virtual. 
* Uma consulta de DNS inverso (PTR) de VNETA VM3 na resolução rede virtual B, para 10.0.0.1, irá receber NXDOMAIN. O motivo é que as consultas de DNS inversos apenas estão no âmbito da mesma rede virtual. 


![Várias resoluções de rede Virtual](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Cenário: Split-Horizon funcionalidade

Neste cenário, tem um caso de utilização para perceber o comportamento de resolução DNS diferente dependendo de onde o cliente encontra-se (dentro do Azure ou para fora na internet), onde pretende para a mesma zona DNS. Por exemplo, poderá ter uma versão pública e privada da sua aplicação com diferentes funcionalidades ou comportamento, mas que pretende utilizar o mesmo nome de domínio para as duas versões. Neste cenário pode ser conseguido com o DNS do Azure através da criação de uma zona de DNS público, bem como uma zona privada, com o mesmo nome.

O diagrama seguinte ilustra esse cenário. Tem uma rede virtual A que tem duas VMs (VM1 também e também VM2) que tem ambos os IPs privados e IPs públicos alocados. Criar uma zona DNS pública denominada contoso.com e registar os IPs públicos para estas VMs como registos DNS na zona. Também é criar uma zona de DNS privado, também denominada contoso.com especificando A que a rede virtual de registo. Azure regista automaticamente as VMs como um registos para a zona privada, apontando para dos respetivos IPs privados.

Agora, quando um cliente de internet emite uma consulta DNS para procurar VM1.contoso.com também, o Azure irá devolver o registo de IP público da zona pública. Se a mesma consulta DNS é emitida a partir de outra VM (por exemplo: VNETA-VM2) na mesma rede virtual A, Azure irá devolver o registo de IP privado da zona privada. 

![Resolução de Brian de divisão](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

Saiba como [criar uma zona DNS privada](./private-dns-getstarted-powershell.md) no DNS do Azure.

Saiba mais sobre zonas e registos DNS, visite a página: [Zonas e registos de descrição geral do DNS](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

