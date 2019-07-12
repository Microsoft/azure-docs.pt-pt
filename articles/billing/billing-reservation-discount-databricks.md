---
title: Como é aplicado um desconto de pré-compra do Azure Databricks
description: Saiba como um desconto de pré-compra do Azure Databricks aplica-se a sua utilização.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827646"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Como é aplicado o desconto de pré-compra do Azure Databricks

Pode usar unidades de consolidação do previamente adquiridas Azure Databricks (DBCU) em qualquer altura durante a vigência de compra. Qualquer utilização é do Azure Databricks deducts automaticamente dos DBCUs previamente adquiridas.

Ao contrário das VMs, unidades previamente compradas não expiram numa base horária. Pode usá-los em qualquer altura durante o período de vigência da compra. Para obter os descontos de compras prévia, não terá de voltar a implementar ou atribuir um plano previamente adquirido a suas áreas de trabalho do Azure Databricks para a utilização.

O desconto de compra prévia de aplica-se apenas a utilização de unidades (DBU) do Azure Databricks. Outros custos, como computação, armazenamento e rede são cobrados em separado.

## <a name="pre-purchase-discount-application"></a>Aplicativo de compra prévia de desconto

Pré-compra do Databricks se aplica a todos os escalões e cargas de trabalho do Databricks. Pode considerar a compra prévia de como um conjunto de unidades de consolidação de Databricks pré-pagos. Utilização é deduzida da agrupamento, independentemente da camada ou carga de trabalho. Utilização é deduzida na proporção seguinte:

| **Carga de trabalho** | **Rácio de aplicativo de DBU — escalão Standard** | **Rácio de aplicativo de DBU — escalão Premium** |
| --- | --- | --- |
| Análise de Dados | 0.4 | 0.55 |
| Engenharia de Dados | 0.15 | 0,30 |
| Engenharia de Dados Leve | 0.07 | 0.22 |

Por exemplo, quando uma quantidade da análise de dados – escalão Standard é consumida, as unidades de consolidação do Databricks previamente adquiridas é deduzidos por 0.4 unidades. Quando uma quantidade de luz de engenharia de dados – é utilizado o escalão Standard, a unidade de consolidação do Databricks previamente adquirida é deduzida por 0.07 unidades

## <a name="determine-plan-use"></a>Determinar a utilização do plano

Para determinar a utilização de plano DBCU, aceda ao portal do Azure > **reservas** e clique no plano de Databricks adquirido. Sua utilização até à data é mostrada com unidades de restantes. Para obter mais informações sobre como determinar sua reserva utilizar, consulte a [ver a utilização de reserva](billing-reservation-apis.md#see-reservation-usage) artigo.

## <a name="how-discount-application-shows-in-usage-data"></a>Como o aplicativo de desconto mostra em dados de utilização

Quando o desconto de compra prévia de aplica-se a sua utilização do Databricks, custos de sob demanda aparecem como zero nos dados de utilização. Para obter mais informações sobre os custos de reserva e a utilização, consulte [utilização e custos de reserva de obter o Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

- Para saber como gerir uma reserva, consulte [Gerir reservas de Azure](billing-manage-reserved-vm-instance.md).
- Para saber mais sobre a pré-compra do Azure Databricks para poupar dinheiro, veja [os custos de otimizar o Azure Databricks com uma compra prévia de](billing-prepay-databricks-reserved-capacity.md).
- Para saber mais sobre as reservas do Azure, veja os artigos seguintes:
  - [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerir reservas no Azure](billing-manage-reserved-vm-instance.md)
  - [Compreender a utilização de reserva para uma subscrição com taxas pay as you go](billing-understand-reserved-instance-usage.md)
  - [Compreender a utilização de reserva para inscrição da sua empresa](billing-understand-reserved-instance-usage-ea.md)
