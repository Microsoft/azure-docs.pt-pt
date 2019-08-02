---
title: Entender o uso de reservas do Azure para Enterprise Agreements
description: Saiba como ler seu uso para entender como a reserva do Azure para seu registro empresarial é aplicada.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598095"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Obter Enterprise Agreement de uso e custos de reserva

Os custos de reserva e os dados de uso estão disponíveis para clientes Enterprise Agreement nas APIs de portal do Azure e REST. Este artigo ajuda você a:

- Obter dados de compra de reserva
- Saber qual assinatura, grupo de recursos ou recurso usou a reserva
- Estorno para utilização de reserva
- Calcular economias de reserva
- Obter dados de reserva de subutilização
- Amortizar custos de reserva

Os encargos do Marketplace são consolidados em dados de uso. Você exibe cobranças para uso de primeira parte, uso do Marketplace e compras de uma única fonte de dados.

## <a name="reservation-charges-in-azure-usage-data"></a>Encargos de reserva nos dados de uso do Azure

Os dados são divididos em dois conjuntos de dados separados: _Custo real_ e _custo amortizado_. Como esses dois conjuntos de valores diferem:

**Custo real** – fornece dados para reconciliar com sua fatura mensal. Esses dados têm custos de compra de reserva e detalhes do aplicativo de reserva. Com esses dados, você pode saber qual assinatura ou grupo de recursos ou recurso recebeu o desconto de reserva em um determinado dia. O EffectivePrice para o uso que recebe o desconto de reserva é zero.

**Custo amortizado** : esse conjunto de dados é semelhante ao conjunto de dados de custo real, exceto pelo fato de que-o EffectivePrice para o uso que obtém o desconto de reserva é o custo rateado da Reserva (em vez de ser zero). Isso ajuda você a saber o valor monetário do consumo de reserva por uma assinatura, um grupo de recursos ou um recurso e pode ajudá-lo a cobrar pela utilização de reserva internamente. O conjunto de e também tem horas de reserva não utilizadas. O conjunto de recursos não tem registros de compra de reserva. 

Comparação de dois conjuntos de dados:

| Data | Conjunto de dados de custo real | Conjunto de dados de custo amortizado |
| --- | --- | --- |
| Compras de reserva | Disponível nesta exibição.<br><br>  Para obter esse filtro de dados em chargetype &quot;=&quot;Purchase. <br><br> Consulte Reservaid ou Reservaname para saber de qual reserva o encargo é.  | Não aplicável a esta exibição. <br><br> Os custos de compra não são fornecidos em dados amortizados. |
| EffectivePrice | O valor é zero para uso que obtém o desconto de reserva. | O valor é o custo rateado por hora da reserva para uso que tem o desconto de reserva. |
| Reserva não utilizada (fornece o número de horas que a reserva não foi usada em um dia e o valor monetário do desperdício) | Não aplicável nesta exibição. | Disponível nesta exibição.<br><br> Para obter esses dados, filtre on chargetype = &quot;UnusedReservation&quot;.<br><br>  Consulte Reservaid ou Reservaname para saber qual reserva foi subutilizada. Essa é a quantidade de reserva desperdiçada no dia.  |
| PreçoUnitário (preço do recurso de sua folha de preços) | Disponível | Disponível |

Outras informações disponíveis nos dados de uso do Azure foram alteradas:

- Informações de produto e medidor – o Azure não substitui o medidor originalmente consumido por Reservaid e Reservaname, como fazia anteriormente.
- Reservaid e Reservaname-eles são seus próprios campos nos dados. Anteriormente, ele costumava estar disponível apenas em AdditionalInfo.
- ProductOrderId-a ID do pedido de reserva, adicionada como seu próprio campo.
- ProductOrderName-o nome do produto da reserva adquirida.
- Termo-12 meses ou 36 meses.
- RINormalizationRatio-disponível em AdditionalInfo. Essa é a razão na qual a reserva é aplicada ao registro de uso. Se a flexibilidade do tamanho da instância estiver habilitada em para sua reserva, ela poderá se aplicar a outros tamanhos. O valor mostra a taxa à qual a reserva foi aplicada para o registro de uso.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Obter dados de uso de reserva e consumo do Azure usando a API

Você pode obter os dados usando a API ou baixá-los de portal do Azure.

Você chama a [API de detalhes de uso](/rest/api/consumption/usagedetails/list) com &quot;a versão de&quot; API 2019-04-01-Preview para obter os novos dados. Para obter detalhes sobre a terminologia, consulte [termos de uso](billing-understand-your-usage.md). O chamador deve ser um administrador corporativo para o Enterprise Agreement usando o [portal de ea](https://ea.azure.com). Os administradores corporativos somente leitura também podem obter os dados.

Os dados não estão disponíveis em [APIs de relatório para clientes Enterprise-detalhes de uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Aqui está um exemplo de chamada para a API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Para obter mais informações sobre {enrollmentid} e {billingPeriodId}, consulte o artigo [detalhes de uso –](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API de lista.

As informações na tabela a seguir sobre métrica e filtro podem ajudar a resolver problemas comuns de reserva.

| **Tipo de dados de API** | Ação de chamada de API |
| --- | --- |
| **Todos os encargos (uso e compras)** | Substitua {Metric} por ActualCost |
| **Uso que obteve o desconto de reserva** | Substitua {Metric} por ActualCost<br><br>Substituir {Filter} por: Properties/reservaid% 20ne% 20 |
| **Uso que não obteve o desconto de reserva** | Substitua {Metric} por ActualCost<br><br>Substituir {Filter} por: Properties/reservaid% 20eq% 20 |
| **Encargos amortizados (uso e compras)** | Substitua {Metric} por AmortizedCost |
| **Relatório de reserva não utilizado** | Substitua {Metric} por AmortizedCost<br><br>Substituir {Filter} por: Properties/Encargotype% 20eq% 20 ' UnusedReservation ' |
| **Compras de reserva** | Substitua {Metric} por ActualCost<br><br>Substitua {Filter} por: Properties/Encargotype% 20eq% 20 "Purchase"  |
| **Reembolsos** | Substitua {Metric} por ActualCost<br><br>Substitua {Filter} por: Properties/Encargotype% 20eq% 20 ' reembolso ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Baixar o arquivo CSV de uso com novos dados

Se você for um administrador de EA, poderá baixar o arquivo CSV que contém novos dados de uso de portal do Azure. Esses dados não estão disponíveis no [portal do ea](https://ea.azure.com).

Na portal do Azure, navegue até [Gerenciamento de custos + cobrança](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Selecione a conta de cobrança.
2. Clique em **uso +** encargos.
3. Clique em **Transferir**.  
![Exemplo mostrando onde baixar o arquivo de dados de uso de CSV no portal do Azure](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. Em **baixar uso +** encargos, em **detalhes de uso versão 2** , selecione **todos os encargos (uso e compras)** e clique em baixar. Repita para **encargos amortizados (uso e compras)** .

Os arquivos CSV que você baixa contêm custos reais e custos amortizados.

## <a name="common-cost-and-usage-tasks"></a>Tarefas comuns de custo e uso

As seções a seguir são tarefas comuns que a maioria das pessoas usam para exibir seus dados de uso e custo de reserva.

### <a name="get-reservation-purchase-costs"></a>Obter custos de compra de reserva

Os custos de compra de reserva estão disponíveis em dados de custo reais. Filtro para _encargotype = compra_. Consulte ProductOrderID para determinar para qual ordem de reserva a compra é.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Obter quantidade e custos de reserva subutilizados

Obtenha dados de custo amortizados e filtro para _chargetype_ _= UnusedReservation_. Você Obtém a quantidade diária de reserva não utilizada e o custo. Você pode filtrar os dados para uma reserva ou ordem de reserva usando os campos reservaid e _ProductOrderId_ , respectivamente. Se uma reserva fosse 100% utilizada, o registro terá uma quantidade de 0.

### <a name="amortize-reservation-costs"></a>Amortizar custos de reserva

Obtenha dados de custo amortizados e filtre por uma ordem de reserva usando _ProductOrderID_ para obter custos de amortização diários para uma reserva.

### <a name="chargeback-for-a-reservation"></a>Estorno para uma reserva

Você pode usar a reserva de chargeback para outras organizações por assinatura, grupos de recursos ou marcas. Os dados de custo amortizado fornecem o valor monetário da utilização de uma reserva nos seguintes tipos de dados:

- Recursos (como uma VM)
- Resource group
- Tags
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Obter a taxa combinada de estorno

Para determinar a taxa combinada, obtenha os dados de custos amortizados e agregue o custo total. Para VMs, você pode usar as informações de Medidorname ou ServiceType de dados JSON AdditionalInfo. Divida o custo total pela quantidade usada para obter a taxa combinada.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Auditar o uso de reserva ideal para flexibilidade de tamanho de instância

Várias quantidades com _RINormalizationRatio_, de AdditionalInfo. Os resultados indicam Quantas horas de uso de reserva foram aplicadas ao registro de uso.

### <a name="determine-reservation-savings"></a>Determinar a economia de reserva

Obtenha os dados de custos amortizados e filtre os dados de uma instância reservada. Clique

1. Obtenha os custos pagos conforme o uso estimado. Multiplique o valor _PreçoUnitário_ por valores de _quantidade_ para obter os custos de pagamento conforme o uso estimados, se o desconto de reserva não se aplicar à utilização.
2. Obtenha os custos de reserva. Some os valores de _custo_ para obter o valor monetário do que você pagou para a instância reservada. Ele inclui os custos usados e não utilizados da reserva.
3. Subtraia os custos de reserva dos custos pagos conforme o uso estimados para obter a economia estimada.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Compras de reserva e amortização na análise de custo

Os custos de reserva estão disponíveis na [análise de custo](https://aka.ms/costanalysis). Por padrão, a análise de custo mostra o **custo real**, que é o modo como os custos serão mostrados em sua fatura. Para exibir as compras de reserva divididas e associadas aos recursos que usaram o benefício, mude para o **custo amortizado**:

![Exemplo mostrando onde selecionar o custo amortizado na análise de custo](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Agrupar por tipo de encargo para ver uma interrupção do uso, das compras e dos reembolsos; ou por reserva para uma divisão de reserva e custos sob demanda. Lembre-se de que os únicos custos de reserva que você verá ao examinar o custo real são compras, mas os custos serão alocados para os recursos individuais que usaram o benfit ao examinar o custo amortizado. Você também verá um novo tipo de encargo **UnusedReservation** ao examinar o custo amortizado.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [O que são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Efetuar o pré-pagamento de Máquinas Virtuais com o Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Efetuar o pré-pagamento de recursos de computação da Base de Dados SQL com a capacidade reservada da Base de Dados SQL do Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gerir o Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de reserva para sua assinatura pré-paga](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não incluídos com reservas](billing-reserved-instance-windows-software-costs.md)
