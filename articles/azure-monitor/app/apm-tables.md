---
title: Azure Monitor Application Insights esquema de recursos baseado no espaço de trabalho
description: Conheça a nova estrutura de mesa e esquema para recursos baseados no espaço de trabalho do Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 05/09/2020
ms.openlocfilehash: 8f0bee64d74cfd5b6abef5c918c023974fda3fcf
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931058"
---
# <a name="workspace-based-resource-changes-preview"></a>Alterações de recursos baseadas no espaço de trabalho (pré-visualização)

Antes da introdução dos recursos de [Insights de Aplicação baseados no espaço de trabalho,](create-workspace-resource.md)os dados do Application Insights foram armazenados separadamente de outros dados de registo no Azure Monitor. Ambos são baseados no Azure Data Explorer e usam a mesma língua de consulta kusto (KQL). Isto é descrito em [Logs in Azure Monitor](../platform/data-platform-logs.md).

Com os dados de recursos de aplicações baseados no espaço de trabalho, os dados de recursos do Log Analytics são armazenados num espaço de trabalho do Log Analytics com outros dados de monitorização e dados de aplicações. Isto simplifica a sua configuração, permitindo-lhe analisar mais facilmente dados em várias soluções e alavancar as capacidades dos espaços de trabalho.

## <a name="table-structure"></a>Estrutura da tabela

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
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|Cadeia|
|localização|string|Localização|string|
|message|string|Mensagem|string|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|size|real|Tamanho|real|
|exito|string|Success|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appbrowsertimings"></a>AppBrowserTimings

Tabela legado: browserTimings

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|name|string|Nome|datetime|
|redeDuração|real|NetworkDurationMs|real|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|processamentoDuração|real|ProcessamentoDurationMs|real|
|receberDuration|real|ReceberDurationMs|real|
|sdkVersion|string|SdkVersion|string|
|sendDuration|real|SendDurationMs|real|
|session_Id|string|SessionId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|totalDuração|real|TotaldurationMs|real|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appdependencies"></a>AppDependencies

Tabela legado: dependências

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|dados|string|Dados|string|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|Cadeia|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|resultadosDesco|string|Resultados Código|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|exito|string|Success|Booleano|
|alvo|string|Destino|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|string|Tipo de Dependência|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appevents"></a>Eventos AppEvents

Tabela legado: costumesEvents

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appmetrics"></a>AppMetrics

Tabela legado: costumesMetrics

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|iKey|string|IKey|string|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|
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
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|real|
|`id`|string|`Id`|string|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|Cadeia|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|performanceBucket|string|PerformanceBucket|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|string|Url|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appperformancecounters"></a>AppPerformanceCounters

Tabela legado: performanceCounters

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|categoria|string|Categoria|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|counter|string|(removido)||
|customDimensionais|dynamic|Propriedades|Dinâmica|
|iKey|string|IKey|string|
|exemplo|string|Instância|string|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|name|string|Nome|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|
|valor|real|Valor|real|

### <a name="apprequests"></a>Recorrentes

Tabela legado: pedidos

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|Dinâmica|
|medidas de medidas personalizadas|dynamic|Medições|Dinâmica|
|duration|real|DurationMs|Real|
|`id`|string|`Id`|Cadeia|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|Cadeia|
|name|string|Nome|Cadeia|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|performanceBucket|string|PerformanceBucket|Cadeia|
|resultadosDesco|string|Resultados Código|Cadeia|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|source|string|Origem|Cadeia|
|exito|string|Success|Booleano|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|url|string|Url|Cadeia|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="appsystemevents"></a>Eventos AppSystemEvents

Tabela legado: exceções

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|montagem|string|Assemblagem|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|dynamic|
|medidas de medidas personalizadas|dynamic|Medições|dynamic|
|detalhes|dynamic|Detalhes|dynamic|
|handledAt|string|HandledAt|string|
|iKey|string|IKey|string|
|mais internamente Montagem|string|Mais internamente|string|
|innerstMessage|string|InternamenteMessage|string|
|innermostMethod|string|InnermostMethod|string|
|Mais íntimosTipo|string|Mais innertType|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|message|string|Mensagem|string|
|método|string|Método|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|exteriorEssaconly|string|ExteriorEseto|string|
|outerMessage|string|OuterMessage|string|
|outerMethod|string|OuterMethod|string|
|outerType|string|OuterType|string|
|problemId|string|ProblemaId|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severidadeLevel|int|SeveridadeLevel|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|tipo|string|ExcepçãoType|string|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

### <a name="apptraces"></a>AppTraces

Tabela do legado: vestígios

|ApplicationInsights|Tipo|LogAnalytics|Tipo|
|:---|:---|:---|:---|
|appId|string|\_Guia de Recursos|string|
|application_Version|string|AppVersão|string|
|appName|string|\_ResourceId|string|
|client_Browser|string|ClientBrowser|string|
|client_City|string|ClientCity|string|
|client_CountryOrRegion|string|Região de ClientCountryOr|string|
|client_IP|string|ClientIP|string|
|client_Model|string|Modelo de Cliente|string|
|client_OS|string|ClienteOS|string|
|client_StateOrProvince|string|ClientStateOrProvince|string|
|client_Type|string|ClientType|string|
|cloud_RoleInstance|string|Aparição de Apensa|string|
|cloud_RoleName|string|Nome do apreoia|string|
|customDimensionais|dynamic|Propriedades|dynamic|
|medidas de medidas personalizadas|dynamic|Medições|dynamic|
|iKey|string|IKey|string|
|itemOconse|int|ItemCount|int|
|itemId|string|\_ItemId|string|
|itemType|string|Tipo|string|
|message|string|Mensagem|string|
|operation_Id|string|OperationId|string|
|operation_Name|string|OperationName|string|
|operation_ParentId|string|OperaçãoParentId|string|
|operation_SyntheticSource|string|OperaçãoSssyntheticSource|string|
|sdkVersion|string|SdkVersion|string|
|session_Id|string|SessionId|string|
|severidadeLevel|int|SeveridadeLevel|int|
|carimbo de data/hora|datetime|TimeGenerated|datetime|
|user_AccountId|string|UserAccountId|string|
|user_AuthenticatedId|string|UserAuthenticatedId|string|
|user_Id|string|IDUtilizador|string|

## <a name="next-steps"></a>Passos seguintes

* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas da Análise](../log-query/log-query-overview.md)

