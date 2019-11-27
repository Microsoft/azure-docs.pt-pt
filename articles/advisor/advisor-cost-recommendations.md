---
title: Reduzir os custos de serviço usando o Azure Advisor | Microsoft Docs
description: Use o Azure Advisor para otimizar o custo de suas implantações do Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: 9f074c8077f41392fa9d45022a6a7a537964afa9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74180591"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduzir os custos de serviço usando o Azure Advisor

O Advisor ajuda a otimizar e reduzir o gasto geral do Azure identificando recursos ociosos e subutilizados. Você pode obter recomendações de custo na guia **custo** no painel do Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar o gasto de máquinas virtuais redimensionando ou desligando instâncias subutilizadas 

Embora determinados cenários de aplicativos possam resultar em baixa utilização por design, muitas vezes você pode economizar dinheiro Gerenciando o tamanho e o número de suas máquinas virtuais. Os modelos de avaliação avançada do Advisor consideram que máquinas virtuais sejam desligadas quando P95th do máximo do valor máximo da utilização da CPU for menor que 3% e a utilização da rede for inferior a 2% em um período de 7 dias. As máquinas virtuais são consideradas para o tamanho certo quando é possível ajustar a carga atual em uma SKU menor (dentro da mesma família de SKUs) ou um número menor de instância, de modo que a carga atual não ultrapasse 80% de utilização quando cargas de trabalho não voltadas para o usuário e não acima de 40% quando a carga de trabalho voltada para o usuário. Aqui, o tipo de carga de trabalho é determinado analisando as características de utilização da CPU da carga de trabalho.

As ações recomendadas são desligadas ou redimensionadas, específicas ao recurso recomendado para o. O Advisor mostra a economia de custos estimada para ações recomendadas – redimensionar ou desligar. Além disso, para a ação recomendada de redimensionamento, o Advisor fornece informações de SKU atuais e de destino. 

Se você quiser ser mais agressivo na identificação de máquinas virtuais subutilizadas, poderá ajustar a regra de utilização da CPU por assinatura.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir custos eliminando circuitos de ExpressRoute não provisionados

O Advisor identifica os circuitos do ExpressRoute que estão no status do provedor de *não provisionado* por mais de um mês e recomenda a exclusão do circuito se você não estiver planejando provisionar o circuito com seu provedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir custos ao excluir ou reconfigurar gateways de rede virtual ociosas

O Advisor identifica os gateways de rede virtual que ficaram ociosos por mais de 90 dias. Como esses gateways são cobrados por hora, você deve considerar reconfigurá-los ou excluí-los se não pretende mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Comprar instâncias de máquinas virtuais reservadas para economizar dinheiro nos custos pagos conforme o uso

O Advisor examinará o uso da sua máquina virtual nos últimos 30 dias e determinará se você pode economizar dinheiro comprando uma reserva do Azure. O Advisor mostrará as regiões e os tamanhos onde você potencialmente tem a maior economia e mostrará a você a economia estimada de reservas de compra. Com as reservas do Azure, você pode adquirir previamente os custos base para suas máquinas virtuais. Os descontos serão aplicados automaticamente a VMs novas ou existentes que tenham o mesmo tamanho e região que suas reservas. [Saiba mais sobre as instâncias de VM reservadas do Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O Advisor também notificará você das instâncias reservadas que você tem que expirarão nos próximos 30 dias. Será recomendável que você compre novas instâncias reservadas para evitar pagar o preço pago conforme o uso.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Exclua endereços IP públicos não associados para economizar dinheiro

O Advisor identifica endereços IP públicos que não estão associados atualmente a recursos do Azure, como balanceadores de carga ou VMs. Esses endereços IP públicos vêm com um custo nominal. Se você não planeja usá-las, excluí-las pode resultar em economia de custos.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Excluir Azure Data Factory pipelines que estão falhando

O Azure Advisor detectará Azure Data Factory pipelines que falham repetidamente e recomendam que você resolva os problemas ou exclua os pipelines com falha se eles não forem mais necessários. Você será cobrado por esses pipelines mesmo se eles não estiverem servindo você enquanto estiverem falhando. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Usar instantâneos padrão para Managed Disks
Para economizar 60% do custo, é recomendável armazenar seus instantâneos no armazenamento padrão, independentemente do tipo de armazenamento do disco pai. Essa é a opção padrão para instantâneos de Managed Disks. O assistente do Azure identificará os instantâneos armazenados no armazenamento Premium e recomendará migrar seu instantâneo do Premium para o armazenamento Standard. [Saiba mais sobre os preços do disco gerenciado](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como acessar as recomendações de custo no Azure Advisor

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel do Advisor, clique na guia **custo** .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Começar](advisor-get-started.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
