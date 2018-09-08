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
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 9f6746f1bf8fb65e39933afa00b74a2b8266a1a9
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095441"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicação para as funções do Azure

Definições da aplicação na aplicação de função contém opções de configuração globais que afetam todas as funções para essa aplicação de função. Ao executar localmente, estas definições estão em variáveis de ambiente. Este artigo lista as definições da aplicação que estão disponíveis em aplicações de funções.

[! INCLUIR [definições de função de aplicações] (... /.. /includes/Functions-App-Settings.md]

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

Especifica o repositório ou o fornecedor a utilizar para armazenamento de chaves. Atualmente, os repositórios suportados são blob ("Blob") e o sistema de ficheiros ("desativado"). A predefinição é o sistema de ficheiros ("desativado").

|Chave|Valor da amostra|
|---|------------|
|AzureWebJobsSecretStorageType|desativado|

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

A versão do runtime das funções do Azure para utilizar esta aplicação de função. Um til com a versão principal significa utilizar a versão mais recente dessa versão principal (por exemplo, "~ 1"). Quando as novas versões para a versão principal estão disponíveis, serão automaticamente instaladas na function app. Para afixar a aplicação para uma versão específica, utilize o número da versão completo (por exemplo, "1.0.12345"). A predefinição é "~ 1".

|Chave|Valor da amostra|
|---|------------|
|AS FUNÇÕES\_EXTENSÃO\_VERSÃO|~1|

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
> Esta definição destina-se uma funcionalidade de pré-visualização.

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_MAX\_DINÂMICO\_APLICATIVO\_DIMENSIONAMENTO\_HORIZONTALMENTE|10|

## <a name="websitenodedefaultversion"></a>WEB SITE\_NÓ\_DEFAULT_VERSION

A predefinição é "6.5.0".

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_NÓ\_DEFAULT_VERSION|6.5.0|

## <a name="websiterunfromzip"></a>WEB SITE\_EXECUTE\_FROM\_ZIP

Permite que a aplicação de funções para execução a partir de um ficheiro de pacote montada.

> [!NOTE]
> Esta definição destina-se uma funcionalidade de pré-visualização.

|Chave|Valor da amostra|
|---|------------|
|WEB SITE\_EXECUTE\_FROM\_ZIP|1|

Os valores válidos são de qualquer um URL que é resolvido para a localização de um ficheiro de pacote de implementação, ou `1`. Quando definido como `1`, o pacote tem de constar da `d:\home\data\SitePackages` pasta. Ao utilizar a implementação de zip com esta definição, o pacote é automaticamente carregado para esta localização.  Para obter mais informações, consulte [executar as suas funções a partir de um ficheiro de pacote](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>Passos Seguintes

[Saiba como atualizar as definições da aplicação](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Ver definições globais no arquivo Host. JSON.](functions-host-json.md)

[Consulte outras definições de aplicação para aplicações de serviço de aplicações](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
