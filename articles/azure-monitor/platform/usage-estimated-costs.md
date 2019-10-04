---
title: Monitorizar a utilização e custos estimados no Azure Monitor
description: Descrição geral do processo de uso de utilização do Azure Monitor e a página de custos estimados
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936630"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorizar a utilização e custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços. Os artigos relacionados para componentes específicos do Azure Monitor incluem:
> - [Gerenciar o uso e os custos com logs de Azure monitor](manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar o uso de dados.
> - [Gerenciar o uso e os custos de Application insights](../../azure-monitor/app/pricing.md) descreve como analisar o uso de dados no Application insights.

No hub do Monitor do portal do Azure, o **utilização e custos estimados** página explica a utilização das principais funcionalidades de monitorização, tal como [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics ](https://azure.microsoft.com/pricing/details/log-analytics/), e [Application Insights do Azure](https://azure.microsoft.com/pricing/details/application-insights/). Para os clientes sobre os planos de preços disponíveis antes de Abril de 2018, isso também inclui a utilização do Log Analytics adquirida por meio das informações e análise da oferta.

Nesta página, os utilizadores podem ver a utilização de recursos nos últimos 31 dias, agregados por cada subscrição. Inícios de exploração de mostram tendências de utilização ao longo do período de 31 dias. Uma grande quantidade de dados tem de se reúnem para esta estimativa, portanto, seja paciente como a página for carregada.

Este exemplo mostra a utilização de monitorização e uma estimativa dos custos resultantes:

![Utilização e custos estimados portal captura de ecrã](./media/usage-estimated-costs/001.png)

Selecione a ligação na coluna mensal de utilização para abrir um gráfico que mostra as tendências de utilização ao longo do último período de 31 dias:

![Incluído por nó de barra captura de ecrã do gráfico](./media/usage-estimated-costs/002.png)

Eis outro uso semelhante e o resumo do custo. Este exemplo mostra uma subscrição no novo Abril de 2018 consumo baseado no modelo de preços. Tenha em atenção a falta de qualquer faturação baseada no nó. Ingestão de dados e o período de retenção para o Log Analytics e Application Insights agora são comunicados num novo medidor comuns.

![Utilização e custos estimados captura de ecrã portal - preços de Abril de 2018](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>Modelo preços

Em Abril de 2018, um [monitorização novo modelo de preços foi lançado](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/).  Isso apresenta preços com base em consumo para a nuvem ("pré-pago"). Paga apenas aquilo que utiliza, sem compromissos com base no nó. Estão disponíveis detalhes do novo modelo de preços para [alertas, métricas, notificações](https://azure.microsoft.com/pricing/details/monitor/), [do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Além do modelo pago conforme o uso, em setembro de 2019, adicionamos reservas de capacidade para Log Analytics que permitem que você economize até 25% em comparação com o preço pago conforme o uso. O preço de reserva de capacidade permite que você compre uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa paga conforme o uso. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços de reserva de capacidade.

Para a integração de clientes para o Log Analytics ou o Application Insights depois de 2 de Abril de 2018, o novo modelo de preços é a única opção. Para os clientes que já utilizam estes serviços, mudar para o novo modelo de preços é opcional.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>Avaliar o impacto do novo modelo de preços
O novo modelo de preços terão diferentes impactos sobre cada cliente com base nos seus padrões de utilização de monitorização. Para os clientes que estavam a utilizar o Log Analytics ou o Application Insights antes de 2 de Abril de 2018, o **utilização e custo estimado** qualquer alteração nos custos as estimativas de página no Azure Monitor se eles mover para o novo modelo de preços. Ele fornece a forma de mover uma subscrição para o novo modelo. Para a maioria dos clientes, o novo modelo de preços será vantajoso. Para os clientes com os padrões de utilização de dados especialmente elevado ou em regiões de custo mais elevado, isso pode não ser o caso.

Para ver uma estimativa dos seus custos para as subscrições que escolheu no **utilização e custos estimados** página, selecione a faixa azul junto à parte superior da página. É melhor fazer esta um subscrição por vez, uma vez que é o nível em que o novo modelo de preços pode ser adotado.

![Monitorizar a utilização e custos estimados no novo preço captura de ecrã do modelo](./media/usage-estimated-costs/004.png)

A nova página mostra uma versão semelhante da página anterior com uma faixa verde:

![Monitorizar a utilização e custos estimados no atual preços captura de ecrã do modelo](./media/usage-estimated-costs/005.png)

A página também mostra um conjunto diferente de medidores que correspondem ao novo modelo de preços. Esta lista é um exemplo:

- Informações e Analytics\Overage por nó
- Informações e Analytics\Included por nó
- Dados de utilização excedida de Insights\Basic de aplicação
- Dados da aplicação Insights\Included

O novo modelo de preços não tem as alocações de dados incluídos com base no nó. Por conseguinte, estes medidores de ingestão de dados são combinados num novo medidor de ingestão dados comuns denominado **partilhados ingestão de Services\Data**. 

Há outra alteração aos dados ingeridos no Log Analytics ou o Application Insights nas regiões com custos superiores. Dados para estas regiões de alto custo serão mostrados com os novos medidores regionais. Um exemplo é **ingestão de dados (e.u.a. Centro-Oeste)** .

> [!NOTE]
> O por subscrição estimado custos não fatorar em nível da conta por elegibilidades de nó da subscrição do Operations Management Suite (OMS). Neste caso, consulte seu representante de conta para uma discussão mais aprofundada sobre o novo modelo de preços.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>Os novos preços modelo e elegibilidades da subscrição do Operations Management Suite

Os clientes que compraram o Microsoft Operations Management Suite E1 e E2 são elegíveis para elegibilidade de ingestão de dados por nó para [do Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Para receber esta elegibilidade para áreas de trabalho do Log Analytics ou recursos do Application Insights numa determinada subscrição: 

- Modelo de preços da subscrição tem de permanecer no modelo de pré-Abril de 2018.
- Áreas de trabalho do log Analytics, devem utilizar o "por nó (OMS)" escalão de preço.
- Recursos do Application Insights devem utilizar o plano de preços "Enterprise".

Dependendo do número de nós do conjunto de aplicações adquirido pela sua organização, mover algumas subscrições para o novo modelo de preços podem ser vantajosos, mas isso requer consideração cuidadosa. Em geral, é aconselhável simplesmente para manter o modelo de pré-Abril de 2018, tal como descrito acima.

> [!WARNING]
> Se a sua organização tiver comprado o Microsoft Operations Management Suite E1 e E2, seja normalmente melhor manter as suas subscrições no modelo de preços do pre-Abril de 2018. 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>Alterações quando estiver a mover para o novo modelo de preços

O novo modelo de preços simplifica o Log Analytics e as opções para apenas uma única camada (ou plano) de preços do Application Insights. Mover uma subscrição para o novo modelo será de preços:

- Alterar o escalão de preço para cada Log Analytics para o novo escalão por GB (chamado "pergb2018" no Azure Resource Manager)
- Quaisquer recursos do Application Insights no plano do Enterprise é alterado para o plano básico.

A estimativa de custos mostra os efeitos dessas alterações.

> [!WARNING]
> Aqui uma observação importante se utilizar o Azure Resource Manager ou o PowerShell para implementar [do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) ou [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) numa subscrição foram movidas para o novo modelo de preços. Se especificar um escalão/plano de preços que não seja "pergb2018" para o Log Analytics ou "Básico" para o Application Insights, em vez a falhar a implementação devido à especificação de um inválido/plano, de escalão de preço conseguirá **mas irá utilizar apenas o válido escalão/plano de preços** (isto não é aplicável para o escalão gratuito de análise do registo em que é gerada uma mensagem de escalão de preço inválida).
>

## <a name="moving-to-the-new-pricing-model"></a>Mover para o novo modelo de preços

Se você decidiu adotar o novo modelo de preços para uma determinada assinatura, vá para cada Application Insights recurso, abra o **uso e os custos estimados** e certifique-se de que ele esteja no tipo de preço básico e vá para cada espaço de trabalho log Analytics, abra cada umPágina de tipo de preço e altere para o tipo de preço **por GB (2018)** . 

> [!NOTE]
> O requisito de que todos os Application Insights recursos e espaços de trabalho de Log Analytics em uma determinada assinatura adotam o modelo de preços mais recente agora foi removido, permitindo maior flexibilidade e configuração mais fácil. 

## <a name="automate-moving-to-the-new-pricing-model"></a>Automatizar a migração para o novo modelo de preços

Conforme observado acima, o não é mais um requisito para mover todos os recursos de monitoramento em uma assinatura para o novo modelo de preços ao mesmo tempo e, portanto, a ação ``migratetonewpricingmodel`` não terá mais nenhum efeito. Agora você pode mover Application Insights recursos e Log Analytics espaços de trabalho separadamente para os tipos de preços mais recentes.  

Automatizar essa alteração é documentado para Application Insights usando [set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) com ``-PricingPlan "Basic"`` e log Analytics usando [set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) com ``-sku "PerGB2018"``. 
