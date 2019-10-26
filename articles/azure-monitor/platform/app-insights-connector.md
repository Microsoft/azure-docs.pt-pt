---
title: Exibir dados de aplicativo do Aplicativo Azure insights | Microsoft Docs
description: Você pode usar a solução Conector do Application Insights para diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando monitorados com Application Insights.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 02/13/2019
ms.openlocfilehash: b956c3bc7d04908db1cc45092cf5926ecfcc305c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932751"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Solução de gerenciamento de Conector do Application Insights (preterida)

![Símbolo de Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Com o suporte a [consultas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md), a solução de gerenciamento de conector do Application insights não é mais necessária. Ele foi preterido e removido do Azure Marketplace, junto com o portal do OMS que foi oficialmente preterido em 15 de janeiro de 2019 para a nuvem comercial do Azure. Ele será desativado em 30 de março de 2019 para a nuvem do governo dos EUA do Azure.
>
>As conexões existentes continuarão a funcionar até 30 de junho de 2019.  Com a substituição do portal do OMS, não há como configurar e remover conexões existentes do Portal. Consulte [removendo o conector com o PowerShell](#removing-the-connector-with-powershell) abaixo para obter um script sobre como usar o PowerShell para remover as conexões existentes.
>
>Para obter orientação sobre como consultar Application Insights dados de log para vários aplicativos, consulte [unificar vários recursos de Application insights de Azure monitor](../log-query/unify-app-resource-data.md). Para obter mais informações sobre a substituição do portal do OMS, consulte [portal do OMS migrando para o Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

A solução de conector do Application insights ajuda você a diagnosticar problemas de desempenho e entender o que os usuários fazem com seu aplicativo quando ele é monitorado com o [Application insights](../../azure-monitor/app/app-insights-overview.md). As exibições da mesma telemetria de aplicativos que os desenvolvedores veem na Application Insights estão disponíveis no Log Analytics. No entanto, quando você integra seus aplicativos Application Insights com o Log Analytics, a visibilidade de seus aplicativos é aumentada com a operação e os dados do aplicativo em um único local. Ter as mesmas exibições ajuda a colaborar com os desenvolvedores de aplicativos. Os modos de exibição comuns podem ajudar a reduzir o tempo para detectar e resolver problemas de aplicativos e plataformas.

Ao usar a solução, você pode:

- Exibir todos os seus aplicativos de Application Insights em um único lugar, mesmo quando eles estiverem em assinaturas diferentes do Azure
- Correlacione dados de infraestrutura com dados de aplicativos
- Visualizar dados de aplicativos com perspectivas na pesquisa de logs
- Dinamizar dados de Log Analytics para seu aplicativo Application Insights no portal do Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Origens ligadas

Ao contrário da maioria das outras soluções Log Analytics, os dados não são coletados para o Conector do Application Insights por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Origem Ligada | Suportadas | Descrição |
| --- | --- | --- |
| [Agentes do Windows](../../azure-monitor/platform/agent-windows.md) | Não | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Não | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do SCOM](../../azure-monitor/platform/om-agents.md) | Não | A solução não coleta informações de agentes em um grupo de gerenciamento do SCOM conectado. |
| [Conta de armazenamento do Azure](collect-azure-metrics-logs.md) | Não | A solução não coleta informações do armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para acessar informações de Conector do Application Insights, você deve ter uma assinatura do Azure
- Você deve ter pelo menos um recurso de Application Insights configurado.
- Você deve ser o proprietário ou colaborador do recurso de Application Insights.

## <a name="configuration"></a>Configuração

1. Habilite a solução de Análise de Aplicativos Web do Azure do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) ou usando o processo descrito em [adicionar soluções de log Analytics do Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Navegue para o [portal do Azure](https://portal.azure.com). Selecione **todos os serviços** para abrir Application insights. Em seguida, pesquise Application Insights. 
3. Em **assinaturas**, selecione uma assinatura que tenha Application insights recursos e, em **nome**, selecione um ou mais aplicativos.
4. Clique em **Guardar**.

Em aproximadamente 30 minutos, os dados ficam disponíveis e o bloco de Application Insights é atualizado com dados, como a imagem a seguir:

![Bloco Application Insights](./media/app-insights-connector/app-insights-tile.png)

Outros pontos para ter em mente:

- Você só pode vincular Application Insights aplicativos a um espaço de trabalho do Log Analytics.
- Você só pode vincular [recursos básicos ou Application insights empresariais](https://azure.microsoft.com/pricing/details/application-insights) a log Analytics. No entanto, você pode usar a camada gratuita de Log Analytics.

## <a name="management-packs"></a>Pacotes de gestão

Essa solução não instala pacotes de gerenciamento em grupos de gerenciamento conectados.

## <a name="use-the-solution"></a>Usar a solução

As seções a seguir descrevem como você pode usar as folhas mostradas no painel Application Insights para exibir e interagir com os dados de seus aplicativos.

### <a name="view-application-insights-connector-information"></a>Exibir informações de Conector do Application Insights

Clique no bloco **Application insights** para abrir o painel de **Application insights** para ver as folhas a seguir.

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Painel do Application Insights](./media/app-insights-connector/app-insights-dash02.png)

O painel inclui as folhas mostradas na tabela. Cada painel apresenta uma lista com um máximo de 10 itens que correspondem aos critérios do âmbito e do intervalo de tempo especificados. Você pode executar uma pesquisa de logs que retorna todos os registros ao clicar em **Ver todos** na parte inferior da folha ou ao clicar no cabeçalho da folha.


| **Pilha** | **Descrição** |
| --- | --- |
| Aplicativos-número de aplicativos | Mostra o número de aplicativos nos recursos do aplicativo. Também lista os nomes de aplicativos e, para cada um, a contagem de registros de aplicativos. Clique no número para executar uma pesquisa de logs para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Clique em um nome de aplicativo para executar uma pesquisa de log para o aplicativo que mostra os registros de aplicativo por host, os registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Volume de dados – hosts enviando dados | Mostra o número de hosts de computador que estão enviando dados. Também lista os hosts de computador e a contagem de registros para cada host. Clique no número para executar uma pesquisa de logs para <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Clique em um nome de computador para executar uma pesquisa de logs para o host que mostra os registros de aplicativo por host, os registros por tipo de telemetria e todos os dados por tipo (com base no último dia). |
| Disponibilidade – resultados do WebTest | Mostra um gráfico de rosca para resultados de teste na Web, indicando aprovação ou falha. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Os resultados mostram o número de passagens e falhas de todos os testes. Ele mostra todos os aplicativos Web com tráfego para o último minuto. Clique em um nome de aplicativo para exibir uma pesquisa de log mostrando detalhes dos testes da Web com falha. |
| Solicitações do servidor – solicitações por hora | Mostra um gráfico de linhas das solicitações do servidor por hora para vários aplicativos. Passe o mouse sobre uma linha no gráfico para ver os três principais aplicativos que recebem solicitações para um ponto no tempo. Também mostra uma lista de solicitações de recebimento de aplicativos e o número de solicitações para o período selecionado. <br><br>Clique no grafo para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linhas mais detalhado das solicitações de servidor por hora para vários aplicativos. <br><br> Clique em um aplicativo na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra uma lista de solicitações, gráficos para solicitações ao longo do tempo e duração da solicitação e uma lista de códigos de resposta de solicitação.   |
| Falhas – solicitações com falha por hora | Mostra um gráfico de linhas de solicitações de aplicativos com falha por hora. Passe o mouse sobre o gráfico para ver os três principais aplicativos com solicitações com falha para um ponto no tempo. Também mostra uma lista de aplicativos com o número de solicitações com falha para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de linhas mais detalhado das solicitações de aplicativos com falha. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> que mostra solicitações com falha, gráficos para solicitações com falha ao longo do tempo e duração da solicitação e uma lista de códigos de resposta de solicitação com falha. |
| Exceções – exceções por hora | Mostra um gráfico de linhas de exceções por hora. Passe o mouse sobre o gráfico para ver os três principais aplicativos com exceções para um ponto no tempo. Também mostra uma lista de aplicativos com o número de exceções para cada um. Clique no gráfico para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> que mostra um gráfico de link mais detalhado de exceções. <br><br>Clique em um item na lista para executar uma pesquisa de logs para <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> que mostra uma lista de exceções, gráficos para exceções ao longo do tempo e solicitações com falha, e uma lista de tipos de exceção.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Exibir a perspectiva de Application Insights com a pesquisa de logs

Ao clicar em qualquer item no painel, você verá uma perspectiva Application Insights mostrada na pesquisa. A perspectiva fornece uma visualização estendida, com base no tipo de telemetria selecionado. Portanto, o conteúdo de visualização muda para diferentes tipos de telemetria.

Ao clicar em qualquer lugar na folha aplicativos, você verá a perspectiva de **aplicativos** padrão.

![Perspectiva de aplicativos Application Insights](./media/app-insights-connector/applications-blade-drill-search.png)

A perspectiva mostra uma visão geral do aplicativo que você selecionou.

A folha **disponibilidade** mostra uma exibição de perspectiva diferente, em que você pode ver resultados de teste na Web e solicitações com falha relacionadas.

![Perspectiva de disponibilidade de Application Insights](./media/app-insights-connector/availability-blade-drill-search.png)

Quando você clica em qualquer lugar nas folhas de solicitações ou **falhas** do **servidor** , os componentes de perspectiva são alterados para fornecer uma visualização relacionada a solicitações.

![Folha falhas de Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Ao clicar em qualquer lugar na folha **exceções** , você verá uma visualização adaptada às exceções.

![Application Insights folha de exceções](./media/app-insights-connector/exceptions-blade-drill-search.png)

Independentemente de você clicar em algo de **conector do Application insights** painel, na própria página de **pesquisa** , qualquer consulta que retorna Application insights dados mostra a perspectiva Application insights. Por exemplo, se você estiver exibindo dados de Application Insights **&#42;** , uma consulta também mostrará a guia perspectiva como a imagem a seguir:

![Estatísticas das Aplicações](./media/app-insights-connector/app-insights-search.png)

Os componentes de perspectiva são atualizados dependendo da consulta de pesquisa. Isso significa que você pode filtrar os resultados usando qualquer campo de pesquisa que ofereça a capacidade de ver os dados de:

- Todos os seus aplicativos
- Um único aplicativo selecionado
- Um grupo de aplicativos

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Dinamizar para um aplicativo na portal do Azure

Conector do Application Insights as folhas são projetadas para permitir que você dinamizar para o aplicativo de Application Insights selecionado *ao usar o portal do Azure*. Você pode usar a solução como uma plataforma de monitoramento de alto nível que ajuda a solucionar problemas de um aplicativo. Quando você vir um possível problema em qualquer um dos aplicativos conectados, poderá fazer uma busca detalhada na pesquisa Log Analytics ou poderá dinamizar diretamente para o aplicativo Application Insights.

Para dinamizar, clique nas reticências ( **...** ) que aparece no final de cada linha e selecione **abrir no Application insights**.

>[!NOTE]
>**Abrir no Application insights** não está disponível no portal do Azure.

![Abrir no Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dados corrigidos por exemplo

Application Insights fornece *[correção de amostragem](../../azure-monitor/app/sampling.md)* para ajudar a reduzir o tráfego de telemetria. Ao habilitar a amostragem em seu aplicativo Application Insights, você obtém um número reduzido de entradas armazenadas no Application Insights e no Log Analytics. Embora a consistência dos dados seja preservada na página **conector do Application insights** e perspectivas, você deve corrigir manualmente os dados de amostra para suas consultas personalizadas.

Aqui está um exemplo de correção de amostragem em uma consulta de pesquisa de log:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

O campo **contagem de amostra** está presente em todas as entradas e mostra o número de pontos de dados que a entrada representa. Se você ativar a amostragem para seu aplicativo Application Insights, a **contagem amostrada** será maior que 1. Para contar o número real de entradas que seu aplicativo gera, some os campos de **contagem de amostra** .

A amostragem afeta apenas o número total de entradas que seu aplicativo gera. Você não precisa corrigir a amostragem para campos de métrica como **RequestDuration** ou **AvailabilityDuration** , pois esses campos mostram a média de entradas representadas.

## <a name="input-data"></a>Dados de entrada

A solução recebe os seguintes tipos de telemetria de dados dos seus aplicativos Application Insights conectados:

- Disponibilidade
- Exceções
- Pedidos
- Exibições de página – para que seu espaço de trabalho receba exibições de página, você deve configurar seus aplicativos para coletar essas informações. Para obter mais informações, consulte [pageviews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Eventos personalizados – para que seu espaço de trabalho receba eventos personalizados, você deve configurar seus aplicativos para coletar essas informações. Para obter mais informações, consulte [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Os dados são recebidos por Log Analytics do Application Insights à medida que se tornam disponíveis.

## <a name="output-data"></a>Dados de saída

Um registro com um *tipo* de *ApplicationInsights* é criado para cada tipo de dados de entrada. Os registros ApplicationInsights têm propriedades mostradas nas seções a seguir:

### <a name="generic-fields"></a>Campos genéricos

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| clientIP |   |
| TimeGenerated | Hora do registo |
| applicationId | Chave de instrumentação do aplicativo Application Insights |
| applicationName | Nome do aplicativo de Application Insights |
| RoleInstance | ID do host do servidor |
| deviceType | Dispositivo do cliente |
| ScreenResolution |   |
| Continente | Continente em que a solicitação foi originada |
| País | País/região em que a solicitação foi originada |
| Província | Província, estado ou localidade em que a solicitação foi originada |
| Cidade | Cidade ou município em que a solicitação foi originada |
| issintética | Indica se a solicitação foi criada por um usuário ou por método automatizado. True = método automatizado ou false = gerado pelo usuário |
| SamplingRate | Porcentagem de telemetria gerada pelo SDK que é enviado ao Portal. Intervalo 0,0-100.0. |
| SampledCount | 100/(SamplingRate). Por exemplo, 4 =&gt; 25% |
| IsAuthenticated | verdadeiro ou falso |
| OperationID | Os itens que têm a mesma ID de operação são mostrados como itens relacionados no Portal. Geralmente a ID da solicitação |
| ParentOperationID | ID da operação pai |
| OperationName |   |
| sessionId | GUID para identificar exclusivamente a sessão em que a solicitação foi criada |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campos específicos de disponibilidade

| Propriedade | Descrição |
| --- | --- |
| Telemetria | Disponibilidade |
| AvailabilityTestName | Nome do teste na Web |
| AvailabilityRunLocation | Fonte geográfica de solicitação http |
| AvailabilityResult | Indica o resultado de êxito do teste na Web |
| AvailabilityMessage | A mensagem anexada ao teste na Web |
| AvailabilityCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| DataSizeMetricValue | 1,0 ou 0,0 |
| DataSizeMetricCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| AvailabilityDuration | Tempo, em milissegundos, da duração do teste na Web |
| AvailabilityDurationCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| Disponibilidadevalue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID exclusivo para o teste na Web |
| AvailabilityTimestamp | Carimbo de data/hora preciso do teste de disponibilidade |
| AvailabilityDurationMin | Para registros de amostra, esse campo mostra a duração mínima do teste na Web (milissegundos) para os pontos de dados representados |
| AvailabilityDurationMax | Para registros de amostra, esse campo mostra a duração máxima do teste na Web (milissegundos) para os pontos de dados representados |
| AvailabilityDurationStdDev | Para registros de amostra, esse campo mostra o desvio padrão entre todas as durações de teste na Web (milissegundos) para os pontos de dados representados |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campos específicos da exceção

| Tipo | ApplicationInsights |
| --- | --- |
| Telemetria | Exceção |
| ExceptionType | Tipo da exceção |
| ExceptionMethod | O método que cria a exceção |
| ExceptionAssembly | O assembly inclui a estrutura e a versão, bem como o token de chave pública |
| Myexception | Tipo da exceção |
| ExceptionHandledAt | Indica o nível que tratou a exceção |
| ExceptionCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| ExceptionMessage | Mensagem da exceção |
| ExceptionStack | Pilha completa da exceção |
| ExceptionHasStack | True, se a exceção tiver uma pilha |



### <a name="request-specific-fields"></a>Campos específicos da solicitação

| Propriedade | Descrição |
| --- | --- |
| Tipo | ApplicationInsights |
| Telemetria | Pedir |
| ResponseCode | Resposta HTTP enviada ao cliente |
| RequestSuccess | Indica êxito ou falha. True ou false. |
| RequestID | ID para identificar exclusivamente a solicitação |
| RequestName | OBTER/postar + base de URL |
| RequestDuration | Tempo, em segundos, da duração da solicitação |
| URL | URL da solicitação sem inclusão do host |
| Host | Host do servidor Web |
| URLBase | URL completa da solicitação |
| ApplicationProtocol | Tipo de protocolo usado pelo aplicativo |
| requestCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| RequestDurationCount | 100/(taxa de amostragem). Por exemplo, 4 =&gt; 25% |
| RequestDurationMin | Para registros de amostra, esse campo mostra a duração mínima da solicitação (milissegundos) para os pontos de dados representados. |
| RequestDurationMax | Para registros de amostra, esse campo mostra a duração máxima da solicitação (milissegundos) para os pontos de dados representados |
| RequestDurationStdDev | Para registros de amostra, esse campo mostra o desvio padrão entre todas as durações de solicitação (milissegundos) para os pontos de dados representados |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

Esta solução não tem um conjunto de pesquisas de log de exemplo mostradas no painel. No entanto, as consultas de pesquisa de log de exemplo com descrições são mostradas na seção [Exibir informações de conector do Application insights](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Removendo o conector com o PowerShell
Com a substituição do portal do OMS, não há como configurar e remover conexões existentes do Portal. Você pode remover as conexões existentes com o seguinte script do PowerShell. Você deve ser o proprietário ou colaborador do espaço de trabalho e o leitor de Application Insights recurso para executar essa operação.

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

Você pode recuperar uma lista de aplicativos usando o seguinte script do PowerShell que invoca uma chamada à API REST. 

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
Esse script requer um token de autenticação de portador para autenticação em relação à Azure Active Directory. Uma maneira de recuperar esse token é usando um artigo no [site de documentação da API REST](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Clique em **experimentar** e faça logon em sua assinatura do Azure. Você pode copiar o token de portador da versão **prévia da solicitação** , conforme mostrado na imagem a seguir.


![Token de portador](media/app-insights-connector/bearer-token.png)


Você também pode recuperar uma lista de aplicativos usando uma consulta de log:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Passos seguintes

- Use a [pesquisa de logs](../../azure-monitor/log-query/log-query-overview.md) para exibir informações detalhadas para seus aplicativos Application insights.
