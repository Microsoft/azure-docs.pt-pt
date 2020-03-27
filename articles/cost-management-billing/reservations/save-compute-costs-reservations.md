---
title: O que é o Azure Reservations?
description: Saiba mais sobre o Azure Reservations e os respetivos preços para poupar nos custos das suas máquinas virtuais, de bases de dados SQL, do Azure Cosmos DB e de outros recursos.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 6277a7e7dc5891a3bc67c298a31344284c92e31d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235639"
---
# <a name="what-are-azure-reservations"></a>O que é o Azure Reservations?

O Azure Reservations ajuda-o a poupar ao subscrever planos de um ou três anos de vários produtos. A subscrição permite-lhe obter um desconto nos recursos que utiliza. As reservas podem reduzir significativamente os custos dos recursos até 72% nos preços pay as you go. O Reservations oferece um desconto de faturação e não afeta o estado de atividade dos seus recursos. Depois de comprar uma reserva, o desconto é aplicado automaticamente aos recursos correspondentes.

Pode pagar uma reserva em adiantado ou mensalmente. O custo total de reservas pagas em adiantado ou mensalmente é o mesmo e não pagará qualquer taxa adicional ao optar por pagamentos mensais. O pagamento mensal está disponível para as reservas do Azure, não para produtos de terceiros.

Pode comprar uma reserva no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Porquê comprar uma reserva?

Se tem uma utilização consistente de recursos que suporte reservas, a compra de uma reserva dá-lhe a opção de reduzir os seus custos. Por exemplo, ao executar instâncias de um serviço continuamente sem uma reserva, ser-lhe-ão cobradas taxas pay as you go. Ao comprar uma reserva, terá direito imediato ao desconto de reserva. Deixarão de ser aplicadas taxas pay as you go aos recursos.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Após a compra, o desconto de reserva é aplicado automaticamente à utilização de recursos que corresponder aos atributos por si selecionados quando comprar a reserva. Os atributos incluem o SKU, regiões (quando aplicável) e âmbito. O âmbito de reserva permite selecionar onde as poupanças na reserva serão aplicadas.

Para obter mais informações sobre como o desconto é aplicado, veja [Aplicação do desconto de instância reservada](reservation-discount-application.md).

Para obter mais informações sobre como funciona o âmbito de reserva, veja [Definir o âmbito de reservas](prepare-buy-reservation.md#scope-reservations).


## <a name="flexibility-with-azure-reservations"></a>Flexibilidade com as reservas do Azure

As Reservas do Azure fornecem flexibilidade para ajudar a dar resposta às suas necessidades em evolução. Pode trocar uma reserva por outra reserva do mesmo tipo. Também pode reembolsar uma reserva, até 50 000 USD no prazo de 12 meses, se já não precisar da mesma. O limite máximo do reembolso aplica-se a todas as reservas no âmbito do contrato com a Microsoft.

Para obter mais informações, veja [Trocas e reembolsos self-service para Reservas do Azure](exchange-and-refund-azure-reservations.md).


## <a name="charges-covered-by-reservation"></a>Custos abrangidos pelas reservas

- **Instância de Máquina Virtual Reservada** – a reserva abrange apenas os custos de computação de máquinas virtuais. Não abrange custos de armazenamento, rede, Windows ou software adicionais.
- **Capacidade reservada do Armazenamento do Microsoft Azure** – uma reserva abrange a capacidade de armazenamento das contas de armazenamento standard do Armazenamento de blobs ou do armazenamento do Azure Data Lake Gen2. A reserva não abrange taxas de largura de banda nem de transação.
- **Capacidade reservada do Azure Cosmos DB** – a reserva abrange o débito aprovisionado para os seus recursos. Não abrange os custos de armazenamento e de rede.
- **VCore reservado de Base de Dados SQL** – apenas os custos de computação são incluídos na reserva. A licença do SQL é faturada separadamente.
- **SQL Data Warehouse** – a reserva abrange a utilização de cDWUs. Não abrange os custos de armazenamento ou rede associados à utilização do SQL Data Warehouse.
- **Azure Databricks** – as reservas abrangem apenas a utilização de DBU. Outros custos, como computação, armazenamento e rede, são aplicados em separado.
- **Imposto de selo do Serviço de Aplicações** – a reserva abrange a utilização de selos. Não se aplica às funções de trabalho, pelo que todos os outros recursos associados ao selo serão cobrados separadamente.
- **Base de Dados do Azure para MySQL** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados ao servidor da Base de Dados MySQL.
- **Base de Dados do Azure para PostgreSQL** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados aos servidores da Base de Dados para PostgreSQL.
- **Azure Database for MariaDB** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de software, rede e armazenamento associados ao servidor da MariaDB Database.
- **Azure Data Explorer** – as reservas incluem os custos de marcação do Data Explorer do Azure. As reservas não abrangem os custos de computação, rede e armazenamento associados aos clusters.
- **Cache do Azure para Redis** – só estão incluídos nas reservas os custos de computação. As reservas não abrangem os custos de rede e armazenamento associados às instâncias de caches Redis.
- **Azure Dedicated Host** - só estão incluídos no Dedicated Host os custos de computação.
- **Reservas de Armazenamento de Discos do Azure** – uma reserva abrange apenas SSDs premium de tamanho P30 ou superior. Não abrange outros tipos ou tamanhos de discos inferiores a P30.

Planos de software:

- **SUSE Linux** – as reservas abrangem os custos do plano de software. Os descontos aplicam-se apenas aos medidores SUSE e não á utilização da máquina virtual.
- **Planos do Red Hat** – as reservas abrangem os custos do plano de software. O desconto aplica-se apenas aos medidores Red Hat, não à utilização da máquina virtual.
- **Azure VMware Solution by CloudSimple** – as reservas abrangem os Nós de VMWare da CloudSimple. Continuam a ser aplicados custos de software adicionais.
- **Azure Red Hat OpenShift** – as reservas aplicam-se aos custos do OpenShift, não aos custos da infraestrutura do Azure.

Para máquinas virtuais do Windows e Base de Dados SQL, o desconto de reserva não se aplica aos custos de software. Pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Azure Reservations através dos seguintes artigos:
    - [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
    - [Compreender a utilização de reservas na sua subscrição com taxas pay as you go](understand-reserved-instance-usage.md)
    - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
    - [Custos de software Windows não incluídos nas reservas](reserved-instance-windows-software-costs.md)
    - [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](/partner-center/azure-reservations)

- Saiba mais sobre as reservas para planos de serviço:
    - [Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Recursos do Azure Cosmos DB com capacidade de reserva do Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre as reservas para planos de software:
    - [Planos de software Red Hat no Azure Reservations](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software SUSE no Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)
