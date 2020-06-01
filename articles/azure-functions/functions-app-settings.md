---
title: Referência das definições de aplicação para as Funções do Azure
description: Documentação de referência para as definições da aplicação Azure Functions ou variáveis ambientais.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 5a0201eeed1678299ec16ff268062463b9c75e5c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235356"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência das definições de aplicação para as Funções do Azure

As configurações da aplicação numa aplicação de função contêm opções de configuração global que afetam todas as funções para essa aplicação de função. Quando funciona localmente, estas definições são acedidas como [variáveis ambientais locais.](functions-run-local.md#local-settings-file) Este artigo lista as definições de aplicações que estão disponíveis em aplicações de função.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existem outras opções de configuração global no ficheiro [host.json](functions-host-json.md) e no ficheiro [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação para Insights de Aplicação. Utilize apenas um de `APPINSIGHTS_INSTRUMENTATIONKEY` ou `APPLICATIONINSIGHTS_CONNECTION_STRING` . Para obter mais informações, consulte [as Funções Do Monitor Azure](functions-monitoring.md). 

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

O fio de ligação para Insights de Aplicação. A utilização `APPLICATIONINSIGHTS_CONNECTION_STRING` em vez de quando a sua `APPINSIGHTS_INSTRUMENTATIONKEY` aplicação de função requer as personalizações adicionais suportadas através da cadeia de ligação. Para obter mais informações, consulte [as cordas de ligação.](../azure-monitor/app/sdk-connection-string.md) 

|Chave|Valor da amostra|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[chave];IngestionEndpoint=[url]; LiveEndpoint=[url]; ProfilerEndpoint=[url]; SnapshotEndpoint=[url];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2.x e versões posteriores do tempo de execução das Funções, configura o comportamento da aplicação com base no ambiente de tempo de execução. Este valor é [lido durante a inicialização.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) Pode definir `AZURE_FUNCTIONS_ENVIRONMENT` qualquer valor, mas três valores são [suportados:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Desenvolvimento,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Encenação](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [Produção.](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) Quando `AZURE_FUNCTIONS_ENVIRONMENT` não está definido, é padrão para `Development` um ambiente local e em `Production` Azure. Esta definição deve ser utilizada em vez de `ASPNETCORE_ENVIRONMENT` definir o ambiente de tempo de execução. 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

Na versão 2.x e versões posteriores do tempo de execução de Funções, as definições de aplicação podem substituir as definições [do host.json](functions-host-json.md) no ambiente atual. Estas sobreposições são expressas como configurações de aplicação denominada `AzureFunctionsJobHost__path__to__setting` . Para obter mais informações, consulte [os valores de Override host.json](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de ligação de conta de armazenamento opcional para armazenar troncos e exibi-los no **separador Monitor** no portal. Esta definição é válida apenas para aplicações que visam a versão 1.x do tempo de execução das Funções Azure. A conta de armazenamento deve ser uma conta de uso geral que suporta bolhas, filas e mesas. Para saber mais, consulte os [requisitos da conta de Armazenamento.](storage-considerations.md#storage-account-requirements)

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|PredefiniçãoendpointsProtocol=https; Nome de <name> conta= ContaKey=<key>|

> [!NOTE]
> Para um melhor desempenho e experiência, as versões runtime 2.x e posterior utilizam APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitorização em vez de `AzureWebJobsDashboard` .

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true`significa desativar a página de aterragem predefinida que é mostrada para o URL raiz de uma aplicação de função. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando esta definição da aplicação é omitida ou definida para `false` , uma página semelhante ao exemplo a seguir é exibida em resposta ao URL `<functionappname>.azurewebsites.net` .

![Página de aterragem de aplicativo de função](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true`significa utilizar o modo de desbloqueio ao compilar o código .NET; `false`significa usar o modo Debug. A predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de funcionalidades beta para ativar. As funcionalidades beta ativadas por estas bandeiras não estão prontas para produção, mas podem ser ativadas para uso experimental antes de entrarem em direto.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|recurso1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o fornecedor para o armazenamento de chaves. Atualmente, os repositórios suportados são o armazenamento de bolhas ("Blob") e o sistema de ficheiros local ("Ficheiros"). O padrão é blob na versão 2 e sistema de ficheiros na versão 1.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|Ficheiros|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução das funções Azure utiliza este fio de ligação de conta de armazenamento para todas as funções, exceto para funções detonadas HTTP. A conta de armazenamento deve ser uma conta de uso geral que suporta bolhas, filas e mesas. Consulte os [requisitos](storage-considerations.md#storage-account-requirements) [da conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e armazenamento.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|PredefiniçãoendpointsProtocol=https; ContaName=[nome]; AccountKey=[chave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para o TypeScript. Permite-lhe anular o padrão, se necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>MODO \_ \_ DE EDIÇÃO DE APLICATIVO \_ DE FUNÇÃO

Dita se a edição no portal Azure está ativada. Valores válidos são "readwrite" e "readonly".

|Chave|Valor da amostra|
|---|------------|
|MODO \_ \_ DE EDIÇÃO DE APLICATIVO \_ DE FUNÇÃO|só de leitura|

## <a name="functions_extension_version"></a>VERSÃO \_ DE EXTENSÃO DE FUNÇÕES \_

A versão do tempo de execução das Funções a utilizar nesta aplicação de função. Um azulejo com versão principal significa usar a versão mais recente dessa versão principal (por exemplo, "~2"). Quando estão disponíveis novas versões para a mesma versão principal, são automaticamente instaladas na aplicação de função. Para fixar a aplicação numa versão específica, utilize o número de versão completa (por exemplo, "2.0.12345"). O padrão é "~2". Um valor de `~1` pins a sua app para a versão 1.x do tempo de execução.

|Chave|Valor da amostra|
|---|------------|
|VERSÃO \_ DE EXTENSÃO DE FUNÇÕES \_|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNÇÕES \_ V2 MODO DE \_ COMPATIBILIDADE \_

Esta definição permite que a sua aplicação de função seja executada num modo compatível com a versão 2.x na versão 3.x tempo de execução. Utilize esta definição apenas se encontrar problemas ao [atualizar a sua aplicação de função da versão 2.x para 3.x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Esta definição destina-se apenas a uma solução de curto prazo enquanto atualiza a sua aplicação para funcionar corretamente na versão 3.x. Esta definição é suportada enquanto o [tempo de execução de 2.x for suportado](functions-versions.md). Se encontrar problemas que impeçam a sua aplicação de funcionar na versão 3.x sem utilizar esta definição, por favor [reporte o seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requer que [a \_ \_ versão de extensão de funções](functions-app-settings.md#functions_extension_version) seja definida para `~3` .

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES \_ V2 MODO DE \_ COMPATIBILIDADE \_|true|

## <a name="functions_worker_process_count"></a>FUNÇÕES \_ CONTAGEM \_ DE PROCESSOS DE TRABALHADOR \_

Especifica o número máximo de processos de trabalhadores linguísticos, com um valor padrão de `1` . O valor máximo permitido `10` é. As invocações de funções são distribuídas uniformemente entre os processos de trabalho linguístico. Os processos de trabalho linguístico são gerados a cada 10 segundos até que a contagem definida por FUNÇÕES \_ CONTAGEM \_ DE PROCESSOS \_ OPERÁRIOS seja alcançada. A utilização de vários processos de trabalho linguístico não é o mesmo que [a escala.](functions-scale.md) Considere usar esta definição quando a sua carga de trabalho tiver uma mistura de invocações ligadas ao CPU e i/o-bound. Esta definição aplica-se a todas as línguas non-.NET.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES \_ CONTAGEM \_ DE PROCESSOS DE TRABALHADOR \_|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES \_ TEMPO \_ DE EXECUÇÃO DO TRABALHADOR

O tempo de funcionamento do trabalhador da língua para carregar na aplicação de função.  Isto corresponderá ao idioma utilizado na sua aplicação (por exemplo, "dotnet"). Para funções em vários idiomas, terá de publicá-las em várias aplicações, cada uma com um valor de tempo de execução correspondente.  Os valores válidos são `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES \_ TEMPO \_ DE EXECUÇÃO DO TRABALHADOR|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Apenas para consumo & Planos Premium. Cadeia de ligação para conta de armazenamento onde o código e configuração da aplicação de função são armazenados. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|PredefiniçãoendpointsProtocol=https; ContaName=[nome]; AccountKey=[chave]|

## <a name="website_contentshare"></a>\_PARTILHA DE CONTEÚDOS DO SITE

Apenas para consumo & Planos Premium. O caminho do ficheiro para o código e configuração da aplicação de função. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome da aplicação da função. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>ESCALA \_ DE \_ \_ APLICAÇÃO MAX DYNAMIC \_ \_ DO SITE

O número máximo de casos a que a aplicação de função pode escalar. O padrão não é limite.

> [!NOTE]
> Esta definição é uma funcionalidade de pré-visualização - e só é fiável se definida para um valor <= 5

|Chave|Valor da amostra|
|---|------------|
|ESCALA \_ DE \_ \_ APLICAÇÃO MAX DYNAMIC \_ \_ DO SITE|5|

## <a name="website_node_default_version"></a>NODE DO SITE \_ \_ DEFAULT_VERSION

_Só janelas._  
Define a versão de Node.js para usar ao executar a sua aplicação de função no Windows. Deve utilizar um azulejo (~) para que o tempo de execução utilize a versão mais recente disponível da versão principal visada. Por exemplo, quando definido `~10` para , a versão mais recente do Node.js 10 é usada. Quando uma versão principal é direcionada com um azulejo, não é preciso atualizar manualmente a versão menor. 

|Chave|Valor da amostra|
|---|------------|
|NODE DO SITE \_ \_ DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>SITE \_ EXECUTADO A PARTIR DE \_ \_ PACOTE

Permite que a sua aplicação de função funcione a partir de um ficheiro de pacote montado.

|Chave|Valor da amostra|
|---|------------|
|SITE \_ EXECUTADO A PARTIR DE \_ \_ PACOTE|1|

Valores válidos são um URL que resolve a localização de um ficheiro de pacote de implantação, ou `1` . Quando `1` programado, a embalagem deve estar na `d:\home\data\SitePackages` pasta. Ao utilizar a implementação zip com esta definição, a embalagem é automaticamente carregada para este local. Na pré-visualização, esta definição foi nomeada `WEBSITE_RUN_FROM_ZIP` . Para obter mais informações, consulte [Executar as suas funções a partir de um ficheiro de pacote](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por predefinição, os proxies de funções utilizarão um atalho para enviar chamadas API de proxies diretamente para funções na mesma App de Função, em vez de criar um novo pedido HTTP. Esta definição permite-lhe desativar esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|As chamadas com um URL de backend que aponta para uma função na App de funções local deixarão de ser enviadas diretamente para a função, e serão, em vez disso, direcionadas para a extremidade frontal HTTP para a App de Função|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. As chamadas com um URL de backend que indique uma função na App de Função local serão reencaminhadas diretamente para essa Função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta definição controla se %2F é descodificado como cortes nos parâmetros de rota quando são inseridos no URL de backend. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Os parâmetros de rota com barras codificadas os descodificados. `example.com/api%2ftest`tornar-se-á`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Este é o comportamento padrão. Todos os parâmetros de rota serão passados inalterados|

### <a name="example"></a>Exemplo

Aqui está um exemplo proxies.json em uma app de função no URL myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Descodagem URL|Input|Saída|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Passos seguintes

[Saiba como atualizar as definições de aplicações](functions-how-to-use-azure-function-app-settings.md#settings)

[Ver configurações globais no ficheiro host.json](functions-host-json.md)

[Consulte outras configurações de aplicativos para aplicações do Serviço de Aplicações](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
