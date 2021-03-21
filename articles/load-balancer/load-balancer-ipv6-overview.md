---
title: Visão geral do IPv6 - Equilibrista de Carga Azure
description: Com este caminho de aprendizagem, começa com o suporte IPv6 para o Azure Load Balancer e VMs equilibrados em carga.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, balançador de carga azul, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94700694"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Visão geral do IPv6 para Equilibrista de Carga Azure


>[!NOTE] 
>Este conteúdo foi substituído pelo [IPv6 para a visão geral do Azure VNet](../virtual-network/ipv6-overview.md). O Azure recomenda que as novas implementações do IPv6 utilizem as novas funcionalidades do IPv6 para redes virtuais Azure.

>[!NOTE]
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre o Balanceador de Carga Padrão, consulte [a visão geral do Balancer de Carga Padrão](./load-balancer-overview.md).

Os equilibradores básicos de carga virados para a Internet SKU podem ser implantados com um endereço IPv6. Além da conectividade IPv4, isto permite as seguintes capacidades:

* Conectividade IPv6 nativa entre clientes públicos da Internet e Máquinas Virtuais Azure (VMs) através do equilibrador de carga.
* Conectividade nativa de saída IPv6 entre VMs e clientes públicos com iPv6 ativado pela Internet.

A imagem a seguir ilustra a funcionalidade IPv6 para O Balancer de Carga Azure.

![Equilibrista de carga Azure com IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Uma vez implantado, um cliente de Internet com iPv4 ou IPv6 pode comunicar com os endereços IPv4 ou IPv6 públicos (ou nomes de anfitrião) do Balançador de Carga virado para a Internet Azure. O equilibrador de carga encaminha os pacotes IPv6 para os endereços IPv6 privados dos VMs utilizando a tradução de endereços de rede (NAT). O cliente da Internet IPv6 não pode comunicar diretamente com o endereço IPv6 dos VMs.

## <a name="features"></a>Funcionalidades

O suporte nativo do IPv6 para VMs implantados através do Azure Resource Manager fornece:

1. Serviços IPv6 equilibrados para clientes IPv6 na Internet
2. Pontos finais nativos IPv6 e IPv4 em VMs ("dual stacked")
3. Ligações IPv6 nativas iniciadas e de saída
4. Protocolos apoiados como TCP, UDP e HTTP(S) permitem uma gama completa de arquiteturas de serviço

## <a name="benefits"></a>Benefícios

Esta funcionalidade permite os seguintes benefícios principais:

* Cumpra os regulamentos governamentais que exigem que as novas aplicações sejam acessíveis a clientes apenas iPv6
* Permitir que os desenvolvedores móveis e internet das coisas (IOT) utilizem máquinas virtuais Azure (IPv4+IPv6) duplas para abordar os crescentes mercados móveis & IOT

## <a name="details-and-limitations"></a>Detalhes e limitações

Detalhes

* O serviço Azure DNS contém registos de nomes IPv4 A e IPv6 AAAA e responde com ambos os registos para o esquilibrista de carga. O cliente escolhe qual endereço (IPv4 ou IPv6) para comunicar.
* Quando um VM inicia uma ligação a um dispositivo público ligado à Internet IPv6, o endereço IPv6 de origem do VM é traduzido para o endereço de rede (NAT) para o endereço público IPv6 do esídulo de carga.
* Os VMs que executam o sistema operativo Linux devem ser configurados para receber um endereço IP IPv6 via DHCP. Muitas das imagens Linux na Galeria Azure já estão configuradas para suportar o IPv6 sem modificações. Para mais informações, consulte [a Configuração DHCPv6 para Os VMs Linux](load-balancer-ipv6-for-linux.md)
* Se optar por utilizar uma sonda de saúde com o seu equilibrista de carga, crie uma sonda IPv4 e use-a com os pontos finais IPv4 e IPv6. Se o serviço no seu VM diminuir, tanto os pontos finais IPv4 como IPv6 são retirados da rotação.

Limitações

* Não é possível adicionar regras de equilíbrio de carga IPv6 no portal Azure. As regras só podem ser criadas através do modelo, CLI, PowerShell.
* Não pode atualizar os VM existentes para utilizar endereços IPv6. Tens de lançar novos VMs.
* Um único endereço IPv6 pode ser atribuído a uma única interface de rede em cada VM.
* Os endereços IPv6 públicos não podem ser atribuídos a um VM. Só podem ser atribuídos a um equilibrador de carga.
* Não é possível configurar a procura inversa de DNS para os seus endereços IPv6 públicos.
* Os VMs com os endereços IPv6 não podem ser membros de um Serviço de Nuvem Azure. Podem ser ligados a uma Rede Virtual Azure (VNet) e comunicar entre si através dos seus endereços IPv4.
* Os endereços IPv6 privados podem ser implantados em VMs individuais num grupo de recursos, mas não podem ser implantados num grupo de recursos através de Conjuntos de Escala.
* Os VMs Azure não podem ligar-se ao IPv6 a outros VMs, outros serviços Azure ou dispositivos no local. Só podem comunicar com o equilibrista de carga Azure sobre o IPv6. No entanto, podem comunicar com estes outros recursos utilizando o IPv4.
* A proteção do Grupo de Segurança de Rede (NSG) para iPv4 é suportada em implementações de dupla pilha (IPv4+IPv6). Os NSGs não se aplicam aos pontos finais do IPv6.
* O ponto final do IPv6 no VM não está diretamente exposto à internet. Está atrás de um equilibrador de carga. Apenas as portas especificadas nas regras do balançador de carga estão acessíveis em relação ao IPv6.
* **Atualmente, não** é suportado a alteração do parâmetro IdleTimeout para o IPv6 . O padrão é de quatro minutos.
* Alterar o parâmetro de cargaDistributionMethod para IPv6 não está **atualmente suportado**.
* O IPv6 para Balanceador de Carga Básica está bloqueado a um **SKU dinâmico.**  O IPv6 para um Balanceador de Carga Padrão está bloqueado num SKU **estático.**
* O NAT64 (tradução do IPv6 para o IPv4) não é suportado.
* A fixação de um NIC secundário que se refere a uma sub-rede IPv6 a uma piscina traseira não está **atualmente suportada**.

## <a name="next-steps"></a>Passos seguintes

Saiba como implantar um equilibrador de carga com o IPv6.

* [Disponibilidade de IPv6 por região](https://go.microsoft.com/fwlink/?linkid=828357)
* [Implemente um equilibrador de carga com IPv6 usando um modelo](load-balancer-ipv6-internet-template.md)
* [Coloque um equilibrador de carga com o IPv6 utilizando o Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Coloque um equilibrador de carga com o IPv6 utilizando o Azure CLI](load-balancer-ipv6-internet-cli.md)