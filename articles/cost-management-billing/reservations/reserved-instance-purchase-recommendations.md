---
title: Como são criadas as recomendações de reservas do Azure
description: Saiba como são criadas as recomendações de reserva do Azure para máquinas virtuais.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851356"
---
# <a name="how-reservation-recommendations-are-created"></a>Como são criadas as recomendações de reservas

As recomendações de compra de instâncias reservadas (RI) do Azure são geradas pela [API de Recomendação de Reservas](/rest/api/consumption/reservationrecommendations) de Consumo do Azure. As recomendações da API também são utilizadas pelo [Assistente do Azure](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs). O assistente mostra as recomendações no portal do Azure.

Se tiver VMs em execução no Azure, pode aproveitar os preços com desconto para RIs e efetuar o pré-pagamento das VMs. A API de Recomendação de consumo da Microsoft avalia o a utilização durante 7, 30 e 60 dias e recomenda as configurações ideais para RIs. Calcula o custo que pagaria se não tivesse RIs em relação ao custo que paga com RIs para otimizar a poupança.

O Assistente do Azure mostra recomendações com base num período de 30 dias.

Para simplificar, o exemplo seguinte mostra os cálculos que ocorrem para uma recomendação de sete dias. É aplicado o mesmo método ao calcular recomendações de 30 ou 60 dias.

## <a name="calculation-method-example"></a>Exemplo do método de cálculo

Vamos assumir que a utilização de VMs do Windows por hora para um SKU e região em específico varia durante os sete dias (168 horas). A utilização mínima é de 65 unidades e a utilização máxima é de 127 unidades durante os sete dias. Neste exemplo, a hora 79 utilizou 80 VMs e comprou 75 RIs.

Se comprar 75 instâncias reservadas, paga os seguintes custos pela hora 79:

- 75 instâncias reservadas. O custo é pré-pago ao comprar RIs.
- As instâncias reservadas cobrem o custo de hardware de VMs em execução, pelo que paga por 75 horas a preço apenas de software.
- A utilização da hora 79 é 80, por isso paga por cinco horas do Windows mais o preço do medidor de combinação de hardware. O preço de combinação baseia-se no Contrato Enterprise (EA) ou na tarifa pay as you go.

Se comprar 75 RIs, pode calcular o custo total ao adicionar os custos por hora anteriores. Também pode calcular o custo atual através da tarifa. A diferença entre os dois montantes corresponde à poupança dos sete dias neste exemplo.

A API efetua cálculos para cada ponto de utilização específico. Em seguida, devolve a quantidade recomendada onde a poupança é maximizada. No exemplo seguinte, o gráfico mostra que a poupança atinge o pico às 68. As poupanças reduzem em seguida, pelo que a API recomenda 68.

![Diagrama que mostra o pico da poupança](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>Outro comportamento esperado da API

- A API mostra as poupanças possíveis com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para o Windows quando o benefício é utilizado. Se o benefício não for utilizado, as recomendações da API baseiam-se no custo principal do Windows. Se estiver disponível para si, considere utilizar o Benefício Híbrido do Azure para aumentar a poupança.
- Ao utilizar um período do histórico de sete dias, é possível que não receba recomendações quando as VMs estiverem desligadas durante mais de um dia.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais acerca da forma [como o desconto de reserva do Azure é aplicado a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
