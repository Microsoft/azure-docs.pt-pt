---
title: Visão geral do IPv6-Azure Load Balancer
description: Com este roteiro de aprendizagem, comece a usar o suporte a IPv6 para Azure Load Balancer e VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 07dd3ee507f31099e32f18143d2beedf76e83c36
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077014"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Visão geral do IPv6 para Azure Load Balancer


>[!NOTE] 
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre o Balanceador de Carga Standard, veja [Descrição geral do Balanceador de Carga Standard](load-balancer-standard-overview.md).

Os balanceadores de carga voltados para a Internet da SKU básica podem ser implantados com um endereço IPv6. Além da conectividade IPv4, isso permite os seguintes recursos:

* Conectividade IPv6 nativa de ponta a ponta entre clientes de Internet públicos e VMs (máquinas virtuais) do Azure por meio do balanceador de carga.
* Conectividade de saída IPv6 nativa de ponta a ponta entre VMs e clientes habilitados para IPv6 de Internet pública.

A imagem a seguir ilustra a funcionalidade IPv6 para Azure Load Balancer.

![Azure Load Balancer com IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Depois de implantado, um cliente de Internet habilitado para IPv4 ou IPv6 pode se comunicar com os endereços IPv4 ou IPv6 públicos (ou nomes de host) do Load Balancer voltado para a Internet do Azure. O balanceador de carga roteia os pacotes IPv6 para os endereços IPv6 privados das VMs usando a NAT (conversão de endereços de rede). O cliente de Internet IPv6 não pode se comunicar diretamente com o endereço IPv6 das VMs.

## <a name="features"></a>Funcionalidades

O suporte nativo a IPv6 para VMs implantadas por meio do Azure Resource Manager fornece:

1. Serviços IPv6 com balanceamento de carga para clientes IPv6 na Internet
2. Pontos de extremidade IPv6 e IPv4 nativos em VMs ("pilha dupla")
3. Conexões IPv6 nativas iniciadas de entrada e de saída
4. Protocolos com suporte como TCP, UDP e HTTP (S) permitem uma gama completa de arquiteturas de serviço

## <a name="benefits"></a>Benefícios

Essa funcionalidade permite os seguintes benefícios principais:

* Conheça as normas governamentais que exigem que novos aplicativos sejam acessíveis para clientes somente IPv6
* Habilite os desenvolvedores de IOT (Internet das coisas) para usar máquinas virtuais do Azure com pilha dupla (IPv4 + IPv6) para lidar com os crescentes mercados móveis & IOT

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço DNS do Azure contém registros de nome IPv4 A e IPv6 AAAA e responde com ambos os registros para o balanceador de carga. O cliente escolhe a qual endereço (IPv4 ou IPv6) se comunicará.
* Quando uma VM inicia uma conexão com um dispositivo conectado à Internet IPv6 pública, o endereço IPv6 de origem da VM é convertido de endereço de rede (NAT) para o endereço IPv6 público do balanceador de carga.
* As VMs que executam o sistema operacional Linux devem ser configuradas para receber um endereço IP IPv6 via DHCP. Muitas das imagens do Linux na galeria do Azure já estão configuradas para dar suporte ao IPv6 sem modificação. Para obter mais informações, consulte [Configurando o DHCPv6 para VMs Linux](load-balancer-ipv6-for-linux.md)
* Se você optar por usar uma investigação de integridade com o balanceador de carga, crie uma investigação de IPv4 e use-a com os pontos de extremidade IPv4 e IPv6. Se o serviço em sua VM ficar inativo, os pontos de extremidade IPv4 e IPv6 serão retirados da rotação.

Limitações

* Não é possível adicionar regras de balanceamento de carga IPv6 no portal do Azure. As regras só podem ser criadas por meio do modelo, da CLI, do PowerShell.
* Você não pode atualizar as VMs existentes para usar endereços IPv6. Você deve implantar novas VMs.
* Um único endereço IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Os endereços IPv6 públicos não podem ser atribuídos a uma VM. Eles só podem ser atribuídos a um balanceador de carga.
* Você não pode configurar a pesquisa inversa de DNS para seus endereços IPv6 públicos.
* As VMs com os endereços IPv6 não podem ser membros de um serviço de nuvem do Azure. Eles podem ser conectados a uma VNet (rede virtual) do Azure e se comunicarem entre si por meio de seus endereços IPv4.
* Endereços IPv6 privados podem ser implantados em VMs individuais em um grupo de recursos, mas não podem ser implantados em um grupo de recursos por meio de conjuntos de dimensionamento.
* As VMs do Azure não podem se conectar via IPv6 a outras VMs, outros serviços do Azure ou dispositivos locais. Eles só podem se comunicar com o Azure Load Balancer por IPv6. No entanto, eles podem se comunicar com esses outros recursos usando IPv4.
* A proteção do NSG (grupo de segurança de rede) para IPv4 tem suporte em implantações de pilha dupla (IPv4 + IPv6). NSGs não se aplicam aos pontos de extremidade IPv6.
* O ponto de extremidade IPv6 na VM não é exposto diretamente à Internet. Ele está atrás de um balanceador de carga. Somente as portas especificadas nas regras do balanceador de carga podem ser acessadas por IPv6.
* **Não há suporte**para a alteração do parâmetro IdleTimeout para IPv6 no momento. O padrão é quatro minutos.
* **Não há suporte**para a alteração do parâmetro LoadDistributionMethod para IPv6 no momento.
* **Não há suporte para**IPS IPv4 reservados (em que IPAllocationMethod = static) no momento.
* Não há suporte para NAT64 (tradução de IPv6 para IPv4).
* Atualmente, os balanceadores de carga SKU Standard não dão suporte a endereços IPv6.

## <a name="next-steps"></a>Passos seguintes

Saiba como implantar um balanceador de carga com IPv6.

* [Disponibilidade do IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implantar um balanceador de carga com IPv6 usando um modelo](load-balancer-ipv6-internet-template.md)
* [Implantar um balanceador de carga com IPv6 usando o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implantar um balanceador de carga com IPv6 usando o CLI do Azure](load-balancer-ipv6-internet-cli.md)
