---
title: Instâncias reservadas de VM do Azure EA
description: Este artigo resume como as reservas do Azure para instâncias reservadas de VM o podem ajudar a economizar dinheiro com a sua inscrição Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 7c8a35e7c8ecd0952f8e02b66c5071253d9b0445
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348930"
---
# <a name="azure-ea-vm-reserved-instances"></a>Instâncias reservadas de VM do Azure EA

Este artigo resume como as reservas do Azure para instâncias reservadas de VM o podem ajudar a economizar dinheiro com a sua inscrição Enterprise. Para obter mais informações sobre as reservas, veja [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Trocas e reembolsos das reservas

Pode trocar uma reserva por outra reserva do mesmo tipo. Também é possível reembolsar uma reserva, até 50 000 USD por ano, se já não precisar da mesma. O portal do Azure pode ser utilizado para trocar ou reembolsar uma reserva. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Reembolsos parciais

Emitiremos um reembolso parcial nos casos em que os clientes EA devolvam reservas que tenham sido compradas com alocação de utilização excedida e não alocação monetária.

O reembolso será apresentado no EA Portal como um ajuste negativo no mês anterior e como ajuste positivo no mês atual. É apresentado de forma semelhante a uma troca de reservas. O memorando do crédito fará referência ao número da fatura original. Por esse motivo, para reconciliar a compra inicial com o memorando de crédito, consulte o número da fatura original.

## <a name="reservation-costs-and-usage"></a>Custos de reserva e utilização

Os clientes do contrato Enterprise podem ver os dados de custo e utilização nas APIs REST e no portal do Azure. Nos custos de reserva e utilização, pode:

- Obter os dados das compras de reservas.
- Saber qual subscrição, grupo de recursos ou recurso utilizou uma reserva.
- Estorno relativo a utilização de reserva.
- Calcular as poupanças das reservas.
- Obter dados de subutilização das reservas.
- Amortizar os custos das reservas.

Para obter mais informações sobre custos de reserva e utilização, veja [Get Enterprise Agreement reservation costs and usage](../reservations/understand-reserved-instance-usage-ea.md) (Compreender custos de reserva e utilização do Contrato Enterprise).

Para obter mais informações sobre preços, veja [Preços – Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços – Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Suporte de API de instâncias reservadas

Utilize as APIs do Azure para obter informações para a sua organização sobre as reservas de software ou serviço do Azure de forma programática. Por exemplo, utilize as APIs para:

- Encontrar reservas para comprar
- Comprar uma reserva
- Ver reservas compradas
- Ver e gerir o acesso a reservas
- Dividir ou unir reservas
- Alterar o âmbito das reservas

Para obter mais informações, veja [APIs para automatização de reservas do Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Instâncias de máquinas virtuais reservadas do Azure

As instâncias reservadas podem reduzir os custos com máquinas virtuais até 72% em relação aos preços Pay As You Go em todas as VMs ou permitir poupanças até 82% quando combinadas com o benefício híbrido do Azure. As instâncias reservadas ajudam a gerir as cargas de trabalho, o orçamento e a fazer melhores previsões com um pagamento à cabeça para períodos de um ano ou de três anos. Também pode trocar ou cancelar as reservas à medida que as necessidades de negócio mudam.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Como comprar instâncias de máquinas virtuais reservadas

Para comprar uma instância de máquina virtual reservada do Azure, um administrador de inscrição do Azure Enterprise tem de ativar a opção de compra _Reservar Instância_. A opção está disponível na secção _Detalhes da Inscrição_ no separador _Inscrição_ no [EA Portal do Azure](https://ea.azure.com/).

Quando a inscrição EA estiver ativada para adicionar instâncias reservadas, qualquer proprietário de conta com uma subscrição ativa associada à inscrição EA pode comprar uma instância de máquina virtual reservada no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, veja [Prepay for virtual machines and save money with Reserved Virtual Machine Instances](../../virtual-machines/prepay-reserved-vm-instances.md) (Efetuar pré-pagamento de máquinas virtuais e economizar com Instâncias de Máquina Virtual Reservadas).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Como ver os detalhes de compra de instâncias reservadas

Pode ver os detalhes de compra da sua instância reservada através do menu _Reservas_ no lado esquerdo do [portal do Azure](https://aka.ms/reservations) ou a partir do [Azure EA Portal](https://ea.azure.com/). Selecione **Relatórios** no menu do lado esquerdo e desloque o ecrã para baixo até à secção _Encargos por Serviços_, no separador _Resumo de Utilização_. Desloque-se até à parte inferior da secção e as suas compras e utilização de instâncias reservadas serão listadas no fim, conforme indicado pela designação `1 year` ou `3 years` ao lado do nome do serviço. Por exemplo: `Standard_DS1_v2 eastus 1 year` ou `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Como posso alterar a subscrição associada à instância reservada ou transferir os benefícios da instância reservada para uma subscrição na mesma conta?

Pode alterar a subscrição que recebe os benefícios de instância reservada ao:

- Iniciar sessão no [portal do Azure](https://aka.ms/reservations).
- Atualizar o âmbito da subscrição aplicada ao associar uma subscrição diferente na mesma conta.

Para obter mais informações sobre como alterar o âmbito de uma reserva, veja [Alterar o âmbito da reserva](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Como ver os detalhes de utilização de instâncias reservadas

Pode ver os detalhes de utilização das suas instâncias reservadas no [portal do Azure](https://aka.ms/reservations) ou no [Azure EA Portal](https://ea.azure.com/) (no caso dos clientes EA que tenham acesso para ver informações de faturação) em _Relatórios_ > _Resumo de Utilização_ > _Encargos por Serviços_. As suas instâncias reservadas podem ser identificadas como nomes de serviço que contêm "Reserva". Por exemplo: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Os detalhes de utilização e o CSV de transferência de relatório avançado contêm informações adicionais sobre a utilização de instâncias reservadas. O campo _Informações Adicionais_ ajuda a identificar a utilização da instância reservada.

Se não utilizou o benefício híbrido do Azure para comprar instâncias de VM reservadas do Azure, as instâncias reservadas emitirão dois medidores (hardware e software). Quando utiliza o benefício híbrido do Azure para comprar a instância reservada, não verá o medidor de software nos detalhes de utilização da instância reservada.

### <a name="reserved-instance-billing"></a>Faturação de instâncias reservadas

Para clientes empresariais, o Pré-pagamento do Azure serve para comprar instâncias de VM reservadas do Azure. Se a sua inscrição tiver um saldo do Pré-pagamento do Azure suficiente para cobrir a compra de instâncias reservadas, o montante será deduzido do seu saldo do Pré-pagamento do Azure e não receberá uma fatura da compra.

Nos cenários em que os clientes Azure EA tiverem utilizado a totalidade do Pré-pagamento do Azure, estes continuam a poder comprar instâncias reservadas e essas compras serão faturadas na fatura de utilização excedida seguinte. A utilização excedida de instâncias reservadas, se existir, fará parte da fatura de utilização excedida normal.

### <a name="reserved-instance-expiration"></a>Expiração de instâncias reservadas

Receberá notificações por e-mail 30 dias antes da reserva e na data de expiração. Assim que a reserva expirar, as VMs implementadas continuam a ser executadas e são faturadas a um preço pay as you go. Para obter mais informações, veja a oferta de [Instâncias de Máquina Virtual Reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as Reservas do Azure, veja [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md).
- Para saber mais sobre custos de reserva e utilização, veja [Get Enterprise Agreement reservation costs and usage](../reservations/understand-reserved-instance-usage-ea.md) (Compreender custos de reserva e utilização do Contrato Enterprise).
- Para obter mais informações sobre preços, veja [Preços – Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ou [Preços – Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).