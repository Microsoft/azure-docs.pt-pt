---
title: Compreender a utilização de reservas de Azure para o Enterprise | Documentos da Microsoft
description: Saiba como ler sua utilização para compreender a forma como é aplicada a reserva do Azure para a inscrição da sua empresa.
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
ms.openlocfilehash: 66fd42e5aa110b4c9ef686b1d66429ff7dbaf550
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857696"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Compreender a utilização de reserva do Azure para a inscrição da sua empresa

Utilize o **Reservationid:{0** da [página de reservas](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o ficheiro de utilização do [portal EA](https://ea.azure.com) para avaliar a utilização de reserva. Também pode ver a utilização de reserva na secção de resumo de utilização de [portal EA](https://ea.azure.com).

Se comprasse a reserva num contexto de faturação pay as you go, consulte [compreender a utilização de reserva para a sua subscrição pay as you go.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Utilização de instâncias de máquinas de virtuais reservadas

Para as secções seguintes, partem do princípio de que está a executar uma VM do Windows Standard_D1_v2 na região Leste região dos EUA e suas informações de reserva parece a tabela seguinte:

| Campo | Value |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eualeste |

A parte de hardware da VM é abrangida porque a VM implementada corresponde aos atributos de reserva. Para ver qual software do Windows não é abrangido pela reserva, consulte [os custos de software do Windows de instâncias de VM do Azure reserva](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Utilização no ficheiro CSV para instâncias de VM reservadas

Pode transferir o ficheiro CSV de utilização empresarial a partir do portal da empresa. No ficheiro CSV, filtre **informações adicionais** e escreva na sua **Reservationid:{0**. Captura de ecrã seguinte mostra os campos relacionados com a reserva:

![Csv de Enterprise Agreement (EA) para a reserva do Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **Reservationid:{0** no **informações adicionais** campo representa a reserva aplicado à VM.
2. **ConsumptionMeter** é o ID do medidor para a VM.
3. **ID do medidor** é o medidor de reserva com o custo de US $0. O custo da VM em execução é pago por instância de VM reservada.
4. Standard_D1 é vCPU de uma VM e a VM está implantado sem o benefício híbrido do Azure. Portanto, este medidor abrange o custo adicional de software do Windows. Para obter o medidor correspondente para a série D 1 núcleo de VM, veja [os custos de software do Windows de instâncias de VM do Azure reserva](billing-reserved-instance-windows-software-costs.md).  Se tiver o benefício híbrido do Azure, não é aplicado este custo adicional.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Utilização da base de dados do SQL & Cosmos DB reservado reservas de capacidade

As secções seguintes utilizam o Azure SQL Database como exemplo para descrever o relatório de utilização. Pode utilizar os mesmos passos para obter a utilização do Azure Cosmos DB também. 

Partem do princípio de que está a executar uma base de dados SQL Gen 4 na região Leste região dos EUA e suas informações de reserva parece a tabela seguinte:

| Campo | Value |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Quantidade |2|
|Produto| Base de dados SQL Gen 4 (2 núcleos)|
|Região | eualeste |

### <a name="usage-in-csv-file"></a>Utilização no ficheiro CSV 

Filtrar **informações adicionais** e escreva na sua **ID de reserva**e escolha o necessária **categoria do medidor** -base de dados SQL do Azure ou do Azure Cosmos DB. Captura de ecrã seguinte mostra os campos relacionados com a reserva.

![Capacidade de reserva de csv de Enterprise Agreement (EA) para a base de dados SQL](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **Reservationid:{0** no **informações adicionais** campo é a reserva aplicado para o recurso de base de dados SQL.
2. **ConsumptionMeter** é o ID do medidor para o recurso de base de dados SQL.
3. **ID do medidor** é o medidor de reserva com o custo de US $0. Qualquer recurso de base de dados SQL é elegível para a reserva mostra este ID de medidor no ficheiro CSV.

## <a name="usage-summary-page-in-enterprise-portal"></a>Página de resumo de utilização no portal da empresa

A utilização de reserva do Azure também apresentada na secção de resumo de utilização do portal da empresa: ![Resumo de utilização de Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Não é cobrada para o componente de hardware da VM como está abrangido por reserva. Para uma reserva de base de dados SQL, verá uma linha com **nome do serviço** como base de dados do Azure SQL reservadas utilização da capacidade.
2. Neste exemplo, não tem o benefício híbrido do Azure, para que lhe é cobrada para o software do Windows utilizado com a VM.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md) 
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

