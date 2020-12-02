---
title: Plano de gestão de custos para a Azure ExpressRoute
description: Saiba como planear e gerir os custos para o Azure ExpressRoute utilizando a análise de custos no portal Azure.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 38b6aa35c1b12e3fdaed4a60cd6e241a05d42efe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501512"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planear e gerir os custos da Azure ExpressRoute

Este artigo descreve como planeia e gere os custos para o ExpressRoute. Em primeiro lugar, utiliza a calculadora de preços Azure para ajudar a planear os custos da ExpressRoute antes de adicionar quaisquer recursos para o serviço para estimar custos. Em seguida, ao adicionar recursos Azure, reveja os custos estimados. 

Depois de ter começado a utilizar recursos ExpressRoute, use funcionalidades de Gestão de Custos para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. 

Tenha em mente que os custos para o ExpressRoute são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir os custos para o ExpressRoute, você está cobrado para todos os serviços e recursos Azure usados na sua subscrição Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos na Gestão de Custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. 

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Local vs. Standard vs. Premium

O ExpressRoute tem três circuitos diferentes SKU: [*Local,*](./expressroute-faqs.md#expressroute-local) *Standard* e [*Premium.*](./expressroute-faqs.md#expressroute-premium) A forma como é cobrado pelo seu uso ExpressRoute varia entre estes três tipos de SKU. Com o SKU local, é automaticamente cobrado um plano de dados ilimitado. Com o Standard e o Premium SKU, pode selecionar entre um Medido ou um plano de dados Ilimitado. Todos os dados de entrada são gratuitos, exceto quando se utiliza o complemento Global Reach. É importante entender quais os tipos de SKU e plano de dados que melhor funcionam para a sua carga de trabalho para otimizar melhor o custo e o orçamento.

## <a name="estimate-costs"></a>Cálculo de custos

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de criar um circuito ExpressRoute. 

1. À esquerda, selecione **Networking,** em seguida, selecione **Azure ExpressRoute** para começar. 

1. Selecione a *Zona* apropriada dependendo da sua localização de espreitar. Consulte os [fornecedores de conectividade ExpressRoute](./expressroute-locations-providers.md#partners) para selecionar a *Zona* apropriada no drop-down. 

1. Em seguida, selecione o *SKU,* *Circuit Speed* e o *Plano de Dados* para o quais gostaria de uma estimativa. 

1. Na *transferência adicional de dados de saída,* introduza uma estimativa em GB de quantos dados de saída poderá utilizar ao longo de um mês. 

1. Por último, pode adicionar o *Global Reach Add-on* à estimativa.

A imagem que se segue mostra a estimativa de custos utilizando a calculadora:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimativa de custo expressRoute na calculadora Azure":::

Para mais informações, consulte [os preços da Azure ExpressRoute.](https://azure.microsoft.com/pricing/details/expressroute/)

### <a name="expressroute-gateway-estimated-cost"></a>Custo estimado do gateway ExpressRoute

Se estiver a utilizar um gateway ExpressRoute para ligar uma rede virtual ao circuito ExpressRoute, utilize os seguintes passos para estimar o custo de utilização do gateway.

1. À esquerda, selecione **Networking** e, em seguida, selecione **VPN Gateway** para começar. 

1. Selecione a *Região* para o gateway e, em seguida, *altere tipo* para **ExpressRoute Gateways**.

1. Selecione o *Tipo gateway* a partir do drop-down.

1. Insira as *horas gateway*. (720 horas = 30 dias)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Compreenda o modelo completo de faturação para o ExpressRoute

O ExpressRoute funciona na infraestrutura Azure que acumula custos juntamente com o ExpressRoute quando implementa o novo recurso. É importante entender que infraestruturas adicionais podem acumular custos. Tens de gerir esse custo quando fazes alterações aos recursos implantados. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Custos que normalmente acumulam com o ExpressRoute

Quando criar um circuito ExpressRoute, poderá optar por criar uma porta de entrada ExpressRoute para ligar a sua rede virtual ao circuito. Os gateways são cobrados a uma taxa horária mais o custo de um circuito ExpressRoute. Consulte [os preços do ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) e selecione ExpressRoute Gateways para ver as tarifas para diferentes SKUs de gateway.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Os custos podem acumular-se após a supressão de recursos

Se tiver uma porta de entrada ExpressRoute depois de eliminar o circuito ExpressRoute, continuará a ser cobrado pelo custo até o eliminar.

### <a name="using-monetary-credit"></a>Utilização de Crédito Monetário

Pode pagar os encargos expressRoute com o seu crédito de compromisso monetário da EA. No entanto, não pode usar o crédito de compromisso monetário da EA para pagar os encargos com produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar os recursos da Azure com o ExpressRoute, incorre em custos. Os custos da unidade de utilização de recursos Azure variam em intervalos de tempo (segundos, minutos, horas e dias) ou por utilização unitária (bytes, megabytes, e assim por diante.) Assim que a utilização do ExpressRoute começar, os custos são incorridos e você pode ver os custos na [análise de custos](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quando utiliza a análise de custos, vê os custos do circuito ExpressRoute em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Vê-se também os custos contra os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E vê onde pode ter ocorrido gastos excessivos. Se criou orçamentos, também pode ver facilmente onde são ultrapassados.

Para visualizar os custos do ExpressRoute na análise de custos:

1. Inicie sessão no portal do Azure.

1. Vá a **Subscrições,** selecione uma subscrição da lista e, em seguida, selecione  **análise de custos** no menu. Selecione **Scope** para mudar para um âmbito diferente na análise de custos. Por padrão, o custo dos serviços é mostrado na primeira tabela de donuts.

    Os custos mensais reais são mostrados quando inicialmente abre a análise de custos. Aqui está um exemplo mostrando todos os custos mensais de uso.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Exemplo mostrando custos acumulados para uma subscrição":::
    

1.  Para reduzir os custos para um único serviço, como Expressroute, **selecione adicionar filtro** e, em seguida, selecione o nome de **Serviço**. Em seguida, selecione **ExpressRoute**.

    Aqui está um exemplo mostrando custos apenas para ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Exemplo mostrando custos acumulados para o ExpressRoute":::

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões de Azure (localizações) e do ExpressRoute por grupo de recursos também são indicados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets-and-alerts"></a>Criar orçamentos e alertas

Pode criar [orçamentos](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro.](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como os preços funcionam com a Azure ExpressRoute. Consulte [o preço geral da Azure ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.
