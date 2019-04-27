---
title: Descrição geral do IPv6 para o Balanceador de carga do Azure
titlesuffix: Azure Load Balancer
description: Noções básicas sobre o suporte de IPv6 para o Azure Load Balancer e as VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861962"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Descrição geral do IPv6 para o Balanceador de carga do Azure


>[!NOTE] 
>O Balanceador de carga do Azure suporta dois tipos diferentes: Basic e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre o Balanceador de carga Standard, veja [descrição geral do Balanceador de carga Standard](load-balancer-standard-overview.md).

Balanceadores de carga com acesso à Internet podem ser implementados com um endereço IPv6. Para além da conectividade IPv4, isso permite que as seguintes capacidades:

* -A-ponto conectividade IPv6 nativa entre os clientes de Internet públicos e máquinas de virtuais (VMs) do Azure através do Balanceador de carga.
* -A-ponto conectividade IPv6 nativa saída entre VMs e os clientes habilitados para Internet IPv6 públicos.

A imagem seguinte ilustra a funcionalidade de IPv6 para o Balanceador de carga do Azure.

![Balanceador de carga do Azure com o IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Uma vez implantado, um cliente de IPv4 ou IPv6 ativado Internet pode comunicar com endereços IPv4 ou IPv6 públicos (ou os nomes de anfitrião) do Balanceador de carga com acesso à Internet do Azure. O Balanceador de carga encaminha os pacotes de IPv6 para os endereços IPv6 privados das VMs através de tradução de endereços de rede (NAT). O cliente de IPv6 Internet não é possível comunicar diretamente com o endereço IPv6 das VMs.

## <a name="features"></a>Funcionalidades

Suporte de IPv6 nativo para as VMs implementadas através do Azure Resource Manager fornece:

1. Serviços de IPv6 com balanceamento de carga para clientes de IPv6 na Internet
2. Pontos finais de IPv4 e IPv6 nativo em VMs ("dual empilhada")
3. Entrada e saída iniciadas por ligações de IPv6 nativas
4. Protocolos suportados, como TCP, UDP e HTTP (S) ativar uma gama completa de arquiteturas de serviço

## <a name="benefits"></a>Benefícios

Esta funcionalidade permite que as seguintes grandes vantagens:

* Cumprir os regulamentos de administração pública que requerem que os novos aplicativos ser acessíveis aos clientes apenas de IPv6
* Ativar mobile e Internet das coisas (IOT) os desenvolvedores a usar máquinas virtuais do Azure (IPv4 + IPv6) empilhadas a dupla para abordar o móveis de cada vez maior e mercados IOT

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço de DNS do Azure contém tanto IPv4 e IPv6 AAAA registos de nomes e responde com ambos os registros do Balanceador de carga. O cliente escolhe qual o endereço (IPv4 ou IPv6) para comunicar com.
* Quando uma VM inicia uma conexão a um dispositivo ligado à Internet IPv6 público, endereço IPv6 de origem da VM é o endereço de rede traduzido (NAT) para o endereço IPv6 público do Balanceador de carga.
* VMs que executam o sistema operativo Linux tem de ser configuradas para receber um endereço de IPv6 IP através de DHCP. Muitas das imagens Linux na galeria do Azure já estão configuradas para oferecer suporte a IPv6 sem modificação. Para obter mais informações, consulte [configurar DHCPv6 para VMs do Linux](load-balancer-ipv6-for-linux.md)
* Se optar por utilizar uma sonda de estado de funcionamento com o Balanceador de carga, crie uma sonda de IPv4 e utilizá-la com pontos finais de IPv4 e IPv6. Se o serviço na sua VM ficar inativo, pontos finais de IPv4 e IPv6 são retirados da rotação.

Limitações

* Não é possível adicionar regras de balanceamento de carga IPv6 no portal do Azure. As regras só podem ser criadas através do modelo, da CLI, o PowerShell.
* Não pode atualizar VMs existentes para utilizar endereços IPv6. Tem de implementar novas VMs.
* Um único endereço de IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Os endereços IPv6 públicos não não possível atribuir a uma VM. Só podem ser atribuídos a um balanceador de carga.
* Não é possível configurar a pesquisa reversa de DNS para os endereços IPv6 públicos.
* As VMs com os endereços IPv6 não podem ser membros de um serviço Cloud do Azure. Eles podem ser ligados a uma rede Virtual do Azure (VNet) e se comunicar entre si através de seus endereços de IPv4.
* Os endereços IPv6 privados podem ser implementados em VMs individuais num grupo de recursos, mas não podem ser implementados num grupo de recursos através de conjuntos de dimensionamento.
* VMs do Azure não consegue ligar através de IPv6 para outras VMs, outros serviços do Azure ou os dispositivos no local. Apenas pode comunicar com o Balanceador de carga do Azure através de IPv6. No entanto, eles podem comunicar com estes outros recursos com o IPv4.
* Proteção de grupo de segurança de rede (NSG) para IPv4 é suportada em implementações de pilha dupla (IPv4 + IPv6). Os NSGs não se aplicam aos pontos finais do IPv6.
* O ponto final de IPv6 na VM não está exposto diretamente à internet. É por trás de um balanceador de carga. Apenas as portas especificadas nas regras do Balanceador de carga são acessíveis através de IPv6.
* Alterar o parâmetro IdleTimeout para IPv6 é **atualmente não suportados**. A predefinição é de quatro minutos.
* Alterar o parâmetro loadDistributionMethod para IPv6 é **atualmente não suportados**.
* Reservado IPs de IPv6 (onde IPAllocationMethod = estático) são **atualmente não suportados**.
* NAT64 (conversão de IPv6 para IPv4) não é suportada.

## <a name="next-steps"></a>Passos Seguintes

Saiba como implementar um balanceador de carga com o IPv6.

* [Disponibilidade do IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implementar um balanceador de carga com o IPv6 com um modelo](load-balancer-ipv6-internet-template.md)
* [Implementar um balanceador de carga com o IPv6 com o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implementar um balanceador de carga com o IPv6 com a CLI do Azure](load-balancer-ipv6-internet-cli.md)
