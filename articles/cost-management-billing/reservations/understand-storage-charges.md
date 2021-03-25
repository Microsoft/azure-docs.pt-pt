---
title: Compreenda como os descontos de reserva são aplicados aos serviços de armazenamento Azure | Microsoft Docs
description: Saiba como os descontos de capacidade reservados são aplicados ao armazenamento Azure Blob, aos Ficheiros Azure e aos recursos da Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024035"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Entenda como os descontos de reserva são aplicados aos serviços de armazenamento Azure 
Os serviços de armazenamento Azure permitem economizar dinheiro nos custos de armazenamento reservando capacidade. Armazenamento Azure Blob, Ficheiros Azure e Azure Data Lake armazenam instâncias de reserva de suporte Gen 2. Após a aquisição da capacidade reservada, o desconto de reserva é automaticamente aplicado aos recursos de armazenamento que correspondem aos termos da reserva. O desconto de reserva aplica-se apenas à capacidade de armazenamento. A taxa de largura de banda e de pedidos são cobradas de acordo com as tarifas pay as you go.

Para obter mais informações sobre o armazenamento Azure Blob e o armazenamento reservado do Lago de Dados Azure Gen 2, consulte [os custos otimizar para armazenamento blob com capacidade reservada.](../../storage/blobs/storage-blob-reserved-capacity.md) Para obter mais informações sobre a capacidade reservada dos Ficheiros Azure, consulte [os custos da Otimização para ficheiros Azure com capacidade reservada](../../storage/files/files-reserve-capacity.md).

Para obter informações sobre o preço da reserva de armazenamento Azure Blob, consulte [os preços do Blob Block](https://azure.microsoft.com/pricing/details/storage/blobs/) e o preço do [Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Para obter informações sobre o preço da reserva de armazenamento Azure Files, consulte [os preços do Azure Files](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>De que forma o desconto de reserva é aplicado
O desconto de capacidade reservada é aplicado aos recursos de armazenamento suportados de hora em hora.

O desconto de capacidade reservada é um desconto "use-it-or-lose-it". Se você não tem nenhuma bolha de bloco, ações de arquivo Azure, ou recursos Azure Data Lake Storage Gen2 que cumprem os termos da reserva por uma hora, então você perde uma quantidade de reserva para essa hora. Não é possível continuar com horas reservadas não utilizadas.

Quando elimina um recurso, o desconto de reserva aplica-se automaticamente a outro recurso correspondente no âmbito especificado. Se não for encontrado nenhum recurso correspondente no âmbito especificado, as horas reservadas serão perdidas.

## <a name="discount-examples"></a>Exemplos de desconto
Os exemplos a seguir mostram como se aplica o desconto de capacidade reservada, dependendo das implementações.

Suponha que tenha comprado 100 TiB de capacidade reservada na região do Oeste dos EUA para um mandato de 1 ano. A sua reserva é para armazenamento localmente redundante (LRS) no nível de acesso a quente.

Vamos assumir que o custo desta reserva de amostra é de 18 540 $. Pode optar por pagar o montante total adiantado ou pagar prestações mensais fixas de 1545 $ por mês durante os próximos doze meses.

Para estes exemplos, vamos assumir que se inscreveu num plano de pagamentos de reserva mensal. Os cenários seguintes descrevem o que acontece caso subaproveite a capacidade reservada ou caso a utilize em excesso.

### <a name="underusing-your-capacity"></a>Subaproveitamento da capacidade
Suponha que em uma hora dentro do período de reserva, você usou apenas 80 TiB da sua capacidade reservada 100 TiB. Os restantes 20 TiB não são aplicados durante essa hora e não são transportados.

### <a name="overusing-your-capacity"></a>Utilização em excesso da capacidade
Suponha que numa hora dentro do período de reserva, usou 101 TiB de capacidade de armazenamento. O desconto de reserva aplica-se a 100 TiB dos seus dados, e os restantes 1 TiB são cobrados a preços de pagamento por essa hora. Se na próxima hora o seu uso mudar para 100 TiB, então todo o uso está coberto pela reserva.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos
Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Otimizar os custos do Armazenamento de blobs com a capacidade reservada](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Otimizar custos para ficheiros Azure com capacidade reservada](../../storage/files/files-reserve-capacity.md)
- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
