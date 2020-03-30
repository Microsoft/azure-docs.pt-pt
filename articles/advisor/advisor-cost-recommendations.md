---
title: Reduzir os custos de serviço utilizando o Azure Advisor
description: Utilize o Azure Advisor para otimizar o custo das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0237feab59551ecab87d78b0d4d66b9fc7b47e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259698"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>Reduzir os custos de serviço utilizando o Azure Advisor

O Advisor ajuda-o a otimizar e reduzir os seus gastos azure globais identificando recursos inativos e subutilizados.Pode obter recomendações de custos a partir do separador **Custo** no painel Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Otimizar a despesa da máquina virtual ao redimensionar ou encerrar instâncias subutilizadas 

Embora certos cenários de aplicação possam resultar em baixa utilização por design, você pode muitas vezes economizar dinheiro gerindo o tamanho e número das suas máquinas virtuais. Os modelos de avaliação avançada saem para a consultoria, quando o P95th do valor máximo de utilização do CPU é inferior a 3% e a utilização da rede é inferior a 2% durante um período de 7 dias. As máquinas virtuais são consideradas para o tamanho certo quando é possível encaixar a carga atual num SKU menor (dentro da mesma família SKU) ou num número menor # de forma a que a carga atual não ultrapasse os 80% de utilização quando não se trata de cargas de trabalho e não superior a 40% quando se trata de carga sucinta. Aqui, o tipo de carga de trabalho é determinado através da análise das características de utilização do CPU da carga de trabalho.

As ações recomendadas são desligadas ou redimensionadas, especificamente para os recursos recomendados. O consultor mostra-lhe a estimativa de poupança de custos para as ações recomendadas - redimensionar ou encerrar. Além disso, para redimensionar a ação recomendada, o Advisor fornece informações atuais e direcionadas para o SKU. 

Se pretender ser mais agressivo na identificação de máquinas virtuais subutilizadas, pode ajustar a regra de utilização do CPU numa base de subscrição.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reduzir custos eliminando circuitos ExpressRoute não provisionados

O Advisor identifica circuitos ExpressRoute que estão no estatuto de fornecedor do *Not Provisioned* há mais de um mês, e recomenda a apagar o circuito se não estiver a planear fornecer o circuito com o seu fornecedor de conectividade.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Reduzir custos ao eliminar ou reconfigurar gateways de rede virtual

O Advisor identifica os portões de rede virtuais que estão inativos há mais de 90 dias. Uma vez que estes gateways são faturados de hora em hora, deve considerar reconfigurá-los ou abater se não pretender mais usá-los. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Comprar instâncias de máquina virtual reservadas para poupar dinheiro sobre os custos pay as you go

O Advisor irá rever o uso da sua máquina virtual nos últimos 30 dias e determinar se pode economizar dinheiro comprando uma reserva Azure. O advisor mostrar-lhe-á as regiões e tamanhos onde potencialmente tem mais poupanças e irá mostrar-lhe as poupanças estimadas de reservas de compra. Com reservas Azure, pode pré-comprar os custos base das suas máquinas virtuais. Os descontos aplicar-se-ão automaticamente a VMs novos ou existentes que tenham o mesmo tamanho e região que as suas reservas. [Saiba mais sobre as Instâncias VM reservadas do Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

O consultor irá também notificá-lo das instâncias reservadas que tem que expirarão nos próximos 30 dias. Recomendará que adquira novas instâncias reservadas para evitar pagar preços de pagamento.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Eliminar endereços IP públicos não associados para economizar dinheiro

O Advisor identifica endereços IP públicos que não estão atualmente associados a recursos Azure, tais como Balancers de Carga ou VMs. Estes endereços IP públicos vêm com uma taxa nominal. Se não planeia usá-los, a sua redução pode resultar em poupanças de custos.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Eliminar pipelines do Azure Data Factory com falhas

O Azure Advisor detetará os gasodutos azure Data Factory que falham repetidamente e recomendam que resolva os problemas ou elimine os gasodutos em falha se já não forem necessários. Será cobrado por estes oleodutos, mesmo que não estejam a servi-lo enquanto estão a falhar. 

## <a name="use-standard-snapshots-for-managed-disks"></a>Utilize instantâneos padrão para discos geridos
Para poupar 60% no custo, recomendamos que guarde os instantâneos no Armazenamento Standard, independentemente do tipo de armazenamento do disco principal. Esta opção é a opção padrão para instantâneos de Discos Geridos. O Azure Advisor identificará instantâneos que são armazenados de Armazenamento Premium e recomendará a migração do seu instantâneo de Premium para O Armazenamento Padrão. [Saiba mais sobre os preços do disco gerido](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="utilize-lifecycle-management"></a>Utilizar a Gestão do Ciclo de Vida
O Azure Advisor utilizará informações sobre a contagem de objetos de armazenamento de blob Azure, tamanho total e transações para detetar se uma ou mais das suas contas de armazenamento seriam mais adequadas para permitir a gestão do ciclo de vida a dados de nível. Irá instá-lo a criar regras de Gestão de Ciclo sonoridade para tieribilizar automaticamente os seus dados para Cool ou Archive para otimizar os seus custos de armazenamento, mantendo os seus dados no armazenamento de blob Azure para compatibilidade com aplicações.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Como aceder às recomendações de custos no Consultor Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Procure e selecione [**Advisor**](https://aka.ms/azureadvisordashboard) a partir de qualquer página.

1. No painel **Advisor,** selecione o separador **Custo.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Alta Disponibilidade Do Conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
