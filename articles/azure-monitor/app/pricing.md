---
title: Gerir o uso e os custos para insights de aplicação azure [ Microsoft Docs
description: Gerir volumes de telemetria e monitorizar os custos em Insights de Aplicação.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0225484de06ae4e595f1dcbcdd520f4e0e4d53f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405378"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gerir a utilização e os custos do Application Insights

> [!NOTE]
> Este artigo descreve como compreender e controlar os seus custos para Insights de Aplicação.  Um artigo relacionado, [monitorização da utilização e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como visualizar o uso e os custos estimados em várias funcionalidades de monitorização do Azure para diferentes modelos de preços.

Application Insights é projetado para obter tudo o que você precisa para monitorizar a disponibilidade, desempenho e uso das suas aplicações web, quer estejam hospedados no Azure ou no local. Application Insights suporta línguas e quadros populares, tais como .NET, Java e Node.js, e integra-se com processos e ferramentas de DevOps como Azure DevOps, Jira e PagerDuty. É importante entender o que determina os custos de monitorização das suas aplicações. Neste artigo, revemos o que impulsiona os custos de monitorização da sua aplicação e como pode monitorizá-los e controlá-los proativamente.

Se tiver dúvidas sobre como funciona o preço para A Aplicação Insights, pode colocar uma pergunta no nosso [fórum.](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)

## <a name="pricing-model"></a>Modelo preços

O preço para [Azure Application Insights][start] é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para uma maior retenção de dados. Cada recurso Application Insights é cobrado como um serviço separado e contribui para a fatura para a sua subscrição Azure. O volume de dados é medido como o tamanho do pacote de dados JSON não comprimido que é recebido pela Application Insights da sua aplicação. Não existe nenhuma carga de volume de dados para a utilização do [Live Metrics Stream](../../azure-monitor/app/live-stream.md).

[Testes web em várias etapas](../../azure-monitor/app/availability-multistep.md) incorrem numa carga adicional. Os testes web em várias etapas são testes web que realizam uma sequência de ações. Não há nenhuma acusação separada para *testes de ping* de uma única página. A telemetria dos testes de ping e dos testes em várias etapas é carregada da mesma forma que outras telemetrias da sua aplicação.

A opção Insights de Aplicação para [permitir alertar sobre dimensões métricas personalizadas](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) também pode gerar em custos adicionais, pois isso pode resultar na criação de métricas adicionais de pré-agregação. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics) sobre métricas baseadas em log e pré-agregados em Insights de Aplicação e sobre [preços](https://azure.microsoft.com/pricing/details/monitor/) para métricas personalizadas do Azure Monitor.

## <a name="estimating-the-costs-to-manage-your-application"></a>Estimando os custos para gerir a sua aplicação

Se ainda não estiver a utilizar o Application Insights, pode utilizar a calculadora de [preços do Monitor Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização de Insights de Aplicação. Comece por introduzir o "Monitor Azure" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Percorra a página para o Monitor Azure e selecione 'Insights de aplicação' a partir do dropdown do Tipo.  Aqui pode introduzir o número de GB de dados que espera recolher por mês, por isso a questão é quanto dados irão recolher os Insights de Aplicação que monitorizam a sua aplicação.

Existem duas abordagens para resolver esta questão: a utilização de monitorização padrão e amostragem adaptável, que está disponível no ASP.NET SDK, ou estimar a sua provável ingestão de dados com base no que outros clientes similares viram.

### <a name="data-collection-when-using-sampling"></a>Recolha de dados ao utilizar amostragem

Com a amostragem [adaptativa](sampling.md#adaptive-sampling)do ASP.NET SDK, o volume de dados é ajustado automaticamente para manter dentro de uma taxa máxima especificada de tráfego para monitorização de Insights de aplicação por padrão. Se a aplicação produzir uma baixa quantidade de telemetria, como quando depuraou ou devido a uma baixa utilização, os itens não serão deixados cair pelo processador de amostragem enquanto o volume estiver abaixo dos eventos configurados por segundo nível. Para uma aplicação de grande volume, com o limiar padrão de cinco eventos por segundo, a amostragem adaptativa limitará o número de eventos diários a 432.000. Utilizando um tamanho médio típico de 1 KB, isto corresponde a 13,4 GB de telemetria por 31 dias por mês de 31 dias hospedando a sua aplicação (uma vez que a amostragem é feita localmente a cada nó.) 

Para Os SDKs que não suportam amostragem adaptativa, pode utilizar amostras de [ingestão](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), que amostras quando os dados são recebidos por Application Insights com base numa percentagem de dados para reter, ou [amostragem de taxa fixa para ASP.NET, ASP.NET sites Core e Java](sampling.md#fixed-rate-sampling) para reduzir o tráfego enviado do seu servidor web e navegadores web

### <a name="learn-from-what-similar-customers-collect"></a>Saiba o que os clientes similares recolhem

Na calculadora de preços de monitorização do Azure para Insights de Aplicação, se ativar a funcionalidade "Estimar o volume de dados com base na atividade da aplicação", pode fornecer informações sobre a sua aplicação (pedidos por mês e visualizações de páginas por mês, caso recolha telemetria do lado do cliente), e então a calculadora irá dizer-lhe a quantidade mediana e 90ª percentil de dados recolhidos por aplicações semelhantes. Estas aplicações abrangem a gama de configurações de Insights de Aplicação (por exemplo, algumas têm [amostragem](../../azure-monitor/app/sampling.md)predefinida , algumas não têm amostragem, etc.), pelo que ainda tem o controlo para reduzir o volume de dados que ingere muito abaixo do nível mediano utilizando a amostragem. Mas este é um ponto de partida para entender o que outros clientes semelhantes estão a ver.

## <a name="understand-your-usage-and-estimate-costs"></a>Compreenda os seus custos de utilização e estimativa

A Aplicação Insights torna fácil entender quais os seus custos provavelmente baseados em padrões de utilização recentes. Para começar, no portal Azure, para o recurso Application Insights, vá à página **de Utilização e Custos Estimados:**

![Escolha preços](./media/pricing/pricing-001.png)

R. Reveja o volume de dados do mês. Isto inclui todos os dados recebidos e retidos (após qualquer [amostragem)](../../azure-monitor/app/sampling.md)das suas aplicações de servidor e cliente, e de testes de disponibilidade.  
B. É feita uma carga separada para [testes web em várias etapas](../../azure-monitor/app/availability-multistep.md). (Isto não inclui testes de disponibilidade simples, que estão incluídos na carga de volume de dados.)  
C. Veja as tendências de volume de dados do mês passado.  
D. Ativar [a amostragem de](../../azure-monitor/app/sampling.md)ingestão de dados .
E. Detete a tampa diária do volume de dados.  

(Note que todos os preços apresentados em imagens neste artigo são apenas para fins, por exemplo. Para preços correntes na sua moeda e região, consulte preços de Insights de [Aplicação][pricing].)

Para investigar mais profundamente o uso dos seus Insights de Aplicação, abra a página **Métricas,** adicione a métrica denominada "Volume de ponto de dados", e, em seguida, selecione a opção de *divisão Apply* para dividir os dados por "Tipo de item de telemetria".

As despesas com insights de aplicação são adicionadas à sua conta Azure. Pode ver detalhes da sua conta Azure na secção **de Faturação** do portal Azure, ou no portal de [faturação Azure](https://account.windowsazure.com/Subscriptions).

![No menu esquerdo, selecione Faturação](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Utilização de métricas de volume de dados
<a id="understanding-ingested-data-volume"></a>

Para saber mais sobre os seus volumes de dados, selecionando **Métricas** para o seu recurso Application Insights, adicione um novo gráfico. Para a métrica do gráfico, em **métricas baseadas em Registos,** selecione volume de **ponto de dados**. Clique **em Aplicar divisão**, e selecione grupo por ** `Telemetryitem` tipo**.

![Use métricas para olhar para o volume de dados](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Consultas para compreender detalhes do volume de dados

Existem duas abordagens para investigar volumes de dados para Insights de Aplicação. O primeiro utiliza informação `systemEvents` agregada na `_BilledSize` tabela, e o segundo utiliza o imóvel, que está disponível em cada evento ingerido.

#### <a name="using-aggregated-data-volume-information"></a>Utilização de informações agregadas sobre volume de dados

Por exemplo, pode `systemEvents` utilizar a tabela para ver o volume de dados ingerido nas últimas 24 horas com a consulta:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ou para ver um gráfico de volume de dados (em bytes) por tipo de dados nos últimos 30 dias, pode utilizar:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Note que esta consulta pode ser usada num Alerta de [Registo Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) para configurar alertas sobre volumes de dados.  

Para saber mais sobre as alterações de dados da sua telemetria, podemos obter a contagem de eventos por tipo usando a consulta:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Utilização do tamanho dos dados por informação do evento

Para saber mais detalhes sobre a origem dos seus `_BilledSize` volumes de dados, pode utilizar a propriedade que está presente em cada evento ingerido.

Por exemplo, para ver quais as operações que geram mais volume `_BilledSize` de dados nos últimos 30 dias, podemos resumir para todos os eventos de dependência:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Visualização de insights de aplicação na sua conta Azure

O Azure fornece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade "Análise de Custos" permite-lhe visualizar os seus gastos com recursos Azure. A adição de um filtro por tipo de recurso (ao microsoft.insights/componentes para Insights de Aplicação) permitirá acompanhar os seus gastos.

Mais compreensão do seu uso pode ser obtida [baixando o seu uso a partir do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
Na folha de cálculo descarregada, pode ver o uso por recurso Azure por dia. Nesta folha de cálculo do Excel, a utilização a partir dos recursos da aplicação Insights pode ser encontrada através da filtragem da coluna "Categoria Meter" para mostrar "Application Insights" e "Log Analytics", e depois adicionar um filtro na coluna "Id de instância" que é "contém microsoft.insights/componentes".  A maioria da utilização de Insights de Aplicação é reportada em metros com a categoria de medidor de Log Analytics, uma vez que existe um único backend de registos para todos os componentes do Monitor Azure.  Apenas os recursos da Application Insights em níveis de preços antigos e testes web em várias etapas são relatados com uma categoria de Medidor de Insights de Aplicação.  A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender a sua conta Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="managing-your-data-volume"></a>Gerir o volume de dados

O volume de dados que envia pode ser gerido utilizando as seguintes técnicas:

* **Amostragem:** Pode utilizar amostras para reduzir a quantidade de telemetria que é enviada das suas aplicações de servidor e cliente, com uma distorção mínima de métricas. A amostragem é a principal ferramenta que pode utilizar para afinar a quantidade de dados que envia. Saiba mais sobre [as características de amostragem.](../../azure-monitor/app/sampling.md)

* **Limita as chamadas do Ajax**: Pode limitar o número de chamadas do Ajax que podem ser [reportadas](../../azure-monitor/app/javascript.md#configuration) em todas as páginas, ou desligar o relatório do Ajax.

* **Desative os módulos desnecessários**: [Editar ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) para desligar os módulos de recolha de que não precisa. Por exemplo, pode decidir que os contadores de desempenho ou os dados de dependência são inessenciais.

* **Métricas pré-agregadas**: Se colocar chamadas para trackMetric na sua aplicação, pode reduzir o tráfego utilizando a sobrecarga que aceita o seu cálculo do desvio médio e padrão de um lote de medições. Ou, pode usar um [pacote pré-agregador.](https://www.myget.org/gallery/applicationinsights-sdk-labs)
 
* **Tampa diária**: Quando cria um recurso Application Insights no portal Azure, a tampa diária está definida para 100 GB/dia. Quando cria um recurso Application Insights no Estúdio Visual, o padrão é pequeno (apenas 32,3 MB/dia). A predefinição diária da tampa está definida para facilitar o teste. Pretende-se que o utilizador aumente a tampa diária antes de implementar a app em produção. 

    A tampa máxima é de 1.000 GB/dia, a menos que solicite um máximo máximo para uma aplicação de tráfego elevado.
    
    Os e-mails de aviso sobre a tampa diária são enviados para conta que são membros destas funções para o seu recurso Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Tenha cuidado quando definir a tampa diária. A sua intenção deve ser *nunca atingir o limite diário.* Se atingir a tampa diária, perde dados para o resto do dia e não pode monitorizar a sua aplicação. Para alterar a tampa diária, utilize a opção diária de tampa de **volume.** Pode aceder a esta opção no painel **de utilização e custos estimados** (isto é descrito mais detalhadamente mais tarde no artigo).
    
    Removemos a restrição a alguns tipos de subscrição que têm crédito que não poderia ser usado para Insights de Aplicação. Anteriormente, se a subscrição tiver um limite de gastos, o diálogo diário da tampa tem instruções para remover o limite de gastos e permitir que a tampa diária seja aumentada para além de 32,3 MB/dia.
    
* **Estrangulamento**: A aceleração limita a taxa de dados a 32.000 eventos por segundo, média de mais de 1 minuto por chave de instrumentação. O volume de dados que a sua aplicação envia é avaliado a cada minuto. Se exceder a taxa por segundo média ao longo do minuto, o servidor recusa alguns pedidos. O SDK amortece os dados e tenta reenviá-lo. Espalha uma onda ao longo de vários minutos. Se a sua aplicação enviar dados de forma consistente a mais do que a taxa de estrangulamento, alguns dados serão retirados. (Os SDKs ASP.NET, Java e JavaScript tentam reenviar dados desta forma; outros SDKs podem simplesmente deixar cair dados acelerados.) Se ocorrer estrangulamento, um aviso de notificação alerta-o de que isso ocorreu.

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie o seu volume máximo de dados diários

Pode utilizar a tampa de volume diária para limitar os dados recolhidos. No entanto, se a tampa for cumprida, ocorre uma perda de toda a telemetria enviada do seu pedido para o resto do dia. Não é *aconselhável* que a sua aplicação atinja o limite diário. Não é possível acompanhar a saúde e o desempenho da sua aplicação depois de atingir o limite diário.

Em vez de utilizar a tampa diária de volume, utilize [amostras](../../azure-monitor/app/sampling.md) para afinar o volume de dados ao nível que deseja. Em seguida, utilize a tampa diária apenas como um "último recurso" caso a sua aplicação comece inesperadamente a enviar volumes muito mais elevados de telemetria.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite de dados diários a definir

Reveja a utilização de Insights de Aplicação e custos estimados para compreender a tendência de ingestão de dados e qual é a tampa de volume diária a definir. Deve ser considerado com cuidado, uma vez que não poderá monitorizar os seus recursos após o limite ser atingido.

### <a name="set-the-daily-cap"></a>Definir o boné diário

Para alterar a tampa diária, na secção **Configure** do seu recurso Application Insights, na página **de Utilização e custos estimados,** selecione **Daily Cap**.

![Ajuste a tampa diária de volume de telemetria](./media/pricing/pricing-003.png)

Para [alterar a tampa diária via Azure Resource Manager,](../../azure-monitor/app/powershell.md)a propriedade a mudar é a `dailyQuota`.  Via Azure Resource Manager também `dailyQuotaResetTime` pode definir a `warningThreshold`tampa diária.

### <a name="create-alerts-for-the-daily-cap"></a>Criar alertas para o Daily Cap

A Aplicação Insights Daily Cap cria um evento no registo de atividade do Azure quando os volumes de dados ingeridos atingem o nível de aviso ou o nível de tampa diária.  Pode [criar um alerta com base nestes eventos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)de registo de atividade. Os nomes de sinal para estes eventos são:

* Aplicativo Insights componente limiar de aviso diário da tampa atingido

* Aplicação Insights componente diária tampa alcançada

## <a name="sampling"></a>Amostragem
[A amostragem](../../azure-monitor/app/sampling.md) é um método para reduzir a taxa a que a telemetria é enviada para a sua aplicação, mantendo ao mesmo tempo a capacidade de encontrar eventos relacionados durante as pesquisas de diagnóstico. Também mantém as contagens de eventos corretas.

A amostragem é uma forma eficaz de reduzir os encargos e manter-se dentro da sua quota mensal. O algoritmo de amostragem retém itens relacionados de telemetria para que, por exemplo, quando utiliza a Search, possa encontrar o pedido relacionado com uma determinada exceção. O algoritmo também retém as contagens corretas para que você veja os valores corretos no Metric Explorer para taxas de pedido, taxas de exceção e outras contagens.

Há várias formas de amostragem.

* [A amostragem adaptável](../../azure-monitor/app/sampling.md) é o padrão para o ASP.NET SDK. A amostragem adaptável ajusta-se automaticamente ao volume de telemetria que a sua aplicação envia. Opera automaticamente no SDK na sua aplicação web para que o tráfego de telemetria na rede seja reduzido. 
* *A amostragem de ingestão* é uma alternativa que funciona no ponto em que a telemetria da sua aplicação entra no serviço Application Insights. A amostragem de ingestão não afeta o volume de telemetria enviado da sua aplicação, mas reduz o volume que é retido pelo serviço. Você pode usar amostras de ingestão para reduzir a quota que é usada por telemetria de navegadores e outros SDKs.

Para definir a amostragem de ingestão, vá ao painel de **preços:**

![Na quota e painel de preços, selecione o azulejo amostras e, em seguida, selecione uma fração de amostragem](./media/pricing/pricing-004.png)

> [!WARNING]
> O painel **de amostragem de dados** controla apenas o valor da amostragem de ingestão. Não reflete a taxa de amostragem aplicada pelo SDK de Insights de Aplicação na sua aplicação. Se a telemetria de entrada já tiver sido amostrada no SDK, a amostragem de ingestão não é aplicada.
>

Para descobrir a taxa de amostragem real, não importa onde tenha sido aplicada, use uma [consulta de Analytics.](analytics.md) A consulta é assim:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Em cada registo `itemCount` retido, indica o número de registos originais que representa. É igual a 1 + o número de registos anteriores descartados.

## <a name="change-the-data-retention-period"></a>Change the data retention period (Alterar o período de retenção de dados)

A retenção padrão dos recursos da Application Insights é de 90 dias. Diferentes períodos de retenção podem ser selecionados para cada recurso Desinformação de Aplicação. O conjunto completo de períodos de retenção disponíveis é de 30, 60, 90, 120, 180, 270, 365, 550 ou 730 dias.

Para alterar a retenção, a partir do seu recurso Application Insights, vá à página **de Utilização e Custos Estimados** e selecione a opção de retenção de **dados:**

![Ajuste a tampa diária de volume de telemetria](./media/pricing/pricing-005.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes de os dados mais antigos forem removidos.

A retenção também pode ser [definida programaticamente usando powerShell](powershell.md#set-the-data-retention) usando o `retentionInDays` parâmetro. Se definir a retenção de dados para 30 dias, pode `immediatePurgeDataOn30Days` desencadear uma purga imediata de dados mais antigos utilizando o parâmetro, o que pode ser útil para cenários relacionados com a conformidade. Esta funcionalidade de purga só é exposta através do Azure Resource Manager e deve ser utilizada com extremo cuidado. O tempo de reset diário para a tampa do volume de `dailyQuotaResetTime` dados pode ser configurado utilizando o Gestor de Recursos Azure para definir o parâmetro.

## <a name="data-transfer-charges-using-application-insights"></a>Encargos de transferência de dados usando Insights de Aplicação

O envio de dados para A aplicação Insights pode incorrer em encargos de largura de banda de dados. Tal como descrito na página de preços da [Largura de Banda do Azure,](https://azure.microsoft.com/pricing/details/bandwidth/)a transferência de dados entre os serviços do Azure, localizadoem em duas regiões, cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, esta carga é muito pequena (poucos %) em comparação com os custos de ingestão de dados de registo de insights de aplicação. Consequentemente, controlar os custos do Log Analytics tem de se concentrar no volume de dados ingerido, e temos orientações para ajudar a compreender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Resumo dos limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Desativar os e-mails diários da tampa

Para desativar os e-mails diários da tampa de volume, sob a secção **Configure** do seu recurso Application Insights, no painel **de utilização e custos estimados,** selecione **Daily Cap**. Existem configurações para enviar e-mail quando a tampa é atingida, bem como quando um nível de aviso ajustável foi atingido. Se desejar desativar todos os e-mails diários relacionados com o volume da tampa, desfaça ambas as caixas.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Legacy Enterprise (Per Node) nível de preços

Para os primeiros adotadores da Azure Application Insights, existem ainda dois possíveis níveis de preços: Básico e Enterprise. O nível de preços básicos é o mesmo descrito acima e é o nível padrão. Inclui todas as funcionalidades de nível Enterprise, sem custos adicionais. O nível básico fatura principalmente sobre o volume de dados que é ingerido.

> [!NOTE]
> Estes níveis de preços legados foram renomeados. O nível de preços da Enterprise é agora chamado **Per Node** e o nível de preços básicos é agora chamado **Per GB**. Estes novos nomes são usados abaixo e no portal Azure.  

O nível Per Nó (anteriormente Enterprise) tem uma taxa por nó, e cada nó recebe um subsídio diário de dados. No nível de preços per nó, é cobrado por dados ingeridos acima do subsídio incluído. Se estiver a utilizar a Suite de Gestão de Operações, deve escolher o nível Per Node.

Para preços correntes na sua moeda e região, consulte preços de Insights de [Aplicação](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> Em abril de 2018, [introduzimos](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) um novo modelo de preços para a monitorização do Azure. Este modelo adota um modelo simples de "pay-as-you-go" em todo o portfólio completo de serviços de monitorização. Saiba mais sobre o novo modelo de [preços,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)como [avaliar o impacto de se mover para este modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) com base nos seus padrões de utilização, e como optar pelo novo [modelo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Por Nível de Nó e Direitos de subscrição da Suite de Gestão de Operações

Os clientes que adquirem a Suite De Gestão de Operações E1 e E2 podem obter insights de aplicação por nó como uma componente adicional sem custos adicionais como [anunciado anteriormente.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) Especificamente, cada unidade de Gestão de Operações Suite E1 e E2 inclui um direito a um nó do nível de Insights de Aplicação Por Nó. Cada nó de Insights de Aplicação inclui até 200 MB de dados ingeridos por dia (separados da ingestão de dados do Log Analytics), com retenção de dados de 90 dias sem custos adicionais. O nível é descrito mais detalhadamente mais tarde no artigo.

Como este nível é aplicável apenas a clientes com uma subscrição de Suite de Gestão de Operações, os clientes que não têm uma subscrição da Suite de Gestão de Operações não vêem uma opção para selecionar este nível.

> [!NOTE]
> Para garantir que obtém este direito, os recursos da Sua Aplicação Insights devem estar no nível de preços Per Node. Este direito aplica-se apenas como nós. Os recursos da Application Insights no nível Per GB não percebem qualquer benefício. Este direito não é visível nos custos estimados mostrados no painel de custos de **utilização e estimativa.** Além disso, se mover uma subscrição do novo modelo de preços de monitorização do Azure em abril de 2018, o nível Per GB é o único nível disponível. Mover uma subscrição do novo modelo de preços de monitorização do Azure não é aconselhável se tiver uma subscrição da Suite de Gestão de Operações.

### <a name="how-the-per-node-tier-works"></a>Como funciona o nível Per Node

* Você paga por cada nó que envia telemetria para quaisquer aplicações no nível Per Node.
  * Um *nó* é uma máquina de servidor físico ou virtual ou uma instância de papel de plataforma como um serviço que acolhe a sua aplicação.
  * Máquinas de desenvolvimento, navegadores de clientes e dispositivos móveis não contam como nós.
  * Se a sua aplicação tiver vários componentes que enviam telemetria, como um serviço web e um trabalhador de back-end, os componentes são contados separadamente.
  * Os dados do [Live Metrics Stream](../../azure-monitor/app/live-stream.md) não são contados para efeitos de preços. Numa subscrição, os seus encargos são por nó, não por app. Se tiver cinco nós que enviam telemetria para 12 apps, a taxa é de cinco nós.
* Embora as acusações sejam citadas por mês, é cobrado apenas por qualquer hora em que um nó envia telemetria de uma aplicação. A carga horária é a taxa mensal citada dividida por 744 (o número de horas num mês de 31 dias).
* É dada uma alocação de volume de dados de 200 MB por dia para cada nó detetado (com granularidade horária). A atribuição de dados não utilizados não é transportada de um dia para o outro.
  * Se escolher o nível de preços Per Nó, cada subscrição recebe uma mesada diária de dados com base no número de nós que enviam telemetria para os recursos da Aplicação Insights nessa subscrição. Assim, se tiver cinco nós que enviam dados durante todo o dia, terá um subsídio conjunto de 1 GB aplicado a todos os recursos da Application Insights nessa subscrição. Não importa se certos nós enviam mais dados do que outros nós porque os dados incluídos são partilhados em todos os nós. Se num determinado dia, os recursos do Application Insights receberem mais dados do que os incluídos na atribuição diária de dados para esta subscrição, aplicam-se os encargos de dados por GB. 
  * O subsídio diário de dados é calculado como o número de horas do dia (utilizando UTC) que cada nó envia telemetria dividida por 24 multiplicados por 200 MB. Assim, se tiver quatro nós que enviam telemetria durante 15 das 24 horas do dia, os dados incluídos nesse dia seriam ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Ao preço de 2,30 USD por GB para sobrecarga de dados, a taxa seria de 1,15 USD se os nós enviassem 1 GB de dados nesse dia.
  * O subsídio diário per nó não é partilhado com pedidos para os quais escolheu o nível Per GB. A mesada não utilizada não é transportada do dia-a-dia.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemplos de como determinar a contagem distinta do nó

| Cenário                               | Contagem total de nó diário |
|:---------------------------------------|:----------------:|
| 1 aplicação utilizando 3 instâncias do Serviço de Aplicações Azure e 1 servidor virtual | 4 |
| 3 aplicações em execução em 2 VMs; os recursos de Insights de Aplicação para estas aplicações estão na mesma subscrição e no nível Per Nó | 2 | 
| 4 aplicações cujos recursos de Insights de Aplicações estejam na mesma subscrição; cada aplicação executando 2 instâncias durante 16 horas fora do pico, e 4 instâncias durante 8 horas de pico | 13.33 |
| Serviços em nuvem com 1 Função de Trabalhador e 1 Função Web, cada um executando 2 instâncias | 4 | 
| Um cluster de tecido de serviço Azure de 5 nós que executa 50 microserviços; cada microserviço executando 3 instâncias | 5|

* A contagem precisa do nó depende de qual o SDK de Insights de Aplicação que a sua aplicação está a utilizar. 
  * Nas versões SDK 2.2 e posteriormente, tanto o [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) de Insights de Aplicação como o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) reportam cada anfitrião de aplicações como um nó. Exemplos são o nome do computador para servidor físico e anfitriões VM ou o nome de exemplo para serviços na nuvem.  A única exceção é uma aplicação que utiliza apenas o [Núcleo .NET](https://dotnet.github.io/) e o SDK de Insights de Aplicação. Nesse caso, apenas um nó é relatado para todos os anfitriões porque o nome do anfitrião não está disponível.
  * Para versões anteriores do SDK, o [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) comporta-se como as versões SDK mais recentes, mas o [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) reporta apenas um nó, independentemente do número de anfitriões de aplicações.
  * Se a sua aplicação utilizar o SDK para definir o **roleInstance** para um valor personalizado, por padrão, esse mesmo valor é usado para determinar a contagem do nó.
  * Se estiver a utilizar uma nova versão SDK com uma aplicação que funciona a partir de máquinas de clientes ou dispositivos móveis, a contagem de nós pode devolver um número que é grande (devido ao grande número de máquinas de clientes ou dispositivos móveis).

## <a name="automation"></a>Automatização

Pode escrever um guião para definir o nível de preços utilizando a Azure Resource Management. [Saiba como](powershell.md#price).

## <a name="next-steps"></a>Passos seguintes

* [Amostragem](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/