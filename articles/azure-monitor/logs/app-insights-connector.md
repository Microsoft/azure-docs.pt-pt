---
title: Ver dados da aplicação Azure Application Insights | Microsoft Docs
description: Pode utilizar a solução Application Insights Connector para diagnosticar problemas de desempenho e compreender o que os utilizadores fazem com a sua aplicação quando monitorizados com o Application Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c21ed461280dfa617c852fe6c18c30e5d697b704
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031281"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Solução de gestão de Conector de Insights de Aplicação (Deprecada)

![Símbolo de Insights de Aplicação](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o apoio de [consultas de recursos cruzados,](../logs/cross-workspace-query.md)a solução de gestão do Connector Application Insights deixou de ser necessária. Foi depreciado e removido do Azure Marketplace, juntamente com o portal OMS que foi oficialmente depreciado a 15 de janeiro de 2019 para a nuvem comercial Azure. Será retirado a 30 de março de 2019 para a nuvem do Governo dos EUA.
>
>As ligações existentes continuarão a funcionar até 30 de junho de 2019.  Com a depreciação do portal OMS, não existe forma de configurar e remover as ligações existentes do portal. Consulte [remover o conector com PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script sobre a utilização do PowerShell para remover as ligações existentes.
>
>Para obter orientações sobre a consulta de dados de registo de insights de aplicações para várias aplicações, consulte [os múltiplos recursos de Insights de Aplicação do Monitor Azure](./unify-app-resource-data.md). Para obter mais informações sobre a depreciação do portal OMS, consulte [o portal OMS movendo-se para Azure](./oms-portal-transition.md).
>
> 

A solução Applications Insights Connector ajuda-o a diagnosticar problemas de desempenho e a compreender o que os utilizadores fazem com a sua aplicação quando é monitorizada com [o Application Insights](../app/app-insights-overview.md). As opiniões sobre a mesma telemetria de aplicação que os desenvolvedores vêem no Application Insights estão disponíveis no Log Analytics. No entanto, quando integra as suas aplicações Application Insights com o Log Analytics, a visibilidade das suas aplicações é aumentada através da operação e dados da aplicação num só local. Ter as mesmas opiniões ajuda-o a colaborar com os seus desenvolvedores de aplicações. As opiniões comuns podem ajudar a reduzir o tempo para detetar e resolver problemas de aplicação e plataforma.

Quando se utiliza a solução, pode:

- Veja todas as suas aplicações Application Insights num só local, mesmo quando estão em diferentes subscrições do Azure
- Correlacionar os dados da infraestrutura com os dados da aplicação
- Visualizar dados de aplicações com perspetivas na pesquisa de registos
- Pivô dos dados do Log Analytics para a sua aplicação Application Insights no portal Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções de Log Analytics, os dados não são recolhidos para o Conector de Insights de Aplicação por agentes. Todos os dados utilizados pela solução provêm diretamente do Azure.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](./../agents/agent-windows.md) | Não | A solução não recolhe informações de agentes do Windows. |
| [Agentes do Linux](../vm/quick-collect-linux-computer.md) | Não | A solução não recolhe informações dos agentes linux. |
| [Grupo de gestão SCOM](../agents/om-agents.md) | Não | A solução não recolhe informações de agentes de um grupo de gestão SCOM ligado. |
| [Conta de armazenamento Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | Não | A solução não ressoou informações do armazenamento da Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para aceder às informações do Conector de Insights de Aplicações, tem de ter uma subscrição do Azure
- Deve ter pelo menos um recurso configurado application insights.
- Deve ser o proprietário ou colaborador do recurso Application Insights.

## <a name="configuration"></a>Configuração

1. Ativar a solução Azure Web Apps Analytics a partir do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps) ou utilizando o processo descrito nas [soluções Add Log Analytics da Galeria de Soluções](../insights/solutions.md).
2. Navegue pelo [portal Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir Insights de Aplicação. Em seguida, procure por Insights de Aplicação. 
3. Em **Subscrições**, selecione uma subscrição que tenha recursos de Insights de Aplicação e, em seguida, em **Nome**, selecione uma ou mais aplicações.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, os dados ficam disponíveis e o azulejo application Insights é atualizado com dados, como a seguinte imagem:

![Azulejos de aplicação](./media/app-insights-connector/app-insights-tile.png)

Outros pontos a ter em mente:

- Só é possível ligar as aplicações Application Insights a um espaço de trabalho do Log Analytics.
- Só é possível ligar recursos básicos ou de [insights de aplicação](https://azure.microsoft.com/pricing/details/application-insights) da empresa ao Log Analytics. No entanto, pode utilizar o nível gratuito de Log Analytics.

## <a name="management-packs"></a>Pacotes de gestão

Esta solução não instala pacotes de gestão em grupos de gestão ligados.

## <a name="use-the-solution"></a>Use a solução

As secções seguintes descrevem como pode utilizar as lâminas mostradas no painel de insights de aplicação para visualizar e interagir com os dados das suas apps.

### <a name="view-application-insights-connector-information"></a>Ver informações do Conector de Insights de Aplicações

Clique no azulejo **'Insights de Aplicação'** para abrir o painel **'Insights de Aplicação'** para ver as seguintes lâminas.

![Screenshot do painel de insights de aplicação mostrando as lâminas para aplicações, volume de dados e disponibilidade.](./media/app-insights-connector/app-insights-dash01.png)

![Screenshot do painel de insights de aplicação mostrando as lâminas para pedidos de servidor, falhas e exceções.](./media/app-insights-connector/app-insights-dash02.png)

O painel inclui as lâminas mostradas na tabela. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Pode executar uma pesquisa de registo que retorna todos os registos quando clicar Em **Ver tudo** na parte inferior da lâmina ou quando clicar no cabeçalho da lâmina.


| **Coluna** | **Descrição** |
| --- | --- |
| Candidaturas - Número de candidaturas | Mostra o número de candidaturas nos recursos de aplicação. Também lista os nomes das candidaturas e, para cada um, a contagem dos registos de candidaturas. Clique no número para executar uma pesquisa de registo <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique num nome de aplicação para executar uma pesquisa de registo para a aplicação que mostra registos de aplicações por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de Dados – Anfitriões que enviam dados | Mostra o número de anfitriões de computador que estão a enviar dados. Também lista anfitriões de computador e contagem de registos para cada anfitrião. Clique no número para executar uma pesquisa de registo <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique num nome de computador para executar uma pesquisa de registo para o anfitrião que mostra registos de aplicações por anfitrião, registos por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – Resultados do Webtest | Mostra um gráfico de donuts para resultados de testes web, indicando passe ou falha. Clique na tabela para executar uma pesquisa de registo <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de passes e falhas para todos os testes. Mostra todas as Aplicações Web com tráfego para o último minuto. Clique num nome de aplicação para ver uma pesquisa de registo mostrando detalhes de testes web falhados. |
| Pedidos de Servidor – Pedidos por hora | Mostra um gráfico de linha dos pedidos do servidor por hora para várias aplicações. Passe por cima de uma linha na tabela para ver as 3 principais aplicações que recebem pedidos por um ponto no tempo. Mostra ainda uma lista das candidaturas que recebem pedidos e o número de pedidos para o período selecionado. <br><br>Clique no gráfico para executar uma pesquisa de registo <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linha mais detalhado dos pedidos do servidor por hora para várias aplicações. <br><br> Clique numa aplicação na lista para executar uma pesquisa de registo <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostre uma lista de pedidos, gráficos de pedidos ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido.   |
| Falhas – Pedidos falhados por hora | Mostra um gráfico de linha de pedidos falhados por hora. Passe por cima da tabela para ver as 3 principais aplicações com pedidos falhados por um ponto no tempo. Também mostra uma lista de candidaturas com o número de pedidos falhados para cada um. Clique na tabela para executar uma pesquisa de registo <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linha mais detalhado de pedidos de aplicação falhados. <br><br>Clique num item da lista para executar uma pesquisa de registo para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>  que mostre pedidos falhados, gráficos para pedidos falhados ao longo do tempo e duração do pedido e uma lista de códigos de resposta de pedido falhados. |
| Exceções - Exceções por hora | Mostra um gráfico de exceções por hora. Passe por cima da tabela para ver as 3 principais aplicações com exceções por um ponto no tempo. Também mostra uma lista de candidaturas com o número de exceções para cada um. Clique na tabela para executar uma pesquisa de registo <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de link mais detalhado de exceções. <br><br>Clique num item da lista para executar uma pesquisa de registo <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> que mostre uma lista de exceções, gráficos para exceções ao longo do tempo e pedidos falhados, e uma lista de tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Ver a perspetiva de Insights de Aplicação com pesquisa de registo

Quando clica em qualquer item no painel de instrumentos, vê uma perspetiva de Insights de Aplicação mostrada na pesquisa. A perspetiva proporciona uma visualização alargada, com base no tipo de telemetria que selecionou. Assim, o conteúdo de visualização muda para diferentes tipos de telemetria.

Quando clica em qualquer lugar da lâmina aplicações, vê a perspetiva padrão **das Aplicações.**

![Perspetiva de aplicações insights](./media/app-insights-connector/applications-blade-drill-search.png)

A perspetiva mostra uma visão geral da aplicação que selecionou.

A lâmina **Disponibilidade** mostra uma visão de perspetiva diferente onde você pode ver os resultados dos testes web e pedidos falhados relacionados.

![Perspetiva de disponibilidade de insights de aplicação](./media/app-insights-connector/availability-blade-drill-search.png)

Quando clica em qualquer lugar das **lâminas de Pedidos** ou **Falhas** do Servidor, os componentes da perspetiva mudam para lhe dar uma visualização relacionada com pedidos.

![Falha de insights de aplicação](./media/app-insights-connector/server-requests-failures-drill-search.png)

Quando clica em qualquer lugar da lâmina **das Exceções,** vê-se uma visualização adaptada às exceções.

![Lâmina de exceções de insights de aplicação](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de clicar em algo do dashboard **do Application Insights Connector,** dentro da própria página **De Pesquisa,** qualquer consulta que devolva os dados do Application Insights mostra a perspetiva de Insights de Aplicação. Por exemplo, se estiver a visualizar os dados do Application Insights, uma consulta **&#42;** também mostra o separador perspetiva como a seguinte imagem:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Os componentes da perspetiva são atualizados dependendo da consulta de pesquisa. Isto significa que pode filtrar os resultados utilizando qualquer campo de pesquisa que lhe dê a capacidade de ver os dados a partir de:

- Todas as suas aplicações
- Uma única aplicação selecionada
- Um grupo de aplicações

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Pivô para uma app no portal Azure

As lâminas do Conector De Insights de Aplicação foram concebidas para que possa orientar-se para a aplicação de Insights de Aplicação selecionada *quando utilizar o portal Azure*. Pode utilizar a solução como uma plataforma de monitorização de alto nível que o ajuda a resolver problemas numa aplicação. Quando vir um problema potencial em qualquer uma das suas aplicações conectadas, pode perfurar a bola na pesquisa do Log Analytics ou pode orientar-se diretamente para a aplicação Application Insights.

Para pivô, clique nas elipses **(...**) que aparecem no final de cada linha e selecione **Abrir em Insights de Aplicação**.

>[!NOTE]
>**Aberto em Informações de Aplicação** não está disponível no portal Azure.

![Aberto em Insights de Aplicações](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados corrigidos por amostras

A Application Insights fornece correção de *[amostragem](../app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Quando ativa a amostragem na sua aplicação Application Insights, obtém um número reduzido de entradas armazenadas tanto no Application Insights como no Log Analytics. Embora a consistência dos dados seja preservada na página e perspetivas do **Application Insights Connector,** deve corrigir manualmente dados amostrados para as suas consultas personalizadas.

Aqui está um exemplo de correção de amostragem numa consulta de pesquisa de registo:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O campo **Contagem Amostrada** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se ligar a amostragem para a sua aplicação Application Insights, **a Contagem amostrada** é superior a 1. Para contar o número real de entradas que a sua aplicação gera, resumir os campos **sampled count.**

A amostragem afeta apenas o número total de entradas que a sua aplicação gera. Não é necessário corrigir amostras para campos métricos como **RequestDuration** ou **AvailabilityDuration**  porque esses campos mostram a média de entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de dados de telemetria das suas aplicações de Insights de Aplicação conectadas:

- Disponibilidade
- Exceções
- Pedidos
- Visualizações da página - Para que o seu espaço de trabalho receba visualizações de página, tem de configurar as suas aplicações para recolher essa informação. Para obter mais informações, consulte [PageViews](../app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – Para que o seu espaço de trabalho receba eventos personalizados, tem de configurar as suas aplicações para recolher essa informação. Mais informações, consulte [TrackEvent](../app/api-custom-events-metrics.md#trackevent).

Os dados são recebidos pelo Log Analytics a partir de Application Insights à medida que se torna disponível.

## <a name="output-data"></a>Dados de saída

É criado um registo com um *tipo* de *ApplicationInsights* para cada tipo de dados de entrada. Os registos ApplicationInsights têm propriedades apresentadas nas seguintes secções:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Hora do registo |
| ApplicationID | Chave de instrumentação da app Application Insights |
| ApplicationName | Nome da app Application Insights |
| RoleInstance | ID do anfitrião do servidor |
| DeviceType | Dispositivo de cliente |
| ScreenResolution |   |
| Continente | Continente onde o pedido teve origem |
| País | País/região de origem do pedido |
| Província | Província, estado ou localidade de onde o pedido teve origem |
| City | Cidade ou cidade onde o pedido teve origem |
| isSynthetic | Indica se o pedido foi criado por um utilizador ou por método automatizado. Verdadeiro = método automatizado ou falso = utilizador gerado |
| AmostragemRate | Percentagem de telemetria gerada pelo SDK que é enviado para o portal. Intervalo 0.0-100.0. |
| Contas amostradas | 100/(SamplingRate). Por exemplo, 4 = &gt; 25% |
| IsAuthenticated | Verdadeiro ou falso |
| OperaçãoID | Os itens que têm o mesmo ID de operação são apresentados como Itens Relacionados no portal. Normalmente o ID do pedido |
| ParentOperationID | ID da operação dos pais |
| OperationName |   |
| SessionId | GUID para identificar exclusivamente a sessão onde o pedido foi criado |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos específicos da disponibilidade

| Propriedade | Descrição |
| --- | --- |
| TelemetriaType | Disponibilidade |
| DisponibilidadeTestName | Nome do teste web |
| DisponibilidadeRunLocation | Fonte geográfica do pedido http |
| DisponibilidadeResult | Indica o resultado do sucesso do teste web |
| DisponibilidadeSSessage | A mensagem anexada ao teste web |
| DisponibilidadeContando | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| DataSizeMetricValue | 1.0 ou 0.0 |
| ContaMétrica de Tamanho Dados | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| DisponibilidadeDuração | Tempo, em milissegundos, da duração do teste web |
| DisponibilidadeSContagem | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| DisponibilidadeValue |   |
| Disponibilidade ContagemMétrica |   |
| DisponibilidadeTestId | GUID exclusivo para o teste web |
| DisponibilidadeTimestamp | Tempo de tempo preciso do teste de disponibilidade |
| DisponibilidadeDurationMin | Para registos amostrados, este campo mostra a duração mínima do teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeDurationMax | Para registos amostrados, este campo mostra a duração máxima do teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeDurationStdDev | Para os registos amostrados, este campo mostra o desvio padrão entre todas as durações de teste web (milissegundos) para os pontos de dados representados |
| DisponibilidadeMin |   |
| DisponibilidadeMax |   |
| DisponibilidadeStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos específicos de exceções

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetriaType | Exceção |
| ExcepçãoType | Tipo de exceção |
| ExcepçãoMethod | O método que cria a exceção |
| ExceçãoAssembly | A montagem inclui o quadro e a versão, bem como o símbolo chave do público |
| Grupo de Exceções | Tipo de exceção |
| ExceçãoHandledAt | Indica o nível que lidou com a exceção |
| ExcepçãoCount | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| Exceção | Mensagem da exceção |
| ExcepçãoStack | Pilha completa da exceção |
| ExcepçãoHasStack | Verdade, se a exceção tem uma pilha |



### <a name="request-specific-fields"></a>Campos específicos do pedido

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| TelemetriaType | Pedir |
| Código de Resposta | Resposta HTTP enviada ao cliente |
| PedidoSSuccess | Indica sucesso ou fracasso. True ou false. |
| RequestID | ID para identificar exclusivamente o pedido |
| Nome de pedido | GET/POST + BASE DE URL |
| PedidoDuração | Tempo, em segundos, da duração do pedido |
| URL | URL do pedido não incluindo o anfitrião |
| Anfitrião | Anfitrião do servidor web |
| URLBase | URL completo do pedido |
| ApplicationProtocol | Tipo de protocolo utilizado pela aplicação |
| PedidoCount | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| PedidoDurationCount | 100/(Taxa de amostragem). Por exemplo, 4 = &gt; 25% |
| PedidoDurationMin | Para os registos amostrados, este campo mostra a duração mínima do pedido (milissegundos) para os pontos de dados representados. |
| PedidoDurationMax | Para os registos amostrados, este campo mostra a duração máxima do pedido (milissegundos) para os pontos de dados representados |
| PedidoDurationStdDev | Para os registos amostrados, este campo mostra o desvio padrão entre todas as durações de pedido (milissegundos) para os pontos de dados representados |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

Esta solução não tem um conjunto de pesquisas de registo de amostras mostradas no painel de instrumentos. No entanto, as consultas de pesquisa de registo de amostras com descrições são mostradas na secção de informações do [Conector de Insights de Aplicação ver.](#view-application-insights-connector-information)

## <a name="removing-the-connector-with-powershell"></a>Remoção do conector com PowerShell
Com a depreciação do portal OMS, não existe forma de configurar e remover as ligações existentes do portal. Pode remover as ligações existentes com o seguinte script PowerShell. Deve ser o proprietário ou colaborador do espaço de trabalho e leitor do recurso Application Insights para realizar esta operação.

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

Pode recuperar uma lista de aplicações utilizando o seguinte script PowerShell que invoca uma chamada de API REST. 

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
Este script requer um símbolo de autenticação ao portador para autenticação contra o Azure Ative Directory. Uma forma de recuperar este token é usar um artigo no [site de documentação da API](/rest/api/loganalytics/datasources/createorupdate)REST. Clique em **Tentar** e inicie sessão na subscrição do Azure. Pode copiar o token do portador da **pré-visualização** do pedido, como mostrado na imagem seguinte.


![Ficha de portador](media/app-insights-connector/bearer-token.png)


Também pode obter uma lista de aplicações que utilizem uma consulta de registo:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Passos seguintes

- Utilize [a Pesquisa de Registo](./log-query-overview.md) para ver informações detalhadas para as suas aplicações Application Insights.