---
title: Fazer o estorno dos custos das Reservas do Azure
description: Saiba como ver os custos das Reservas do Azure para estorno.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: banders
ms.openlocfilehash: 22e2ee897236a2e079f0ce6e230502d5242a02e8
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714356"
---
# <a name="charge-back-azure-reservation-costs"></a>Fazer o estorno dos custos das Reservas do Azure

Os leitores de faturação do Contrato Enterprise e do Contrato de Cliente Microsoft podem ver os dados dos custos amortizados referentes a reservas. Podem utilizar os dados dos custos para fazer o estorno do valor monetário de uma subscrição, grupo de recursos, recurso ou de uma etiqueta para os seus parceiros. Nos dados amortizados, o preço efetivo corresponde ao custo da reserva horária proporcional. O custo corresponde ao custo total da utilização da reserva pelo recurso nesse dia.

Os utilizadores com uma subscrição individual podem obter os dados do custo amortizado a partir do respetivo ficheiro de utilização. Quando um recurso obtém um desconto de reserva, a secção *AdditionalInfo* no ficheiro de utilização contém os detalhes da reserva. Para obter mais informações, veja [Transferir a utilização do portal do Azure](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv).

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Obter dados de estorno da reserva para estorno

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até **Cost Management + Faturação**.
1. Em **Custo Real**, selecione a métrica **Custo Amortizado**.
1. Para ver quais foram os recursos utilizados por uma reserva, aplique um filtro para **Reserva** e, em seguida, selecione reservas.
1. Defina a**Granularidade** como **Mensal** ou **Diária**.
1. Defina o tipo de gráfico como **Tabela**.
1. Defina a opção **Agrupar por** como **Recurso**.

[![Exemplo que mostra os custos dos recursos da reserva que pode utilizar para o estorno](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Aqui está um vídeo que mostra como ver os custos de utilização da reserva no portal do Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

- Para saber como gerir uma reserva, veja [Gerir Reservas do Azure](manage-reserved-vm-instance.md).
- Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:
  - [O que são as reservas do Azure?](save-compute-costs-reservations.md)
  - [Gerir Reservas no Azure](manage-reserved-vm-instance.md)
