---
title: Plano de gestão de custos para a Azure Cognitive Services
description: Saiba como planear e gerir os custos para os Serviços Cognitivos Azure utilizando a análise de custos no portal Azure.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101699934"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planear e gerir os custos dos Serviços Cognitivos da Azure

Este artigo descreve como planeia e gere os custos para os Serviços Cognitivos Azure. Em primeiro lugar, você usa a calculadora de preços Azure para ajudar a planear os custos dos Serviços Cognitivos antes de adicionar quaisquer recursos para o serviço para estimar custos. Em seguida, ao adicionar recursos Azure, reveja os custos estimados. Depois de ter começado a utilizar recursos de Serviços Cognitivos (por exemplo, Fala, Visão De Computador, LUIS, Análise de Texto, Tradutor, etc.), utilize funcionalidades de Gestão de Custos para definir orçamentos e monitorizar custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. Os custos dos Serviços Cognitivos são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir custos para serviços cognitivos, você está cobrado para todos os serviços e recursos Azure usados na sua assinatura Azure, incluindo os serviços de terceiros.

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos na Gestão de Custos suporta a maioria dos tipos de conta Azure, mas nem todos. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Para visualizar dados de custos, precisa pelo menos ler o acesso a uma conta Azure. Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Estimativa de custos antes de utilizar serviços cognitivos

Utilize a [calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para estimar os custos antes de adicionar Serviços Cognitivos.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Calculadora de preços Azure para serviços cognitivos" border="true":::

Ao adicionar novos recursos ao seu espaço de trabalho, volte a esta calculadora e adicione aqui o mesmo recurso para atualizar as suas estimativas de custos.

Para mais informações, consulte [os preços dos Serviços Cognitivos Azure.](https://azure.microsoft.com/pricing/details/cognitive-services/)

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Compreender o modelo completo de faturação dos Serviços Cognitivos

Os Serviços Cognitivos funcionam na infraestrutura Azure que [acumula custos](https://azure.microsoft.com/pricing/details/cognitive-services/) quando implementa o novo recurso. É importante entender que infraestruturas adicionais podem acumular custos. Tens de gerir esse custo quando fazes alterações aos recursos implantados. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Custos que normalmente acumulam com serviços cognitivos

Normalmente, depois de implementar um recurso Azure, os custos são determinados pelo seu nível de preços e as chamadas API que faz para o seu ponto final. Se o serviço que está a utilizar tiver um nível de compromisso, passar por cima das chamadas atribuídas no seu nível pode incorrer numa taxa de sobrecarga.

Os custos adicionais podem acumular-se na utilização destes serviços:

#### <a name="qna-maker"></a>Criador de FAQ

Quando cria recursos para o QnA Maker, podem também ser criados recursos para outros serviços Azure. Estas incluem:

- [Serviço de Aplicações Azure (para o tempo de funcionamento)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Custos que podem acumular após a supressão de recursos

#### <a name="qna-maker"></a>Criador de FAQ

Depois de eliminar os recursos da QnA Maker, os seguintes recursos podem continuar a existir. Continuam a acumular custos até os eliminares.

- [Serviço de Aplicações Azure (para o tempo de funcionamento)](https://azure.microsoft.com/pricing/details/app-service/)
- [Pesquisa Cognitiva Azure (para os dados)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Utilização do crédito de pré-pagamento da Azure com serviços cognitivos

Você pode pagar os custos dos Serviços Cognitivos com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="monitor-costs"></a>Monitorizar os custos

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Ao utilizar os recursos da Azure com os Serviços Cognitivos, incorre em custos. Os custos da unidade de utilização do recurso azul variam em intervalos de tempo (segundos, minutos, horas e dias) ou por utilização unitária (bytes, megabytes, e assim por diante). Assim que a utilização de um Serviço Cognitivo (ou Serviços Cognitivos) começa, os custos são incorridos e você pode ver os custos na [análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Quando utiliza a análise de custos, vê os custos dos Serviços Cognitivos em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Vê-se também os custos contra os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E vê onde pode ter ocorrido gastos excessivos. Se criou orçamentos, também pode ver facilmente onde são ultrapassados.

Para visualizar os custos dos Serviços Cognitivos na análise de custos:

1. Inicie sessão no portal do Azure.
2. Abra o âmbito no portal Azure e selecione **análise de custos** no menu. Por exemplo, vá a **Subscrições,** selecione uma subscrição da lista e, em seguida, selecione  **análise de custos** no menu. Selecione **Scope** para mudar para um âmbito diferente na análise de custos.
3. Por padrão, o custo dos serviços é mostrado na primeira tabela de donuts. Selecione a área na tabela marcada serviços cognitivos.

Os custos mensais reais são mostrados quando inicialmente abre a análise de custos. Aqui está um exemplo mostrando todos os custos mensais de uso.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Exemplo mostrando custos acumulados para uma subscrição":::

- Para reduzir os custos para um único serviço, como serviços cognitivos, **selecione adicionar filtro** e, em seguida, selecione o nome de **Serviço**. Em seguida, **selecione Serviços Cognitivos**.

Aqui está um exemplo mostrando custos apenas para serviços cognitivos.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Exemplo mostrando custos acumulados para serviços cognitivos":::

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões de Azure (localizações) e dos serviços cognitivos por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não crie acidentalmente novos recursos que lhe custem dinheiro adicional. Para saber mais sobre as opções do filtro quando criar um orçamento, consulte [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer análise adicional de dados para custos. Por exemplo, as equipas de financiamento podem analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="next-steps"></a>Passos seguintes

- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.