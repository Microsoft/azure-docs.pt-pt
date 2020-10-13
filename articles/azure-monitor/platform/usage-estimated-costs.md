---
title: Monitorização da utilização e dos custos estimados no Azure Monitor
description: Visão geral do processo de utilização da utilização do Monitor Azure e página de custos estimados
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: eb96537f67c61fb31759da020068f784d0e89993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323405"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Monitorização da utilização e dos custos estimados no Azure Monitor

> [!NOTE]
> Este artigo descreve como visualizar o uso e os custos estimados em várias funcionalidades de monitorização do Azure. Os artigos relacionados para componentes específicos do Monitor Azure incluem:
> - [Gerir o uso e os custos com os Registos do Monitor Azure](manage-cost-storage.md) descreve como controlar os seus custos alterando o período de retenção de dados e como analisar e alertar sobre a utilização dos seus dados.
> - [Gerir o uso e os custos para a Application Insights](../app/pricing.md) descreve como analisar o uso de dados em Insights de Aplicação.

## <a name="azure-monitor-pricing-model"></a>Modelo de preços do Azure Monitor

O modelo básico de faturação do Azure Monitor é um preço baseado no consumo e amigo da nuvem ("Pay-As-You-Go"). Só paga o que utilizar. Os detalhes dos preços estão disponíveis para [alerta, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) e [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). 

Além do modelo Pay-As-You-Go para dados de registo, o Log Analytics tem Reservas de Capacidade, que lhe permitem economizar até 25% em comparação com o preço Pay-As-You-Go. O preço da reserva de capacidade permite-lhe comprar uma reserva a partir de 100 GB/dia. Qualquer utilização acima do nível de reserva será faturada na tarifa Pay-As-You-Go. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços da Reserva de Capacidade.

Alguns clientes terão acesso aos [níveis de preços do Log Analytics](./manage-cost-storage.md#legacy-pricing-tiers) e ao antigo nível de preços da Enterprise Application [Insights.](../app/pricing.md#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Compreender os custos do Monitor Azure

Há duas fases para compreender os custos. A primeira é considerar o Azure Monitor como a sua solução de monitorização. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerir o seu ambiente

Se ainda não estiver a utilizar os Registos do Monitor Azure, pode utilizar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização do Azure Monitor. Comece por introduzir "Azure Monitor" na caixa de pesquisa e clique no azulejo Azure Monitor resultante. Desloque a página para O Monitor Azure e selecione uma das opções a partir do dropdown do tipo:

- Consultas métricas e alertas  
- Log Analytics
- Application Insights

Em cada uma delas, a calculadora de preços irá ajudá-lo a estimar os seus custos prováveis com base na sua utilização esperada.

Por exemplo, com o Log Analytics pode introduzir o número de VMs e o GB de dados que espera recolher de cada VM. Normalmente, 1 GB a 3 GB de mês de dados é ingerido a partir de um VM típico do Azure. Se já está a avaliar os Registos do Monitor Azure, pode utilizar as suas estatísticas de dados a partir do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorizados](./manage-cost-storage.md#understanding-nodes-sending-data) e o [volume de dados que o seu espaço de trabalho está a ingerir.](./manage-cost-storage.md#understanding-ingested-data-volume)

Da mesma forma para o Application Insights, se ativar o "Volume de dados de estimativa com base na atividade da aplicação", pode fornecer inputs sobre a sua aplicação (pedidos por mês e vistas de página por mês, caso recolha telemetria do lado do cliente), e então a calculadora irá dizer-lhe a quantidade média e 90º por cento de dados recolhidos por aplicações semelhantes. Estas aplicações abrangem a gama de configurações de Application Insights (por exemplo, algumas têm amostragem padrão, algumas não têm amostragem, etc.), pelo que ainda tem o controlo para reduzir o volume de dados que ingeriu muito abaixo do nível mediano utilizando a amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão a ver. [Saiba mais](../app/pricing.md#estimating-the-costs-to-manage-your-application) sobre a estimativa dos custos para a Application Insights.

### <a name="understanding-your-usage-and-estimated-costs"></a>Compreender a sua utilização e os custos estimados

É importante compreender e acompanhar o seu uso uma vez usando o Azure Monitor, e existem um rico conjunto de ferramentas para facilitar isso. 

O Azure fornece uma grande funcionalidade útil no centro [Azure Cost Management + Billing.](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) Depois de abrir o hub **Azure Cost Management + Billing,** clique na **Gestão de Custos** e selecione o [Scope](../../cost-management-billing/costs/understand-work-scopes.md) (o conjunto de recursos para investigar). 

Em seguida, para ver os custos do Monitor Azure nos últimos 30 dias, clique no azulejo **Custos Diários,** escolha "Últimos 30 dias" em datas relativas e adicione um filtro que selecione os nomes de Serviço:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Informações e Análise de Dados

Isto resulta numa visão como:

![Screenshot da Azure Cost Management](./media/usage-estimated-costs/010.png)

A partir daqui, você pode perfurar a partir deste resumo de custo acumulado para obter os detalhes mais finos na vista "Custo por recurso". Nos níveis de preços atuais, os dados do Azure Log são cobrados no mesmo conjunto de contadores, quer sejam originários de Log Analytics ou Application Insights. Para separar os custos da utilização do Log Analytics ou do Application Insights, pode adicionar um filtro no **tipo de recurso.** Para ver todos os custos do Application Insights, filtrar o tipo de Recurso para "microsoft.insights/componentes", e para os custos do Log Analytics, filtrar o tipo de recurso para "microsoft.operationalinsights/workspaces". 

Mais detalhes sobre a sua utilização estão disponíveis [descarregando a sua utilização a partir do portal Azure.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal) Na folha de cálculo descarregada, pode ver a utilização por recurso Azure por dia. Nesta folha de cálculo do Excel, a utilização dos recursos de Insights de Aplicação pode ser encontrada através da primeira filtragem na coluna "Meter Category" para mostrar "Application Insights" e "Log Analytics", e depois adicionar um filtro na coluna "Instance ID" que é "contém microsoft.insights/componentes".  A maioria dos Insights de Aplicação é reportada em metros com a categoria de medidor de Log Analytics, uma vez que existe um único backend de registos para todos os componentes do Monitor Azure.  Apenas os recursos de Insights de Aplicação em níveis de preços antigos e testes web em várias etapas são reportados com uma categoria de medidor de Insights de aplicação.  A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Estão disponíveis mais detalhes para o ajudar a [entender a fatura do Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

> [!NOTE]
> A **utilização da Gestão de Custos** no centro **Azure Cost Management + Billing** é a abordagem preferida para compreender amplamente os custos de monitorização.  As experiências **de utilização e custos estimados** para [insights de registo](./manage-cost-storage.md#understand-your-usage-and-estimate-costs)  e [aplicações](../app/pricing.md#understand-your-usage-and-estimate-costs) fornecem insights mais profundos para cada uma dessas partes do Azure Monitor.

Outra opção para visualizar a utilização do Monitor Azure é a página **de utilização e custos estimados** no hub do Monitor. Isto mostra a utilização de funcionalidades de monitorização principais tais como [alerta, métricas, notificações,](https://azure.microsoft.com/pricing/details/monitor/) [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)e [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/). Para os clientes nos planos de preços disponíveis antes de abril de 2018, isto também inclui o uso do Log Analytics adquirido através da oferta Insights e Analytics.

Nesta página, os utilizadores podem ver o seu uso de recursos nos últimos 31 dias, agregados por subscrição. `Drill-ins` mostrar tendências de utilização ao longo do período de 31 dias. Muitos dados precisam de se juntar para esta estimativa, por isso, por favor, seja paciente à medida que a página se carrega.

Este exemplo mostra a utilização da monitorização e uma estimativa dos custos resultantes:

![Utilização e utilização estimada do portal de custos](./media/usage-estimated-costs/001.png)

Selecione o link na coluna de utilização mensal para abrir um gráfico que mostre as tendências de utilização ao longo do último período de 31 dias: 

![Incluído por imagem de gráfico de barra de nó](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Direitos de subscrição do Suite de Gestão de Operações

Os clientes que adquiriram o Microsoft Operations Management Suite E1 e E2 são elegíveis para direitos de ingestão de dados por nó para [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) e [Application Insights](../app/pricing.md). Para receber estes direitos de espaços de trabalho log analytics ou recursos de Insights de Aplicação numa determinada subscrição: 

- Os espaços de trabalho do Log Analytics devem utilizar o nível de preços "Per-nó (OMS)".
- Os recursos de Insights de Aplicação devem utilizar o nível de preços "Enterprise".

Dependendo do número de nós da suite que a sua organização adquiriu, mover algumas subscrições para um nível de preços Pay-As-You-Go (Per GB) pode ser vantajoso, mas isso requer uma consideração cuidadosa.

> [!WARNING]
> Se a sua organização tem os atuais Microsoft Operations Management Suite E1 e E2, normalmente é melhor manter os seus espaços de trabalho Log Analytics no nível de preços "Per-node (OMS)" e os seus recursos de Insights de Aplicação no nível de preços "Enterprise". 
>

