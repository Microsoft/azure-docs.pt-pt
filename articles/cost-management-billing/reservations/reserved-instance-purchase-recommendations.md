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
ms.openlocfilehash: 2f7b09c14553fdb5d642080d286ce123176b997f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991053"
---
# <a name="how-reservation-recommendations-are-created"></a>Como são criadas as recomendações de reservas
As recomendações de compra de instâncias reservadas (RI) do Azure são fornecidas através da [API de Recomendação de Reservas](/rest/api/consumption/reservationrecommendations) de Consumo do Azure, do [Assistente do Azure](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e da experiência de compra de reservas no portal do Azure.

Os passos seguintes definem como são calculadas as recomendações: 
1. O motor de recomendação avalia a utilização horária dos recursos no âmbito fornecido nos últimos 7, 30 e 60 dias.
2. Com base nos dados de utilização, o motor simula os custos com e sem reservas.
3. Os custos são simulados para diferentes quantidades, e é recomendada a quantidade que maximiza a poupança.
4. Se os recursos forem encerrados regularmente, a simulação não encontrará poupanças e não é fornecida nenhuma recomendação de compra.

## <a name="recommendations-in-azure-advisor"></a>Recomendações do Assistente do Azure
Estão disponíveis recomendações de compra de reservas para máquinas virtuais no Assistente do Azure. Tenha em consideração os seguintes pontos: 
- O Assistente tem apenas recomendações de âmbito de subscrição única.
- Estão disponíveis recomendações calculadas num período do histórico de 30 dias no Assistente.
- Se comprar uma reserva de âmbito partilhado, as recomendações de compra de reservas do Assistente podem demorar até 30 dias a desaparecer.

## <a name="other-expected-api-behavior"></a>Outro comportamento esperado da API
- Ao utilizar um período do histórico de sete dias, é possível que não receba recomendações quando as VMs estiverem desligadas durante mais de um dia.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais acerca da forma [como o desconto de reserva do Azure é aplicado a máquinas virtuais](../manage/understand-vm-reservation-charges.md).
