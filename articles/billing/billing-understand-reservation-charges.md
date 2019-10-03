---
title: Compreender o desconto de reservas para as Bases de Dados SQL do Azure | Microsoft Docs
description: Saiba como um desconto de reserva é aplicado às Bases de Dados SQL do Azure em execução.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: d7a1a451bf40a09b1bd9b9d823e0778d335a690b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71701795"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Como um desconto de reserva é aplicado às Bases de Dados SQL do Azure

Depois de comprar uma capacidade reservada da Base de Dados SQL do Azure, o desconto de reserva é automaticamente aplicado às Bases de Dados SQL que correspondam aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação da sua Base de Dados SQL. É-lhe cobrada uma taxa normal de software, armazenamento e rede. Pode cobrir os custos de licenciamento das Bases de Dados SQL com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para as Azure Reserved Virtual Machine Instances, veja [Compreender o desconto das Azure Reserved VM Instances](billing-understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Portanto, se não tiver recursos correspondentes para nenhuma hora, perderá uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando encerra um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="discount-applied-to-sql-databases"></a>O desconto aplicado às Bases de Dados SQL

 O desconto da capacidade reservada da Base de Dados SQL são aplicados a Bases de Dados SQL em execução de hora em hora. A reserva que comprar é correspondida à utilização de computação emitida pelas Bases de Dados SQL em execução. No caso das Bases de Dados SQL que não são executadas durante horas completas, a reserva é automaticamente aplicada a outras Bases de Dados SQL que correspondam aos atributos da reserva. O desconto pode ser aplicado a Bases de Dados SQL que estejam a ser executadas ao mesmo tempo. Se não tiver Bases de Dados SQL que sejam executadas durante toda a hora que correspondam aos atributos da reserva, não irá tirar partido completo das vantagens do desconto de reserva para essa hora.

Os seguintes exemplos mostram como o desconto de capacidade reservada da Base de Dados SQL se aplica em função do número de núcleos que comprou e quando os mesmos estão em execução.

- Cenário 1: compra uma capacidade reservada da Base de Dados SQL para uma Base de Dados SQL de 8 núcleos. Executa uma Base de Dados SQL de 16 núcleos que corresponde ao resto dos atributos da reserva. É-lhe cobrado o preço pay-as-you-go pela utilização de computação da Base de Dados SQL de 8 núcleos. Obtém o desconto de reserva por uma hora de utilização de computação da Base de Dados SQL de 8 núcleos.

Para o resto destes exemplos, assuma que a capacidade reservada da Base de Dados SQL que comprou é para uma Base de Dados SQL de 16 núcleos e que o resto dos atributos de reserva correspondem às Bases de Dados SQL em execução.

- Cenário 2: executa duas bases de dados SQL com 8 núcleos cada uma durante uma hora. O desconto de reserva de 16 núcleos é aplicado para a utilização de computação para ambas as Bases de Dados SQL de 8 núcleos.
- Cenário 3: executa uma Base de Dados SQL de 16 núcleos das 13:00 às 13:30. Executa outra Base de Dados SQL de 16 núcleos das 13:30 às 14:00. Ambas são abrangidas pelo desconto de reserva.
- Cenário 4: executa uma Base de Dados SQL de 16 núcleos das 13:00 às 13:45. Executa outra Base de Dados SQL de 16 núcleos das 13:30 às 14:00. É-lhe cobrado o preço pay-as-you-go pela sobreposição de 15 minutos. O desconto de reserva aplica-se à utilização de computação durante o resto do tempo.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](billing-understand-reserved-instance-usage.md)
- [Compreender a utilização de reservas na inscrição Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Compreender a utilização de reservas nas subscrições do CSP](/partner-center/azure-reservations)
