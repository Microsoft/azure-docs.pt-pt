---
title: Gerir a utilização e os custos da Azure Application Insights | Microsoft Docs
description: Gerir volumes de telemetria e monitorizar os custos em Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 3/30/2021
ms.reviewer: lagayhar
ms.openlocfilehash: e048e788e674e90a62b15784c590c07e5d36b816
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078405"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerir a utilização e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como compreender e controlar os seus custos para a Application Insights.  Um artigo relacionado, [monitorização da utilização e custos estimados](..//usage-estimated-costs.md) descreve como visualizar a utilização e os custos estimados em várias funcionalidades de monitorização do Azure para diferentes modelos de preços.

O Application Insights foi concebido para obter tudo o que precisa para monitorizar a disponibilidade, desempenho e utilização das suas aplicações web, quer estejam hospedados no Azure ou no local. A Application Insights suporta linguagens e quadros populares, tais como .NET, Java e Node.js, e integra-se com processos e ferramentas de DevOps como Azure DevOps, Jira e PagerDuty. É importante entender o que determina os custos de monitorização das suas aplicações. Neste artigo, revemos o que impulsiona os custos de monitorização da sua aplicação e como pode monitorizá-los e controlá-los de forma proativa.

Se tiver dúvidas sobre como funciona o preço para a Application Insights, pode publicar uma pergunta na nossa [página de perguntas microsoft Q&A](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Modelo preços

O preço da [Azure Application Insights][start] é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para uma maior retenção de dados. Cada recurso Application Insights é cobrado como um serviço separado e contribui para a fatura da sua subscrição Azure. O volume de dados é medido como o tamanho do pacote de dados JSON não comprimido que é recebido pela Application Insights da sua aplicação. O volume de dados é medido em GB (10^9 bytes). Não existe uma carga de volume de dados para a utilização do [Live Metrics Stream](./live-stream.md).

[Os testes web em várias etapas](./availability-multistep.md) incorrem numa taxa adicional. Testes web em várias etapas são testes web que realizam uma sequência de ações. Não há carga separada para *testes* de ping de uma única página. A telemetria dos testes de ping e os testes em várias etapas são cobrados da mesma forma que outras telemetrias da sua aplicação.

A opção Application Insights para [Permitir alertar sobre as dimensões métricas personalizadas](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) também pode gerar em custos adicionais, uma vez que isso pode resultar na criação de métricas adicionais de pré-agregação. [Saiba mais](./pre-aggregated-metrics-log-metrics.md) sobre métricas baseadas em log e pré-agregadas em Application Insights e sobre [preços](https://azure.microsoft.com/pricing/details/monitor/) para métricas personalizadas do Azure Monitor.

### <a name="workspace-based-application-insights"></a>Insights de aplicação baseados no espaço de trabalho

Para os recursos de Insights de Aplicação que enviam os seus dados para um espaço de trabalho Log Analytics, chamado [recursos de Insights de Aplicação baseados no espaço de trabalho,](create-workspace-resource.md)a faturação para ingestão e retenção de dados é feita pelo espaço de trabalho onde os dados do Application Insights estão localizados. Isto permite que os clientes aproveitem todas as opções do modelo de [preços](../logs/manage-cost-storage.md#pricing-model) do Log Analytics que inclui Reservas de Capacidade, além de Pay-As-You-Go. O Log Analytics também tem mais opções para a retenção de dados, incluindo [a retenção por tipo de dados.](../logs/manage-cost-storage.md#retention-by-data-type) Os tipos de dados do Application Insights no espaço de trabalho recebem 90 dias de retenção sem encargos. O uso de testes web e o alerta de alerta sobre as dimensões métricas personalizadas ainda é relatado através de Application Insights. Saiba como rastrear os custos de ingestão e retenção de dados no Log Analytics utilizando as consultas [de Utilização e Custos Estimados](../logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + Billing](../logs/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) e [Log Analytics](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerir a sua aplicação

Se ainda não estiver a utilizar o Application Insights, pode utilizar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização de Insights de Aplicação. Comece por introduzir "Azure Monitor" na caixa de pesquisa e clique no azulejo Azure Monitor resultante. Percorra a página para O Monitor Azure e selecione Insights de Aplicação a partir do dropdown do tipo.  Aqui pode introduzir o número de GB de dados que espera recolher por mês, pelo que a questão é saber quantos dados os Application Insights irão recolher para monitorizar a sua aplicação.

Existem duas abordagens para resolver esta questão: utilização de monitorização padrão e amostragem adaptativa, que está disponível no ASP.NET SDK, ou estimar a sua provável ingestão de dados com base no que outros clientes similares viram.

### <a name="data-collection-when-using-sampling"></a>Recolha de dados ao utilizar amostragem

Com a [amostragem adaptativa](sampling.md#adaptive-sampling)da ASP.NET SDK, o volume de dados é ajustado automaticamente para manter dentro de uma taxa máxima de tráfego especificada para monitorização padrão de Insights de Aplicação. Se a aplicação produzir uma baixa quantidade de telemetria, como quando depurado ou devido a uma utilização baixa, os itens não serão deixados pelo processador de amostragem enquanto o volume estiver abaixo dos eventos configurados por segundo nível. Para uma aplicação de alto volume, com o limiar de padrão de cinco eventos por segundo, a amostragem adaptativa limitará o número de eventos diários a 432.000. Utilizando um tamanho médio típico de evento de 1 KB, isto corresponde a 13,4 GB de telemetria por 31 dias por mês por nó, uma vez que a amostragem é feita localmente a cada nó.

> [!NOTE]
> O tamanho dos dados de registo do Azure Monitor é calculado em GB (1 GB = 10^9 bytes).

Para os SDKs que não suportam amostras adaptativas, pode utilizar amostras de [ingestão,](./sampling.md#ingestion-sampling)que amostras quando os dados são recebidos pela Application Insights com base numa percentagem de dados para reter, ou [amostragem de taxa fixa para ASP.NET, ASP.NET Web e Java websites](sampling.md#fixed-rate-sampling) para reduzir o tráfego enviado do seu servidor web e navegadores web

### <a name="learn-from-what-similar-customers-collect"></a>Saiba com o que os clientes semelhantes recolhem

Na calculadora de preços de monitorização Azure para Insights de Aplicação, se ativar a funcionalidade "Estimar o volume de dados com base na atividade da aplicação", pode fornecer entradas sobre a sua aplicação (pedidos por mês e vistas de página por mês, caso recolha telemetria do lado do cliente), e então a calculadora lhe dirá a quantidade média e 90 de percentil de dados recolhidos por aplicações semelhantes. Estas aplicações abrangem a gama de configurações de Application Insights (por exemplo, algumas têm [amostragem](./sampling.md)padrão, algumas não têm amostragem, etc.), pelo que ainda tem o controlo para reduzir o volume de dados que ingeriu muito abaixo do nível mediano utilizando a amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão a ver.

## <a name="understand-your-usage-and-estimate-costs"></a>Compreenda o seu uso e calcule os custos

O Application Insights facilita a compreensão dos seus custos, provavelmente, baseados em padrões de utilização recentes. Para começar, no portal Azure, para o recurso Application Insights, vá à página **de Utilização e custos estimados:**

![Escolha preços](./media/pricing/pricing-001.png)

A. Reveja o seu volume de dados para o mês. Isto inclui todos os dados que são recebidos e retidos (após qualquer [amostra)](./sampling.md)das aplicações do seu servidor e cliente, e de testes de disponibilidade.  
B. É feita uma carga separada para [testes web em várias etapas.](./availability-multistep.md) (Isto não inclui testes simples de disponibilidade, que estão incluídos na carga de volume de dados.)  
C. Ver as tendências do volume de dados do mês passado.  
D. Permitir a recolha de [amostras](./sampling.md)de ingestão de dados.
E. Desa estada a tampa diária do volume de dados.  

(Note que todos os preços apresentados em imagens deste artigo são apenas para fins. Para os preços correntes na sua moeda e região, consulte [os preços do Application Insights][pricing].)

Para investigar mais profundamente o uso do seu Application Insights, abra a página **Métricas,** adicione a métrica denominada "Volume de ponto de dados", e, em seguida, selecione a opção *de divisão de Aplicação* para dividir os dados por "Tipo de artigo de telemetria".

Os custos de Insights de Aplicação são adicionados à sua conta Azure. Pode ver detalhes da sua conta Azure na secção De Gestão de **Custos + Faturação** do portal Azure, ou no [portal de faturação Azure](https://account.windowsazure.com/Subscriptions).  [Consulte abaixo](#viewing-application-insights-usage-on-your-azure-bill) os detalhes sobre a utilização deste para Insights de Aplicações. 

![No menu esquerdo, selecione Billing](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Usando métricas de volume de dados
<a id="understanding-ingested-data-volume"></a>

Para saber mais sobre os seus volumes de dados, selecionando **Métricas** para o seu recurso Application Insights, adicione um novo gráfico. Para a métrica do gráfico, em **métricas baseadas em Log,** selecione **volume de ponto de dados**. Clique **em Aplicar a divisão** e selecionar grupo por **`Telemetryitem` tipo**.

![Use métricas para olhar para o volume de dados](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Consultas para entender detalhes do volume de dados

Existem duas abordagens para investigar volumes de dados para insights de aplicação. O primeiro usa informação agregada na `systemEvents` tabela, e o segundo usa a `_BilledSize` propriedade, que está disponível em cada evento ingerido. `systemEvents`não terá informações sobre o tamanho dos dados para [insights de aplicação baseados no espaço de trabalho.](#data-volume-for-workspace-based-application-insights-resources)

#### <a name="using-aggregated-data-volume-information"></a>Utilização de informações agregadas de volume de dados

Por exemplo, pode utilizar a `systemEvents` tabela para ver o volume de dados ingerido nas últimas 24 horas com a consulta:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ou para ver um gráfico de volume de dados (in bytes) por tipo de dados durante os últimos 30 dias, pode utilizar:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Note que esta consulta pode ser usada num [Alerta de Registo Azure](../alerts/alerts-unified-log.md) para configurar alertas sobre volumes de dados.  

Para saber mais sobre as alterações de dados de telemetria, podemos obter a contagem de eventos por tipo usando a consulta:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Usando o tamanho dos dados por informação do evento

Para saber mais detalhes sobre a origem dos seus volumes de dados, pode utilizar a `_BilledSize` propriedade que está presente em cada evento ingerido.

Por exemplo, para ver quais as operações que geram mais volume de dados nos últimos 30 dias, podemos resumir `_BilledSize` todos os eventos de dependência:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Volume de dados para recursos de Insights de Aplicação baseados em espaço de trabalho

Para ver as tendências do volume de dados para todos os recursos de [Insights de Aplicação baseados](create-workspace-resource.md) no espaço de trabalho num espaço de trabalho durante a última semana, vá ao espaço de trabalho do Log Analytics e faça a consulta:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Para consultar as tendências do volume de dados por tipo para um recurso específico de Insights de Aplicação baseado em espaço de trabalho, na utilização do espaço de trabalho Log Analytics:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visualização do uso de Insights de Aplicação na sua conta Azure

O Azure fornece uma grande funcionalidade útil no centro [Azure Cost Management + Billing.](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade "Análise de Custos" permite-lhe visualizar os seus gastos para os recursos Azure. A adição de um filtro por tipo de recurso (a microsoft.insights/componentes para Insights de Aplicações) permitir-lhe-á acompanhar os seus gastos. Em seguida, para "Grupo por" selecione "Categoria de Contador" ou "Medidor".  Para os recursos de Insights de Aplicação nos planos de preços atuais, a maioria da utilização aparecerá como Log Analytics para a categoria Medidor, uma vez que existe um único backend de registos para todos os componentes do Azure Monitor. 

Pode compreender melhor a utilização ao [transferir a utilização a partir do portal do Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
Na folha de cálculo descarregada, pode ver a utilização por recurso Azure por dia. Nesta folha de cálculo do Excel, a utilização dos recursos de Insights de Aplicação pode ser encontrada através da primeira filtragem na coluna "Meter Category" para mostrar "Application Insights" e "Log Analytics", e depois adicionar um filtro na coluna "Instance ID" que é "contém microsoft.insights/componentes".  A maioria dos Insights de Aplicação é reportada em metros com a categoria de medidor de Log Analytics, uma vez que existe um único backend de registos para todos os componentes do Monitor Azure.  Apenas os recursos de Insights de Aplicação em níveis de preços antigos e testes web em várias etapas são reportados com uma categoria de medidor de Insights de aplicação.  A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Estão disponíveis mais detalhes para o ajudar a [entender a fatura do Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Gerir o seu volume de dados

O volume de dados enviados pode ser gerido utilizando as seguintes técnicas:

* **Amostragem**: Pode utilizar a amostragem para reduzir a quantidade de telemetria enviada a partir do seu servidor e aplicações de clientes, com a mínima distorção de métricas. A amostragem é a ferramenta primária que pode utilizar para sintonizar a quantidade de dados que envia. Saiba mais sobre [as características de amostragem.](./sampling.md)

* **Limite as chamadas do Ajax**: Pode limitar o número de chamadas do Ajax que podem ser [reportadas](./javascript.md#configuration) em todas as visualizações de página ou desligar o relatório do Ajax. Note que desativar chamadas do Ajax irá desativar a [correlação JavaScript](./javascript.md#enable-correlation).

* **Desativar módulos não necessários**: [Editar ApplicationInsights.config](./configuration-with-applicationinsights-config.md) para desligar os módulos de recolha de que não necessita. Por exemplo, pode decidir que os contadores de desempenho ou dados de dependência são inessenciais.

* **Métricas pré-agregadas**: Se colocar chamadas para TrackMetric na sua aplicação, pode reduzir o tráfego utilizando a sobrecarga que aceita o seu cálculo do desvio médio e padrão de um lote de medições. Ou pode utilizar um [pacote pré-agregado.](https://www.myget.org/gallery/applicationinsights-sdk-labs)
 
* **Tampa diária**: Quando cria um recurso Application Insights no portal Azure, a tampa diária está definida para 100 GB/dia. Quando cria um recurso Application Insights no Visual Studio, o padrão é pequeno (apenas 32,3 MB/dia). O padrão da tampa diária é definido para facilitar o teste. Pretende-se que o utilizador aumente a tampa diária antes de implementar a app em produção. 

    A tampa máxima é de 1.000 GB/dia, a menos que solicite um máximo superior para uma aplicação de tráfego elevado.
    
    Os e-mails de aviso sobre a tampa diária são enviados para conta que são membros destas funções para o seu recurso Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Tenha cuidado quando definir o limite diário. A sua intenção deve ser *nunca atingir o limite diário.* Se atingir o limite diário, perderá os dados do resto do dia e não poderá monitorizar a aplicação. Para alterar a tampa diária, utilize a opção **tampa de volume Diário.** Pode aceder a esta opção no painel **de utilização e custos estimados** (isto é descrito mais detalhadamente mais tarde no artigo).
    
    Removemos a restrição de alguns tipos de subscrição que têm crédito que não poderia ser usado para Insights de Aplicação. Anteriormente, se a subscrição tiver um limite de gastos, o diálogo diário da tampa tem instruções para remover o limite de gastos e permitir que o limite diário seja aumentado para além de 32,3 MB/dia.
    
* **Estrangulamento**: A aceleração limita a taxa de dados a 32.000 eventos por segundo, com uma média superior a 1 minuto por tecla de instrumentação. O volume de dados que a sua aplicação envia é avaliado a cada minuto. Se exceder a taxa por segundo média ao longo do minuto, o servidor recusa alguns pedidos. O SDK tampona os dados e tenta reencamê-lo. Espalha uma onda ao longo de vários minutos. Se a sua aplicação enviar consistentemente dados a mais do que a taxa de estrangulamento, alguns dados serão eliminados. (Os ASP.NET, Java e JavaScript SDKs tentam reencombá-lo desta forma; outros SDKs podem simplesmente deixar cair dados aceleradores.) Se ocorrer estrangulamento, um aviso de notificação avisa-o de que tal ocorreu.

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie o seu volume máximo de dados diários

Pode utilizar a tampa de volume diária para limitar os dados recolhidos. No entanto, se a tampa for cumprida, ocorre uma perda de toda a telemetria enviada do seu pedido para o resto do dia. Não é *aconselhável* que a sua aplicação atinja a tampa diária. Não é possível acompanhar a saúde e o desempenho da sua aplicação depois de atingir o limite diário.

Em vez de utilizar a tampa de volume diária, utilize [a amostragem](./sampling.md) para sintonizar o volume de dados ao nível que pretende. Em seguida, utilize a tampa diária apenas como um "último recurso" caso a sua aplicação comece inesperadamente a enviar volumes muito mais elevados de telemetria.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar que limite de dados diários para definir

Rever aplicações Insights Utilização e custos estimados para compreender a tendência de ingestão de dados e qual é a tampa diária de volume para definir. Deve ser considerado com cuidado, uma vez que não poderá monitorizar os seus recursos após o limite ser atingido.

### <a name="set-the-daily-cap"></a>Definir o Daily Cap

Para alterar a tampa diária, na secção **Configure** do seu recurso Application Insights, na página **De Utilização e custos estimados,** selecione  **Daily Cap**.

![Ajuste a tampa diária de volume de telemetria](./media/pricing/pricing-003.png)

Para [alterar a tampa diária através do Azure Resource Manager,](./powershell.md)a propriedade a alterar é a `dailyQuota` .  Através do Azure Resource Manager também pode definir o `dailyQuotaResetTime` e o da tampa diária `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Criar alertas para o Daily Cap

O Application Insights Daily Cap cria um evento no registo de atividade do Azure quando os volumes de dados ingeridos atingem o nível de aviso ou o nível de tampa diária.  Pode [criar um alerta com base nestes eventos de registo de atividade.](../alerts/alerts-activity-log.md#create-with-the-azure-portal) Os nomes dos sinais para estes eventos são:

* Limite de aviso diário de limite de limite de aplicação Insights atingido

* Limite diário de componente de Aplicações Insights alcançado

## <a name="sampling"></a>Amostragem
[A amostragem](./sampling.md) é um método de redução da taxa a que a telemetria é enviada para a sua aplicação, mantendo ao mesmo tempo a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Também mantém a contagem correta do evento.

A amostragem é uma forma eficaz de reduzir os encargos e ficar dentro da sua quota mensal. O algoritmo de amostragem retém itens relacionados de telemetria para que, por exemplo, quando utilizar o Search, possa encontrar o pedido relacionado com uma exceção particular. O algoritmo também mantém as contagens corretas para que você veja os valores corretos no Metric Explorer para taxas de pedido, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [A amostragem adaptativa](./sampling.md) é o padrão para o ASP.NET SDK. A amostragem adaptativa ajusta-se automaticamente ao volume de telemetria que a sua aplicação envia. Opera automaticamente no SDK na sua aplicação web para que o tráfego de telemetria na rede seja reduzido. 
* *A amostragem de ingestão* é uma alternativa que funciona no ponto em que a telemetria da sua aplicação entra no serviço Application Insights. A amostragem de ingestão não afeta o volume de telemetria enviada da sua app, mas reduz o volume que é retido pelo serviço. Você pode usar a amostragem de ingestão para reduzir a quota que é usada pela telemetria de navegadores e outros SDKs.

Para definir a amostragem de ingestão, vá ao **painel de preços:**

![No painel de quotas e preços, selecione o azulejo samples e, em seguida, selecione uma fração de amostragem](./media/pricing/pricing-004.png)

> [!WARNING]
> O **painel de amostragem de dados** controla apenas o valor da amostragem de ingestão. Não reflete a taxa de amostragem aplicada pela App Insights SDK na sua aplicação. Se a telemetria de entrada já tiver sido amostrada no SDK, a amostragem de ingestão não é aplicada.
>

Para descobrir a taxa de amostragem real, não importa onde tenha sido aplicada, use uma [consulta de Analytics.](../logs/log-query-overview.md) A consulta é assim:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

Em cada registo retido, `itemCount` indica o número de registos originais que representa. É igual a 1 + o número de registos anteriores descartados.

## <a name="change-the-data-retention-period"></a>Change the data retention period (Alterar o período de retenção de dados)

A retenção por defeito para os recursos de Insights de Aplicação é de 90 dias. Podem ser selecionados diferentes períodos de retenção para cada recurso De Insights de Aplicação. O conjunto completo de períodos de retenção disponíveis é de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre preços para uma retenção mais longa de dados. 

Para alterar a retenção, a partir do seu recurso Application Insights, aceda à página **Utilização e Custos Estimados** e selecione a opção **de Retenção de Dados:**

![Screenshot que mostra onde alterar o período de retenção de dados.](./media/pricing/pricing-005.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes de os dados mais antigos serem removidos.

A retenção também pode ser [definida programaticamente usando o PowerShell](powershell.md#set-the-data-retention) usando o `retentionInDays` parâmetro. Se definir a retenção de dados para 30 dias, pode desencadear uma purga imediata de dados mais antigos usando o `immediatePurgeDataOn30Days` parâmetro, o que pode ser útil para cenários relacionados com a conformidade. Esta funcionalidade de purga só é exposta através do Azure Resource Manager e deve ser usada com extremo cuidado. O tempo de reset diário para a tampa do volume de dados pode ser configurado usando o Gestor de Recursos Azure para definir o `dailyQuotaResetTime` parâmetro.

## <a name="data-transfer-charges-using-application-insights"></a>Taxas de transferência de dados usando Insights de Aplicação

O envio de dados para o Application Insights pode incorrer em taxas de largura de banda de dados. Conforme descrito na página de preços da [largura de banda Azure, transferência de](https://azure.microsoft.com/pricing/details/bandwidth/)dados entre os serviços Azure localizados em duas regiões cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, esta taxa é muito pequena (poucas %) em comparação com os custos para a ingestão de dados de registo de informações de informação de aplicação. Consequentemente, o controlo dos custos para o Log Analytics tem de se concentrar no seu volume de dados ingerido, e temos orientações para ajudar a compreender isso [aqui.](#managing-your-data-volume)

## <a name="limits-summary"></a>Resumo dos limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desativar os e-mails diários da tampa

Para desativar os e-mails diários da tampa do volume, na secção **Configure** do seu recurso Application Insights, no painel **de utilização e custos estimados,** selecione  **Daily Cap**. Existem configurações para enviar e-mails quando a tampa é alcançada, bem como quando um nível de aviso ajustável foi atingido. Se desejar desativar todos os e-mails diários relacionados com o volume, desmarque ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Nível de preços da Legacy Enterprise (Por Nó)

Para os primeiros adotantes da Azure Application Insights, existem ainda dois níveis possíveis de preços: Básico e Empresa. O nível de preços básicos é o mesmo que descrito acima e é o nível padrão. Inclui todas as funcionalidades de nível da Enterprise, sem custos adicionais. As notas básicas de nível principalmente sobre o volume de dados que são ingeridos.

Estes níveis de preços antigos foram renomeados. O nível de preços da Enterprise chama-se **agora Per Node** e o nível básico de preços é agora chamado **Per GB**. Estes novos nomes são usados abaixo e no portal Azure.  

O nível Per Node (ex-Enterprise) tem uma taxa por nó, e cada nó recebe um subsídio de dados diário. No nível de preços per nó, é cobrado por dados ingeridos acima do subsídio incluído. Se estiver a utilizar o Suite de Gestão de Operações, deve escolher o nível Per Node. Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para a monitorização do Azure. Este modelo adota um modelo simples "pay-as-you-go" em todo o portfólio de serviços de monitorização. Saiba mais sobre o [novo modelo de preços.](..//usage-estimated-costs.md)

Para os preços correntes na sua moeda e região, consulte [os preços de Insights de Aplicação.](https://azure.microsoft.com/pricing/details/application-insights/)

### <a name="understanding-billed-usage-on-the-legacy-enterprise-per-node-tier"></a>Compreensão do uso faturado no nível da Enterprise (Per Node) 

Como descrito abaixo em mais detalhes, o nível legacy Enterprise (Per Node) combina o uso de todos os recursos de Insights de Aplicação numa subscrição para calcular o número de nós e a sobrecarave de dados. Devido a este processo de combinação, **a utilização de todos os recursos de Insights de Aplicação numa subscrição é reportada contra apenas um dos recursos**.  Isto torna muito complicado conciliar o seu [uso faturado](#viewing-application-insights-usage-on-your-azure-bill) com o uso observado para cada recursos de Insights de Aplicação. 

> [!WARNING]
> Devido à complexidade do rastreio e compreensão do uso dos recursos de Application Insights no nível legado Enterprise (Per Node), recomendamos vivamente a utilização do atual nível de preços Pay-As-You-Go. 

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Por nível de nó e direitos de subscrição do Suite de Gestão de Operações

Os clientes que adquirem o Suite E1 e o E2 de Gestão de Operações podem obter insights de aplicação por nó como componente adicional sem custos adicionais, como [anunciado anteriormente.](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription) Especificamente, cada unidade da Suite E1 e E2 de Gestão de Operações inclui o direito a um nó do nível de Insights de Aplicação por Nó. Cada nó de Insights de Aplicação inclui até 200 MB de dados ingeridos por dia (separados da ingestão de dados do Log Analytics), com retenção de dados de 90 dias sem custos adicionais. O nível é descrito mais detalhadamente mais tarde no artigo.

Como este nível é aplicável apenas aos clientes com uma assinatura Do Suite de Gestão de Operações, os clientes que não possuem uma subscrição do Suite de Gestão de Operações não vêem uma opção para selecionar este nível.

> [!NOTE]
> Para garantir que obtém este direito, os seus recursos application insights devem estar no nível de preços per nó. Este direito aplica-se apenas como nós. Os recursos de Insights de Aplicação no nível Per GB não percebem qualquer benefício. Este direito não é visível nos custos estimados indicados no Painel de Utilização e no painel de **custos estimado.** Além disso, se mudar uma subscrição do novo modelo de preços de monitorização Azure em abril de 2018, o nível Per GB é o único nível disponível. Mover uma subscrição do novo modelo de preços de monitorização Azure não é aconselhável se tiver uma subscrição da Suite de Gestão de Operações.

### <a name="how-the-per-node-tier-works"></a>Como funciona o nível per nó

* Você paga por cada nó que envia telemetria para quaisquer aplicações no nível Per Node.
  * Um *nó* é uma máquina de servidor físico ou virtual ou uma instância de função de plataforma como serviço que acolhe a sua aplicação.
  * Máquinas de desenvolvimento, navegadores de clientes e dispositivos móveis não contam como nós.
  * Se a sua aplicação tiver vários componentes que enviam telemetria, como um serviço web e um trabalhador de back-end, os componentes são contados separadamente.
  * [Os dados do Live Metrics Stream](./live-stream.md) não são contados para efeitos de preços. Numa subscrição, os seus custos são por nó, não por app. Se tiver cinco nós que enviam telemetria para 12 aplicações, a taxa é de cinco nós.
* Embora as taxas sejam citadas por mês, é cobrado apenas por qualquer hora em que um nó envie telemetria a partir de uma aplicação. A taxa horária é a taxa mensal citada dividida por 744 (número de horas num mês de 31 dias).
* É dada uma atribuição de volume de dados de 200 MB por dia para cada nó detetado (com granularidade horária). A atribuição de dados não reutilizados não é transitada de um dia para o outro.
  * Se escolher o nível de preços per nó, cada subscrição recebe uma licença diária de dados com base no número de nós que enviam telemetria para os recursos de Insights de Aplicação nessa subscrição. Assim, se tiver cinco nós que enviam dados durante todo o dia, terá uma licença de 1 GB aplicada a todos os recursos de Insights de Aplicação nessa subscrição. Não importa se certos nós enviam mais dados do que outros nós porque os dados incluídos são partilhados em todos os nós. Se num determinado dia, os recursos do Application Insights receberem mais dados do que os incluídos na atribuição diária de dados para esta subscrição, aplicam-se os encargos por excesso de dados por GB. 
  * O subsídio diário de dados é calculado como o número de horas do dia (usando UTC) que cada nó envia telemetria dividida por 24 multiplicadas por 200 MB. Assim, se tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos para esse dia seriam ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Ao preço de 2,30 USD por GB para excesso de dados, a taxa seria de 1,15 USD se os nós enviassem 1 GB de dados nesse dia.
  * O subsídio diário de nível Per Nó não é partilhado com aplicações para as quais escolheu o nível Per GB. A mesada não é feita do dia-a-dia.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem de nóis distintos

| Scenario                               | Contagem total de nó diário |
|:---------------------------------------|:----------------:|
| 1 aplicação utilizando 3 instâncias do Serviço de Aplicações Azure e 1 servidor virtual | 4 |
| 3 aplicações em execução em 2 VMs; os recursos de Insights de Aplicação para estas aplicações estão na mesma subscrição e no nível Per Node | 2 | 
| 4 aplicações cujos recursos de Insights de Aplicações estão na mesma subscrição; cada aplicação executando 2 instâncias durante 16 horas fora do pico, e 4 instâncias durante 8 horas de pico | 13.33 |
| Serviços em nuvem com 1 Papel de Trabalhador e 1 Função Web, cada um executando 2 instâncias | 4 | 
| Um cluster de tecido de serviço Azure de 5 nós com 50 microserviços; cada microserviço executando 3 instâncias | 5|

* A contagem exata do nó depende da aplicação Insights SDK que a sua aplicação está a utilizar. 
  * Nas versões SDK 2.2 e posteriormente, tanto o Núcleo de Insights de Aplicação [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) como o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) reportam cada anfitrião de aplicações como um nó. Exemplos são o nome do computador para servidores físicos e anfitriões VM ou o nome de exemplo para serviços na nuvem.  A única exceção é uma aplicação que utiliza apenas o [Núcleo .NET](https://dotnet.github.io/) e o Núcleo de Insights de Aplicação SDK. Nesse caso, apenas um nó é reportado para todos os anfitriões porque o nome do anfitrião não está disponível.
  * Para versões anteriores do SDK, o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) comporta-se como as versões SDK mais recentes, mas o [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) reporta apenas um nó, independentemente do número de anfitriões de aplicações.
  * Se a sua aplicação utilizar o SDK para definir **funçõesInstância** a um valor personalizado, por padrão, esse mesmo valor é usado para determinar a contagem de nós.
  * Se estiver a utilizar uma nova versão SDK com uma aplicação que vai a partir de máquinas clientes ou dispositivos móveis, a contagem de nós pode devolver um número que é grande (devido ao grande número de máquinas clientes ou dispositivos móveis).

## <a name="automation"></a>Automatização

Pode escrever um script para definir o nível de preços utilizando a Azure Resource Management. [Saiba como.](powershell.md#price)

## <a name="next-steps"></a>Passos seguintes

* [amostragem](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/