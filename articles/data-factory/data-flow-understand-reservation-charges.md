---
title: Compreenda o desconto de reservas para fluxos de dados da Azure Data Factory | Microsoft Docs
description: Saiba como é aplicado um desconto de reserva para executar fluxos de dados ADF. O desconto é aplicado a estes fluxos de dados de hora em hora.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716300"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Como é aplicado um desconto de reserva nos fluxos de dados da Azure Data Factory

Depois de comprar capacidade reservada ao fluxo de dados ADF, o desconto de reserva é automaticamente aplicado aos fluxos de dados utilizando um tempo de execução de integração Azure que corresponda ao tipo de cálculo e contagem de base da reserva.

## <a name="how-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

Um desconto de reserva é “*use-it-or-lose-it*” (utilizar ou perder). Então, se você não tem recursos de integração Azure usados por qualquer hora, então você perde uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando deixa de utilizar o tempo de execução da integração para fluxos de dados, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão *perdidas*.

## <a name="discount-applied-to-adf-data-flows"></a>Desconto aplicado aos fluxos de dados da ADF

O fluxo de dados ADF é aplicado ao tempo de execução dos tempos de integração numa base horária. A reserva que compra corresponde ao tipo de cálculo que está a ser utilizado pelos tempos de integração para os seus fluxos de dados. Para fluxos de dados que não executam a hora completa, a reserva é automaticamente aplicada a outros fluxos de dados correspondentes aos atributos da reserva. O desconto também pode aplicar-se aos fluxos de dados que estão a ser executadas simultaneamente. Se não tiver fluxos de dados que se recorram durante toda a hora que correspondam aos atributos da reserva, não obtém o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o fluxo de dados ADF reserva o desconto de capacidade se aplica em função do número de núcleos que comprou e quando estão a funcionar.

- Cenário 1: Você compra uma reserva de fluxo de dados ADF para 1 hora de 80 núcleos de cálculo otimizado de memória, introduzindo 80 como a quantidade para o tipo de computação otimizada de memória. Você executou um fluxo de dados com um tempo de execução de integração Azure definido para 144 núcleos de memória otimizados durante uma hora. Cobram-lhe o preço de pagamento por 64 núcleos de uso do fluxo de dados durante uma hora. Você obtém o desconto de reserva por uma hora de 80 núcleos de memória otimizado uso.
- Cenário 2: Você compra uma reserva de fluxo de dados ADF para 1 hora de 32 núcleos de computação de finalidade geral, introduzindo 32 como quantidade para o tipo de computação de finalidade geral. Depura os seus fluxos de dados durante 1 hora usando 32 núcleos de tempo de integração do cálculo geral Azure. Você tem o desconto de reserva para toda a hora de uso.

Para compreender e ver a aplicação das Reservas do Azure em relatórios de utilização da faturação, veja [Compreender a utilização das reservas do Azure](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre reservas do Azure, consulte o seguinte artigo:

- [O que são as reservas do Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)