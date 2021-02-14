---
title: Fazer o estorno dos custos das Reservas do Azure
description: Saiba como ver os custos das Reservas do Azure para estorno.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/10/2021
ms.author: banders
ms.openlocfilehash: 4fb15a7e677d566454d5d487c1cf69767d7f3a30
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368749"
---
# <a name="charge-back-azure-reservation-costs"></a>Fazer o estorno dos custos das Reservas do Azure

Os leitores de faturação do Contrato Enterprise e do Contrato de Cliente Microsoft podem ver os dados dos custos amortizados referentes a reservas. Podem utilizar os dados dos custos para fazer o estorno do valor monetário de uma subscrição, grupo de recursos, recurso ou de uma etiqueta para os seus parceiros. Nos dados amortizados, o preço efetivo corresponde ao custo da reserva horária proporcional. O custo corresponde ao custo total da utilização da reserva pelo recurso nesse dia.

Os utilizadores com uma subscrição individual podem obter os dados do custo amortizado a partir do respetivo ficheiro de utilização. Quando um recurso obtém um desconto de reserva, a secção *AdditionalInfo* no ficheiro de utilização contém os detalhes da reserva. Para obter mais informações, veja [Transferir a utilização do portal do Azure](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Consulte os dados de utilização da reserva para mostrar de volta e respune

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegar para **gestão de custos + faturação** 
3. Selecione análise de **custos** da navegação à esquerda 
4. Em **Custo Real**, selecione a métrica **Custo Amortizado**.
5. Para ver quais foram os recursos utilizados por uma reserva, aplique um filtro para **Reserva** e, em seguida, selecione reservas.
6. Defina a **Granularidade** como **Mensal** ou **Diária**.
7. Defina o tipo de gráfico como **Tabela**.
8. Defina a opção **Agrupar por** como **Recurso**.

[![Exemplo que mostra os custos dos recursos da reserva que pode utilizar para o estorno](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Aqui está um vídeo que mostra como ver os custos de utilização da reserva no portal do Azure.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Obtenha os dados para mostrar de volta e cobrar de volta
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegar para **gestão de custos + faturação** 
3. Selecione **Exportação** da navegação à esquerda 
4. Clique no botão **Adicionar**
5. Selecione o custo amortizado como o botão métrico e configurar a sua exportação

o EffectivePrice para o uso que obtém desconto de reserva é o custo prostim da reserva (em vez de ser zero). Isto ajuda o saber o valor monetário do consumo de reserva por uma subscrição, um grupo de recursos ou um recurso, e pode ajudá-lo a cobrar internamente pela utilização da reserva. O conjunto de dados também tem horas de reserva não utilizadas. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obter dados de utilização de reserva e de consumo do Azure com a API

Pode obter os dados com a API ou transferi-los no portal do Azure.

Pode chamar a [API de Detalhes de Utilização](/rest/api/consumption/usagedetails/list) para obter os novos dados. Para obter detalhes sobre a terminologia, veja os [termos de utilização](../understand/understand-usage.md).

Veja a seguir um exemplo de chamada para a API de Detalhes de Utilização:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Para obter mais informações sobre {enrollmentid} e {billingPeriodId}, veja o artigo sobre API: [Detalhes de Utilização – Lista](/rest/api/consumption/usagedetails/list).

As informações na tabela a seguir sobre métrica e filtro podem ajudar a resolver problemas de reserva comuns.

| **Tipo de dados de API** | Ação de chamada de API |
| --- | --- |
| **Todos os Encargos (utilização e compras)** | Substituir {metric} por ActualCost |
| **Utilização que obteve o desconto de reserva** | Substituir {metric} por ActualCost<br><br>Substituir {Filter} por Properties/reservaid% 20ne% 20 |
| **Utilização que não obteve o desconto de reserva** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por properties/reservationId%20eq%20 |
| **Encargos amortizados (utilização e compras)** | Substituir {metric} por AmortizedCost |
| **Relatório de reserva não utilizado** | Substituir {metric} por AmortizedCost<br><br>Substituir {filter} por properties/ChargeType%20eq%20'UnusedReservation' |
| **Compras de reservas** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por properties/ChargeType%20eq%20'Purchase'  |
| **Reembolsos** | Substituir {metric} por ActualCost<br><br>Substituir {filter} por properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Transferir o ficheiro CSV de utilização com novos dados

Se for um administrador da EA, pode descarregar o ficheiro CSV que contém novos dados de utilização do portal Azure. Estes dados não estão disponíveis no EA Portal (ea.azure.com), pelo que deve transferir o ficheiro de utilização no portal do Azure (portal.azure.com) para ver os novos dados.

No portal do Azure, navegue até [Gestão de Custos + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de faturação.
2. Clique em **Utilização + custos**.
3. Clique em **Transferir**.  
![Exemplo que mostra onde Transferir o ficheiro CSV de dados da utilização no portal do Azure](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Em **Detalhes de Utilização,** selecione **dados de utilização amortizados**.

Os ficheiros CSV que transfere contêm custos reais e custos amortizados.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes
- Para saber mais sobre os dados de utilização da Azure Reservations, consulte os seguintes artigos:
  - [Acordo de Empresa e Custos de reserva do Acordo de Cliente da Microsoft](understand-reserved-instance-usage-ea.md)
 
