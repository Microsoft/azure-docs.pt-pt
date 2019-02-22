---
title: Reduzir os custos de serviço utilizando o Assistente do Azure | Documentos da Microsoft
description: Utilize o Assistente do Azure para otimizar o custo das suas implementações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: cc5c4310d5f9886518bee6612113fba174130bec
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648721"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduzir os custos de serviço utilizando o Assistente do Azure

O assistente o ajuda a otimizar e reduzir sua gerais com o Azure gasta ao identificar recursos subutilizados e inativos. Pode obter custar recomendações do **custo** separador no dashboard do Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar a máquina virtual passar por redimensionamento ou encerrar subutilizadas instâncias 

Embora determinados cenários de aplicação podem resultar numa utilização reduzida por design, pode poupar dinheiro várias vezes ao gerir o tamanho e número de máquinas virtuais. O assistente monitoriza a utilização da máquina virtual durante sete dias e, em seguida, identifica as máquinas virtuais de baixa utilização. Virtual máquinas são consideradas baixa utilização se a utilização da CPU é de 5% ou menos e a utilização de rede é inferior a % de 2 ou se a atual carga de trabalho pode ser atendida por um tamanho mais pequeno da máquina virtual.

O assistente mostra o custo estimado de continuar a executar a máquina virtual, para que pode optar por encerrá-lo ou redimensioná-la.

Se pretender que seja mais agressiva na identificação de máquinas virtuais subutilizadas, pode ajustar a regra de utilização de CPU média numa base por subscrição.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir os custos, eliminando o circuito do ExpressRoute não aprovisionado

Advisor identifica os circuitos do ExpressRoute que já estão no estado de fornecedor *não aprovisionado* para mais de um mês e recomenda eliminando o circuito se não estiver a planear aprovisioná-lo com a conectividade fornecedor.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir os custos, eliminar ou reconfigurar gateways de rede virtual Inativos

Advisor identifica as portas de rede virtual que tem estado Inativas durante mais de 90 dias. Uma vez que esses gateways são cobradas à hora, deve considerar a reconfiguração ou excluí-los, se não pretender utilizá-los mais. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Comprar instâncias de máquinas virtuais reservadas para poupar dinheiro sobre os custos de pay as you go

O assistente irá rever a utilização da máquina virtual durante os últimos 30 dias e determinar se pode poupar dinheiro ao comprar uma reserva do Azure. O assistente mostrará a os regiões e os tamanhos em que potencialmente tem as maioria das poupanças e irá mostrar-lhe a poupança estimada na compra de reservas. 

Com reservas do Azure, pode comprar previamente os custos base para as suas máquinas virtuais. Descontos serão aplicado automaticamente para as VMs de novas ou existentes com o mesmo tamanho e a região, como as reservas. [Saiba mais sobre as instâncias de VM reservadas do Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Assistente do Azure

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  No dashboard do assistente, clique nas **custo** separador.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as recomendações do assistente, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de desempenho do Assistente](advisor-cost-recommendations.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-cost-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-cost-recommendations.md)
