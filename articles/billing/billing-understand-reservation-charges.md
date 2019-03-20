---
title: Compreender o desconto de reservas do Azure | Documentos da Microsoft
description: Saiba como é aplicado um desconto de reserva para bases de dados SQL a executar.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: banders
ms.openlocfilehash: ff31b9be0e927cca7d272e5deaa5ecf48a52580d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57845470"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Compreender a forma como um desconto de reserva do Azure é aplicado às bases de dados SQL

Depois de comprar uma capacidade de base de dados do SQL Azure reservado, o desconto de reserva é automaticamente aplicado às bases de dados do SQL que correspondem a atributos e a quantidade da reserva. Uma reserva aborda os custos de computação de base de dados SQL. É-lhe cobrado para software, armazenamento e rede às tarifas normais. Que podem ser cobertos os custos de licenciamento para bases de dados SQL com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para as instâncias de Máquina Virtual reservadas, consulte [compreender Azure Reserved VM Instances desconto](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Desconto de reserva aplicado a bases de dados SQL

 O desconto de capacidade de reserva de base de dados SQL é aplicado à execução de bases de dados SQL numa base horária. A reserva que adquirir é correspondida à utilização de computação emitida por bancos de dados SQL em execução. No caso das Bases de Dados SQL que não são executadas durante horas completas, a reserva é automaticamente aplicada a outras Bases de Dados SQL que correspondam aos atributos da reserva. O desconto pode aplicar a bases de dados do SQL que estão em execução em simultâneo. Se não tiver bases de dados SQL que são executadas durante a hora completa que correspondem os atributos de reserva, não obtém todos os benefícios do desconto de reserva para essa hora.

Os exemplos seguintes mostram como o desconto de capacidade de reserva de base de dados SQL aplica-se consoante o número de núcleos comprou e, quando estiver em execução.

- Cenário 1: Comprar uma capacidade de base de dados de SQL reservadas para uma base de dados SQL de 8 núcleos. Executar uma base de dados do SQL que correspondem aos rest dos atributos da reserva de 16 núcleos. É-lhe cobrado o preço pay as you go de 8 núcleos de utilização de computação de base de dados SQL. Obter o desconto de reserva de uma hora de 8 núcleos de utilização de computação de base de dados SQL.

No restante destes exemplos, partem do princípio de que a capacidade de base de dados de SQL reservadas que comprar é para uma base de dados SQL de 16 núcleos e o restante dos atributos de reserva correspondem aos bancos de dados SQL em execução.

- Cenário 2: Execute duas bases de dados SQL com 8 núcleos por uma hora. O desconto de reserva de 16 núcleos é aplicado a utilização para as duas de 8 núcleos de bases de dados SQL de computação.
- Cenário 3: Execução de uma de 16 núcleos de base de dados SQL do pm de 1 para 1 17:30. Executar outro 16 núcleos base de dados SQL a partir 1:30 para 2 pm. Ambos estão abrangidas pelo desconto de reserva.
- Cenário 4: Execução de uma de 16 núcleos de base de dados SQL do pm de 1 para 1 às 17:45. Executar outro 16 núcleos base de dados SQL a partir 1:30 para 2 pm. É-lhe cobrado o preço pay as you go para a sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação para o resto do tempo.

Para compreender e ver a aplicação das reservas de Azure no relatórios de utilização de faturação, veja [utilização de reserva de compreender o Azure](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reserva para subscrições de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
