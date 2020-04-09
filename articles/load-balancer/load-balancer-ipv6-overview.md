---
title: Visão geral do IPv6 - Equilíbrio de Carga Azure
description: Com este caminho de aprendizagem, inicie com suporte IPv6 para O Equilíbrio de Carga Azure e VMs equilibrados em carga.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, equilibrador de carga azul, dupla pilha, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: d2966f32fcf7f8be2a93d1639f0a63f49768c306
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981856"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Visão geral do IPv6 para O Equilíbrio de Carga Azure


>[!NOTE] 
>Este conteúdo foi substituído pelo [IPv6 para a Visão Geral do Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview). O Azure recomenda que as novas implementações do IPv6 utilizem o novo IPv6 para funcionalidades da Azure Virtual Networks.

>[!NOTE]
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo aborda o Balanceador de Carga Básico. Para mais informações sobre o Standard Load Balancer, consulte a visão geral do [Balancer de Carga Padrão](load-balancer-standard-overview.md).

Os equilibradores básicos de carga virados para a Internet sKU podem ser implantados com um endereço IPv6. Além da conectividade IPv4, isto permite as seguintes capacidades:

* Conectividade iPv6 nativa entre clientes públicos da Internet e Máquinas Virtuais Azure (VMs) através do equilibrador de carga.
* Conectividade de saída iPv6 nativa entre VMs e clientes públicos iPv6 habilitados para internet.

A imagem que se segue ilustra a funcionalidade IPv6 para o Azure Load Balancer.

![Equilíbrio de carga Azure com IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Uma vez implementado, um cliente de Internet iPv4 ou IPv6 pode comunicar com os endereços iPv4 ou IPv6 (ou nomes de anfitriões) do Balancer de Carga virado para a Internet Azure. O equilibrista de carga direciona os pacotes IPv6 para os endereços IPv6 privados dos VMs utilizando a tradução de endereços de rede (NAT). O cliente iPv6 internet não pode comunicar diretamente com o endereço IPv6 dos VMs.

## <a name="features"></a>Funcionalidades

O suporte nativo IPv6 para VMs implantados via Azure Resource Manager fornece:

1. Serviços IPv6 equilibrados em carga para clientes IPv6 na Internet
2. Pontos finais nativoIPv6 e IPv4 em VMs ("dual stacked")
3. Ligações iPv6 nativas de entrada e saída
4. Protocolos suportados como TCP, UDP e HTTP(S) permitem uma gama completa de arquiteturas de serviços

## <a name="benefits"></a>Vantagens

Esta funcionalidade permite os seguintes benefícios-chave:

* Cumprir os regulamentos governamentais que exigem que novas aplicações sejam acessíveis a clientes apenas iPv6
* Permitir que os desenvolvedores de dispositivos móveis e internet das coisas (IOT) utilizem máquinas virtuais azure de dupla pilha (IPv4+IPv6) para abordar os crescentes mercados de IOT de & móvel

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço Azure DNS contém os registos de nomeIPv4 A e IPv6 AAAA e responde com ambos os registos para o equilibrante de carga. O cliente escolhe com que endereço (IPv4 ou IPv6) para comunicar.
* Quando um VM inicia uma ligação a um dispositivo público ligado à Internet IPv6, o endereço IPv6 de origem do VM é traduzido na rede (NAT) para o endereço iPv6 público do equilibrador de carga.
* Os VMs que executam o sistema operativo Linux devem ser configurados para receber um endereço IP IPv6 via DHCP. Muitas das imagens linux na Galeria Azure já estão configuradas para suportar o IPv6 sem modificação. Para mais informações, consulte [Configurar DHCPv6 para VMs Linux](load-balancer-ipv6-for-linux.md)
* Se optar por utilizar uma sonda de saúde com o seu equilibrador de carga, crie uma sonda IPv4 e use-a com os pontos finais IPv4 e IPv6. Se o serviço no seu VM diminuir, tanto os pontos finais IPv4 como IPv6 são retirados da rotação.

Limitações

* Não é possível adicionar regras de equilíbrio de carga IPv6 no portal Azure. As regras só podem ser criadas através do modelo, CLI, PowerShell.
* Não pode atualizar os VMs existentes para utilizar endereços IPv6. Tens de implantar novos VMs.
* Um único endereço IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Os endereços públicos do IPv6 não podem ser atribuídos a um VM. Só podem ser atribuídos a um equilibrador de carga.
* Não é possível configurar a procura inversa do DNS para os seus endereços IPv6 públicos.
* Os VMs com os endereços IPv6 não podem ser membros de um Serviço Azure Cloud. Podem ser ligados a uma Rede Virtual Azure (VNet) e comunicar entre si através dos seus endereços IPv4.
* Os endereços IPv6 privados podem ser implantados em VMs individuais num grupo de recursos, mas não podem ser implantados num grupo de recursos através de Conjuntos de Escala.
* Os VMs Azure não podem ligar o IPv6 a outros VMs, outros serviços Azure ou dispositivos no local. Só podem comunicar com o equilibrador de carga Azure sobre o IPv6. No entanto, podem comunicar com estes outros recursos usando o IPv4.
* A proteção do Network Security Group (NSG) para iPv4 é suportada em implementações de dupla pilha (IPv4+IPv6). Os NSGs não se aplicam aos pontos finais do IPv6.
* O ponto final do IPv6 no VM não é exposto diretamente à internet. Está por trás de um equilibrista de carga. Apenas as portas especificadas nas regras do equilíbrio de carga são acessíveis ao iPv6.
* A alteração do parâmetro IdleTimeout para IPv6 não é **suportada atualmente.** O padrão é de quatro minutos.
* A alteração do parâmetro loadDistributionMethod para IPv6 não está **atualmente suportada**.
* IPv6 IPs reservados (onde IPAllocationMethod = estático) não são **atualmente suportados**.
* O NAT64 (tradução do IPv6 para IPv4) não é suportado.
* A fixação de um NIC secundário que se refere a uma sub-rede IPv6 a uma piscina de back-end não é **atualmente suportada**.

## <a name="next-steps"></a>Passos seguintes

Aprenda a implantar um equilibrador de carga com o IPv6.

* [Disponibilidade de IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implante um equilibrador de carga com IPv6 usando um modelo](load-balancer-ipv6-internet-template.md)
* [Implante um equilibrador de carga com iPv6 utilizando o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implante um equilibrador de carga com IPv6 utilizando o Azure CLI](load-balancer-ipv6-internet-cli.md)
