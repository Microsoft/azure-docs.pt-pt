---
title: Ver dados de aplicações do Azure Application Insights | Documentos da Microsoft
description: Pode utilizar a solução do conector do Application Insights para diagnosticar problemas de desempenho e compreender o que os utilizadores fazem com a sua aplicação quando monitorizados com o Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665159"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Soluçõe de gestão de conector de insights de aplicação (Preprecated)

![Símbolo do Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o apoio de [consultas de recursos cruzados,](../../azure-monitor/log-query/cross-workspace-query.md)a solução de gestão do Conector de Insights de Aplicação já não é necessária. Foi depreciado e removido do Azure Marketplace, juntamente com o portal OMS que foi oficialmente depreciado a 15 de janeiro de 2019 para a nuvem comercial azure. Será retirado a 30 de março de 2019 para a nuvem do Governo dos EUA.
>
>As ligações existentes continuarão a funcionar até 30 de junho de 2019.  Com a depreciação do portal OMS, não há forma de configurar e remover as ligações existentes do portal. Consulte [A remoção do conector com o PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script sobre a utilização do PowerShell para remover as ligações existentes.
>
>Para obter orientações sobre consulta de dados de registo de insights de aplicação para várias aplicações, consulte Unificar vários recursos de Insights de [Aplicação do Monitor Azure](../log-query/unify-app-resource-data.md). Para obter mais informações sobre a deprecação do portal OMS, consulte o [portal OMS movendo-se para o Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

A solução ApplicationInsights Insights Connector ajuda-o a diagnosticar problemas de desempenho e a compreender o que os utilizadores fazem com a sua aplicação quando é monitorizada com insights de [aplicação](../../azure-monitor/app/app-insights-overview.md). Vistas da mesma telemetria de aplicações que os desenvolvedores vêem no Application Insights estão disponíveis no Log Analytics. No entanto, quando integrar as suas aplicações do Application Insights com o Log Analytics, visibilidade de seus aplicativos é aumentada em ter dados operacionais e da aplicação num único local. Ter os mesmos modos de exibição ajuda-o a colaborar com os programadores de aplicações. As vistas comuns podem ajudar a reduzir o tempo para detetar e resolver os problemas de plataforma de aplicativos e.

Quando utiliza a solução, pode:

- Ver todas as suas aplicações do Application Insights num único local, mesmo quando eles estão em diferentes subscrições do Azure
- Correlacione dados de infraestrutura com dados de aplicação
- Visualizar dados de aplicação com perspetivas na pesquisa de registos
- Dinâmica de dados do Log Analytics para a sua aplicação do Application Insights no portal do Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções do Log Analytics, os dados não são recolhidos para o conector do Application Insights por agentes. Todos os dados utilizados pela solução é fornecido diretamente a partir do Azure.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de agentes do Linux. |
| [Grupo de gestão scom](../../azure-monitor/platform/om-agents.md) | Não | A solução não recolhe informações de agentes num grupo de gestão ligado do SCOM. |
| [Conta de armazenamento do Azure](collect-azure-metrics-logs.md) | Não | A solução faz não informações da coleção do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder às informações de conector do Application Insights, tem de ter uma subscrição do Azure
- Tem de ter pelo menos um recurso do Application Insights configurado.
- Tem de ser o proprietário ou contribuinte de recurso do Application Insights.

## <a name="configuration"></a>Configuração

1. Ative a solução Azure Web Apps Analytics do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) ou utilizando o processo descrito nas [soluções Add Log Analytics da Galeria Solutions](../../azure-monitor/insights/solutions.md).
2. Navegue para o [portal do Azure](https://portal.azure.com). Selecione **todos os serviços** para abrir insights de aplicação. Em seguida, procure o Application Insights. 
3. Em **Subscrições**, selecione uma subscrição que tenha recursos de Insights de Aplicação e, em seguida, em **Nome,** selecione uma ou mais aplicações.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, dados ficarem disponíveis e o mosaico do Application Insights é atualizado com dados, semelhante à imagem seguinte:

![Mosaico do Application Insights](./media/app-insights-connector/app-insights-tile.png)

Outros pontos a ter em mente:

- Só pode ligar aplicações do Application Insights a uma área de trabalho do Log Analytics.
- Só pode ligar [os recursos de Insights de Aplicação Básica ou Empresarial](https://azure.microsoft.com/pricing/details/application-insights) ao Log Analytics. No entanto, pode utilizar o escalão gratuito do Log Analytics.

## <a name="management-packs"></a>Pacotes de gestão

Esta solução não instala os pacotes de gestão em grupos de gestão ligados.

## <a name="use-the-solution"></a>Utilizar a solução

As secções seguintes descrevem como pode utilizar os painéis mostrados no dashboard do Application Insights para ver e interagir com os dados das suas aplicações.

### <a name="view-application-insights-connector-information"></a>Ver informações de conector do Application Insights

Clique no azulejo Deinsights de **Aplicação** para abrir o painel de insights de **aplicação** para ver as seguintes lâminas.

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash02.png)

O dashboard inclui os painéis mostrados na tabela. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registo que retorna todos os registos quando clicar Em **Ver tudo** na parte inferior da lâmina ou quando clicar no cabeçalho da lâmina.


| **Coluna** | **Descrição** |
| --- | --- |
| Aplicações - número de aplicativos | Mostra o número de aplicativos em recursos de aplicativos. Também apresenta uma lista de nomes de aplicativo e para cada um, a contagem de registos de aplicação. Clique no número para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique num nome de aplicação para executar uma pesquisa de registos para a aplicação que mostra os registos de aplicação por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de dados – anfitriões a enviar dados | Mostra o número de computadores anfitriões que estão a enviar dados. Também apresenta uma lista de anfitriões de computador e a contagem de registos para cada anfitrião. Clique no número para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique no nome do computador para executar uma pesquisa de registos para o anfitrião que mostra os registos de aplicação por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados Webtest | Mostra um gráfico de anel para resultados de teste da web, indicando aprovação ou reprovação. Clique na tabela para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de passos e falhas de todos os testes. Mostra todas as aplicações Web com tráfego para o último minuto. Clique num nome de aplicação para ver uma pesquisa de registos a mostrar os detalhes de testes da web com falha. |
| Pedidos de servidor – pedidos por hora | Mostra um gráfico de linhas de pedidos de servidor por hora por vários aplicativos. Coloque o cursor sobre uma linha no gráfico para ver as aplicações de 3 principais receber pedidos para um ponto no tempo. Também mostra uma lista dos aplicativos recebem pedidos e o número de pedidos durante o período selecionado. <br><br>Clique no gráfico para executar uma pesquisa de registo para <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linha mais detalhado dos pedidos do servidor por hora para várias aplicações. <br><br> Clique numa aplicação na lista para executar uma pesquisa de registo para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra uma lista de pedidos, gráficos para pedidos ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido.   |
| Falhas – pedidos falhados por hora | Mostra um gráfico de linhas de pedidos de aplicação que falhou por hora. Paire o rato sobre o gráfico para ver as principais aplicações de 3 com pedidos falhados para um ponto no tempo. Também mostra uma lista de aplicativos com o número de pedidos falhados para cada um. Clique na tabela para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linha mais detalhado de pedidos de aplicação falhados. <br><br>Clique num item na lista para executar uma pesquisa de registo para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra pedidos falhados, gráficos para pedidos falhados ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido falhados. |
| Exceções – exceções por hora | Mostra um gráfico de linhas de exceções por hora. Paire o rato sobre o gráfico para ver as principais aplicações de 3 com exceções para um ponto no tempo. Também mostra uma lista de aplicativos com o número de exceções para cada um. Clique na tabela para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostre um gráfico de ligação mais detalhado de exceções. <br><br>Clique num item na lista para fazer uma pesquisa de registo para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> que mostre uma lista de exceções, gráficos para exceções ao longo do tempo e pedidos falhados, e uma lista de tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Ver o ponto de vista do Application Insights com pesquisa de registos

Quando clicar em qualquer item no dashboard, verá um ponto de vista do Application Insights mostrado na pesquisa. O ponto de vista fornece uma visualização expandida, consoante o tipo de telemetria que selecionou. Então, o conteúdo de visualização é alterado para tipos de telemetria diferentes.

Quando clica em qualquer lugar da lâmina aplicações, vê a perspetiva de **Aplicações predefinidas.**

![Perspetiva de aplicativos de informações da aplicação](./media/app-insights-connector/applications-blade-drill-search.png)

O ponto de vista mostra uma descrição geral da aplicação que selecionou.

A lâmina **de disponibilidade** mostra uma visão de perspetiva diferente onde pode ver os resultados dos testes web e pedidos falhados relacionados.

![Perspetiva de disponibilidade de informações da aplicação](./media/app-insights-connector/availability-blade-drill-search.png)

Quando clica em qualquer lugar das lâminas de Pedidos ou **Falhas** do **Servidor,** os componentes de perspetiva mudam para lhe dar uma visualização relacionada com pedidos.

![Painel de informações de falhas de aplicações](./media/app-insights-connector/server-requests-failures-drill-search.png)

Quando clica em qualquer lugar da lâmina **exceções,** vê-se uma visualização adaptada a exceções.

![Painel de informações de exceções de aplicações](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de clicar em algo no dashboard Do **Conector** de Insights de Aplicação, dentro da própria página **de Pesquisa,** qualquer consulta que devolveos dados de Insights de Aplicação mostra a perspetiva de Insights de Aplicação. Por exemplo, se estiver a ver **&#42;** dados do Application Insights, uma consulta também mostra o separador de perspetiva como a seguinte imagem:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Componentes de perspetiva são atualizadas dependendo da consulta de pesquisa. Isso significa que pode filtrar os resultados ao utilizar qualquer campo de pesquisa que lhe dá a capacidade de ver os dados da:

- Todas as suas aplicações
- Uma única aplicação selecionada
- Um grupo de aplicações

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Utilize também a uma aplicação no portal do Azure

As lâminas do Conector Insights de Aplicação foram concebidas para permitir que se desloque para a aplicação Deinsights de Aplicação selecionada *quando utilizar o portal Azure*. Pode utilizar a solução como uma plataforma de monitorização de alto nível que o ajuda a resolver problemas numa aplicação. Quando vir um problema em potencial em qualquer uma das suas aplicações ligadas, pode de qualquer teste para o mesmo na pesquisa do Log Analytics ou pode passar diretamente para a aplicação Application Insights.

Para girar, clique nas elipses**que**aparecem no final de cada linha, e selecione Open in **Application Insights**.

>[!NOTE]
>**Open in Application Insights** não está disponível no portal Azure.

![Abrir no Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Foi corrigido o exemplo de dados

Application Insights fornece *[correção de amostragem](../../azure-monitor/app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Ao ativar a amostragem no seu aplicativo do Application Insights, obtém uma redução do número de entradas armazenados no Application Insights e no Log Analytics. Embora a consistência dos dados seja preservada na página e perspetivas do **Conector** de Insights de Aplicação, deve corrigir manualmente os dados amostrados para as suas consultas personalizadas.

Eis um exemplo de correção de amostragem numa consulta de pesquisa de registo:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O campo **Contagem Amostrada** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se ligar a amostragem para a sua aplicação Insights, a **Contagem De Amostras** é superior a 1. Para contar o número real de entradas que a sua aplicação gera, soma os campos **de Contagem Amostrada.**

Amostragem afeta apenas o número total de entradas que gera a sua aplicação. Não é necessário corrigir a amostragem para campos métricos como **A Duração** do Pedido ou **a Duração da Disponibilidade** porque esses campos mostram a média para entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria dos dados das suas aplicações ligadas do Application Insights:

- Disponibilidade
- Exceções
- Pedidos
- Vistas de página – sua área de trabalho receber as vistas de página, tem de configurar as suas aplicações para coletar essas informações. Para mais informações, consulte [pageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – sua área de trabalho receber eventos personalizados, tem de configurar as suas aplicações para coletar essas informações. Para mais informações, consulte [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Dados são recebidos pelo Log Analytics do Application Insights à medida que ficam disponível.

## <a name="output-data"></a>Dados de saída

É criado um registo com um *tipo* de *ApplicationInsights* para cada tipo de dados de entrada. Registos do Application Insights tem propriedades mostradas nas seções a seguir:

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
| País | País/região onde o pedido teve origem |
| Província | Província, estado ou região em que o pedido teve origem |
| Localidade | Cidade ou cidade em que o pedido teve origem |
| isSynthetic | Indica se o pedido foi criado por um utilizador ou pelo método automatizado. Verdadeiro = método automatizado ou falso = utilizador gerado |
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

Esta solução não tem um conjunto de pesquisas de registos de exemplo mostrado no dashboard. No entanto, as consultas de pesquisa de registo de amostras com descrições são mostradas na secção de informação do [Conector de](#view-application-insights-connector-information) Insights de Aplicação view.

## <a name="removing-the-connector-with-powershell"></a>Remoção do conector com powerShell
Com a depreciação do portal OMS, não há forma de configurar e remover as ligações existentes do portal. Pode remover as ligações existentes com o seguinte script PowerShell. Deve ser o proprietário ou colaborador do espaço de trabalho e leitor de recursos da Application Insights para realizar esta operação.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Pode recuperar uma lista de aplicações utilizando o seguinte script PowerShell que invoca uma chamada REST API. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Este script requer um símbolo de autenticação ao portador para autenticação contra o Diretório Ativo Azure. Uma forma de recuperar este símbolo é usar um artigo no site de [documentação REST API](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Clique em **Experimentá-lo** e iniciar sessão na subscrição do Azure. Pode copiar o token do portador a partir da **Pré-Visualização** de Pedido, como mostrado na imagem seguinte.


![Símbolo do portador](media/app-insights-connector/bearer-token.png)


Também pode recuperar uma lista de aplicações que utilizam uma consulta de registo:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Passos seguintes

- Utilize a Pesquisa de [Registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar informações detalhadas para as suas aplicações De insights de aplicação.
