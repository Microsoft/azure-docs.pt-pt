---
title: Compreender o desconto de reservas para a Base de Dados SQL do Azure | Microsoft Docs
description: Saiba como um desconto de reserva é aplicado às bases de dados SQL do Azure em execução. O desconto é aplicado a essas bases de dados hora a hora.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: banders
ms.openlocfilehash: 054641d8136d121e611182c8d8b104aefcbc6481
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057880"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Como um desconto de reserva é aplicado à Base de Dados SQL do Azure

Depois de comprar uma capacidade reservada da Base de Dados SQL do Azure, o desconto de reserva é automaticamente aplicado às bases de dados SQL que correspondam aos atributos e à quantidade da reserva. Uma reserva aplica-se aos custos de computação da Base de Dados SQL, incluindo a réplica primária e quaisquer réplicas secundárias faturáveis. É-lhe cobrada uma taxa normal de software, armazenamento e rede. Pode cobrir os custos de licenciamento da Base de Dados SQL com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Note que os descontos de reserva não se aplicam à Base de Dados SQL do Azure sem servidor.

Para as Azure Reserved Virtual Machine Instances, veja [Compreender o desconto das Azure Reserved VM Instances](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="discount-applied-to-running-sql-databases"></a>Desconto aplicado às bases de dados SQL em execução

O desconto da capacidade reservada da Base de Dados SQL é aplicado a bases de dados SQL em execução de hora em hora. A reserva que comprar é correspondida à utilização de computação emitida pelas bases de dados SQL em execução. No caso das bases de dados SQL que não são executadas durante a hora completa, a reserva é automaticamente aplicada a outras bases de dados SQL que correspondam aos atributos da reserva. O desconto pode ser aplicado a bases de dados SQL que estejam a ser executadas ao mesmo tempo. Se não tiver bases de dados SQL que sejam executadas durante a hora completa que correspondam aos atributos da reserva, não irá tirar o máximo partido do desconto da reserva para essa hora.

Os seguintes exemplos mostram como o desconto de capacidade reservada da Base de Dados SQL se aplica em função do número de núcleos que comprou e quando os mesmos estão em execução.

- Cenário 1: compra uma capacidade reservada da Base de Dados SQL para uma Base de Dados SQL de 8 núcleos. Executa uma Base de Dados SQL de 16 núcleos que corresponde ao resto dos atributos da reserva. É-lhe cobrado o preço pay-as-you-go pela utilização de computação da Base de Dados SQL de 8 núcleos. Obtém o desconto de reserva por uma hora de utilização de computação da Base de Dados SQL de 8 núcleos.

Para o resto destes exemplos, assuma que a capacidade reservada da Base de Dados SQL que comprou é para uma Base de Dados SQL de 16 núcleos e que o resto dos atributos de reserva correspondem às bases de dados SQL em execução.

- Cenário 2: executa duas bases de dados SQL com 8 núcleos cada uma durante uma hora. O desconto da reserva de 16 núcleos é aplicado à utilização de computação para ambas as bases de dados SQL de 8 núcleos.
- Cenário 3: executa uma Base de Dados SQL de 16 núcleos das 13:00 às 13:30. Executa outra Base de Dados SQL de 16 núcleos das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.
- Cenário 4: executa uma Base de Dados SQL de 16 núcleos das 13:00 às 13:45. Executa outra Base de Dados SQL de 16 núcleos das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.
- Cenário 5: executa uma base de dados de Hiperescala SQL de 4 núcleos com três réplicas secundárias, cada uma com 4 núcleos. A reserva aplica-se à utilização de computação da réplica primária e de todas as réplicas secundárias.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)
