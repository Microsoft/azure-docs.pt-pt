---
title: Utilização de reservas do Azure de uma subscrição individual
description: Saiba como ler a sua utilização para compreender como é aplicada a reserva do Azure para a sua subscrição individual com tarifas pay as you go.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 522b67e62e7ad8560bb48e8d056ca6daeeb45251
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350913"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Compreender a utilização de reservas do Azure para a sua subscrição individual com a subscrição de tarifas pay as you go

Utilize o ReservationId (ID da Reserva) da [página da reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o ficheiro de utilização do [portal de Contas do Azure](https://account.azure.com) para avaliar a utilização da sua reserva.

Se for um cliente com um Contrato Enterprise, veja como [Compreender a utilização da reserva da sua inscrição Enterprise](understand-reserved-instance-usage-ea.md).

Este artigo pressupõe que a reserva está aplicada a uma única subscrição. Se a reserva estiver aplicada a mais do que uma subscrição, o benefício da reserva poderá abranger múltiplos ficheiros CSV de utilização.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Utilização para Instâncias de Máquina Virtual Reservada

Para as secções a seguir, suponha que está a executar uma VM Windows Standard_DS1_v2 na região leste dos E.U.A. e que as informações da instância de VM reservada são semelhantes à tabela a seguir:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eastus |

A parte de hardware da VM está abrangida porque a VM implementada coincide com os atributos da reserva. Para ver qual o software do Windows que não está abrangido pela instância de VM reservada, veja [Custos de software do Windows de Instâncias de VM Reservadas do Azure](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Secção de declaração do ficheiro CSV para VMs

Esta secção do ficheiro CSV mostra a utilização total da sua reserva. Aplique o filtro no campo **Meter Subcategory** (Subcategoria do Medidor) que contém **"Reservation-"** (Reserva-). Verá algo semelhante à seguinte captura de ecrã:

![Captura de ecrã de detalhes de utilização e custos da reserva filtrada](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A linha **Reservation-Base VM** (Reserva-Base VM) tem o número total de horas abrangidas pela reserva. Esta linha mostra 0,00 $ porque é abrangida pela reserva. A linha **Reservation-Windows Svr (1 Core)** (Reserva-Windows Svr [1 Núcleo]) abrange o custo do software Windows.

### <a name="daily-usage-section-of-csv-file"></a>Secção de utilização diária do ficheiro CSV

Filtre por **Additional Info** (Informações Adicionais) e escreva o seu **Reservation ID** (ID da Reserva). A captura de ecrã a seguir mostra os campos relacionados com a reserva.

![Captura de ecrã de detalhes e custos de utilização diária](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. O **ReservationId** (ID da Reserva) no campo **Additional Info** (Informações Adicionais) é a reserva aplicada à VM.
2. O **ConsumptionMeter** (Medidor de Consumo) é o ID do medidor para a VM.
3. A linha **Instâncias Reservadas de VM de Base** em **Subcategoria do Medidor** representa o custo 0 $ na secção do extrato. O custo da execução desta VM já está pago pela reserva.
4. O **Meter ID** (ID do Medidor) é o ID do medidor para a reserva. O custo deste medidor é de 0 $. Este ID de medidor é apresentado para qualquer VM elegível para o desconto de reserva.
5. Standard_DS1_v2 é uma VM com um vCPU e é implementada sem Benefício Híbrido do Azure. Portanto, este medidor abrange o custo adicional do software Windows. Para localizar o medidor correspondente à VM com 1 núcleo da série D, veja [Custos de software Windows de Instâncias de VM Reservadas do Azure](reserved-instance-windows-software-costs.md). Se tiver o Benefício Híbrido do Azure, este custo adicional não será aplicado.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Utilização para reservas de Base de Dados SQL e Cosmos DB

As secções a seguir utilizam a Base de Dados SQL do Azure como exemplo para descrever o relatório de utilização. Pode também utilizar os mesmos passos para obter a utilização do Azure Cosmos DB.

Suponha que está a executar uma Base de Dados SQL de 4.ª geração na região leste dos E.U.A. e que as informações da reserva são semelhantes à tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Quantidade |2|
|Produto| SQL Database Gen 4 (2 Core)|
|Região | eastus |

### <a name="statement-section-of-csv-file"></a>Secção de declaração do ficheiro CSV

Filtre pelo nome do medidor **Utilização de Instâncias Reservadas** e selecione a **Categoria do Medidor** necessária, Base de Dados SQL do Azure ou Azure Cosmos DB. Verá algo semelhante à seguinte captura de ecrã:

![Captura de ecrã que mostra a entrada Categoria do Medidor.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

A linha **Reserved Instance Usage** (Utilização de Instâncias Reservadas) tem o número total de horas principais abrangidas pela reserva. A tarifa é de 0 $ para esta linha uma vez que a reserva abrangeu o custo.

### <a name="detail-section-of-csv-file"></a>Secção detalhada do ficheiro CSV

Filtre por **Additional Info** (Informações Adicionais) e escreva o seu **Reservation ID** (ID da Reserva). A captura de ecrã a seguir mostra os campos relacionados com a reserva de capacidade reservada da Base de Dados SQL.

![Captura de ecrã que mostra detalhes de um ficheiro CSV de capacidade reservada.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. O **ReservationId** no campo **Informações Adicionais** corresponde à reserva de capacidade reservada da Base de Dados SQL aplicada ao recurso da Base de Dados SQL.
2. O **ConsumptionMeter** (Medidor de Consumo) é o ID do medidor para o recurso da Base de Dados SQL.
3. O **Meter Id** (ID do Medidor) é o medidor da reserva. O custo deste medidor é de 0 $. Todos os recursos da Base de Dados SQL elegíveis para o desconto de reserva mostram este ID do medidor no ficheiro CSV.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender como o desconto das reservas é aplicado](../manage/understand-vm-reservation-charges.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Custos de software Windows não incluídos nas Reservas](reserved-instance-windows-software-costs.md)