---
title: Azure Monitor Application Insights esquema de recursos baseado no espaço de trabalho
description: Conheça a nova estrutura de mesa e esquema para recursos baseados no espaço de trabalho do Azure Monitor Application Insights.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/09/2020
ms.openlocfilehash: 1d7275c928b4d25e200a3a8d3d690c7575c056e7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323184"
---
# <a name="workspace-based-resource-changes-preview"></a>Alterações de recursos baseadas no espaço de trabalho (pré-visualização)

Antes da introdução dos recursos de [Insights de Aplicação baseados no espaço de trabalho,](create-workspace-resource.md)os dados do Application Insights foram armazenados separadamente de outros dados de registo no Azure Monitor. Ambos são baseados no Azure Data Explorer e usam a mesma língua de consulta kusto (KQL). Isto é descrito em [Logs in Azure Monitor](../platform/data-platform-logs.md).

Com os dados de recursos de aplicações baseados no espaço de trabalho, os dados de recursos do Log Analytics são armazenados num espaço de trabalho do Log Analytics com outros dados de monitorização e dados de aplicações. Isto simplifica a sua configuração, permitindo-lhe analisar mais facilmente dados em várias soluções e alavancar as capacidades dos espaços de trabalho.

## <a name="table-structure"></a>Estrutura de mesa

| Nome da mesa do legado | Novo nome de mesa | Descrição |
|:---|:---|:---|
| disponibilidadeResults | AppDilabilityResults |  Dados sumários dos testes de disponibilidade.|
| browsersa | AppBrowserTimings | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados recebidos.|
| dependências | AppDependencies | Chamadas da aplicação para outros componentes (incluindo componentes externos) gravadas via TrackDependency() – por exemplo, chamadas para API REST, base de dados ou um sistema de ficheiros.  |
| costumesEvents | Eventos AppEvents | Eventos personalizados criados pela sua aplicação. |
| costumesMetricos | AppMetrics | Métricas personalizadas criadas pela sua aplicação. |
| pageVers | AppPageViews| Dados sobre cada visualização do site com informações do navegador. |
| performanceCounters | AppPerformanceCounters | Medições de desempenho dos recursos computativos que suportam a aplicação, por exemplo, contadores de desempenho do Windows. |
| pedidos | Recorrentes | Pedidos recebidos pelo seu pedido. Por exemplo, é registado um registo de pedidos separado para cada pedido HTTP que a sua aplicação web recebe.  |
| exceções | Eventos AppSystemEvents | Exceções lançadas pelo tempo de execução da aplicação, captura as exceções do lado do servidor e do lado do cliente (navegadores). |
| vestígios | AppTraces | Registos detalhados (vestígios) emitidos através de quadros de código de aplicação/registo registados através do TrackTrace(). |

## <a name="table-schemas"></a>Esquemas de mesa

As secções seguintes mostram o mapeamento entre os nomes clássicos da propriedade e os novos nomes de propriedades de Aplicações baseadas no espaço de trabalho.  Utilize estas informações para converter quaisquer consultas utilizando tabelas antigas.

A maioria das colunas tem o mesmo nome com capitalização diferente. Uma vez que o KQL é sensível a casos, terá de alterar o nome de cada coluna juntamente com os nomes de tabela nas consultas existentes. Destacam-se as colunas com alterações para além da capitalização. Ainda pode utilizar as suas consultas **clássicas** de Insights de Aplicação dentro do painel de Logs do seu recurso Application Insights, mesmo que seja um recurso baseado no espaço de trabalho. Os novos nomes de propriedade são necessários para quando consulta a partir do contexto da experiência do espaço de trabalho Log Analytics.

### <a name="appavailabilityresults"></a>AppDilabilityResults

Tabela legado: disponibilidade

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|Cadeia|
|localização|cadeia|Localização|cadeia|
|message|cadeia|Mensagem|cadeia|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|performanceBucket|cadeia|PerformanceBucket|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|size|real|Tamanho|real|
|exito|cadeia|Success|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabela legado: browserTimings

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|name|cadeia|Nome|datetime|
|redeDuração|real|NetworkDurationMs|real|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|performanceBucket|cadeia|PerformanceBucket|cadeia|
|processamentoDuração|real|ProcessamentoDurationMs|real|
|receberDuration|real|ReceberDurationMs|real|
|sdkVersion|cadeia|SdkVersion|cadeia|
|sendDuration|real|SendDurationMs|real|
|session_Id|cadeia|SessionId|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|totalDuração|real|TotaldurationMs|real|
|url|cadeia|Url|cadeia|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appdependencies"></a>AppDependencies

Tabela legado: dependências

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|dados|cadeia|Dados|cadeia|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|Cadeia|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|performanceBucket|cadeia|PerformanceBucket|cadeia|
|resultadosDesco|cadeia|Resultados Código|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|exito|cadeia|Success|Booleano|
|alvo|cadeia|Destino|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|cadeia|Tipo de Dependência|cadeia|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appevents"></a>Eventos AppEvents

Tabela legado: costumesEvents

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appmetrics"></a>AppMetrics

Tabela legado: costumesMetrics

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|iKey|cadeia|IKey|cadeia|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|
|valor|real|(removido)||
|valorCount|int|ValorCount|int|
|valorMax|real|ValueMax|real|
|valorMin|real|ValorMin|real|
|valorStdDev|real|ValorStdDev|real|
|valorSum|real|ValueSum|real|

### <a name="apppageviews"></a>AppPageViews

Tabela legado: pageViews

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|real|
|`id`|string|`Id`|cadeia|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|Cadeia|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|performanceBucket|cadeia|PerformanceBucket|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|cadeia|Url|cadeia|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabela legado: performanceCounters

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|categoria|cadeia|Categoria|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|counter|cadeia|(removido)||
|customDimensionais|dynamic|Propriedades|Dinâmica|
|iKey|cadeia|IKey|cadeia|
|exemplo|cadeia|Instância|cadeia|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|name|cadeia|Nome|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|
|valor|real|Valor|real|

### <a name="apprequests"></a>Recorrentes

Tabela legado: pedidos

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|Real|
|`id`|cadeia|`Id`|Cadeia|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|Cadeia|
|name|cadeia|Nome|Cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|performanceBucket|cadeia|PerformanceBucket|Cadeia|
|resultadosDesco|cadeia|Resultados Código|Cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|source|cadeia|Origem|Cadeia|
|exito|cadeia|Success|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|cadeia|Url|Cadeia|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="appsystemevents"></a>Eventos AppSystemEvents

Tabela legado: exceções

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|montagem|cadeia|Assemblagem|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|dynamic|
|medidas de medidas personalizadas|dynamic|Medições|dynamic|
|detalhes|dynamic|Detalhes|dynamic|
|handledAt|cadeia|HandledAt|cadeia|
|iKey|cadeia|IKey|cadeia|
|mais internamente Montagem|cadeia|Mais internamente|cadeia|
|innerstMessage|cadeia|InternamenteMessage|cadeia|
|innermostMethod|cadeia|InnermostMethod|cadeia|
|Mais íntimosTipo|cadeia|Mais innertType|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|message|cadeia|Mensagem|cadeia|
|método|cadeia|Método|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|exteriorEssaconly|cadeia|ExteriorEseto|cadeia|
|outerMessage|cadeia|OuterMessage|cadeia|
|outerMethod|cadeia|OuterMethod|cadeia|
|outerType|cadeia|OuterType|cadeia|
|problemId|cadeia|ProblemaId|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|severidadeLevel|int|SeveridadeLevel|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|cadeia|ExcepçãoType|cadeia|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

### <a name="apptraces"></a>AppTraces

Tabela do legado: vestígios

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|cadeia|\_Guia de Recursos|cadeia|
|application_Version|cadeia|AppVersão|cadeia|
|appName|cadeia|\_ResourceId|cadeia|
|client_Browser|cadeia|ClientBrowser|cadeia|
|client_City|cadeia|ClientCity|cadeia|
|client_CountryOrRegion|cadeia|Região de ClientCountryOr|cadeia|
|client_IP|cadeia|ClientIP|cadeia|
|client_Model|cadeia|Modelo de Cliente|cadeia|
|client_OS|cadeia|ClienteOS|cadeia|
|client_StateOrProvince|cadeia|ClientStateOrProvince|cadeia|
|client_Type|cadeia|ClientType|cadeia|
|cloud_RoleInstance|cadeia|Aparição de Apensa|cadeia|
|cloud_RoleName|cadeia|Nome do apreoia|cadeia|
|customDimensionais|dynamic|Propriedades|dynamic|
|medidas de medidas personalizadas|dynamic|Medições|dynamic|
|iKey|cadeia|IKey|cadeia|
|itemOconse|int|ItemCount|int|
|itemId|cadeia|\_ItemId|cadeia|
|itemType|cadeia|Tipo|string|
|message|cadeia|Mensagem|cadeia|
|operation_Id|cadeia|OperationId|cadeia|
|operation_Name|cadeia|OperationName|cadeia|
|operation_ParentId|cadeia|OperaçãoParentId|cadeia|
|operation_SyntheticSource|cadeia|OperaçãoSssyntheticSource|cadeia|
|sdkVersion|cadeia|SdkVersion|cadeia|
|session_Id|cadeia|SessionId|cadeia|
|severidadeLevel|int|SeveridadeLevel|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|cadeia|UserAccountId|cadeia|
|user_AuthenticatedId|cadeia|UserAuthenticatedId|cadeia|
|user_Id|cadeia|IDUtilizador|cadeia|

## <a name="next-steps"></a>Passos seguintes

* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas da Análise](../log-query/log-query-overview.md)

