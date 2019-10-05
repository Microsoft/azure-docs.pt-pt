---
title: Cenários para Zonas Privadas do DNS do Azure
description: Visão geral de cenários comuns para usar Zonas Privadas do DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: 747fe891bf4d6bd042e689107cd87680795eb82b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959333"
---
# <a name="azure-dns-private-zones-scenarios"></a>Cenários de zonas privadas do DNS do Azure

Zonas Privadas do DNS do Azure fornecer resolução de nomes em uma rede virtual, bem como entre redes virtuais. Neste artigo, veremos alguns cenários comuns que podem ser percebidos usando esse recurso.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Cenário: Resolução de nomes com escopo para uma única rede virtual
Nesse cenário, você tem uma rede virtual no Azure que tem vários recursos do Azure, incluindo VMs (máquinas virtuais). Você deseja resolver os recursos de dentro da rede virtual por meio de um nome de domínio específico (zona DNS) e precisa que a resolução de nomes seja privada e não acessível pela Internet. Além disso, para as VMs na VNET, você precisa que o Azure Registre-as automaticamente na zona DNS. 

Este cenário é descrito abaixo. A rede virtual denominada "A" contém duas VMs (VNETA-VM1 e VNETA-VM2). Cada um deles tem IPs privados associados. Depois de criar uma zona privada chamada contoso.com e vincular essa rede virtual como uma rede virtual de registro, o DNS do Azure criará automaticamente dois registros A na zona, conforme descrito. Agora, as consultas DNS de VNETA-VM1 para resolver o VNETA-VM2.contoso.com receberão uma resposta DNS que contém o IP privado de VNETA-VM2. Além disso, uma consulta DNS reversa (PTR) para o IP privado de VNETA-VM1 (10.0.0.1) emitido de VNETA-VM2 receberá uma resposta DNS que contém o nome de VNETA-VM1, conforme esperado. 

![Resolução de rede virtual única](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Cenário: Resolução de nomes entre redes virtuais

Esse cenário é o caso mais comum em que você precisa associar uma zona privada a várias redes virtuais. Esse cenário pode ajustar arquiteturas como o modelo Hub e spoke em que há uma rede virtual hub central à qual várias outras redes virtuais spoke estão conectadas. A rede virtual do hub central pode ser vinculada como a rede virtual de registro a uma zona privada e as redes virtuais spoke podem ser vinculadas como redes virtuais de resolução. 

O diagrama a seguir mostra uma versão simples desse cenário em que há apenas duas redes virtuais: A e B. Um é designado como uma rede virtual de registro e B é designado como uma rede virtual de resolução. A intenção é que ambas as redes virtuais compartilhem um contoso.com de zona comum. Quando a zona for criada e as redes virtuais de resolução e registro estiverem vinculadas à zona, o Azure registrará automaticamente os registros DNS para as VMs (VNETA-VM1 e VNETA-VM2) da rede virtual A. Você também pode adicionar manualmente os registros DNS à zona para VMs na rede virtual de resolução B. Com essa configuração, você observará o seguinte comportamento para consultas de DNS diretas e reversas:
* Uma consulta DNS de VNETB-VM1 na rede virtual de resolução B, para VNETA-VM1.contoso.com, receberá uma resposta DNS que contém o IP privado de VNETA-VM1.
* Uma consulta DNS reversa (PTR) de VNETB-VM2 na rede virtual de resolução B, para 10.1.0.1, receberá uma resposta DNS que contém o FQDN VNETB-VM1.contoso.com. O motivo é que as consultas de DNS reverso têm como escopo a mesma rede virtual. 
* Uma consulta DNS reversa (PTR) de VNETB-VM3 na rede virtual de resolução B, para 10.0.0.1, receberá NXDOMAIN. O motivo é que as consultas de DNS reverso são apenas no escopo da mesma rede virtual. 


![Várias resoluções de rede virtual](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Cenário: Funcionalidade de horizonte dividido

Nesse cenário, você tem um caso de uso em que deseja obter um comportamento de resolução DNS diferente dependendo de onde o cliente reside (dentro do Azure ou da Internet), para a mesma zona DNS. Por exemplo, você pode ter uma versão pública e privada do seu aplicativo que tem funcionalidade ou comportamento diferente, mas você deseja usar o mesmo nome de domínio para ambas as versões. Esse cenário pode ser percebido com o DNS do Azure criando uma zona DNS pública, bem como uma zona privada, com o mesmo nome.

O diagrama a seguir ilustra esse cenário. Você tem uma rede virtual A que tem duas VMs (VNETA-VM1 e VNETA-VM2) que têm IPs privados e IPs públicos alocados. Você cria uma zona DNS pública chamada contoso.com e registra os IPs públicos para essas VMs como registros DNS dentro da zona. Você também cria uma zona de DNS privado também chamada contoso.com especificando a como a rede virtual de registro. O Azure registra automaticamente as VMs como registros a na zona privada, apontando para seus IPs privados.

Agora, quando um cliente da Internet emite uma consulta DNS para procurar VNETA-VM1.contoso.com, o Azure retornará o registro IP público da zona pública. Se a mesma consulta DNS for emitida de outra VM (por exemplo: VNETA-VM2) na mesma rede virtual A, o Azure retornará o registro de IP privado da zona privada. 

![Dividir a resolução de Brian](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre zonas DNS privadas, veja [Utilizar o DNS do Azure para domínios privados](private-dns-overview.md).

Saiba como [criar uma zona DNS privada](./private-dns-getstarted-powershell.md) no DNS do Azure.

Saiba mais sobre as zonas e os registros DNS visitando: [Visão geral de zonas e registros DNS](dns-zones-records.md).

Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.

