---
title: Como é aplicado um desconto de reserva do Azure
description: Este artigo ajuda a compreender como são geralmente aplicados os descontos de instância reservada.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574877"
---
# <a name="how-a-reservation-discount-is-applied"></a>Como é aplicado um desconto de reserva

Este artigo ajuda a compreender como são geralmente aplicados os descontos de instância reservada. O desconto de reserva é aplicado à utilização de recursos que corresponder aos atributos por si selecionados quando comprar a reserva. Os atributos incluem o âmbito no qual as VMs, as bases de dados SQL, o Azure Cosmos DB ou outros recursos são executados. Por exemplo, se quiser um desconto de reserva para quatro máquinas virtuais Standard D2 na região E.U.A. Oeste, selecione a subscrição onde as VMs estão a ser executadas.

Um desconto de reserva é "*use-it-or-lose-it*" (utilizar ou perder). Se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

Por exemplo, poderá criar um recurso mais tarde e ter uma reserva correspondente subutilizada. O desconto de reserva é aplicado automaticamente ao novo recurso correspondente.

Se as máquinas virtuais estiverem a ser executadas em subscrições diferentes na sua inscrição/conta, selecione o âmbito como sendo partilhado. O âmbito partilhado permite que o desconto de reserva seja aplicado em várias subscrições. Pode sempre alterar o âmbito após comprar uma reserva. Para obter mais informações, veja [Gerir o Azure Reservations](manage-reserved-vm-instance.md).

Um desconto de reserva só é aplicado a recursos associados a um Contrato Enterprise, um Contrato de Cliente da Microsoft, CSPs ou subscrições com taxas pay as you go. Os recursos executados numa subscrição com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando o termo de reserva expira

No final do período de reserva, o desconto sobre a faturação expira e os recursos passam a ser faturados com base no preço pay as you go. Por predefinição, as reservas não estão definidas para renovação automática. Pode optar por ativar a renovação automática de uma reserva ao selecionar a opção nas definições de renovação. Com a renovação automática, uma reserva de substituição será comprada quando a reserva existente expirar. Por predefinição, a reserva de substituição tem os mesmos atributos da reserva que expira. Pode, opcionalmente, alterar a frequência, o período ou a quantidade da faturação nas definições de renovação. Qualquer utilizador com acesso de proprietário na reserva e na subscrição utilizada para faturação pode configurar a renovação.  

## <a name="discount-applies-to-different-sizes"></a>O desconto aplica-se a diferentes tamanhos

Quando compra uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que se encontram dentro do mesmo grupo de tamanho. Esta funcionalidade é conhecida como flexibilidade de tamanho de instância. A flexibilidade da cobertura do desconto depende do tipo de reserva e dos atributos que escolher quando comprar a reserva.

Planos do serviço:

- Instâncias de VM Reservada: ao comprar a reserva e selecionar a opção **Otimizado para flexibilidade de tamanho de instância**, a cobertura do desconto depende do tamanho da VM que selecionar. A reserva pode ser aplicada aos tamanhos das máquinas virtuais (VMs) no mesmo grupo de série de tamanhos. Para obter mais informações, veja [Flexibilidade de tamanho da máquina virtual com Reserved VM Instances](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Capacidade reservada do Armazenamento do Microsoft Azure: pode comprar capacidade reservada para contas de Armazenamento do Microsoft Azure standard em unidades de 100 TiB ou 1 PiB por mês. Para obter mais informações sobre que regiões suportam a capacidade reservada do Armazenamento do Azure, veja [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/). A capacidade reservada do Armazenamento do Azure está disponível para todas as camadas de acesso (frequente, esporádico ou de arquivo) e para qualquer configuração de replicação (LRS, GRS ou ZRS).
- Capacidade de reserva da Base de Dados SQL: a cobertura do desconto depende do escalão de desempenho que escolher. Para obter mais informações, veja [Compreender a forma como é aplicado um desconto de reserva do Azure](understand-reservation-charges.md).
- Capacidade reservada do Azure Cosmos DB: a cobertura do desconto depende do débito aprovisionado. Para obter mais informações, veja [Compreender a forma como é aplicado um desconto de reserva do Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Como os descontos se aplicam a serviços específicos do Azure

Leia os artigos seguintes que se aplicam a si para saber de que forma é que os descontos se aplicam a um serviço específico do Azure:

- [Serviço de Aplicações](reservation-discount-app-service.md)
- [Cache do Azure para Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [BD do Cosmos](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Base de Dados para MySQL](understand-reservation-charges-mysql.md)
- [Base de Dados para PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Hosts](billing-understand-dedicated-hosts-reservation-charges.md)
- [Armazenamento de Discos](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Planos de software](understand-suse-reservation-charges.md)
- [Armazenamento](understand-storage-charges.md)
- [Base de Dados SQL](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Máquinas virtuais](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Passos seguintes

- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição com taxas pay as you go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)