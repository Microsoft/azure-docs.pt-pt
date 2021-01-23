---
title: Planear e gerir os custos da Azure API Management
description: Saiba como planear e gerir os custos para a Azure API Management utilizando a análise de custos no portal Azure.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: e171e642440b7c6c99353169e426a722885f1bcf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725942"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planear e gerir custos para a Gestão da API

Este artigo descreve como planeia e gere os custos para a Azure API Management. Em primeiro lugar, utiliza a calculadora de preços Azure para ajudar a planear os custos de Gestão da API antes de adicionar quaisquer recursos para o serviço para estimar custos. Depois de ter começado a utilizar recursos de Gestão de API, use funcionalidades de Gestão de Custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. 

Os custos para a Gestão da API são apenas uma parte dos custos mensais na sua fatura Azure. Embora este artigo explique como planear e gerir custos para a API Management, você está cobrado para todos os serviços e recursos Azure usados na sua subscrição Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos na Gestão de Custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Estimativa de custos antes de utilizar a API Management

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar a API Management. 

1. Pesquisa rumo à *Gestão da API,* ou selecione   >  **Gestão de API de** Integração.
1. Selecione **Ver** para adicionar uma estimativa de custos padrão para uma instância de serviço de Gestão API.

> [!NOTE]
> Os custos apresentados neste exemplo são apenas para fins de demonstração. Consulte [os preços da API Management](https://azure.microsoft.com/pricing/details/api-management/) para obter as últimas informações sobre preços.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Estimativa de custos para o nível de desenvolvedor":::

* A estimativa de custos por defeito baseia-se numa instância de serviço de Gestão API no [nível](api-management-features.md) de serviço **do Desenvolvedor** com 1 [unidade de capacidade](api-management-capacity.md). O nível de Desenvolvedor destina-se a casos e avaliações de utilização não produtivo. Não é apoiado por um acordo de nível de serviço.

* Para estimar os custos para unidades de capacidade adicionais ou um nível de serviço diferente, selecione outras opções das **unidades** e **descidas de nível.**

* Dependendo da disponibilidade de recursos e do nível de serviço, podem ser aplicados custos adicionais para a utilização de [gateways auto-hospedados](self-hosted-gateway-overview.md).

Para obter preços adicionais e detalhes sobre funcionalidades, consulte:

* [Preços de gestão da API](https://azure.microsoft.com/pricing/details/api-management/)
* [Comparação baseada em recursos dos escalões de Gestão API da Azure](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Utilização de crédito monetário com API Management

Você pode pagar os encargos de Gestão da API com o seu pré-pagamento Azure (anteriormente chamado compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar os recursos da Azure com a API Management, incorre em custos. Os custos da unidade de utilização do recurso azul variam em intervalos de tempo (segundos, minutos, horas e dias) ou por utilização unitária (bytes, megabytes, e assim por diante). Assim que a utilização da API Management começa, os custos são incorridos e você pode ver os custos na [análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Quando utiliza a análise de custos, vê os custos da Gestão de API em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Vê-se também os custos contra os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E vê onde pode ter ocorrido gastos excessivos. Se criou orçamentos, também pode ver facilmente onde são ultrapassados.

> [!NOTE]
> Os custos apresentados neste exemplo são apenas para fins de demonstração. Os seus custos variarão consoante o uso de recursos e os preços atuais.

Para visualizar os custos de gestão da API na análise de custos:

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com).
1. Abra a janela **De Gestão de Custos + Faturação,** selecione **a gestão** de custos do menu e, em seguida, selecione um **âmbito de faturação**. Por exemplo, selecione uma subscrição da lista.
1. Selecione **a Gestão** de Custos do menu e, em seguida, selecione **análise de custos**.
1. Por padrão, os custos mensais para todos os serviços são apresentados na primeira tabela de donuts. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Custos mensais para a subscrição":::

1. Para reduzir os custos de um único serviço, como a Gestão de API, selecione **adicionar filtro** e, em seguida, selecione o nome **de Serviço**. Em seguida, selecione **API Management**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Exemplo mostrando custos acumulados para a Gestão de API":::

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões Azure (localizações) e da API Management são igualmente indicados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa que outros façam análises adicionais de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Outras formas de gerir e reduzir custos para a Gestão de API

### <a name="choose-tier"></a>Escolha o nível

Reveja a [comparação baseada em funcionalidades dos níveis de Gestão Azure API](api-management-features.md) para ajudar a decidir qual o nível de serviço que pode ser apropriado para os seus cenários. Os diferentes níveis de serviço suportam combinações de funcionalidades e capacidades projetadas para vários casos de uso, com custos diferentes. [Atualize](upgrade-and-scale.md) para um nível de serviço diferente a qualquer momento.

* O nível de serviço **de consumo** oferece uma opção leve e sem servidor que não incorre em custos fixos. É faturado com base no número de chamadas da API para o serviço acima de um determinado limiar. A capacidade também escala automaticamente com base na carga do serviço.
* Outros níveis de Gestão da API incorrem em custos mensais, e fornecem conjuntos de características mais ricos e de produção para avaliação e carga de trabalho de produção.

### <a name="scale-using-capacity-units"></a>Escala usando unidades de capacidade

Com exceção do nível de serviço de consumo, a API Management suporta a escala através da adição ou remoção de [*unidades de capacidade.*](api-management-capacity.md) À medida que a carga aumenta num caso de Gestão de API, a adição de unidades de capacidade pode ser mais económica do que a atualização para um nível de serviço mais elevado. O número máximo de unidades depende do nível de serviço.

Cada unidade de capacidade tem uma certa capacidade de processamento de pedido que depende do nível do serviço. Por exemplo, uma unidade do nível Básico tem uma produção máxima estimada de aproximadamente 1.000 pedidos por segundo. 

Ao adicionar ou remover unidades, a capacidade e a escala de custos proporcionalmente. Por exemplo, duas unidades do nível Standard fornecem uma produção estimada de aproximadamente 2.000 pedidos por segundo. A produção real pode diferir devido à dimensão de pedidos ou respostas, padrões de conexão, número de clientes que fazem pedidos e outros fatores.

[Monitorize](api-management-howto-use-azure-monitor.md) a métrica de Capacidade para o seu exemplo de Gestão de API para ajudar a tomar decisões quer dimensionar ou atualizar um caso de Gestão de API para acomodar mais carga.

## <a name="next-steps"></a>Próximos passos

- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.
- Conheça a [capacidade de](api-management-capacity.md)Gestão da API.
- Consulte os passos para escalar e atualizar a Gestão da API utilizando o [portal Azure,](upgrade-and-scale.md)e saiba sobre [autoscaling](api-management-howto-autoscale.md).