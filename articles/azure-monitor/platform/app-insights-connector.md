---
title: Ver dados da aplicação Insights de Aplicação Azure Microsoft Docs
description: Pode utilizar a solução de Conector de Insights de Aplicação para diagnosticar problemas de desempenho e entender o que os utilizadores fazem com a sua aplicação quando monitorizados com insights de aplicação.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665159"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Soluçõe de gestão de conector de insights de aplicação (Preprecated)

![Símbolo de insights de aplicação](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o apoio de [consultas de recursos cruzados,](../../azure-monitor/log-query/cross-workspace-query.md)a solução de gestão do Conector de Insights de Aplicação já não é necessária. Foi depreciado e removido do Azure Marketplace, juntamente com o portal OMS que foi oficialmente depreciado a 15 de janeiro de 2019 para a nuvem comercial azure. Será retirado a 30 de março de 2019 para a nuvem do Governo dos EUA.
>
>As ligações existentes continuarão a funcionar até 30 de junho de 2019.  Com a depreciação do portal OMS, não há forma de configurar e remover as ligações existentes do portal. Consulte [A remoção do conector com o PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script sobre a utilização do PowerShell para remover as ligações existentes.
>
>Para obter orientações sobre consulta de dados de registo de insights de aplicação para várias aplicações, consulte Unificar vários recursos de Insights de [Aplicação do Monitor Azure](../log-query/unify-app-resource-data.md). Para obter mais informações sobre a deprecação do portal OMS, consulte o [portal OMS movendo-se para o Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

A solução ApplicationInsights Insights Connector ajuda-o a diagnosticar problemas de desempenho e a compreender o que os utilizadores fazem com a sua aplicação quando é monitorizada com insights de [aplicação](../../azure-monitor/app/app-insights-overview.md). As opiniões da mesma telemetria de aplicações que os desenvolvedores vêem em Application Insights estão disponíveis no Log Analytics. No entanto, quando integra as suas aplicações De Insights de Aplicação com o Log Analytics, a visibilidade das suas aplicações é aumentada por ter dados de operação e aplicação num só local. Ter as mesmas opiniões ajuda-o a colaborar com os desenvolvedores das suas aplicações. Os pontos de vista comuns podem ajudar a reduzir o tempo para detetar e resolver problemas de aplicação e plataforma.

Quando utilizar a solução, pode:

- Veja todas as suas aplicações Application Insights num só local, mesmo quando estão em diferentes subscrições do Azure
- Correlacionar dados de infraestruturas com dados de aplicações
- Visualizar dados de aplicações com perspetivas de pesquisa de registo
- Pivô dos dados do Log Analytics para a sua aplicação Application Insights no portal Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções de Log Analytics, os dados não são recolhidos para o Conector de Insights de Aplicação por agentes. Todos os dados utilizados pela solução provêm diretamente do Azure.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não recolhe informações de agentes linux. |
| [Grupo de gestão scom](../../azure-monitor/platform/om-agents.md) | Não | A solução não recolhe informação de agentes de um grupo de gestão SCOM conectado. |
| [Conta de armazenamento azure](collect-azure-metrics-logs.md) | Não | A solução não recolectiza informações do armazenamento azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder à informação do Conector insights de aplicação, deve ter uma subscrição Azure
- Deve ter pelo menos um recurso configurado para insights de aplicação.
- Deve ser o proprietário ou colaborador do recurso Application Insights.

## <a name="configuration"></a>Configuração

1. Ative a solução Azure Web Apps Analytics do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) ou utilizando o processo descrito nas [soluções Add Log Analytics da Galeria Solutions](../../azure-monitor/insights/solutions.md).
2. Navegue pelo [portal Azure.](https://portal.azure.com) Selecione **todos os serviços** para abrir insights de aplicação. Em seguida, procure insights de aplicação. 
3. Em **Subscrições**, selecione uma subscrição que tenha recursos de Insights de Aplicação e, em seguida, em **Nome,** selecione uma ou mais aplicações.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, os dados ficam disponíveis e o azulejo De insights de aplicação é atualizado com dados, como a seguinte imagem:

![Telha insights de aplicação](./media/app-insights-connector/app-insights-tile.png)

Outros pontos a ter em mente:

- Só é possível ligar aplicações de Apps Insights a um espaço de trabalho de Log Analytics.
- Só pode ligar [os recursos de Insights de Aplicação Básica ou Empresarial](https://azure.microsoft.com/pricing/details/application-insights) ao Log Analytics. No entanto, pode utilizar o nível livre de Log Analytics.

## <a name="management-packs"></a>Pacotes de gestão

Esta solução não instala quaisquer pacotes de gestão em grupos de gestão conectados.

## <a name="use-the-solution"></a>Use a solução

As seguintes secções descrevem como pode utilizar as lâminas mostradas no painel de insights de aplicação para visualizar e interagir com dados das suas apps.

### <a name="view-application-insights-connector-information"></a>Ver informações sobre conector de insights de aplicação

Clique no azulejo Deinsights de **Aplicação** para abrir o painel de insights de **aplicação** para ver as seguintes lâminas.

![Dashboard do Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Dashboard do Application Insights](./media/app-insights-connector/app-insights-dash02.png)

O painel inclui as lâminas mostradas na mesa. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registo que retorna todos os registos quando clicar Em **Ver tudo** na parte inferior da lâmina ou quando clicar no cabeçalho da lâmina.


| **Coluna** | **Descrição** |
| --- | --- |
| Candidaturas - Número de candidaturas | Mostra o número de candidaturas nos recursos da Aplicação. Também lista os nomes das candidaturas e para cada um, a contagem dos registos de candidatura. Clique no número para executar uma pesquisa de registo<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique num nome de aplicação para executar uma pesquisa de registo para a aplicação que mostra registos de aplicação por anfitrião, registos por tipo de telemetria, e todos os dados por tipo (com base no último dia). |
| Volume de Dados – Anfitriões enviam dados | Mostra o número de anfitriões de computador que estão a enviar dados. Também lista os anfitriões de computador e a contagem de registos para cada anfitrião. Clique no número para executar uma pesquisa de registo<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique num nome de computador para executar uma pesquisa de registo para o anfitrião que mostra registos de aplicação por anfitrião, registos por tipo de telemetria, e todos os dados por tipo (com base no último dia). |
| Disponibilidade – Resultados do Webtest | Mostra um gráfico de donut para resultados de testes web, indicando passe ou falha. Clique na tabela para executar uma pesquisa de registo<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de passes e falhas para todos os testes. Mostra todas as Aplicações Web com tráfego de última hora. Clique num nome de aplicação para visualizar uma pesquisa de registo mostrando detalhes de testes web falhados. |
| Pedidos de servidores – Pedidos por hora | Mostra um gráfico de linha dos pedidos do servidor por hora para várias aplicações. Pairar sobre uma linha na tabela para ver as 3 aplicações top 3 recebendo pedidos por um ponto no tempo. Mostra ainda uma lista dos pedidos que recebem pedidos e o número de pedidos para o período selecionado. <br><br>Clique no gráfico para executar <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> uma pesquisa de registo que mostra um gráfico de linha mais detalhado dos pedidos do servidor por hora para várias aplicações. <br><br> Clique numa aplicação na lista para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> executar uma pesquisa de registo que mostra uma lista de pedidos, gráficos para pedidos ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido.   |
| Falhas – Pedidos falhados por hora | Mostra um gráfico de linha de pedidos de pedido falhados por hora. Passe por cima da tabela para ver as 3 melhores aplicações com pedidos falhados para um ponto no tempo. Também mostra uma lista de pedidos com o número de pedidos falhados para cada um. Clique na tabela para executar <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> uma pesquisa de registo que mostra um gráfico de linha mais detalhado de pedidos de aplicação falhados. <br><br>Clique num item na lista para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> executar uma pesquisa de registo que mostra pedidos falhados, gráficos para pedidos falhados ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido falhados. |
| Exceções – Exceções por hora | Mostra um gráfico de linhas de exceções por hora. Passe por cima da tabela para ver as 3 melhores aplicações com exceções por um ponto no tempo. Também mostra uma lista de candidaturas com o número de exceções para cada um. Clique na tabela para fazer <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> uma pesquisa de registo que mostre um gráfico de ligação mais detalhado de exceções. <br><br>Clique num item na lista para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> fazer uma pesquisa de registo que mostre uma lista de exceções, gráficos para exceções ao longo do tempo e pedidos falhados, e uma lista de tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Ver a perspetiva insights da aplicação com pesquisa de registo

Quando clica em qualquer item no painel de instrumentos, vê uma perspetiva de Insights de Aplicação mostrada em pesquisa. A perspetiva proporciona uma visualização alargada, baseada no tipo de telemetria que selecionou. Assim, o conteúdo de visualização muda para diferentes tipos de telemetria.

Quando clica em qualquer lugar da lâmina aplicações, vê a perspetiva de **Aplicações predefinidas.**

![Perspetiva de aplicações de insights de aplicação](./media/app-insights-connector/applications-blade-drill-search.png)

A perspetiva mostra uma visão geral da aplicação que selecionou.

A lâmina **de disponibilidade** mostra uma visão de perspetiva diferente onde pode ver os resultados dos testes web e pedidos falhados relacionados.

![Perspetiva de disponibilidade de insights de aplicação](./media/app-insights-connector/availability-blade-drill-search.png)

Quando clica em qualquer lugar das lâminas de Pedidos ou **Falhas** do **Servidor,** os componentes de perspetiva mudam para lhe dar uma visualização relacionada com pedidos.

![Lâmina de falhas de insights de aplicação](./media/app-insights-connector/server-requests-failures-drill-search.png)

Quando clica em qualquer lugar da lâmina **exceções,** vê-se uma visualização adaptada a exceções.

![Visão de aplicação Insights Exceções lâmina](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de clicar em algo no dashboard Do **Conector** de Insights de Aplicação, dentro da própria página **de Pesquisa,** qualquer consulta que devolveos dados de Insights de Aplicação mostra a perspetiva de Insights de Aplicação. Por exemplo, se estiver a ver dados do Application Insights, uma consulta **&#42;** também mostra o separador de perspetiva como a seguinte imagem:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Os componentes de perspetiva são atualizados dependendo da consulta de pesquisa. Isto significa que pode filtrar os resultados utilizando qualquer campo de pesquisa que lhe dê a capacidade de ver os dados a partir de:

- Todas as suas aplicações
- Uma única aplicação selecionada
- Um grupo de aplicações

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivô para uma app no portal Azure

As lâminas do Conector Insights de Aplicação foram concebidas para permitir que se desloque para a aplicação Deinsights de Aplicação selecionada *quando utilizar o portal Azure*. Pode utilizar a solução como uma plataforma de monitorização de alto nível que o ajuda a resolver uma aplicação. Quando vir um problema potencial em qualquer uma das suas aplicações conectadas, pode perfurar na pesquisa de Log Analytics ou pode orientar-se diretamente para a aplicação Application Insights.

Para girar, clique nas elipses**que**aparecem no final de cada linha, e selecione Open in **Application Insights**.

>[!NOTE]
>**Open in Application Insights** não está disponível no portal Azure.

![Aberto em Insights de Aplicação](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados corrigidos pela amostra

Application Insights fornece *[correção de amostragem](../../azure-monitor/app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Ao ativar a amostragem na sua aplicação Application Insights, obtém um número reduzido de entradas armazenadas tanto em Application Insights como no Log Analytics. Embora a consistência dos dados seja preservada na página e perspetivas do **Conector** de Insights de Aplicação, deve corrigir manualmente os dados amostrados para as suas consultas personalizadas.

Aqui está um exemplo de correção de amostragem numa consulta de pesquisa de registo:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O campo **Contagem Amostrada** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se ligar a amostragem para a sua aplicação Insights, a **Contagem De Amostras** é superior a 1. Para contar o número real de entradas que a sua aplicação gera, soma os campos **de Contagem Amostrada.**

A amostragem afeta apenas o número total de entradas que a sua aplicação gera. Não é necessário corrigir a amostragem para campos métricos como **A Duração** do Pedido ou **a Duração da Disponibilidade** porque esses campos mostram a média para entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de dados de telemetria das suas aplicações connected Application Insights:

- Disponibilidade
- Exceções
- Pedidos
- Visualizações de página – Para que o seu espaço de trabalho receba visualizações de página, tem de configurar as suas aplicações para recolher essa informação. Para mais informações, consulte [pageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – Para que o seu espaço de trabalho receba eventos personalizados, tem de configurar as suas apps para recolher essa informação. Para mais informações, consulte [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Os dados são recebidos pelo Log Analytics a partir de Insights de Aplicação à medida que se torna disponível.

## <a name="output-data"></a>Dados de saída

É criado um registo com um *tipo* de *ApplicationInsights* para cada tipo de dados de entrada. Os registos applicationInsights têm propriedades mostradas nas seguintes secções:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Hora do registo |
| ApplicationID | Chave de instrumentação da app Application Insights |
| ApplicationName | Nome da aplicação Insights de Aplicação |
| RoleInstance | ID do anfitrião do servidor |
| DeviceType | Dispositivo cliente |
| Resolução de ecrã |   |
| Continente | Continente onde o pedido teve origem |
| País | País/região onde o pedido teve origem |
| Província | Província, estado ou local onde o pedido teve origem |
| Localidade | Cidade ou cidade onde o pedido teve origem |
| isSynthetic | Indica se o pedido foi criado por um utilizador ou por método automatizado. Verdadeiro = método automatizado ou falso = utilizador gerado |
| Taxa de amostragem | Percentagem de telemetria gerada pelo SDK que é enviada para o portal. Intervalo 0.0-100.0. |
| Contagem de amostras | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| É Autenticado | Verdadeiro ou falso |
| OperaçãoID | Itens que possuem o mesmo ID de operação são mostrados como Itens Relacionados no portal. Normalmente a identificação do pedido |
| ParentOperationID | Identificação da operação dos pais |
| OperationName |   |
| SessãoId | GUID para identificar exclusivamente a sessão onde o pedido foi criado |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos específicos de disponibilidade

| Propriedade | Descrição |
| --- | --- |
| TelemetriaType | Disponibilidade |
| Nome de Teste de Disponibilidade | Nome do teste web |
| DisponibilidadeRunLocation | Fonte geográfica do pedido http |
| DisponibilidadeResult | Indica o resultado do sucesso do teste web |
| Mensagem de disponibilidade | A mensagem anexa ao teste web |
| Contagem de disponibilidades | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| Valor métrico datasizemétrico | 1.0 ou 0.0 |
| DataSizeMetricCount | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| DisponibilidadeDura | Tempo, em milissegundos, da duração do teste web |
| DisponibilidadeDuraCount | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| DisponibilidadeValor |   |
| Contagem métrica de disponibilidade |   |
| DisponibilidadeTestId | GUID único para o teste web |
| DisponibilidadeTimestamp | Carimbo de tempo preciso do teste de disponibilidade |
| DisponibilidadeDuraMin | Para os registos amostrados, este campo mostra a duração mínima do teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeDurationMax | Para os registos amostrados, este campo mostra a duração máxima do teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeDuraStdDev | Para os registos amostrados, este campo mostra o desvio padrão entre todas as durações do teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeMin |   |
| DisponibilidadeMax |   |
| DisponibilidadeStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos específicos de exceção

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetriaType | Exceção |
| Tipo de exceção | Tipo da exceção |
| Método de Exceção | O método que cria a exceção |
| Exceção Assembleia | A montagem inclui o quadro e a versão, bem como a chave pública |
| Grupo de Exceções | Tipo da exceção |
| ExcepçõesHandledAt | Indica o nível que tratou da exceção |
| ExcepçõesContagem | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| Mensagem de exceção | Mensagem da exceção |
| ExcepçãoStack | Pilha completa da exceção |
| ExcepçõesHasStack | É verdade, se a exceção tem uma pilha |



### <a name="request-specific-fields"></a>Campos específicos de pedido

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| TelemetriaType | Pedir |
| Código de Resposta | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica sucesso ou fracasso. True ou false. |
| RequestID | ID para identificar exclusivamente o pedido |
| Nome de pedido | GET/POST + BASE URL |
| Duração do pedido | Tempo, em segundos, da duração do pedido |
| do IdP | URL do pedido sem incluir o anfitrião |
| Anfitrião | Anfitrião de servidor web |
| URLBase | URL completo do pedido |
| ApplicationProtocol | Tipo de protocolo utilizado pela aplicação |
| Contagem de Pedidos | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| Contagem de Pedidos | 100/(Taxa de amostragem). Por exemplo,&gt; 4 = 25% |
| Pedido DuraçãoMin | Para os registos amostrados, este campo mostra a duração mínima do pedido (milissegundos) para os pontos de dados representados. |
| PedidoDurationMax | Para os registos amostrados, este campo mostra a duração máxima do pedido (milissegundos) para os pontos de dados representados |
| PedidoDuraçãoDev | Para os registos amostrados, este campo mostra o desvio padrão entre todas as durações do pedido (milissegundos) para os pontos de dados representados |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

Esta solução não dispõe de um conjunto de pesquisas de registo de amostras mostradas no painel de instrumentos. No entanto, as consultas de pesquisa de registo de amostras com descrições são mostradas na secção de informação do [Conector de](#view-application-insights-connector-information) Insights de Aplicação view.

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
