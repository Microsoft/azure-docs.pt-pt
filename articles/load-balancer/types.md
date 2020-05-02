---
title: Tipos de balanceadores de carga azure
description: Visão geral dos tipos de balanceadores de carga Azure
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629974"
---
# <a name="azure-load-balancer-types"></a>Tipos de balanceadores de carga azure

O Azure Load Balancer tem dois tipos e dois SKU's.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Equilibrador de carga pública

Um equilibrador de carga pública mapeia o IP público e o porto de tráfego de entrada para o IP privado e porto do VM. Os mapas do equilíbrio de carga são contrários para o tráfego de resposta do VM. Pode distribuir tipos específicos de tráfego em vários VMs ou serviços aplicando regras de equilíbrio de carga. Por exemplo, pode distribuir a carga do tráfego de pedidos da Web entre múltiplos servidores Web.

>[!NOTE]
>Só pode implementar um equilibrador de carga pública e um equilibrador de carga interna por conjunto de disponibilidade.

O valor seguinte mostra um ponto final equilibrado em carga para o tráfego web que é partilhado entre três VMs para o público e porta TCP 80. Estas três VMs estão num conjunto com carga balanceada.

![Exemplo de equilibrador de carga pública](./media/load-balancer-overview/load-balancer.png)

*Figura: Equilibrar o tráfego web utilizando um equilibrador de carga pública*

Os clientes da Internet enviam pedidos de página web para o endereço IP público de uma aplicação web na porta 80 do TCP. O Azure Load Balancer distribui os pedidos pelos três VMs no conjunto equilibrado em carga. Para obter mais informações sobre algoritmos de equilíbrio de carga, consulte os conceitos de [balanceadores de carga.](concepts.md)

O Azure Load Balancer distribui o tráfego da rede de forma igual entre várias instâncias vm por padrão. Também pode configurar a afinidade de sessão. Para mais informações, consulte Configure o modo de [distribuição para O Equilíbrio de Carga Azure](load-balancer-distribution-mode.md).

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Equilibrador de carga interna

Um equilibrador de carga interna distribui tráfego a recursos que estão dentro de uma rede virtual. O Azure restringe o acesso aos endereços IP front-end de uma rede virtual que são equilibradas em carga. 

Os endereços IP front-end e as redes virtuais nunca são diretamente expostos a um ponto final da Internet. As aplicações de linha de negócio internas são executadas no Azure e acedidas de dentro do Azure ou a partir de recursos no local.

Um equilibrador de carga interno permite os seguintes tipos de equilíbrio de carga:

* **Dentro de uma rede virtual**: O equilíbrio de carga dos VMs na rede virtual para um conjunto de VMs que estão na mesma rede virtual.
* **Para uma rede virtual transversal**: O equilíbrio de carga dos computadores no local para um conjunto de VMs que se encontram na mesma rede virtual.
* **Para aplicações de vários níveis**: Equilibre a carga para aplicações multi-camadas viradas para a Internet, onde os níveis de back-end não estejam virados para a Internet. Os níveis de back-end requerem o equilíbrio da carga de tráfego a partir do nível virado para a Internet. Veja a próxima figura.
* **Para aplicações de linha de negócio**: balanceamento de carga para aplicações de linha de negócio que estão alojadas no Azure sem hardware ou software adicional de balanceador de carga. Este cenário inclui servidores no local que estão no conjunto de computadores cujo tráfego é equilibrado.

![Exemplo de Balanceador de Carga Interno](./media/load-balancer-overview/load-balancer.png)

*Figura: Equilibrar aplicações a vários níveis utilizando equilibrantes de carga públicos e internos*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Comparação de SKUs do Balanceador de Carga

O equilibrador de carga suporta as SKUs Básicas e Standard. Estas SKUs diferem na escala de cenários, funcionalidades e preços. Qualquer cenário que seja possível com o equilíbrio de carga Basic pode ser criado com o equilibrador de carga Standard.

Para comparar e compreender as diferenças, veja a tabela seguinte. Para mais informações, consulte a visão geral do Equilíbrio de [Carga Padrão Azure](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft recomenda um equilíbrio de carga Standard.
As VMs autónomas, os conjuntos de disponibilidade e os conjuntos de dimensionamento de máquinas virtuais só podem ser ligados a um SKU, nunca a ambos. O equilibrador de carga e o endereço IP público SKU devem coincidir quando os utilizar com endereços IP públicos. O equilibrador de carga e as IP SKUs públicas não são mutáveis.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Para obter mais informações, consulte [os limites do equilíbrio de carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Relativamente aos detalhes do Balanceador de Carga Standard, veja a [descrição geral](load-balancer-standard-overview.md), os [preços](https://aka.ms/lbpricing) e o [SLA](https://aka.ms/lbsla).

## <a name="next-steps"></a>Passos seguintes

- Consulte [Criar um Balancer de Carga Padrão público](quickstart-load-balancer-standard-public-portal.md) para começar com o uso de um Balancer de Carga.
- Saiba mais sobre a utilização de Zonas de [Equilíbrio de Carga Padrão e De Disponibilidade.](load-balancer-standard-availability-zones.md)
- Saiba mais sobre sondas de [saúde.](load-balancer-custom-probe-overview.md)
- Aprenda a utilizar o [Balancer de Carga para ligações de saída](load-balancer-outbound-connections.md).
- Saiba mais sobre o [Balancer de Carga Standard com regras](load-balancer-ha-ports-overview.md)de equilíbrio de carga ha ports .
- Saiba mais sobre grupos de [segurança de rede.](../virtual-network/security-overview.md)