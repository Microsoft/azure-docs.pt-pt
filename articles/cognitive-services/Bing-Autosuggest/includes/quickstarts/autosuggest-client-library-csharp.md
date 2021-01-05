---
title: Bing Autosuggest C# biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 5bfe28a8111128ba7d8072c27d8647e6137620cd
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844717"
---
Começa com a biblioteca de clientes Bing Autosuggest para .NET. Siga estes passos para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Utilize a biblioteca de clientes Bing Autosuggest para .NET para obter sugestões de pesquisa com base em cadeias de consulta parciais.

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme?view=azure-dotnet)  |  [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/)  |  [Código de amostra](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, [pode criar uma gratuitamente.](https://azure.microsoft.com/free/cognitive-services)
* A versão atual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Criar variáveis de ambiente

>[!NOTE]
> Os pontos finais para recursos criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado apresentado abaixo. Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../../../cognitive-services-custom-subdomains.md)

Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para a autenticação:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: A chave de recursos para autenticar os seus pedidos.
* `AUTOSUGGEST_ENDPOINT`: O ponto final do recurso para o envio de pedidos de API. Devia ser assim: `https://<your-custom-subdomain>.api.cognitive.microsoft.com` .

Utilize as instruções para o seu sistema operativo.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

### <a name="macos"></a>[macOS](#tab/unix)

Edite a sua `.bash_profile` variável e adicione a variável ambiental:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.
***

## <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Crie uma nova aplicação .NET Core no seu editor preferido ou IDE.

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `bing-autosuggest-quickstart` . Este comando cria um projeto simples "Hello World" C# com um único ficheiro de origem: *program.cs*.

```console
dotnet new console -n bing-autosuggest-quickstart
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A partir do diretório do projeto, abra o ficheiro *program.cs* no seu editor preferido ou IDE. Adicione as `using` seguintes diretivas:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Na `Program` classe, crie variáveis para o ponto final e chave Azure do seu recurso. Se criou a variável ambiental depois de ter lançado a aplicação, terá de fechar e reabrir o editor, o IDE, ou a shell que a executa para aceder à variável.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

No método da `Main` aplicação, adicione as seguintes chamadas de método, que definirá mais tarde.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>Instalar a biblioteca do cliente

Dentro do diretório de aplicações, instale a biblioteca de clientes Bing Autosuggest para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Se estiver a utilizar o Visual Studio IDE, a biblioteca do cliente está disponível como um pacote NuGet transferível.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Bing Autosuggest para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Enviar um pedido de Autosuggest](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>Autenticar o cliente

> [!NOTE]
> Este quickstart pressupõe que [criou uma variável ambiental](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a sua chave Bing Autosuggest, nomeada , e uma para o seu ponto `AUTOSUGGEST_SUBSCRIPTION_KEY` final, chamada `AUTOSUGGEST_ENDPOINT` .


Num novo método assíncronos, instantaneamente um cliente com o seu ponto final e chave. Crie um objeto [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) com a sua chave e use-o com o seu ponto final para criar um objeto [AutosuggestClient.](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet)

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

### <a name="send-an-autosuggest-request"></a>Enviar um pedido de Autosuggest

No mesmo método, utilize o método [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) do cliente para enviar uma consulta ao Bing. Em seguida, iterate sobre a resposta [sugestões,](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) e imprima a primeira sugestão.

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

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados:

* [Eliminar um grupo de recursos no portal Azure](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Eliminar um grupo de recursos no Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial da Sugestão Automática do Bing](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Veja também

- [O que é a Sugestão Automática do Bing?](../../get-suggested-search-terms.md)
- [Bing Autosuggest referência dotnet](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme?view=azure-dotnet)