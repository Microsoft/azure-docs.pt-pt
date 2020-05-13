---
title: Esquema de recursos baseado no espaço de trabalho Azure Monitor Insights
description: Conheça a nova estrutura de tabela e o esquema para os recursos baseados no espaço de trabalho do Azure Monitor Application Insights.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 21f387a87224615ea6afbdce620c56e3ad2cc6ea
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210544"
---
# <a name="workspace-based-resource-changes-preview"></a>Alterações de recursos baseadas no espaço de trabalho (pré-visualização)

Antes da introdução de recursos de Insights de [Aplicação baseados no espaço de trabalho,](create-workspace-resource.md)os dados do Application Insights foram armazenados separados de outros dados de registo no Monitor Azure. Ambos são baseados no Azure Data Explorer e usam a mesma linguagem de consulta Kusto (KQL). Isto é descrito em [Registos no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

Com os dados de recursos de aplicação baseados no espaço de trabalho, os dados dos recursos insights são armazenados num espaço de trabalho de Log Analytics com outros dados de monitorização e dados de aplicação. Isto simplifica a sua configuração, permitindo-lhe analisar mais facilmente os dados através de múltiplas soluções e alavancar as capacidades dos espaços de trabalho.

## <a name="table-structure"></a>Estrutura de mesa

| Nome de mesa legado | Novo nome de mesa | Descrição |
|:---|:---|:---|
| disponibilidadeResultados | Resultados da Disponibilidade de Aplicações |  Resumo dos dados dos testes de disponibilidade.|
| browserTimings | AppBrowserTimings | Dados sobre o desempenho do cliente, como o tempo que se demorou a processar os dados que chegam.|
| dependências | Dependências de Aplicações | Chamadas da aplicação para outros componentes (incluindo componentes externos) registadas via TrackDependency() – por exemplo, chamadas para REST API, base de dados ou um sistema de ficheiros.  |
| eventos personalizados | Eventos de Aplicações | Eventos personalizados criados pela sua aplicação. |
| métricas personalizadas | AppMetrics | Métricas personalizadas criadas pela sua aplicação. |
| pageViews | Vistas de página de aplicações| Dados sobre cada visualização do site com informações do navegador. |
| performanceCounters | AppPerformanceCounters | Medições de desempenho dos recursos computacionais que suportam a aplicação, por exemplo, contadores de desempenho do Windows. |
| pedidos | Pedidos de Aplicações | Pedidos recebidos pelo seu pedido. Por exemplo, um registo de pedido separado é registado para cada pedido HTTP que a sua aplicação web recebe.  |
| exceções | Eventos de Sistemas de Aplicações | Exceções lançadas pelo tempo de execução da aplicação, captura tanto o lado do servidor como as exceções do lado do cliente (navegadores). |
| vestígios | AppTraces | Registos detalhados (vestígios) emitidos através do código de aplicação/quadros de registo registados através do TrackTrace(). |

## <a name="table-schemas"></a>Esquemias de mesa

As seguintes secções mostram o mapeamento entre os nomes clássicos da propriedade e os novos nomes de propriedades de Application Insights baseados no espaço de trabalho.  Utilize estas informações para converter quaisquer consultas utilizando tabelas antigas.

A maioria das colunas tem o mesmo nome com capitalização diferente. Uma vez que o KQL é sensível aos casos, terá de alterar cada nome de coluna juntamente com os nomes de tabela sinuosos em consultas existentes. Destacam-se as colunas com alterações para além da capitalização. Ainda pode utilizar as suas consultas clássicas de Insights de Aplicação dentro do painel de **Registos** do seu recurso Application Insights, mesmo que seja um recurso baseado no espaço de trabalho. Os novos nomes de propriedade são necessários para quando se consulta no contexto da experiência do espaço de trabalho Log Analytics.

### <a name="appavailabilityresults"></a>Resultados da Disponibilidade de Aplicações

Tabela legacy: disponibilidade

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DuraçãoMs|real|
|`id`|string|`Id`|string|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|String|
|localização|string|Localização|string|
|message|string|Mensagem|string|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|size|real|Tamanho|real|
|exito|string|Êxito|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabela legacy: browserTimings

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|nome|string|Name|datetime|
|redeDuração|real|Duração da redeMs|real|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processamentoDuração|real|ProcessamentoDuraMs|real|
|receberDuração|real|ReceberDuraMs|real|
|sdkVersion|string|SdkVersion|string|
|enviarDuração|real|EnvioDeDuraMs|real|
|session_Id|string|SessãoId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|totalDuração|real|TotalDeduraMs|real|
|url|string|Url|string|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appdependencies"></a>Dependências de Aplicações

Tabela legado: dependências

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|data|string|Dados|string|
|duration|real|DuraçãoMs|real|
|`id`|string|`Id`|string|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|String|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultadoCódigo|string|Código de Resultados|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|exito|string|Êxito|Booleano|
|alvo|string|Destino|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|string|DependencyType|string|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appevents"></a>Eventos de Aplicações

Tabela legacy: customEvents

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appmetrics"></a>AppMetrics

Tabela legacy: customMetrics

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|iChave|string|IKey|string|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|
|valor|real|(removido)||
|valorCount|int|ValorCount|int|
|valorMax|real|ValorMax|real|
|valorMin|real|ValorMin|real|
|valorStdDev|real|ValorStdDev|real|
|valorSum|real|ValorSSoma|real|

### <a name="apppageviews"></a>Vistas de página de aplicações

Tabela legacy: pageViews

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DuraçãoMs|real|
|`id`|string|`Id`|string|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|String|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|string|Url|string|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabela legacy: performanceCounters

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|categoria|string|Categoria|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|counter|string|(removido)||
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|iChave|string|IKey|string|
|instância|string|Instância|string|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|nome|string|Name|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|
|valor|real|Valor|real|

### <a name="apprequests"></a>Pedidos de Aplicações

Tabela legacy: pedidos

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|Dinâmica|
|medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DuraçãoMs|Real|
|`id`|string|`Id`|String|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|String|
|nome|string|Name|String|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|performanceBucket|string|PerformanceBucket|String|
|resultadoCódigo|string|Código de Resultados|String|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|source|string|Origem|String|
|exito|string|Êxito|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|string|Url|String|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appsystemevents"></a>Eventos de Sistemas de Aplicações

Tabela legado: exceções

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|montagem|string|Assemblagem|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|dynamic|
|medidas personalizadas|dynamic|Medições|dynamic|
|detalhes|dynamic|Detalhes|dynamic|
|handledAt|string|HandledAt|string|
|iChave|string|IKey|string|
|InnermostAssembly|string|Assembleia Interior|string|
|mensagem mais interior|string|Mensagem Interior|string|
|innermostMethod|string|Método Interior|string|
|mais íntimoType|string|InnermostType|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|message|string|Mensagem|string|
|método|string|Método|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|ExteriorAssem|string|Assembleia Exterior|string|
|mensagem exterior|string|Mensagem Externa|string|
|método exterior|string|Método Exterior|string|
|outerType|string|OuterType|string|
|problemaId|string|Problema|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|gravidadeN|int|Nível de gravidade|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|string|Tipo de exceção|string|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="apptraces"></a>AppTraces

Tabela legacy: vestígios

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Seguid|string|
|application_Version|string|AppVersion|string|
|nome de app|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|ClientCountryOrRegion|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|Clientes|string|
|client_StateOrProvince|string|ClientStateorProvince|string|
|client_Type|string|Tipo de cliente|string|
|cloud_RoleInstance|string|AppRoleInstance|string|
|cloud_RoleName|string|Nome de Funções de Aplicação|string|
|dimensões personalizadas|dynamic|Propriedades|dynamic|
|medidas personalizadas|dynamic|Medições|dynamic|
|iChave|string|IKey|string|
|itensCount|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|artigoType|string|Tipo|string|
|message|string|Mensagem|string|
|operation_Id|string|OperaçãoId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentóide|string|
|operation_SyntheticSource|string|OperaçãoSyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessãoId|string|
|gravidadeN|int|Nível de gravidade|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountid|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

## <a name="next-steps"></a>Passos seguintes

* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md)
* [Escrever consultas da Análise](../../azure-monitor/app/analytics.md)