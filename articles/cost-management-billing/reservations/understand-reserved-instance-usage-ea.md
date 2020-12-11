---
title: Compreender a utilização de reservas do Azure para os Contratos Enterprise
description: Saiba como ler a utilização para compreender como se aplica a reserva do Azure para a sua inscrição no Enterprise.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: 874d5cb022a38b172bb37009bd86b5e6988f3204
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545609"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Obter custos e utilização de reservas do Contrato Enterprise

Os custos de reserva e os dados de utilização estão disponíveis para os clientes do contrato Enterprise no portal do Azure e nas APIs REST. Este artigo ajuda-o a:

- Obter os dados de compra de reserva
- Saber qual subscrição, grupo de recursos ou recurso utilizou a reserva
- Estorno para a utilização de reserva
- Calcular as poupanças das reservas
- Obter dados de subutilização das reservas
- Amortizar custos de reserva

Os encargos do Marketplace estão consolidados nos dados da utilização. Pode ver os encargos para a utilização do proprietário, a utilização do Marketplace e as compras de uma única origem de dados.

## <a name="reservation-charges-in-azure-usage-data"></a>Encargos de reserva nos dados de utilização do Azure

Os dados estão divididos em dois conjuntos de dados distintos: _Custo Real_ e _Custo Amortizado_. De que forma diferem estes dois conjuntos de dados:

**Custo Real** – fornece os dados para reconciliar com a sua fatura mensal. Estes dados têm os custos de compra de reserva e os detalhes da aplicação de reserva. Com estes dados, pode saber qual subscrição ou grupo de recursos ou recurso recebeu o desconto de reserva num determinado dia. O EffectivePrice para a utilização que recebe o desconto de reserva é zero.

**Custo Amortizado** – este conjunto de dados é semelhante ao conjunto de dados de Custo Real, exceto o facto de o EffectivePrice para a utilização que obtém o desconto de reserva ser o custo proporcional da Reserva (em vez de ser zero). Isto ajuda o saber o valor monetário do consumo de reserva por uma subscrição, um grupo de recursos ou um recurso, e pode ajudá-lo a cobrar internamente pela utilização da reserva. O conjunto de dados também tem horas de reserva não utilizadas. O conjunto de dados não tem registos de compra de reserva.

Comparação de dois conjuntos de dados:

| Dados | Conjunto de dados do Custo Real | Conjunto de dados do Custo Amortizado |
| --- | --- | --- |
| Compras de Reservas | Disponível nesta vista.<br><br>  Para obter este filtro de dados em ChargeType = &quot;Compra&quot;. <br><br> Veja ReservationID ou ReservationName para saber de que reserva é o encargo.  | Não aplicável a esta vista. <br><br> Os custos de compra não são fornecidos nos dados amortizados. |
| EffectivePrice | O valor é zero para a utilização que obtém o desconto de reserva. | O valor é o custo calculado por hora da reserva para a utilização que tem o desconto de reserva. |
| Reserva não utilizada (fornece o número de horas que a reserva não foi utilizada num dia e o valor monetário do desperdício) | Não aplicável nesta vista. | Disponível nesta vista.<br><br> Para obter estes dados, filtre com ChargeType = &quot;UnusedReservation&quot;.<br><br>  Veja ReservationID ou ReservationName para saber que reserva foi subutilizada. Esta é a quantidade de reserva desperdiçada no dia.  |
| UnitPrice (preço do recurso na sua folha de preços) | Disponível | Disponível |

Outras informações disponíveis nos dados de utilização do Azure foram alteradas:

- Informações do Produto e Medidor – o Azure não substitui o medidor originalmente consumido por ReservationId e ReservationName, como fazia anteriormente.
- ReservationId e ReservationName – são os seus próprios campos nos dados. Anteriormente, costumava estar disponível apenas em AdditionalInfo.
- ProductOrderId – a ID do pedido de reserva, adicionada como o seu próprio campo.
- ProductOrderName – o nome do produto da reserva comprada.
- Termo – 12 meses ou 36 meses.
- RINormalizationRatio – disponível em AdditionalInfo. Esta é a proporção na qual a reserva é aplicada ao registo de utilização. Se a flexibilidade do tamanho da instância estiver ativada para a sua reserva, poderá ser aplicada a outros tamanhos. O valor mostra a proporção à qual a reserva foi aplicada para o registo de utilização.

[Veja a definição de campo](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obter dados de utilização de reserva e de consumo do Azure com a API

Pode obter os dados com a API ou transferi-los no portal do Azure.

Pode chamar a [API de Detalhes de Utilização](/rest/api/consumption/usagedetails/list) para obter os novos dados. Para obter detalhes sobre a terminologia, veja os [termos de utilização](../understand/understand-usage.md). O autor da chamada deve ser um Administrador da Empresa para o contrato Enterprise através do [portal de EA](https://ea.azure.com). Os Administradores de Empresa só de leitura também podem obter os dados.

Tenha em atenção que estes dados não estão disponíveis em [APIs de Relatórios para clientes Enterprise – Detalhes de Utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

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

Se for um administrador de EA, poderá transferir o ficheiro CSV que contém os novos dados de utilização a partir do portal do Azure. Estes dados não estão disponíveis no EA Portal (ea.azure.com), pelo que deve transferir o ficheiro de utilização no portal do Azure (portal.azure.com) para ver os novos dados.

No portal do Azure, navegue até [Gestão de Custos + Faturação](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de faturação.
2. Clique em **Utilização + custos**.
3. Clique em **Transferir**.  
![Exemplo que mostra onde Transferir o ficheiro CSV de dados da utilização no portal do Azure](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Em **Transferir Utilização + Encargos**, em **Detalhes de Utilização Versão 2**, selecione **Todos os Encargos (utilização e compras)** e, em seguida, clique em transferir. Repita para **Encargos amortizados (utilização e compras)** .

Os ficheiros CSV que transfere contêm custos reais e custos amortizados.

## <a name="common-cost-and-usage-tasks"></a>Tarefas comuns de custo e utilização

As secções a seguir são tarefas comuns utilizadas pela maioria das pessoas para ver os respetivos dados de utilização e custos de reserva.

### <a name="get-reservation-purchase-costs"></a>Obter os custos de compra de reserva

Os custos de compra de reserva estão disponíveis nos dados de Custo Real. Filtro por _ChargeType = Purchase_. Veja ProductOrderID para determinar para qual ordem de reserva é a compra.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obter quantidade e custos de reserva subutilizados

Obtenha os dados do Custo Amortizado e filtre por _ChargeType_ _= UnusedReservation_. Obtém a quantidade diária de reserva não utilizada e o custo. Pode filtrar os dados para uma reserva ou ordem de reserva com os campos _ReservationId_ e _ProductOrderId_, respetivamente. Se uma reserva fosse utilizada a 100%, o registo teria uma quantidade de 0.

### <a name="amortize-reservation-costs"></a>Amortizar os custos das reservas

Obtenha dados de Custo Amortizado e filtre por uma ordem de reserva com _ProductOrderID_ para obter custos amortizados diários para uma reserva.

### <a name="chargeback-for-a-reservation"></a>Estorno para uma reserva

Pode estornar a utilização de reserva para outras organizações por subscrição, grupos de recursos ou etiquetas. Os dados de custo amortizado fornecem o valor monetário da utilização de uma reserva nos seguintes tipos de dados:

- Recursos (como uma VM)
- Grupo de recursos
- Etiquetas
- Subscrição

### <a name="get-the-blended-rate-for-chargeback"></a>Obter a taxa combinada de estorno

Para determinar a taxa combinada, obtenha os dados de custos amortizados e agregue o custo total. No caso das VMs, pode utilizar as informações do MeterName ou do ServiceType a partir dos dados JSON AdditionalInfo. Divida o custo total pela quantidade utilizada para obter a taxa combinada.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Fazer uma auditoria à utilização de reserva ótima quanto à flexibilidade do tamanho da instância

Multiplique a quantidade por _RINormalizationRatio_, a partir de AdditionalInfo. Os resultados indicam quantas horas de utilização de reserva foram aplicadas ao registo de utilização.

### <a name="determine-reservation-savings"></a>Determinar poupanças na reserva

Obtenha os dados de Custos amortizados e filtre os dados de uma instância reservada. Em seguida:

1. Obtenha os custos pay as you go estimados. Multiplique o valor de _UnitPrice_ pelos valores de _Quantity_ para obter os custos pay as you go estimados, caso o desconto de reserva não se aplique à utilização.
2. Obtenha os custos de reserva. Some os valores de _Custo_ para obter o valor monetário do que pagou pela instância reservada. O valor inclui os custos utilizados e não utilizados da reserva.
3. Subtraia os custos da reserva dos custos pay as you go estimados para obter a poupança estimada.

Tenha em atenção que se tiver uma reserva subutilizada, a entrada _UnusedReservation_ para _ChargeType_ passa a ser um fator a considerar. Quando tem uma reserva totalmente utilizada, recebe o máximo de poupanças possível. Qualquer quantidade de _UnusedReservation_ reduz as poupanças.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Compras de reserva e amortização na análise de custo

Os custos de reserva estão disponíveis na [análise de custo](https://aka.ms/costanalysis). Por predefinição, a análise de custo mostra **Custo real**, que é o modo como os custos serão mostrados na sua fatura. Para ver as compras de reserva discriminadas e associadas aos recursos que utilizaram o benefício, mude para **Custo amortizado**:

![Exemplo que mostra onde selecionar o custo amortizado na análise de custo](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Agrupar por tipo de encargo para ver uma discriminação da utilização, das compras e dos reembolsos; ou por reserva para uma discriminação de reserva e custos a pedido. Lembre-se de que os únicos custos de reserva que verá ao examinar o custo real são as compras, mas os custos serão alocados aos recursos individuais que utilizaram o benefício ao examinar o custo amortizado. Também verá um novo tipo de encargo **UnusedReservation** ao examinar o custo amortizado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

- [O que são as reservas do Azure?](save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../../azure-sql/database/reserved-capacity-overview.md)
- [Gerir o Azure Reservations](manage-reserved-vm-instance.md)
- [Compreender como o desconto das reservas é aplicado](../manage/understand-vm-reservation-charges.md)
- [Compreender a utilização de reservas na sua subscrição Pay As You Go](understand-reserved-instance-usage.md)
- [Custos de software Windows não incluídos nas Reservas](reserved-instance-windows-software-costs.md)