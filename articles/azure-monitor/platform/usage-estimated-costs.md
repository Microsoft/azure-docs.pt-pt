---
title: Monitorizar a utilização e custos estimados no Azure Monitor
description: Descrição geral do processo de uso de utilização do Azure Monitor e a página de custos estimados
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658820"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorizar a utilização e custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como visualizar o uso e os custos estimados em várias funcionalidades de monitorização do Azure. Artigos relacionados para componentes específicos do Monitor Azure incluem:
> - [Gerir a utilização e os custos com o Azure Monitor Logs](manage-cost-storage.md) descreve como controlar os seus custos alterando o seu período de retenção de dados e como analisar e alertar sobre a utilização dos seus dados.
> - [Gerir o uso e os custos para a Aplicação Insights](../../azure-monitor/app/pricing.md) descreve como analisar o uso de dados em Insights de Aplicação.

## <a name="azure-monitor-pricing-model"></a>Modelo de preços do Monitor Azure

O modelo básico de faturação do Monitor Azure é um preço baseado no consumo ("Pay-As-You-Go"). Só paga o que utilizar. Os detalhes de preços estão disponíveis para [alerta, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e Insights [de Aplicação.](https://azure.microsoft.com/pricing/details/application-insights/) 

Além do modelo Pay-As-You-Go para dados de registo, o Log Analytics tem Reservas de Capacidade, que lhe permitem economizar até 25% em comparação com o preço pay-As-You-Go. O preço da reserva de capacidade permite-lhe comprar uma reserva a partir de 100 GB/dia. Qualquer utilização acima do nível de reserva será faturada à taxa Pay-As-You-Go. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços da Reserva de Capacidade.

Alguns clientes terão acesso aos [antigos níveis](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) de preços do Log Analytics e ao legado nível de preços da [Aplicação Empresarial Insights.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Compreender os custos do seu Monitor Azure

Há duas fases para compreender os custos. A primeira é quando se considera o Monitor Azure como a sua solução de monitorização. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerir o seu ambiente

Se ainda não estiver a utilizar registos do Monitor Azure, pode utilizar a calculadora de [preços do Monitor Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização do Monitor Azure. Comece por introduzir o "Monitor Azure" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Percorra a página até ao Monitor Azure e selecione uma das opções a partir do dropdown tipo:

- Consultas métricas e alertas  
- Log Analytics
- Application Insights

Em cada uma delas, a calculadora de preços irá ajudá-lo a estimar os seus custos prováveis com base na sua utilização esperada.

Por exemplo, com o Log Analytics pode introduzir o número de VMs e o GB de dados que espera recolher de cada VM. Normalmente, 1 GB a 3 GB de mês de dados é ingerido a partir de um VM Azul típico. Se já está a avaliar os Registos do Monitor Do Azure, pode utilizar as suas estatísticas de dados a partir do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorizados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o volume de dados que o seu espaço de [trabalho está a ingerir](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).

Da mesma forma para os Insights de Aplicação, se ativar a funcionalidade "Estimar o volume de dados com base na atividade da aplicação", pode fornecer informações sobre a sua aplicação (pedidos por mês e visualizações de página por mês, caso recolha telemetria do lado do cliente), e, em seguida, a calculadora irá dizer-lhe a quantidade mediana e 90%a quantidade de dados recolhidos por aplicações semelhantes. Estas aplicações abrangem a gama de configurações de Insights de Aplicação (por exemplo, algumas têm amostragem predefinida, algumas não têm amostragem, etc.), pelo que ainda tem o controlo para reduzir o volume de dados que ingere muito abaixo do nível mediano utilizando a amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão a ver. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) sobre a estimativa de custos para insights de aplicação.

### <a name="understanding-your-usage-and-estimated-costs"></a>Compreender o seu uso e custos estimados

É importante compreender e rastrear o seu uso uma vez usando o Monitor Azure, e existem um conjunto rico de ferramentas para facilitar isso. 

O Azure fornece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Depois de abrir o hub **Azure Cost Management + Billing,** clique em **Cost Management** e selecione o [Scope](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (o conjunto de recursos para investigar). 

Em seguida, para ver os custos do Monitor Azure nos últimos 30 dias, clique no azulejo **Custos Diários,** escolha "Últimos 30 dias" em datas relativas e adicione um filtro que selecione os nomes do Serviço:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Informações e Análise de Dados

Isto resulta numa visão como:

![Screenshot de Azure Cost Management](./media/usage-estimated-costs/010.png)

A partir daqui, você pode perfurar a partir deste resumo de custos acumulado para obter os detalhes mais finos na vista "Custo por recurso". Nos níveis de preços atuais, os dados do Azure Log são carregados no mesmo conjunto de contadores, quer sejam originários de Log Analytics ou Application Insights. Para separar os custos da utilização do Log Analytics ou da Aplicação Insights, pode adicionar um filtro no **tipo Desuso**. Para ver todos os custos de Insights de Aplicação, filtre o tipo de Recurso para "microsoft.insights/componentes", e para os custos do Log Analytics, filtrar o tipo de Recurso para "microsoft.operationalinsights/workspaces". 

Mais detalhes sobre o seu uso estão disponíveis [através do download da sua utilização a partir do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na folha de cálculo descarregada, pode ver o uso por recurso Azure por dia. Nesta folha de cálculo do Excel, a utilização a partir dos recursos da aplicação Insights pode ser encontrada através da filtragem da coluna "Categoria Meter" para mostrar "Application Insights" e "Log Analytics", e depois adicionar um filtro na coluna "Id de instância" que é "contém microsoft.insights/componentes".  A maioria da utilização de Insights de Aplicação é reportada em metros com a categoria de medidor de Log Analytics, uma vez que existe um único backend de registos para todos os componentes do Monitor Azure.  Apenas os recursos da Application Insights em níveis de preços antigos e testes web em várias etapas são relatados com uma categoria de Medidor de Insights de Aplicação.  A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender a sua conta Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

> [!NOTE]
> A **utilização** da Cost Management no hub **Azure Cost Management + Billing** é a abordagem preferida para compreender amplamente os custos de monitorização.  As experiências **de Utilização e Custos Estimados** para [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) e [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) fornecem insights mais profundos para cada uma dessas partes do Monitor Azure.

Outra opção para visualizar o seu uso do Monitor Azure é a página **de utilização e custos estimados** no centro do Monitor. Isto mostra o uso de funcionalidades de monitorização do núcleo tais como [alerta, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)e Insights de [Aplicação Azure](https://azure.microsoft.com/pricing/details/application-insights/). Para os clientes sobre os planos de preços disponíveis antes de Abril de 2018, isso também inclui a utilização do Log Analytics adquirida por meio das informações e análise da oferta.

Nesta página, os utilizadores podem ver a utilização de recursos nos últimos 31 dias, agregados por cada subscrição. `Drill-ins` mostrar tendências de utilização durante o período de 31 dias. Uma grande quantidade de dados tem de se reúnem para esta estimativa, portanto, seja paciente como a página for carregada.

Este exemplo mostra a utilização de monitorização e uma estimativa dos custos resultantes:

![Utilização e custos estimados portal captura de ecrã](./media/usage-estimated-costs/001.png)

Selecione a ligação na coluna mensal de utilização para abrir um gráfico que mostra as tendências de utilização ao longo do último período de 31 dias: 

![Incluído por nó de barra captura de ecrã do gráfico](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Direitos de subscrição da Suite de Gestão de Operações

Os clientes que compraram o Microsoft Operations Management Suite E1 e E2 são elegíveis para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing). Para receber esta elegibilidade para áreas de trabalho do Log Analytics ou recursos do Application Insights numa determinada subscrição: 

- Áreas de trabalho do log Analytics, devem utilizar o "por nó (OMS)" escalão de preço.
- Os recursos da Application Insights devem utilizar o nível de preços "Enterprise".

Dependendo do número de nós da suite que a sua organização adquiriu, mover algumas subscrições para um nível de preços Pay-As-You-Go (Per GB) pode ser vantajoso, mas isso requer uma cuidadosa consideração.

> [!WARNING]
> Se a sua organização tem atualmente microsoft Operations Management Suite E1 e E2, normalmente é melhor manter os seus espaços de trabalho Log Analytics no nível de preços "Per-nó (OMS)" e os seus recursos de Aplicação Insights no nível de preços "Enterprise". 
>
