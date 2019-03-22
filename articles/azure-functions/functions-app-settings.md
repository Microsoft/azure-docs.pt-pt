---
title: Referência de configurações de aplicação para as funções do Azure
description: Documentação de referência para as definições de aplicação de funções do Azure ou variáveis de ambiente.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: d49a6f88f3475359a74be74bf528fb5699dce632
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57860657"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicação para as funções do Azure

Definições da aplicação na aplicação de função contém opções de configuração globais que afetam todas as funções para essa aplicação de função. Ao executar localmente, estas definições são acessadas como local [variáveis de ambiente](functions-run-local.md#local-settings-file). Este artigo lista as definições da aplicação que estão disponíveis em aplicações de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Existem outras opções de configuração global na [Host. JSON](functions-host-json.md) ficheiro e, no [Settings](functions-run-local.md#local-settings-file) ficheiro.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

A chave de instrumentação Application Insights, se estiver a utilizar o Application Insights. Ver [monitorizar as funções do Azure](functions-monitoring.md).

|Chave|Valor da amostra|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

A cadeia de ligação de conta de armazenamento opcional para armazenar os registos e exibi-los no **Monitor** separador no portal. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Ver [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [os requisitos de conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

> [!TIP]
> Para o desempenho e experiência, é recomendado utilizar APPINSIGHTS_INSTRUMENTATIONKEY e das App Insights para monitorização, em vez de AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa que desativar a predefinição da página que é apresentada para o URL de raiz de uma aplicação de funções de destino. A predefinição é `false`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Quando esta definição de aplicação é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é apresentada na resposta para o URL `<functionappname>.azurewebsites.net`.

![Página de destino da aplicação de função](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa que utilize o modo de versão, ao compilar o código do .NET; `false` significa usar o modo de depuração. A predefinição é `true`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas dos recursos de beta para ativar. Ativado por estes sinalizadores de recursos de beta não estão prontos para produção, mas podem ser ativados para utilização experimental antes que se propaguem.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

O caminho para o diretório de raiz em que o *Host. JSON* função de ficheiros e pastas estão localizadas. Na aplicação de função, a predefinição é `%HOME%\site\wwwroot`.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o fornecedor a utilizar para armazenamento de chaves. Atualmente, os repositórios suportados são o armazenamento de BLOBs ("Blob") e o sistema de arquivos local ("ficheiros"). A predefinição é o blob na versão 2 e o sistema de ficheiros na versão 1.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|Ficheiros|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O runtime das funções do Azure utiliza esta cadeia de ligação de conta de armazenamento para todas as funções, exceto para funções de acionada por HTTP. A conta de armazenamento tem de ser um para fins gerais que suporta blobs, filas e tabelas. Ver [conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [os requisitos de conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador utilizado para o TypeScript. Pode substituir a predefinição se for necessário.

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNÇÃO\_APP\_EDITAR\_MODO

Valores válidos são "readwrite" e "só de leitura".

|Chave|Valor da amostra|
|---|------------|
|FUNÇÃO\_APP\_EDITAR\_MODO|só de leitura|

## <a name="functionsextensionversion"></a>AS FUNÇÕES\_EXTENSÃO\_VERSÃO

A versão do runtime das funções para utilizar esta aplicação de função. Um til com a versão principal significa utilizar a versão mais recente dessa versão principal (por exemplo, "~ 2"). Quando as novas versões para a versão principal estão disponíveis, serão automaticamente instaladas na function app. Para afixar a aplicação para uma versão específica, utilize o número da versão completo (por exemplo, "2.0.12345"). A predefinição é "~ 2". Um valor de `~1` fixa a sua aplicação para a versão 1.x do runtime.

|Chave|Valor da amostra|
|---|------------|
|AS FUNÇÕES\_EXTENSÃO\_VERSÃO|~ 2|

## <a name="functionsworkerruntime"></a>AS FUNÇÕES\_TRABALHO\_TEMPO DE EXECUÇÃO

O runtime de trabalho de idioma carregar na function app.  Corresponderá à linguagem a ser utilizada na sua aplicação (por exemplo, "dotnet"). Para as funções em vários idiomas será preciso publicá-los para várias aplicações, cada um com um valor de tempo de execução de trabalho correspondente.  Os valores válidos são `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), e `python` (Python).

|Chave|Valor da amostra|
|---|------------|
|AS FUNÇÕES\_TRABALHO\_TEMPO DE EXECUÇÃO|DotNet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Para apenas os planos de consumo. Cadeia de ligação para a conta de armazenamento onde o código de aplicação de função e a configuração são armazenadas. Ver [criar uma aplicação de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nome]; AccountKey = [a chave]|

## <a name="websitecontentshare"></a>WEB SITE\_CONTENTSHARE

Para apenas os planos de consumo. O caminho de ficheiro para o código de aplicação de função e a configuração. Utilizado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. A predefinição é uma cadeia exclusiva que começa com o nome da aplicação de função. Ver [criar uma aplicação de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor da amostra|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEB SITE\_MAX\_DINÂMICO\_APLICATIVO\_DIMENSIONAMENTO\_HORIZONTALMENTE

O número máximo de instâncias de que a aplicação de função pode aumentar horizontalmente para. A predefinição não é nenhum limite.

> [!NOTE]
> Esta definição é uma pré-visualização recurso - e só será confiável se definido como um valor < = 5

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_MAX\_DINÂMICO\_APLICATIVO\_DIMENSIONAMENTO\_HORIZONTALMENTE|5|

## <a name="websitenodedefaultversion"></a>WEB SITE\_NÓ\_DEFAULT_VERSION

A predefinição é "8.11.1".

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_NÓ\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEB SITE\_EXECUTE\_FROM\_PACOTE

Permite que a aplicação de funções para execução a partir de um ficheiro de pacote montada.

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_EXECUTE\_FROM\_PACOTE|1|

Os valores válidos são de qualquer um URL que é resolvido para a localização de um ficheiro de pacote de implementação, ou `1`. Quando definido como `1`, o pacote tem de constar da `d:\home\data\SitePackages` pasta. Ao utilizar a implementação de zip com esta definição, o pacote é automaticamente carregado para esta localização. Em pré-visualização, esta definição foi chamada `WEBSITE_RUN_FROM_ZIP`. Para obter mais informações, consulte [executar as suas funções a partir de um ficheiro de pacote](run-functions-from-deployment-package.md).

## <a name="azurefunctionproxydisablelocalcall"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Por predefinição, os proxies de funções utilizará um atalho para enviar as chamadas de API a partir de proxies diretamente para as funções na mesma Function App, em vez de criar um novo pedido HTTP. Esta definição permite-lhe desativar esse comportamento.

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Chamadas com um url de back-end que aponta para uma função no local a aplicação de função deixarão de ser enviada diretamente para a função e em vez disso, será direcionada para o front-end HTTP para a aplicação de funções|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este é o valor predefinido. Chamadas com um url de back-end que aponta para uma função no local a aplicação de função será reencaminhada diretamente para essa função|


## <a name="azurefunctionproxybackendurldecodeslashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta definição controla se o % 2F é descodificado como barras nos parâmetros de rota, quando eles são inseridos no URL de back-end. 

|Chave|Valor|Descrição|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parâmetros de rota com barras codificadas terão-los a descodificar. `example.com/api%2ftest` irá tornar-se `example.com/api/test`|
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


## <a name="next-steps"></a>Passos Seguintes

[Saiba como atualizar as definições da aplicação](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Ver definições globais no arquivo Host. JSON.](functions-host-json.md)

[Consulte outras definições de aplicação para aplicações de serviço de aplicações](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
