---
title: Ver dados de aplicações do Azure Application Insights | Documentos da Microsoft
description: Pode utilizar a solução do conector do Application Insights para diagnosticar problemas de desempenho e compreender o que os utilizadores fazem com a sua aplicação quando monitorizados com o Application Insights.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: magoedte
ms.openlocfilehash: b9a847f04048cd17d550ca66bd3e6502577746eb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878460"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Solução de gestão do Application Insights Connector (preterido)

![Símbolo do Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o apoio da [consultas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md), a solução de gestão do conector do Application Insights já não é necessária. Foi preterida e removida do Azure Marketplace, juntamente com o portal do OMS que oficialmente foi preterido no dia 15 de Janeiro de 2019 para cloud comercial do Azure. Ele será retirado a 30 de Março de 2019 para a cloud do Azure US Government.
>
>Ligações existentes continuarão a funcionar até 30 de Junho de 2019.  Com a descontinuação de portal do OMS, não é possível configurar e remover as ligações existentes do portal. Ver [remover o conector com o PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script sobre como utilizar o PowerShell para remover as ligações existentes.
>
>Para obter orientações sobre como consultar o Application Insights para registar dados para várias aplicações, consulte [uniformizar a vários recursos do Azure Monitor Application Insights](../log-query/unify-app-resource-data.md). Para obter mais informações sobre a descontinuação de portal do OMS, consulte [portal do OMS para o Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

A solução de conector de informações de aplicações ajuda-o a diagnosticar problemas de desempenho e compreender o que os utilizadores fazem com a sua aplicação quando está a ser monitorizado com [Application Insights](../../azure-monitor/app/app-insights-overview.md). Vistas da mesma telemetria de aplicações que os desenvolvedores vêem no Application Insights estão disponíveis no Log Analytics. No entanto, quando integrar as suas aplicações do Application Insights com o Log Analytics, visibilidade de seus aplicativos é aumentada em ter dados operacionais e da aplicação num único local. Ter os mesmos modos de exibição ajuda-o a colaborar com os programadores de aplicações. As vistas comuns podem ajudar a reduzir o tempo para detetar e resolver os problemas de plataforma de aplicativos e.

Quando utiliza a solução, pode:

- Ver todas as suas aplicações do Application Insights num único local, mesmo quando eles estão em diferentes subscrições do Azure
- Correlacione dados de infraestrutura com dados de aplicação
- Visualizar dados de aplicação com perspetivas na pesquisa de registos
- Dinâmica de dados do Log Analytics para a sua aplicação do Application Insights no portal do Azure

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são recolhidos para o conector do Application Insights por agentes. Todos os dados utilizados pela solução é fornecido diretamente a partir do Azure.

| Origem Ligada | Suportadas | Descrição |
| --- | --- | --- |
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de agentes do Linux. |
| [Grupo de gestão do SCOM](../../azure-monitor/platform/om-agents.md) | Não | A solução não recolhe informações de agentes num grupo de gestão ligado do SCOM. |
| [Conta de armazenamento do Azure](collect-azure-metrics-logs.md) | Não | A solução faz não informações da coleção do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder às informações de conector do Application Insights, tem de ter uma subscrição do Azure
- Tem de ter pelo menos um recurso do Application Insights configurado.
- Tem de ser o proprietário ou contribuinte de recurso do Application Insights.

## <a name="configuration"></a>Configuração

1. Ativar a solução de análise de aplicações Web do Azure a partir do [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) ou utilizando o processo descrito na [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Navegue para o [portal do Azure](https://portal.azure.com). Selecione **todos os serviços** para abrir o Application Insights. Em seguida, procure o Application Insights. 
3. Sob **subscrições**, selecione uma subscrição que tem os recursos do Application Insights e, em **nome**, selecione uma ou mais aplicações.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, dados ficarem disponíveis e o mosaico do Application Insights é atualizado com dados, semelhante à imagem seguinte:

![Mosaico do Application Insights](./media/app-insights-connector/app-insights-tile.png)

Outros pontos a ter em mente:

- Só pode ligar aplicações do Application Insights a uma área de trabalho do Log Analytics.
- Só pode ligar [recursos básico ou empresarial Application Insights](https://azure.microsoft.com/pricing/details/application-insights) ao Log Analytics. No entanto, pode utilizar o escalão gratuito do Log Analytics.

## <a name="management-packs"></a>Pacotes de gestão

Esta solução não instala os pacotes de gestão em grupos de gestão ligados.

## <a name="use-the-solution"></a>Utilizar a solução

As secções seguintes descrevem como pode utilizar os painéis mostrados no dashboard do Application Insights para ver e interagir com os dados das suas aplicações.

### <a name="view-application-insights-connector-information"></a>Ver informações de conector do Application Insights

Clique nas **Application Insights** mosaico para abrir o **Application Insights** dashboard para ver os seguintes painéis.

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash02.png)

O dashboard inclui os painéis mostrados na tabela. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar **ver todas as** na parte inferior do painel ou ao clicar no cabeçalho do painel.


| **Coluna** | **Descrição** |
| --- | --- |
| Aplicações - número de aplicativos | Mostra o número de aplicativos em recursos de aplicativos. Também apresenta uma lista de nomes de aplicativo e para cada um, a contagem de registos de aplicação. Clique no número para executar uma pesquisa de registos para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique num nome de aplicação para executar uma pesquisa de registos para a aplicação que mostra os registos de aplicação por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de dados – anfitriões a enviar dados | Mostra o número de computadores anfitriões que estão a enviar dados. Também apresenta uma lista de anfitriões de computador e a contagem de registos para cada anfitrião. Clique no número para executar uma pesquisa de registos para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique no nome do computador para executar uma pesquisa de registos para o anfitrião que mostra os registos de aplicação por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados Webtest | Mostra um gráfico de anel para resultados de teste da web, indicando aprovação ou reprovação. Clique no gráfico para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de passos e falhas de todos os testes. Mostra todas as aplicações Web com tráfego para o último minuto. Clique num nome de aplicação para ver uma pesquisa de registos a mostrar os detalhes de testes da web com falha. |
| Pedidos de servidor – pedidos por hora | Mostra um gráfico de linhas de pedidos de servidor por hora por vários aplicativos. Coloque o cursor sobre uma linha no gráfico para ver as aplicações de 3 principais receber pedidos para um ponto no tempo. Também mostra uma lista dos aplicativos recebem pedidos e o número de pedidos durante o período selecionado. <br><br>Clique no gráfico para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linhas mais detalhado dos pedidos de servidor por hora por vários aplicativos. <br><br> Clique num aplicativo na lista para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra uma lista de pedidos, gráficos de pedidos durante o período de tempo e a pedido e uma lista do pedido de códigos de resposta.   |
| Falhas – pedidos falhados por hora | Mostra um gráfico de linhas de pedidos de aplicação que falhou por hora. Paire o rato sobre o gráfico para ver as principais aplicações de 3 com pedidos falhados para um ponto no tempo. Também mostra uma lista de aplicativos com o número de pedidos falhados para cada um. Clique no gráfico para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linhas mais detalhado de pedidos de aplicação que falhou. <br><br>Clique num item na lista para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra gráficos para pedidos com falha de pedidos falhados ao longo do período de tempo e a pedido e uma lista de códigos de resposta de solicitação com falha. |
| Exceções – exceções por hora | Mostra um gráfico de linhas de exceções por hora. Paire o rato sobre o gráfico para ver as principais aplicações de 3 com exceções para um ponto no tempo. Também mostra uma lista de aplicativos com o número de exceções para cada um. Clique no gráfico para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de ligação mais detalhado de exceções. <br><br>Clique num item na lista para executar uma pesquisa de registos para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> que mostra uma lista de exceções, gráficos para exceções ao longo do tempo e com falhas de pedidos e uma lista dos tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Ver o ponto de vista do Application Insights com pesquisa de registos

Quando clicar em qualquer item no dashboard, verá um ponto de vista do Application Insights mostrado na pesquisa. O ponto de vista fornece uma visualização expandida, consoante o tipo de telemetria que selecionou. Então, o conteúdo de visualização é alterado para tipos de telemetria diferentes.

Ao clicar em qualquer lugar no painel de aplicações, consulte a predefinição **aplicativos** perspetiva.

![Perspetiva de aplicativos de informações da aplicação](./media/app-insights-connector/applications-blade-drill-search.png)

O ponto de vista mostra uma descrição geral da aplicação que selecionou.

O **disponibilidade** painel mostra uma exibição de ponto de vista diferentes onde pode ver os resultados de teste da web e pedidos falhados relacionados.

![Perspetiva de disponibilidade de informações da aplicação](./media/app-insights-connector/availability-blade-drill-search.png)

Ao clicar em qualquer lugar no **pedidos de servidor** ou **falhas** painéis, os componentes de perspetiva alterar para lhe fornecer uma visualização que relacionadas com os pedidos.

![Painel de informações de falhas de aplicações](./media/app-insights-connector/server-requests-failures-drill-search.png)

Ao clicar em qualquer lugar no **exceções** painel, verá uma visualização que está adaptada às exceções.

![Painel de informações de exceções de aplicações](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente se clicar em algo um a **conector do Application Insights** dashboard, dentro da **pesquisa** página em si, qualquer consulta retornar dados do Application Insights mostram o aplicativo Ponto de vista de informações. Por exemplo, se estiver a visualizar dados do Application Insights, um **&#42;** consulta também mostra o separador de ponto de vista semelhante à imagem seguinte:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Componentes de perspetiva são atualizadas dependendo da consulta de pesquisa. Isso significa que pode filtrar os resultados ao utilizar qualquer campo de pesquisa que lhe dá a capacidade de ver os dados da:

- Todas as suas aplicações
- Uma única aplicação selecionada
- Um grupo de aplicações

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Utilize também a uma aplicação no portal do Azure

Os painéis do Application Insights Connector foram concebidos para permitir que passar para a aplicação selecionada do Application Insights *ao utilizar o portal do Azure*. Pode utilizar a solução como uma plataforma de monitorização de alto nível que o ajuda a resolver problemas numa aplicação. Quando vir um problema em potencial em qualquer uma das suas aplicações ligadas, pode de qualquer teste para o mesmo na pesquisa do Log Analytics ou pode passar diretamente para a aplicação Application Insights.

Para dinamizar, clique nas reticências (**...** ) que aparece no final de cada linha e selecione **abrir no Application Insights**.

>[!NOTE]
>**Abrir no Application Insights** não está disponível no portal do Azure.

![Abrir no Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Foi corrigido o exemplo de dados

O Application Insights fornece *[correção de amostragem](../../azure-monitor/app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Ao ativar a amostragem no seu aplicativo do Application Insights, obtém uma redução do número de entradas armazenados no Application Insights e no Log Analytics. Embora a consistência dos dados é preservada na **conector do Application Insights** página e perspectivas, deve corrigir manualmente amostras de dados para as suas consultas personalizadas.

Eis um exemplo de correção de amostragem numa consulta de pesquisa de registo:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O **contagem de objeto de amostragem** campo está presente em todas as entradas e mostra o número de pontos de dados que representa a entrada. Se ativar a amostragem de para a sua aplicação do Application Insights **contagem de objeto de amostragem** for superior a 1. Para contar o número real de entradas que gera a sua aplicação, soma o **contagem de objeto de amostragem** campos.

Amostragem afeta apenas o número total de entradas que gera a sua aplicação. Não precisa de corrigir a amostragem para como campos de métrica **RequestDuration** ou **AvailabilityDuration** porque esses campos mostram a média para entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria dos dados das suas aplicações ligadas do Application Insights:

- Disponibilidade
- Exceções
- Pedidos
- Vistas de página – sua área de trabalho receber as vistas de página, tem de configurar as suas aplicações para coletar essas informações. Obter mais informações, consulte [vistas de página](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – sua área de trabalho receber eventos personalizados, tem de configurar as suas aplicações para coletar essas informações. Obter mais informações, consulte [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Dados são recebidos pelo Log Analytics do Application Insights à medida que ficam disponível.

## <a name="output-data"></a>Dados de saída

Um registo com um *tipo* dos *Application Insights* é criada para cada tipo de dados de entrada. Registos do Application Insights tem propriedades mostradas nas seções a seguir:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Hora do registo |
| ApplicationId | Chave de instrumentação da aplicação Application Insights |
| ApplicationName | Nome do Application Insights aplicação |
| RoleInstance | ID de anfitrião do servidor |
| deviceType | Dispositivo de cliente |
| ScreenResolution |   |
| Continente | Continente em que o pedido teve origem |
| País | País onde o pedido teve origem |
| Província | Província, estado ou região em que o pedido teve origem |
| Localidade | Cidade ou cidade em que o pedido teve origem |
| isSynthetic | Indica se o pedido foi criado por um utilizador ou pelo método automatizado. = TRUE gerados pelo utilizador ou = false método automatizado |
| SamplingRate | Percentagem de telemetria gerada pelo SDK do que é enviado para o portal. Intervalo de 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Por exemplo, 4 =&gt; 25% |
| IsAuthenticated | Verdadeiro ou falso |
| OperationID | Itens que tenham a mesma operação ID são apresentadas como itens relacionados no portal. Normalmente, o ID do pedido |
| ParentOperationID | ID da operação principal |
| OperationName |   |
| SessionId | GUID para identificar exclusivamente a sessão em que o pedido foi criado |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos de disponibilidade específica

| Propriedade | Descrição |
| --- | --- |
| TelemetryType | Disponibilidade |
| AvailabilityTestName | Nome do teste web |
| AvailabilityRunLocation | Origem geográfica de pedido de http |
| AvailabilityResult | Indica o resultado de êxito do teste web |
| AvailabilityMessage | A mensagem ligada para o teste web |
| AvailabilityCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| DataSizeMetricValue | 1.0 ou 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| AvailabilityDuration | Tempo, em milissegundos, da duração do teste web |
| AvailabilityDurationCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID exclusivo para o teste web |
| AvailabilityTimestamp | Timestamp precisa do teste de disponibilidade |
| AvailabilityDurationMin | Para os registos de amostras, este campo mostra a duração do teste web mínimo (milissegundos) para os pontos de dados representados |
| AvailabilityDurationMax | Para os registos de amostras, este campo mostra a duração do teste web máximo (milissegundos) para os pontos de dados representados |
| AvailabilityDurationStdDev | Para os registos de amostras, este campo mostra o desvio-padrão entre todas as durações de teste de web (milissegundos) para os pontos de dados representados |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos de exceção específico

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetryType | Exceção |
| ExceptionType | Tipo de exceção |
| ExceptionMethod | O método que cria a exceção |
| ExceptionAssembly | Assembly inclui o framework e versão, bem como o token de chave pública |
| ExceptionGroup | Tipo de exceção |
| ExceptionHandledAt | Indica o nível que a exceção de processados |
| ExceptionCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| ExceptionMessage | Mensagem de exceção |
| ExceptionStack | Pilha completo da exceção |
| ExceptionHasStack | VERDADEIRO se a exceção tem uma pilha |



### <a name="request-specific-fields"></a>Campos de específico da solicitação

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| TelemetryType | Pedir |
| ResponseCode | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica o êxito ou falha. VERDADEIRO ou FALSO. |
| RequestID | ID para identificar exclusivamente o pedido |
| RequestName | GET/pós + base de URL |
| RequestDuration | Tempo, em segundos, da duração do pedido |
| do IdP | URL do pedido não incluindo o anfitrião |
| Anfitrião | Anfitrião do servidor Web |
| URLBase | URL completo do pedido |
| ApplicationProtocol | Tipo de protocolo utilizado pela aplicação |
| RequestCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Por exemplo, 4 =&gt; 25% |
| RequestDurationMin | Para os registos de amostras, este campo mostra a duração mínima de pedido (milissegundos) para os pontos de dados representados. |
| RequestDurationMax | Para os registos de amostras, este campo mostra a duração máxima de pedido (milissegundos) para os pontos de dados representados |
| RequestDurationStdDev | Para os registos de amostras, este campo mostra o desvio-padrão entre todas as durações de pedido (milissegundos) para os pontos de dados representados |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

Esta solução não tem um conjunto de pesquisas de registos de exemplo mostrado no dashboard. No entanto, as consultas de pesquisa de registo de exemplo com as descrições são apresentadas na [informações do conector do Application Insights do modo de exibição](#view-application-insights-connector-information) secção.

## <a name="removing-the-connector-with-powershell"></a>Remover o conector com o PowerShell
Com a descontinuação de portal do OMS, não é possível configurar e remover as ligações existentes do portal. Pode remover as ligações existentes com o seguinte script do PowerShell. Tem de ser o proprietário ou contribuinte da área de trabalho e o leitor de recurso do Application Insights para efetuar esta operação.

```PowerShell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId $Subscription_app
$AIApp = Get-AzureRmApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzureRmContext -SubscriptionId $Subscription_workspace
Remove-AzureRmOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Pode obter uma lista de aplicativos usando o seguinte script do PowerShell que invoca uma chamada à REST API. 

```PowerShell
Connect-AzureRmAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzureRmContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzureRmOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Este script requer um token de autenticação de portador para autenticação no Azure Active Directory. Uma forma de obter este token está a utilizar um artigo na [site de documentação da REST API](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Clique em **experimentar** e inicie sessão na sua subscrição do Azure. Pode copiar o token de portador a partir da **pedir pré-visualização** conforme mostrado na imagem seguinte.


![Token de portador](media/app-insights-connector/bearer-token.png)


Também pode obter uma lista de utilização de aplicações uma consulta de registo:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Passos Seguintes

- Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas para as suas aplicações do Application Insights.
