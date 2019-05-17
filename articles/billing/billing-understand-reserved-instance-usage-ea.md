---
title: Compreender a utilização de reservas do Azure para contratos Enterprise
description: Saiba como ler sua utilização para compreender a forma como é aplicada a reserva do Azure para a inscrição da sua empresa.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: 09242eaa6058229226062801f5f71f2bf4c7a9e8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789380"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Obter os custos de reserva do contrato Enterprise e utilização

Dados de utilização e custos de reserva, estão disponíveis para clientes com contrato Enterprise no portal do Azure e REST APIs. Este artigo ajuda-o:

- Obter dados de compra de reserva
- Obter dados de utilização insuficientemente de reserva
- Amortizar os custos de reserva
- Encargos de utilização de reserva
- Calcular a economia de reserva

Encargos do Marketplace são consolidados em dados de utilização. Ver as cobranças da primeira utilização de terceiros, a utilização de marketplace e compras de uma origem de dados individual.

## <a name="reservation-charges-in-azure-usage-data"></a>Custos de reserva nos dados de utilização do Azure

Dados são divididos em dois conjuntos de dados separados: _Custo real_ e _amortizado custo_. Como diferem os esses dois conjuntos de dados:

**Custo real** -fornece dados para reconciliar com a sua fatura mensal. Estes dados tem custos de compra de reserva. Ele tem zero EffectivePrice a utilização que receberam o desconto de reserva.

**Custo de amortizado** -o recurso EffectiveCost que obtém o desconto de reserva é o custo é calculado da instância reservada. O conjunto de dados também tem os custos de reserva não utilizada. A soma do custo de reserva e reserva não utilizada fornece o custo amortizado diário da reserva.

Comparação de dois conjuntos de dados:

| Dados | Conjunto de dados de custo real | Conjunto de dados de custo amortizado |
| --- | --- | --- |
| Compras de reserva | Está disponível nesta vista.<br>  Para obter este filtro de dados em ChargeType = &quot;Compra&quot;. <br> Consulte Reservationid:{0 ou ReservationName saber qual o custo é para reserva.  | Não é aplicável a esta vista. <br> Os custos de compra não são fornecidos nos dados amortizados. |
| effectivePrice | O valor é zero para utilização que obtém o desconto de reserva. | O valor é o custo de rateada por hora de reserva para utilização com o desconto de reserva. |
| Reserva não utilizada (fornece o número de horas que a reserva não foi usada num dia e o valor monetário o desperdício) | Não aplicável nesta vista. | Está disponível nesta vista.<br> Para obter esses dados, filtre por ChargeType = &quot;UnusedReservation&quot;.<br>  Consulte Reservationid:{0 ou ReservationName saber qual reserva foi subutilizada. Esta é a quantidade da reserva foi desperdiçadas com para o dia.  |
| UnitPrice (preço do recurso da sua folha de preços) | Disponível | Disponível |

Outras informações disponíveis nos dados de utilização do Azure foi alterado:

- Produtos e as informações de medidor - o Azure não substitui o medidor originalmente consumido com o Reservationid:{0 e ReservationName, como fazia anteriormente.
- Reservationid:{0 e ReservationName – eles são seus próprios campos de dados. Anteriormente, ele usado para estar disponível apenas em AdditionalInfo.
- ProductOrderId - o ID de encomenda de reserva, adicionado como seu próprio campo.
- ProductOrderName - o nome de produto da reserva adquirida.
- Termo - 12 meses ou 36 meses.
- RINormalizationRatio - disponível em AdditionalInfo. Este é o rácio em que a reserva se aplica para o registo de utilização. Se a flexibilidade de tamanho de instância está ativada para sua reserva, em seguida, pode aplicar a outros tamanhos. O valor mostra a taxa de que a reserva foi aplicada para o registo de utilização.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obterem dados de utilização de consumo e de reserva do Azure com a API

Pode obter os dados com a API ou transferi-lo a partir do portal do Azure.

Chama o [API de detalhes de utilização](/rest/api/consumption/usagedetails/list) com a versão de API &quot;2019-04-01-pré-visualização&quot; para obter os novos dados. Para obter detalhes sobre a terminologia, consulte [termos de utilização](billing-understand-your-usage.md). O chamador deve ser um administrador empresarial para o contrato de enterprise utilizando as [portal EA](https://ea.azure.com). Administradores da empresa só de leitura também pode obter os dados.

Os dados não estão disponíveis no [APIs de relatórios para os clientes empresariais - detalhes de utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Eis uma chamada de exemplo para a API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Para obter mais informações sobre {enrollmentId} e {billingPeriodId}, consulte a [detalhes de utilização – lista](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) artigo de API.

Informações na tabela a seguir sobre métricas e filtro podem ajudar a resolver problemas comuns de reserva.

| **Tipo de dados de API** | Ação de chamada de API |
| --- | --- |
| **Todas as despesas (utilização e compras)** | Substitua {métrica} ActualCost |
| **Utilização que recebeu o desconto de reserva** | Substitua {métrica} ActualCost<br>Substituir {filtro} pelo: properties/reservationId%20ne%20 |
| **Utilização que não obtiveram o desconto de reserva** | Substitua {métrica} ActualCost<br>Substituir {filtro} pelo: properties/reservationId%20eq%20 |
| **Custos amortizados (utilização e compras)** | Substitua {métrica} AmortizedCost |
| **Relatório de reserva não utilizada** | Substitua {métrica} AmortizedCost<br>Substituir {filtro} pelo: properties/ChargeType%20eq%20'UnusedReservation' |
| **Compras de reserva** | Substitua {métrica} com ActualCostReplace {filtro}: properties/ChargeType%20eq%20'Purchase'  |
| **Reembolsos** | Substitua {métrica} ActualCost<br>Substituir {filtro} pelo: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Transfira o ficheiro CSV de utilização com novos dados

Se for um administrador EA, pode transferir o ficheiro CSV que contém os novos dados de utilização do portal do Azure. Estes dados não estão disponíveis a partir da [portal EA](https://ea.azure.com).

No portal do Azure, navegue até [gestão de custos + faturação](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de cobrança.
2. Clique em **utilização e custos**.
3. Clique em **Transferir**.  
![Exemplo que mostra onde pode transferir o ficheiro de dados de utilização CSV no portal do Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Na **transferir utilização + custos** , em **versão 2 dos detalhes de utilização** , selecione **todas as despesas (utilização e compras)** e, em seguida, clique em transferir. Repita esta operação para **amortizado custos (utilização e compras)**.

Os ficheiros CSV que transferir a contenham custos reais e os custos amortizados.

## <a name="common-cost-and-usage-tasks"></a>Tarefas comuns de custos e utilização

As secções seguintes são tarefas comuns que utilizam a maioria das pessoas para ver os seus dados de custo e a utilização de reserva.

### <a name="get-reservation-purchase-costs"></a>Obtenha custos de compra de reserva

Os custos de compra de reserva estão disponíveis nos dados de custo real. Filtrar por _ChargeType = Compra_. Consulte ProductOrderID para determinar qual a compra é para a encomenda de reserva.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obter a quantidade de reserva subutilizados e os custos

Obter dados de custo amortizado e filtrar por _ChargeType_ _= UnusedReservation_. Obtém a quantidade de reserva não utilizada diária e o custo. Pode filtrar os dados para uma reserva ou através de encomenda de reserva _Reservationid:{0_ e _ProductOrderId_ campos, respectivamente. Se uma reserva de 100% utilizado, o registo tem uma quantidade de 0.

### <a name="amortize-reservation-costs"></a>Amortizar os custos de reserva

Obter dados de custo amortizado e filtro para uma utilização de ordem de reserva _ProductOrderID_ para obter os custos de amortizado diários para uma reserva.

### <a name="chargeback-for-a-reservation"></a>Estorno para uma reserva

Pode utilizar de reserva de estorno para outras organizações por etiquetas, grupos de recursos ou subscrição. Dados de custo amortizado fornecem valor monetário da utilização de uma reserva atentamente os seguintes tipos de dados:

- Recursos (como uma VM)
- Grupo de recursos
- Tags
- Subscrição

### <a name="get-the-blended-rate-for-chargeback"></a>Obter a taxa combinada para encargos

Para determinar a taxa combinada, obter os dados de custos amortizado e agregar o custo total. Para as VMs, pode utilizar informações MeterName ou ServiceType de dados JSON de AdditionalInfo. Divida o custo pela quantidade utilizada para obter a taxa combinada total.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Reserva de ideal de auditoria use, por exemplo, a flexibilidade de tamanho

Vários quantidade com o _RINormalizationRatio_, de AdditionalInfo. Os resultados indicam quantas horas de utilização de reserva foi aplicada no registo de utilização.

### <a name="determine-reservation-savings"></a>Determinar as poupanças de reserva

Obter os dados de custos Amortized e filtrar os dados para uma instância reservada. Em seguida:

1. Obtenha custos estimados de pay as you go. Multiplique o _UnitPrice_ valor com _quantidade_ estimado de valores para obter os custos de pay as you go, se o desconto de reserva não se aplicam à utilização de.
2. Obtenha os custos de reserva. Soma os _custo_ valores para obter o valor monetário do que paga pela instância reservada. Ele inclui os custos de utilizado e não utilizados da reserva.
3. Subtrai os custos de reserva de custos estimados de pay as you go para obter a poupança estimada.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>As compras de reserva e amortização na análise de custos do Azure

Custo de instância reservada está disponível no [modo de pré-visualização de análise de custo do Azure](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Por predefinição, a vista de dados de custo é para o custo real. Pode mudar para o custo amortizado. Eis um exemplo.

![Exemplo que mostra onde pode selecionar o custo amortizado na análise de custos](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Aplica filtros para ver as cobranças por um tipo de reserva ou custo. Agrupar por nome da reserva para ver os custos divididos por reservas.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, veja os artigos seguintes:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Compreender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Compreender a utilização de reserva para a sua subscrição pay as you go](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
