---
title: Referência de configurações de aplicação para as funções do Azure
description: Documentação de referência para as definições de aplicação de funções do Azure ou variáveis de ambiente.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 35ecebfb1956422470bf20e6d510543897ca0910
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227393"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicação para as funções do Azure

Definições da aplicação na aplicação de função contém opções de configuração globais que afetam todas as funções para essa aplicação de função. Quando você executa localmente, essas configurações são acessadas como [variáveis de ambiente](functions-run-local.md#local-settings-file)locais. Este artigo lista as definições da aplicação que estão disponíveis em aplicações de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Há outras opções de configuração global no arquivo [host. JSON](functions-host-json.md) e no arquivo [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação Application Insights, se estiver a utilizar o Application Insights. Consulte [monitorar Azure Functions](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2. x do tempo de execução do functions, o configura o comportamento do aplicativo com base no ambiente de tempo de execução. Esse valor é [lido durante a inicialização](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Você pode definir `AZURE_FUNCTIONS_ENVIRONMENT` para qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` não é definido, o padrão é `Development` em um ambiente local e `Production` no Azure. Essa configuração deve ser usada em vez de `ASPNETCORE_ENVIRONMENT` para definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **monitorar** no Portal. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Consulte [requisitos](functions-create-function-app-portal.md#storage-account-requirements)de conta de armazenamento e [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

> [!TIP]
> Para o desempenho e experiência, é recomendado utilizar APPINSIGHTS_INSTRUMENTATIONKEY e das App Insights para monitorização, em vez de AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida em resposta à URL `<functionappname>.azurewebsites.net`.

![Página de destino da aplicação de função](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de liberação ao compilar código .NET; `false` significa usar o modo de depuração. A predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas dos recursos de beta para ativar. Ativado por estes sinalizadores de recursos de beta não estão prontos para produção, mas podem ser ativados para utilização experimental antes que se propaguem.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o fornecedor a utilizar para armazenamento de chaves. Atualmente, os repositórios suportados são o armazenamento de BLOBs ("Blob") e o sistema de arquivos local ("ficheiros"). A predefinição é o blob na versão 2 e o sistema de ficheiros na versão 1.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|Ficheiros|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O runtime das funções do Azure utiliza esta cadeia de ligação de conta de armazenamento para todas as funções, exceto para funções de acionada por HTTP. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Consulte [requisitos](functions-create-function-app-portal.md#storage-account-requirements)de conta de armazenamento e [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para o TypeScript. Pode substituir a predefinição se for necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNÇÃO\_aplicativo\_editar modo de\_

Determina se a edição no portal do Azure está habilitada. Valores válidos são "readwrite" e "só de leitura".

|Chave|Valor da amostra|
|---|------------|
|FUNÇÃO\_aplicativo\_editar modo de\_|só de leitura|

## <a name="functions_extension_version"></a>\_versão da extensão do\_do Functions

A versão do runtime das funções para utilizar esta aplicação de função. Um til com a versão principal significa utilizar a versão mais recente dessa versão principal (por exemplo, "~ 2"). Quando as novas versões para a versão principal estão disponíveis, serão automaticamente instaladas na function app. Para afixar a aplicação para uma versão específica, utilize o número da versão completo (por exemplo, "2.0.12345"). A predefinição é "~ 2". Um valor de `~1` fixa seu aplicativo para a versão 1. x do tempo de execução.

|Chave|Valor da amostra|
|---|------------|
|\_versão da extensão do\_do Functions|~ 2|

## <a name="functions_worker_process_count"></a>O Functions\_o processo de\_do trabalhador\_contagem

Especifica o número máximo de processos de trabalho de idioma, com um valor padrão de `1`. O valor máximo permitido é `10`. As invocações de função são distribuídas uniformemente entre os processos de trabalho de linguagem. Os processos de trabalho de idioma são gerados a cada 10 segundos até que a contagem definida por funções\_o processo de\_de trabalho\_contagem seja atingida. O uso de vários processos de trabalho de idioma não é o mesmo que o [dimensionamento](functions-scale.md). Considere usar essa configuração quando sua carga de trabalho tiver uma combinação de invocações associadas à CPU e de e/s. Essa configuração se aplica a todos os idiomas do non-.NET.

|Chave|Valor da amostra|
|---|------------|
|O Functions\_o processo de\_do trabalhador\_contagem|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_tempo de execução de\_de trabalho

O runtime de trabalho de idioma carregar na function app.  Corresponderá à linguagem a ser utilizada na sua aplicação (por exemplo, "dotnet"). Para as funções em vários idiomas será preciso publicá-los para várias aplicações, cada um com um valor de tempo de execução de trabalho correspondente.  Os valores válidos são `dotnet`C#(F#/), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_tempo de execução de\_de trabalho|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para consumo & apenas planos Premium. Cadeia de ligação para a conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas. Consulte [criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="website_contentshare"></a>SITE\_CONTENTSHARE

Para consumo & apenas planos Premium. O caminho de ficheiro para o código de aplicação de função e a configuração. Utilizado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. A predefinição é uma cadeia exclusiva que começa com o nome da aplicação de função. Consulte [criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>O site\_máximo\_dinâmico\_aplicativo\_escala\_OUT

O número máximo de instâncias de que a aplicação de função pode aumentar horizontalmente para. A predefinição não é nenhum limite.

> [!NOTE]
> Esta definição é uma pré-visualização recurso - e só será confiável se definido como um valor < = 5

|Chave|Valor da amostra|
|---|------------|
|O site\_máximo\_dinâmico\_aplicativo\_escala\_OUT|5|

## <a name="website_node_default_version"></a>\_de nó\_do site DEFAULT_VERSION

_Somente Windows._  
Define a versão do node. js a ser usada ao executar seu aplicativo de funções no Windows. Você deve usar um til (~) para que o tempo de execução use a versão mais recente disponível da versão principal de destino. Por exemplo, quando definido como `~10`, a versão mais recente do node. js 10 é usada. Quando uma versão principal é destinada a um til, você não precisa atualizar manualmente a versão secundária. 

|Chave|Valor da amostra|
|---|------------|
|\_de nó\_do site DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>O site\_executar\_do pacote\_

Permite que a aplicação de funções para execução a partir de um ficheiro de pacote montada.

|Chave|Valor da amostra|
|---|------------|
|O site\_executar\_do pacote\_|1|

Os valores válidos são uma URL que resolve para o local de um arquivo de pacote de implantação ou `1`. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao utilizar a implementação de zip com esta definição, o pacote é automaticamente carregado para esta localização. Na visualização, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, consulte [executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por predefinição, os proxies de funções utilizará um atalho para enviar as chamadas de API a partir de proxies diretamente para as funções na mesma Function App, em vez de criar um novo pedido HTTP. Esta definição permite-lhe desativar esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local não serão mais enviadas diretamente para a função e, em vez disso, serão direcionadas de volta para o front-end HTTP para o Aplicativo de funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. Chamadas com um url de back-end que aponta para uma função no local a aplicação de função será reencaminhada diretamente para essa função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta definição controla se o % 2F é descodificado como barras nos parâmetros de rota, quando eles são inseridos no URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parâmetros de rota com barras codificadas terão-los a descodificar. `example.com/api%2ftest` se tornará `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Este é o comportamento padrão. Rota de todos os parâmetros serão transmitidos inalterado|

### <a name="example"></a>Exemplo

Eis um exemplo de proxies na aplicação de função em myfunction.com o URL

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
|Descodificação de URL|Input|Saída|
|-|-|-|
|true|MyFunction.com/test%2fapi|example.com/test/API
|false|MyFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Passos seguintes

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte Configurações globais no arquivo host. JSON](functions-host-json.md)

[Ver outras configurações de aplicativo para aplicativos do serviço de aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
