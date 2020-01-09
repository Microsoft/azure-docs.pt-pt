---
title: 'Início rápido: biblioteca de cliente do Sugestão Automática do Bing para .NET | Microsoft Docs'
description: Comece a usar a biblioteca de cliente do Sugestão Automática do Bing para .NET e obtenha sugestões de pesquisa com base em cadeias de caracteres de consulta parciais.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473928"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Início rápido: biblioteca de cliente do Sugestão Automática do Bing para .NET

Introdução à biblioteca de cliente do Sugestão Automática do Bing para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente Sugestão Automática do Bing para .NET para obter sugestões de pesquisa com base em cadeias de caracteres de consulta parciais.

[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | o NuGet ( [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [pacote)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [código de exemplo](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-an-azure-resource"></a>Criar um recurso do Azure

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos de não avaliação criados após 1º de julho de 2019 usam o formato de subdomínio personalizado mostrado abaixo. Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando a chave e o ponto de extremidade do recurso que você criou, crie duas variáveis de ambiente para autenticação:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`-a chave de recurso para autenticar suas solicitações.
* `AUTOSUGGEST_ENDPOINT`-o ponto de extremidade de recurso para enviar solicitações de API. Ele terá a seguinte aparência: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Use as instruções para seu sistema operacional.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile`e adicione a variável de ambiente:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

### <a name="create-a-new-c-application"></a>Criar um novo C# aplicativo

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido. 

Em uma janela de console (como cmd, PowerShell ou bash), use o comando `dotnet new` para criar um novo aplicativo de console com o nome `bing-autosuggest-quickstart`. Este comando cria um projeto simples de " C# Olá, mundo" com um único arquivo de origem: *Program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no seu editor ou IDE preferido. Adicione as seguintes diretivas de `using`:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Na classe `Program`, crie variáveis para o ponto de extremidade e a chave do Azure do recurso. Se você criou a variável de ambiente depois de iniciar o aplicativo, será necessário fechar e reabrir o editor, IDE ou shell que o executa para acessar a variável.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

No método de `Main` do aplicativo, adicione as seguintes chamadas de método, que você definirá mais tarde.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de cliente

No diretório do aplicativo, instale a biblioteca de cliente do Sugestão Automática do Bing para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de cliente estará disponível como um pacote NuGet baixável.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código mostram como realizar as seguintes tarefas com a biblioteca de cliente Sugestão Automática do Bing para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar uma solicitação de sugestão automática](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este início rápido pressupõe que você [criou uma variável de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para sua chave de sugestão automática do Bing, chamada `AUTOSUGGEST_SUBSCRIPTION_KEY`e outra para o ponto de extremidade chamado `AUTOSUGGEST_ENDPOINT`.


Em um novo método assíncrono, crie uma instância de um cliente com o ponto de extremidade e a chave. Crie um objeto [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) com sua chave e use-o com seu ponto de extremidade para criar um objeto [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) .

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Enviar uma solicitação de sugestão automática

No mesmo método, use o método [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) do cliente para enviar uma consulta ao Bing. Em seguida, itere sobre a resposta de [sugestões](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) e imprima a primeira sugestão.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Executar a aplicação

Execute o aplicativo no diretório do aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial da Sugestão Automática do Bing](../tutorials/autosuggest.md)

## <a name="see-also"></a>Ver também

- [O que é a Sugestão Automática do Bing?](../get-suggested-search-terms.md)
- [Sugestão Automática do Bing referência dotnet](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
