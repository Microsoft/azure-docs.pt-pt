---
title: Compreender a utilização de reservas de Azure para a subscrição pay as you go | Documentos da Microsoft
description: Saiba como ler sua utilização para compreender a forma como é aplicada a reserva do Azure para a sua subscrição pay as you go.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: banders
ms.openlocfilehash: a3bacdb19dfe16a63cf1f89ef6fb162d08cb4d32
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883785"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Compreender a utilização de reserva do Azure para a sua subscrição pay as you go

Utilizar Reservationid:{0 partir [página de reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o ficheiro de utilização do [portal de contas do Azure](https://account.azure.com) para avaliar a utilização de reserva.

Se for um cliente com um Enterprise Agreement, veja [compreender a utilização de reserva para inscrição da sua empresa.](billing-understand-reserved-instance-usage-ea.md).

Este artigo pressupõe que a reserva é aplicada a uma única subscrição. Se a reserva é aplicada a mais de uma subscrição, o seu benefício de reserva poderá abranger vários ficheiros CSV de utilização.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Utilização de instâncias de Máquina Virtual reservada

Para as secções seguintes, partem do princípio de que está a executar uma VM do Windows Standard_DS1_v2 na região Leste região dos EUA e sua reservado é de informações de instância VM semelhante a tabela seguinte:

| Campo | Value |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eualeste |

A parte de hardware da VM é abrangida porque a VM implementada corresponde aos atributos de reserva. Para ver qual software do Windows não é abrangido por instância de VM reservada, consulte [os custos de software do Windows de instâncias de VM do Azure reserva](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Secção de instrução do ficheiro CSV para VMs

Esta secção do ficheiro CSV mostra a utilização total para a sua reserva. Aplique o filtro no **subcategoria do medidor** campo que contém **"Reserva-"**. Verá algo semelhante a captura de ecrã seguinte:

![Captura de ecrã de detalhes de utilização de reserva filtrado e custos](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

O **VM Base de reserva** linha tem o número total de horas durante as quais estão abrangidas pela reserva. Essa linha é $0,00 porque a reserva abrange-lo. O **Windows de instâncias reservadas do Windows Server (1 núcleo)** linha abrange o custo de software do Windows.

### <a name="daily-usage-section-of-csv-file"></a>Secção de utilização diária do ficheiro CSV

Filtre **informações adicionais** e escreva na sua **ID de reserva**. Captura de ecrã seguinte mostra os campos relacionados com a reserva.

![Captura de ecrã de detalhes de utilização diária e encargos](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **Reservationid:{0** no **informações adicionais** campo é a reserva aplicado à VM.
2. **ConsumptionMeter** é o ID do medidor para a VM.
3. O **VM Base de reserva** **subcategoria do medidor** linha representa o custo de US $0 na secção de instrução. O custo de executar esta VM já é pago pela reserva.
4. **ID do medidor** é o ID do medidor para a reserva. O custo deste medidor é US $0. Este id de medidor aparece para qualquer VM que é elegível para obter o desconto de reserva.
5. Standard_DS1_v2 é vCPU de uma VM e a VM está implantado sem o benefício híbrido do Azure. Portanto, este medidor abrange o custo adicional do Windows software. Para obter o medidor correspondente, a série D, 1 núcleo de VM, veja [os custos de software do Windows de instâncias de VM do Azure reserva](billing-reserved-instance-windows-software-costs.md). Se tiver o benefício híbrido do Azure, este custo adicional não é aplicado.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Utilização da base de dados do SQL & Cosmos DB reservado reservas de capacidade

As secções seguintes utilizam o Azure SQL Database como exemplo para descrever o relatório de utilização. Pode utilizar os mesmos passos para obter a utilização do Azure Cosmos DB também. 

Partem do princípio de que está a executar uma base de dados SQL Gen 4 na região Leste região dos EUA e suas informações de reserva parece a tabela seguinte:

| Campo | Value |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Quantidade |2|
|Produto| Base de dados SQL Gen 4 (2 núcleos)|
|Região | eualeste |

### <a name="statement-section-of-csv-file"></a>Secção de instrução do ficheiro CSV

Filtre **utilização da instância reservada** nome do medidor e escolha o necessário **categoria do medidor** -base de dados SQL do Azure ou do Azure Cosmos DB. Verá algo semelhante a captura de ecrã seguinte:

![Capacidade de reserva de ficheiro CSV para a base de dados SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

O **utilização da instância reservada** linha tem o número total de horas de núcleo abrangido pela reserva. A taxa é US $0 para esta linha, como a reserva abrangido o custo.

### <a name="detail-section-of-csv-file"></a>Secção de detalhes do ficheiro CSV

Filtre **informações adicionais** e escreva na sua **ID de reserva**. Captura de ecrã seguinte mostra os campos relacionados com a reserva de capacidade de base de dados de SQL reservadas.

![Capacidade de reserva de ficheiro CSV para a base de dados SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **Reservationid:{0** no **informações adicionais** campo é a reserva de capacidade de base de dados de SQL reservadas que é aplicada ao recurso de base de dados SQL.
2. **ConsumptionMeter** é o ID do medidor para o recurso de base de dados SQL.
3. O **Id do medidor** é o medidor de reserva. O custo deste medidor é US $0. Quaisquer recursos de base de dados SQL que se qualificar para o desconto de reserva mostra este ID de medidor no ficheiro CSV.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).


