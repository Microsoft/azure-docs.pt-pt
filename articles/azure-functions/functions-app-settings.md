---
title: Referência de configurações de aplicação para as funções do Azure
description: Documentação de referência para as definições de aplicação de funções do Azure ou variáveis de ambiente.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355913"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicação para as funções do Azure

Definições da aplicação na aplicação de função contém opções de configuração globais que afetam todas as funções para essa aplicação de função. Quando se executa localmente, estas configurações são acedidas como [variáveis ambientais locais.](functions-run-local.md#local-settings-file) Este artigo lista as definições da aplicação que estão disponíveis em aplicações de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existem outras opções de configuração global no ficheiro [host.json](functions-host-json.md) e no ficheiro [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação Application Insights, se estiver a utilizar o Application Insights. Ver [Monitor Funções Azure](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Na versão 2.x e versões posteriores do tempo de execução das Funções, configura o comportamento da aplicação com base no ambiente de tempo de execução. Este valor é lido durante a [inicialização.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43) Pode definir `AZURE_FUNCTIONS_ENVIRONMENT` a qualquer valor, mas [são apoiados três valores:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Desenvolvimento,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Encenação](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)e [Produção.](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production) Quando `AZURE_FUNCTIONS_ENVIRONMENT` não está definida, não passa de `Development` num ambiente local e `Production` em Azure. Esta definição deve ser utilizada em vez de `ASPNETCORE_ENVIRONMENT` para definir o ambiente de tempo de execução. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de ligação opcional da conta de armazenamento para armazenar registos e exibi-los no separador **Monitor** no portal. Esta definição só é válida para aplicações que visam a versão 1.x do tempo de funcionamento das Funções Azure. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Para saber mais, consulte os requisitos da [conta de armazenamento.](storage-considerations.md#storage-account-requirements)

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; Nome de conta=<name>; AccountKey=<key>|

> [!NOTE]
> Para um melhor desempenho e experiência, a versão 2.x e as versões posteriores utilizam APPINSIGHTS_INSTRUMENTATIONKEY e App Insights para monitorização em vez de `AzureWebJobsDashboard`.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desativar a página de aterragem padrão que é mostrada para o URL raiz de uma aplicação de função. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando esta definição de aplicação é omitida ou definida para `false`, uma página semelhante ao seguinte exemplo é exibida em resposta ao URL `<functionappname>.azurewebsites.net`.

![Página de destino da aplicação de função](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa utilizar o modo de desbloqueio ao compilar o código .NET; `false` significa utilizar o modo Debug. A predefinição é `true`.

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

O runtime das funções do Azure utiliza esta cadeia de ligação de conta de armazenamento para todas as funções, exceto para funções de acionada por HTTP. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Consulte os [requisitos](storage-considerations.md#storage-account-requirements)da [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e da conta de armazenamento .

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para o TypeScript. Pode substituir a predefinição se for necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNÇÃO\_APP\_EDITAR\_MODE

Dita se a edição no portal Azure está ativada. Valores válidos são "readwrite" e "só de leitura".

|Chave|Valor da amostra|
|---|------------|
|FUNÇÃO\_APP\_EDITAR\_MODE|só de leitura|

## <a name="functions_extension_version"></a>FUNÇÕES\_VERSÃO\_EXTENSIÇÃO

A versão do runtime das funções para utilizar esta aplicação de função. Um til com a versão principal significa utilizar a versão mais recente dessa versão principal (por exemplo, "~ 2"). Quando as novas versões para a versão principal estão disponíveis, serão automaticamente instaladas na function app. Para afixar a aplicação para uma versão específica, utilize o número da versão completo (por exemplo, "2.0.12345"). A predefinição é "~ 2". Um valor de `~1` coloca a sua aplicação na versão 1.x do tempo de execução.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_VERSÃO\_EXTENSIÇÃO|~ 2|

## <a name="functions_v2_compatibility_mode"></a>FUNÇÕES\_V2\_MODO de\_de COMPATIBILIDADE

Esta definição permite que a sua aplicação de função seja executada num modo compatível com a versão 2.x no tempo de execução da versão 3.x. Utilize esta definição apenas se encontrar problemas ao [atualizar a sua aplicação de funções da versão 2.x para 3.x do tempo de execução](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> Esta definição destina-se apenas a uma suposição de curto prazo enquanto atualiza a sua aplicação para ser executada corretamente na versão 3.x. Esta definição é suportada desde que o tempo de [execução 2.x seja suportado](functions-versions.md). Se encontrar problemas que impeçam a sua aplicação de funcionar na versão 3.x sem utilizar esta definição, por favor [informe o seu problema](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Requer que as [funções\_versão\_extensão](functions-app-settings.md#functions_extension_version) sejam definidas para `~3`.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_V2\_MODO de\_de COMPATIBILIDADE|true|

## <a name="functions_worker_process_count"></a>FUNÇÕES\_processo de\_de trabalhador\_CONTAGEM

Especifica o número máximo de processos de trabalhadores linguísticos, com um valor padrão de `1`. O valor máximo permitido é `10`. As invocações de funções são distribuídas uniformemente entre os processos dos trabalhadores linguísticos. Os processos dos trabalhadores linguísticos são gerados a cada 10 segundos até que seja alcançada a contagem definida pelas funções\_trabalhador\_processo\_COUNT. A utilização de múltiplos processos de trabalhador estonteante linguagem não é o mesmo que [escalonar](functions-scale.md). Considere utilizar esta definição quando a sua carga de trabalho tiver uma mistura de invocações ligadas a CPU e I/O-bound. Esta definição aplica-se a todas as línguas non-.NET.

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_processo de\_de trabalhador\_CONTAGEM|2|


## <a name="functions_worker_runtime"></a>FUNÇÕES\_trabalhador\_tempo de funcionamento

O runtime de trabalho de idioma carregar na function app.  Corresponderá à linguagem a ser utilizada na sua aplicação (por exemplo, "dotnet"). Para as funções em vários idiomas será preciso publicá-los para várias aplicações, cada um com um valor de tempo de execução de trabalho correspondente.  Os valoresC#válidos são `dotnet` (/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) e `python` (Python).

|Chave|Valor da amostra|
|---|------------|
|FUNÇÕES\_trabalhador\_tempo de funcionamento|DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Apenas para planos de Consumo e Premium. Cadeia de ligação para a conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Apenas para planos de Consumo e Premium. O caminho de ficheiro para o código de aplicação de função e a configuração. Utilizado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. A predefinição é uma cadeia exclusiva que começa com o nome da aplicação de função. Ver [Criar uma aplicação de função](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_APLICAÇÃO\_DINÂMICA\_SCALE\_OUT

O número máximo de instâncias de que a aplicação de função pode aumentar horizontalmente para. A predefinição não é nenhum limite.

> [!NOTE]
> Esta definição é uma pré-visualização recurso - e só será confiável se definido como um valor < = 5

|Chave|Valor da amostra|
|---|------------|
|WEBSITE\_MAX\_APLICAÇÃO\_DINÂMICA\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>SITE\_NODE\_DEFAULT_VERSION

_Só janelas._  
Define a versão do Node.js para utilizar ao executar a sua aplicação de funções no Windows. Deve utilizar um tilde (~) para que o tempo de execução utilize a versão mais recente disponível da versão principal direcionada. Por exemplo, quando definido para `~10`, a versão mais recente do Nó.js 10 é usada. Quando uma versão principal é direcionada com um tilde, não é preciso atualizar manualmente a versão menor. 

|Chave|Valor da amostra|
|---|------------|
|SITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_executar\_do pacote de\_

Permite que a aplicação de funções para execução a partir de um ficheiro de pacote montada.

|Chave|Valor da amostra|
|---|------------|
|WEBSITE\_executar\_do pacote de\_|1|

Os valores válidos são um URL que resolve a localização de um ficheiro de pacote de implementação, ou `1`. Quando programado para `1`, a embalagem deve estar na pasta `d:\home\data\SitePackages`. Ao utilizar a implementação de zip com esta definição, o pacote é automaticamente carregado para esta localização. Na pré-visualização, este cenário foi nomeado `WEBSITE_RUN_FROM_ZIP`. Para mais informações, consulte [Executar as suas funções a partir de um ficheiro](run-functions-from-deployment-package.md)de pacote .

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por predefinição, os proxies de funções utilizará um atalho para enviar as chamadas de API a partir de proxies diretamente para as funções na mesma Function App, em vez de criar um novo pedido HTTP. Esta definição permite-lhe desativar esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|As chamadas com um URL de backend que indique uma função na App de Função local deixarão de ser enviadas diretamente para a função e serão, em vez disso, direcionadas para a extremidade frontal http para a App de Funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. Chamadas com um URL de backend apontando para uma função na App de Função local serão reencaminhadas diretamente para essa Função|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta definição controla se o % 2F é descodificado como barras nos parâmetros de rota, quando eles são inseridos no URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parâmetros de rota com barras codificadas terão-los a descodificar. `example.com/api%2ftest` tornar-se-á `example.com/api/test`|
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

[Saiba como atualizar as definições de aplicativos](functions-how-to-use-azure-function-app-settings.md#settings)

[Ver configurações globais no ficheiro host.json](functions-host-json.md)

[Consulte outras definições de aplicativos para apps de Serviço de Aplicações](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
