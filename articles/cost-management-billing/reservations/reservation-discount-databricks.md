---
title: Como é aplicado um desconto de pré-compra de Azure Databricks
description: Saiba como um desconto de pré-compra de Azure Databricks se aplica à sua utilização. Pode utilizar estes Databricks em qualquer altura durante o termo da compra.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 380c4c7fdcccf45d83adaf355c2cc12da0b327fc
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460325"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Como é aplicado um desconto de pré-compra de Azure Databricks

Pode usar as unidades de consolidação de Azure Databricks (DBCU) pré-compradas em qualquer momento durante o termo de compra. Qualquer utilização de Azure Databricks é deduzida das DBCUs pré-compradas automaticamente.

Ao contrário das VMs, as unidades pré-compradas não expiram por hora. Pode usá-las em qualquer altura durante o termo da compra. Para obter os descontos de pré-compra, não precisa de reimplementar ou atribuir um plano pré-comprado às suas áreas de trabalho do Azure Databricks para a utilização.

O desconto de pré-compra aplica-se apenas à utilização de unidades de Databricks (DBU). Outros custos, como computação, armazenamento e rede, são cobrados em separado.

## <a name="pre-purchase-discount-application"></a>Aplicação de desconto de pré-compra

A pré-compra de Databricks aplica-se a todas as cargas de trabalho e escalões de Databricks. Pode considerar a pré-compra como um conjunto de unidades de consolidação de Databricks pré-pagos. A utilização é deduzida do conjunto, independentemente da carga de trabalho ou do escalão. A utilização é deduzida na seguinte proporção:

| **Carga de trabalho** | **Taxa de aplicações de DBU — escalão Standard** | **Taxa de aplicações de DBU — escalão Premium** |
| --- | --- | --- |
| Análise de Dados | 0.4 | 0.55 |
| Engenharia de Dados | 0.15 | 0.30 |
| Engenharia de Dados Leve | 0.07 | 0.22 |

Por exemplo, quando uma quantidade de Análise de Dados – escalão Standard é consumida, as unidades de consolidação de Databricks pré-compradas são deduzidas por 0,4 unidades. Quando uma quantidade de Engenharia de Dados Leve – escalão Standard é utilizada, a unidade de consolidação de Databricks pré-comprada é deduzida por 0,07 unidades

## <a name="determine-plan-use"></a>Determinar a utilização do plano

Para determinar a utilização do seu plano DBCU, aceda ao portal do Azure > **Reservas** e clique no plano de Databricks comprado. A sua utilização até à data é mostrada com quaisquer unidades restantes. Para obter mais informações sobre como determinar a sua utilização de reservas, veja o artigo [Ver utilização da reserva](reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Como a aplicação de desconto é mostrada nos dados de utilização

Quando o desconto de pré-compra se aplica à sua utilização de Databricks, os encargos a pedido aparecem como zero nos dados de utilização. Para obter mais informações sobre custos de reserva e utilização, veja [Get Enterprise Agreement reservation costs and usage](understand-reserved-instance-usage-ea.md) (Compreender custos de reserva e utilização do Contrato Enterprise).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre a pré-compra de Azure Databricks para poupar dinheiro, veja [Otimizar custos de Azure Databricks com uma pré-compra](prepay-databricks-reserved-capacity.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerir Reservas no Azure](manage-reserved-vm-instance.md)
  - [Compreender a utilização de reservas para uma subscrição com tarifas pay as you go](understand-reserved-instance-usage.md)
  - [Compreender a utilização de reservas na inscrição Enterprise](understand-reserved-instance-usage-ea.md)
