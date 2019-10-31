---
title: Referência de configurações do aplicativo para Azure Functions
description: Documentação de referência para as configurações do aplicativo Azure Functions ou as variáveis de ambiente.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 896179a393b870390991a8e9942f6e7287ec5c90
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063308"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações do aplicativo para Azure Functions

As configurações do aplicativo em um aplicativo de funções contêm opções de configuração globais que afetam todas as funções desse aplicativo de funções. Quando você executa localmente, essas configurações são acessadas como [variáveis de ambiente](functions-run-local.md#local-settings-file)locais. Este artigo lista as configurações do aplicativo que estão disponíveis em aplicativos de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Há outras opções de configuração global no arquivo [host. JSON](functions-host-json.md) e no arquivo [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação de Application Insights se você estiver usando Application Insights. Consulte [monitorar Azure Functions](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2. x do tempo de execução do functions, o configura o comportamento do aplicativo com base no ambiente de tempo de execução. Esse valor é [lido durante a inicialização](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Você pode definir `AZURE_FUNCTIONS_ENVIRONMENT` para qualquer valor, mas há suporte para [três valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [desenvolvimento](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [preparo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [produção](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Quando `AZURE_FUNCTIONS_ENVIRONMENT` não é definido, o padrão é `Development` em um ambiente local e `Production` no Azure. Essa configuração deve ser usada em vez de `ASPNETCORE_ENVIRONMENT` para definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **monitorar** no Portal. A conta de armazenamento deve ser uma finalidade geral que dá suporte a BLOBs, filas e tabelas. Consulte [requisitos](functions-create-function-app-portal.md#storage-account-requirements)de conta de armazenamento e [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [chave]|

> [!TIP]
> Para desempenho e experiência, é recomendável usar o APPINSIGHTS_INSTRUMENTATIONKEY e o app insights para monitoramento em vez de AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida em resposta à URL `<functionappname>.azurewebsites.net`.

![Página de aterrissagem do aplicativo de funções](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de liberação ao compilar código .NET; `false` significa usar o modo de depuração. A predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de recursos beta a serem habilitados. Os recursos beta habilitados por esses sinalizadores não estão prontos para produção, mas podem ser habilitados para uso experimental antes de ficarem ativos.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou provedor a ser usado para o armazenamento de chaves. Atualmente, os repositórios com suporte são o armazenamento de BLOBs ("blob") e o sistema de arquivos local ("arquivos"). O padrão é blob na versão 2 e sistema de arquivos na versão 1.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|Ficheiros|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução Azure Functions usa essa cadeia de conexão de conta de armazenamento para todas as funções, exceto para funções disparadas por HTTP. A conta de armazenamento deve ser uma finalidade geral que dá suporte a BLOBs, filas e tabelas. Consulte [requisitos](functions-create-function-app-portal.md#storage-account-requirements)de conta de armazenamento e [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [chave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite que você substitua o padrão se necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNÇÃO\_aplicativo\_editar modo de\_

Determina se a edição no portal do Azure está habilitada. Os valores válidos são "ReadWrite" e "ReadOnly".

|Chave|Valor da amostra|
|---|------------|
|FUNÇÃO\_aplicativo\_editar modo de\_|leitura|

## <a name="functions_extension_version"></a>\_versão da extensão do\_do Functions

A versão do tempo de execução do Functions a ser usada neste aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~ 2"). Quando novas versões para a mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo em uma versão específica, use o número de versão completo (por exemplo, "2.0.12345"). O padrão é "~ 2". Um valor de `~1` fixa seu aplicativo para a versão 1. x do tempo de execução.

|Chave|Valor da amostra|
|---|------------|
|\_versão da extensão do\_do Functions|~ 2|

## <a name="functions_worker_process_count"></a>O Functions\_o processo de\_do trabalhador\_contagem

Especifica o número máximo de processos de trabalho de idioma, com um valor padrão de `1`. O valor máximo permitido é `10`. As invocações de função são distribuídas uniformemente entre os processos de trabalho de linguagem. Os processos de trabalho de idioma são gerados a cada 10 segundos até que a contagem definida por funções\_o processo de\_de trabalho\_contagem seja atingida. O uso de vários processos de trabalho de idioma não é o mesmo que o [dimensionamento](functions-scale.md). Considere usar essa configuração quando sua carga de trabalho tiver uma combinação de invocações associadas à CPU e de e/s. Essa configuração se aplica a todos os idiomas do non-.NET.

|Chave|Valor da amostra|
|---|------------|
|O Functions\_o processo de\_do trabalhador\_contagem|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_tempo de execução de\_de trabalho

O tempo de execução de trabalho do idioma a ser carregado no aplicativo de funções.  Isso corresponderá à linguagem que está sendo usada em seu aplicativo (por exemplo, "dotnet"). Para funções em vários idiomas, será necessário publicá-los em vários aplicativos, cada um com um valor de tempo de execução de trabalho correspondente.  Os valores válidos são `dotnet`C#(F#/), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_tempo de execução de\_de trabalho|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para consumo & apenas planos Premium. Cadeia de conexão para a conta de armazenamento em que o código e a configuração do aplicativo de funções são armazenados. Consulte [criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [chave]|

## <a name="website_contentshare"></a>SITE\_CONTENTSHARE

Para consumo & apenas planos Premium. O caminho do arquivo para o código do aplicativo de funções e a configuração. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia de caracteres exclusiva que começa com o nome do aplicativo de funções. Consulte [criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>O site\_máximo\_dinâmico\_aplicativo\_escala\_OUT

O número máximo de instâncias para as quais o aplicativo de funções pode ser expandido. O padrão é sem limite.

> [!NOTE]
> Essa configuração é um recurso de visualização – e somente confiável se definido com um valor < = 5

|Chave|Valor da amostra|
|---|------------|
|O site\_máximo\_dinâmico\_aplicativo\_escala\_OUT|5|

## <a name="website_node_default_version"></a>NÓ de\_do site\_DEFAULT_VERSION

_Somente Windows._  
Define a versão do node. js a ser usada ao executar seu aplicativo de funções no Windows. Você deve usar um til (~) para que o tempo de execução use a versão mais recente disponível da versão principal de destino. Por exemplo, quando definido como `~10`, a versão mais recente do node. js 10 é usada. Quando uma versão principal é destinada a um til, você não precisa atualizar manualmente a versão secundária. 

|Chave|Valor da amostra|
|---|------------|
|NÓ de\_do site\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>O site\_executar\_do pacote\_

Permite que seu aplicativo de funções seja executado a partir de um arquivo de pacote montado.

|Chave|Valor da amostra|
|---|------------|
|O site\_executar\_do pacote\_|1|

Os valores válidos são uma URL que resolve para o local de um arquivo de pacote de implantação ou `1`. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação de zip com essa configuração, o pacote é automaticamente carregado para esse local. Na visualização, essa configuração foi nomeada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, consulte [executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por padrão, os proxies de funções usarão um atalho para enviar chamadas de API de proxies diretamente para funções na mesma Aplicativo de funções, em vez de criar uma nova solicitação HTTP. Essa configuração permite que você desabilite esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local não serão mais enviadas diretamente para a função e, em vez disso, serão direcionadas de volta para o front-end HTTP para o Aplicativo de funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. Chamadas com uma URL de back-end apontando para uma função no Aplicativo de funções local serão encaminhadas diretamente para essa função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Essa configuração controla se% 2F é decodificado como barras em parâmetros de rota quando eles são inseridos na URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Os parâmetros de rota com barras codificadas os terão decodificados. `example.com/api%2ftest` se tornará `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Esse é o comportamento padrão. Todos os parâmetros de rota serão passados em conjunto inalterados|

### <a name="example"></a>Exemplo

Aqui está um exemplo de proxies. JSON em um aplicativo de funções na URL myfunction.com

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
|Decodificação de URL|Input|Saída|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Passos seguintes

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte Configurações globais no arquivo host. JSON](functions-host-json.md)

[Ver outras configurações de aplicativo para aplicativos do serviço de aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
