---
title: Compreender como o desconto de reserva é aplicado ao Armazenamento do Microsoft Azure | Microsoft Docs
description: Saiba como o desconto de capacidade reservada do Armazenamento do Microsoft Azure é aplicado ao blob de blocos e aos recursos do Azure Data Lake Storage Gen2.
author: tamram
ms.service: billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: ebef727cfa291744b3c97299da2a1340f34f5d72
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73746264"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Compreender como o desconto de reserva é aplicado ao Armazenamento do Microsoft Azure

Depois de comprar a capacidade reservada do Armazenamento do Microsoft Azure, o desconto de reserva é automaticamente aplicado ao blob de blocos e aos recursos do Azure Data Lake Storage Gen2 que correspondam aos termos da reserva. O desconto de reserva aplica-se apenas à capacidade de armazenamento. A taxa de largura de banda e de pedidos são cobradas de acordo com as tarifas pay as you go.

Para obter mais informações sobre a capacidade reservada do Armazenamento do Microsoft Azure, veja[Otimizar os custos do Armazenamento de blobs com a capacidade reservada](../storage/blobs/storage-blob-reserved-capacity.md).

Para obter mais informações sobre os preços de reserva do Armazenamento do Microsoft Azure, veja os [Preços do blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/) e os [Preços do Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado

O desconto de capacidade reservada do Armazenamento do Microsoft Azure é aplicado ao blob de blocos e aos recursos do Azure Data Lake Storage Gen2.

O desconto de capacidade reservada do Armazenamento do Microsoft Azure é um desconto “use-it-or-lose-it” (utilizar ou perder). Caso não tenha nenhum blob de blocos ou recursos do Azure Data Lake Storage Gen2 que cumpram os termos da reserva para uma determinada hora, perde uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando elimina um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto

Os seguintes exemplos mostram como o desconto de capacidade reservada do Armazenamento do Microsoft Azure se aplica em função das implementações.

Vamos supor que comprou 100 TB de capacidade reservada na região EUA Oeste 2 para o prazo de um ano. A reserva é para o armazenamento localmente redundante (LRS) na camada de acesso frequente.

Vamos assumir que o custo desta reserva de amostra é de 18 540 $. Pode optar por pagar o montante total adiantado ou pagar prestações mensais fixas de 1545 $ por mês durante os próximos doze meses.

Para estes exemplos, vamos assumir que se inscreveu num plano de pagamentos de reserva mensal. Os cenários seguintes descrevem o que acontece caso subaproveite a capacidade reservada ou caso a utilize em excesso.

### <a name="underusing-your-capacity"></a>Subaproveitamento da capacidade

Vamos supor que, numa determinada hora dentro do período de reserva, utilizou apenas 80 TB da capacidade reservada de 100 TB. Os 20 TB restantes não são aplicados a essa hora e não são transferidos.

### <a name="overusing-your-capacity"></a>Utilização em excesso da capacidade

Vamos supor que, numa determinada hora dentro do período de reserva, utilizou 101 TB de capacidade reservada. O desconto de reserva é aplicado a 100 TB dos dados e o 1 TB restante é cobrado de acordo com as tarifas pay as you go para aquela hora. Se durante a próxima hora a utilização mudar para 100 TB, toda a utilização será abrangida pela reserva.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Otimizar os custos do Armazenamento de blobs com a capacidade reservada](../storage/blobs/storage-blob-reserved-capacity.md)
- [O que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
