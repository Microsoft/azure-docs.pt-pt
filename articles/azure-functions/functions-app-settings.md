---
title: Referência das definições de aplicação para as Funções do Azure
description: Documentação de referência para as definições de aplicativos Funções Azure ou variáveis ambientais.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277833"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência das definições de aplicação para as Funções do Azure

As definições da aplicação numa aplicação de função contêm opções de configuração global que afetam todas as funções dessa aplicação de função. Quando se executa localmente, estas configurações são acedidas como [variáveis ambientais locais.](functions-run-local.md#local-settings-file) Este artigo lista as definições da aplicação que estão disponíveis em aplicações de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existem outras opções de configuração global no ficheiro [host.json](functions-host-json.md) e no ficheiro [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação De Insights de Aplicação se estiver a utilizar insights de aplicação. Ver [Monitor Funções Azure](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2.x e versões posteriores do tempo de execução das Funções, configura o comportamento da aplicação com base no ambiente de tempo de execução. Este valor é lido durante a [inicialização.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) Pode definir `AZURE_FUNCTIONS_ENVIRONMENT` qualquer valor, mas [são apoiados três valores:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Desenvolvimento,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Encenação](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [Produção.](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) Quando `AZURE_FUNCTIONS_ENVIRONMENT` não está definido, não `Development` se passa `Production` num ambiente local e em Azure. Esta definição deve `ASPNETCORE_ENVIRONMENT` ser utilizada em vez de definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de ligação opcional da conta de armazenamento para armazenar registos e exibi-los no separador **Monitor** no portal. Esta definição só é válida para aplicações que visam a versão 1.x do tempo de funcionamento das Funções Azure. A conta de armazenamento deve ser de propósito geral que suporte bolhas, filas e mesas. Para saber mais, consulte os requisitos da [conta de armazenamento.](storage-considerations.md#storage-account-requirements)

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; Nome de<name>conta= ; Chave de conta=<key>|

> [!NOTE]
> Para um melhor desempenho e experiência, a versão 2.x e as versões posteriores utilizam APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitorização em vez de `AzureWebJobsDashboard`.

## <a name="azurewebjobsdisablehomepage"></a>Página AzureWebJobsDisableHomepage

`true`significa desativar a página de aterragem predefinida que é mostrada para o URL raiz de uma aplicação de função. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|Página AzureWebJobsDisableHomepage|true|

Quando esta definição de aplicação é omitida ou definida para `false` `<functionappname>.azurewebsites.net`, uma página semelhante ao seguinte exemplo é exibida em resposta ao URL .

![Página de aterragem de aplicativo sinuoso](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>Compilação AzureWebJobsDotNetReleaseCompilation

`true`meios utilizar o modo de desbloqueio ao compilar o código .NET; `false` significa utilizar o modo Debug. A predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|Compilação AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista de funcionalidades beta delimitada sem vírvia para ativar. As funcionalidades beta ativadas por estas bandeiras não estão prontas para a produção, mas podem ser ativadas para uso experimental antes de entrarem em direto.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|recurso1,recurso2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou fornecedor a utilizar para armazenamento de chaves. Atualmente, os repositórios suportados são o armazenamento de blob ("Blob") e o sistema de ficheiros local ("Ficheiros"). O predefinido é blob na versão 2 e sistema de ficheiros na versão 1.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|Ficheiros|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de funcionamento das Funções Azure utiliza esta cadeia de ligação da conta de armazenamento para todas as funções, exceto para funções ativadas em HTTP. A conta de armazenamento deve ser de propósito geral que suporte bolhas, filas e mesas. Consulte os [requisitos](storage-considerations.md#storage-account-requirements)da [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e da conta de armazenamento .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https; Nome de conta=[nome]; Chave de conta=[chave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para typescript. Permite-lhe anular o padrão se necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>MODO\_\_DE\_EDIÇÃO DE APLICATIVOS DE FUNÇÃO

Dita se a edição no portal Azure está ativada. Os valores válidos são "ler" e "ler apenas".

|Chave|Valor da amostra|
|---|------------|
|MODO\_\_DE\_EDIÇÃO DE APLICATIVOS DE FUNÇÃO|só de leitura|

## <a name="functions_extension_version"></a>VERSÃO\_DE\_EXTENSÃO FUNÇÕES

A versão do tempo de execução das Funções para utilizar nesta aplicação de função. Um tilde com versão principal significa usar a versão mais recente dessa versão principal (por exemplo, "~2"). Quando estão disponíveis novas versões para a mesma versão principal, são automaticamente instaladas na aplicação de funções. Para fixar a aplicação numa versão específica, utilize o número completo da versão (por exemplo, "2.0.12345"). Padrão é "~2". Um valor `~1` de pins a sua aplicação para versão 1.x do tempo de execução.

|Chave|Valor da amostra|
|---|------------|
|VERSÃO\_DE\_EXTENSÃO FUNÇÕES|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNÇÕES\_V2\_MODO DE\_COMPATIBILIDADE

Esta definição permite que a sua aplicação de função seja executada num modo compatível com a versão 2.x no tempo de execução da versão 3.x. Utilize esta definição apenas se encontrar problemas ao [atualizar a sua aplicação de funções da versão 2.x para 3.x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Esta definição destina-se apenas a uma suposição de curto prazo enquanto atualiza a sua aplicação para ser executada corretamente na versão 3.x. Esta definição é suportada desde que o tempo de [execução 2.x seja suportado](functions-versions.md). Se encontrar problemas que impeçam a sua aplicação de funcionar na versão 3.x sem utilizar esta definição, por favor [informe o seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requer que a VERSÃO DE `~3` [EXTENSÃO\_\_FUNÇÕES](functions-app-settings.md#functions_extension_version) seja definida para .

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_V2\_MODO DE\_COMPATIBILIDADE|true|

## <a name="functions_worker_process_count"></a>FUNÇÕES\_\_CONTAGEM\_DE PROCESSO DE TRABALHADORES

Especifica o número máximo de processos de trabalhadores linguísticos, com um valor predefinido de `1`. O valor máximo `10`permitido é . As invocações de funções são distribuídas uniformemente entre os processos dos trabalhadores linguísticos. Os processos de trabalhador estoireiro são gerados\_\_a\_cada 10 segundos até que a contagem definida pelo TEMPO DE TRABALHO FUNÇÕES COUNT. A utilização de múltiplos processos de trabalhador estonteante linguagem não é o mesmo que [escalonar](functions-scale.md). Considere utilizar esta definição quando a sua carga de trabalho tiver uma mistura de invocações ligadas a CPU e I/O-bound. Esta definição aplica-se a todas as línguas non-.NET.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_\_CONTAGEM\_DE PROCESSO DE TRABALHADORES|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_\_TEMPO DE FUNCIONAMENTO DO TRABALHADOR

O tempo de funcionamento do trabalhador linguístico para carregar na aplicação de funções.  Isto corresponderá à linguagem utilizada na sua aplicação (por exemplo, "dotnet"). Para funções em vários idiomas, terá de publicá-las em várias aplicações, cada uma com um valor de execução correspondente.  Os valores válidos são `dotnet` `node` (C#/F#), `java` (JavaScript/TypeScript), (Java), `powershell` (PowerShell) e `python` Python.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_\_TEMPO DE FUNCIONAMENTO DO TRABALHADOR|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para consumo & planos Premium. Fio de ligação para a conta de armazenamento onde o código e configuração da aplicação de função são armazenados. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https; Nome de conta=[nome]; Chave de conta=[chave]|

## <a name="website_contentshare"></a>PARTILHA\_DE CONTEÚDOS DO SITE

Para consumo & planos Premium. O caminho do ficheiro para o código e configuração da aplicação de função. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Padrão é uma cadeia única que começa com o nome da aplicação de função. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|app funapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>ESCALA\_\_DE\_\_\_APLICAÇÃO DINÂMICA DO SITE MAX OUT

O número máximo de casos que a aplicação de função pode escalar para fora. O padrão não é limite.

> [!NOTE]
> Esta definição é uma funcionalidade de pré-visualização - e só fiável se for definida para um valor <= 5

|Chave|Valor da amostra|
|---|------------|
|ESCALA\_\_DE\_\_\_APLICAÇÃO DINÂMICA DO SITE MAX OUT|5|

## <a name="website_node_default_version"></a>SITE\_NODE\_DEFAULT_VERSION

_Só janelas._  
Define a versão do Node.js para utilizar ao executar a sua aplicação de funções no Windows. Deve utilizar um tilde (~) para que o tempo de execução utilize a versão mais recente disponível da versão principal direcionada. Por exemplo, quando `~10`definido para , a versão mais recente do Nó.js 10 é usada. Quando uma versão principal é direcionada com um tilde, não é preciso atualizar manualmente a versão menor. 

|Chave|Valor da amostra|
|---|------------|
|SITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>SITE\_\_EXECUTAR\_A PARTIR DE PACOTE

Permite que a sua aplicação de funções seja executada a partir de um ficheiro de pacote montado.

|Chave|Valor da amostra|
|---|------------|
|SITE\_\_EXECUTAR\_A PARTIR DE PACOTE|1|

Valores válidos são ou um URL que resolve a `1`localização de um ficheiro de pacote de implementação, ou . Quando definido `1`para , a `d:\home\data\SitePackages` embalagem deve estar na pasta. Ao utilizar a implantação em zip com esta definição, a embalagem é automaticamente enviada para este local. Na pré-visualização, `WEBSITE_RUN_FROM_ZIP`esta definição foi nomeada . Para mais informações, consulte [Executar as suas funções a partir de um ficheiro](run-functions-from-deployment-package.md)de pacote .

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por predefinição, os proxies de Funções utilizarão um atalho para enviar chamadas DePi de proxies diretamente para funções na mesma App de Funções, em vez de criar um novo pedido http. Esta definição permite-lhe desativar esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|As chamadas com um URL de backend que indique uma função na App de Função local deixarão de ser enviadas diretamente para a função e serão, em vez disso, direcionadas para a extremidade frontal http para a App de Funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. Chamadas com um URL de backend apontando para uma função na App de Função local serão reencaminhadas diretamente para essa Função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta definição controla se %2F é descodificada como cortes nos parâmetros da rota quando são inseridos no URL de backend. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Os parâmetros da rota com os cortes codificados vão descodificar- los. `example.com/api%2ftest`tornar-se-á`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Este é o comportamento padrão. Todos os parâmetros da rota serão passados inalterados|

### <a name="example"></a>Exemplo

Aqui está um exemplo proxies.json em uma aplicação de função no URL myfunction.com

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
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Passos seguintes

[Saiba como atualizar as definições de aplicativos](functions-how-to-use-azure-function-app-settings.md#settings)

[Ver configurações globais no ficheiro host.json](functions-host-json.md)

[Consulte outras definições de aplicativos para apps de Serviço de Aplicações](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
