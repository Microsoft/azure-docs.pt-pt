---
ms.openlocfilehash: 07a8d792bbb17df1401b5892b09fb7ff2f5f8e52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629427"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- A versão mais recente [.NET Core biblioteca de clientes](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo.
- Um recurso ativo dos Serviços de Comunicação e cadeia de ligação. [Criar um recurso de Serviços de Comunicação.](../../create-communication-resource.md)

### <a name="prerequisite-check"></a>Verificação pré-requisito

- Numa janela de terminal ou comando, verifique `dotnet` se a biblioteca do cliente .NET está instalada.

## <a name="setting-up"></a>Configuração

### <a name="create-a-new-c-application"></a>Criar uma nova aplicação C#

Numa janela de consola (como cmd, PowerShell ou Bash), utilize o `dotnet new` comando para criar uma nova aplicação de consola com o nome `PhoneNumbersQuickstart` . Este comando cria um projeto "Hello World" C# com um único ficheiro de origem: **Programa.cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Mude o seu diretório para a pasta de aplicação recém-criada e use o `dotnet build` comando para compilar a sua aplicação.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca cliente Azure Communication PhoneNumbers para o pacote .NET utilizando o `dotnet add package` comando.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.5
```

Adicione uma `using` diretiva ao topo do **Programa.cs** para incluir os espaços de nome.

```csharp
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
using Azure.Communication.PhoneNumbers.Models;
```

Atualizar `Main` a assinatura da função para ser async.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Os clientes de número de telefone podem ser autenticados usando uma cadeia de ligação adquirida a partir de um Azure Communication Resources no [Portal Azure][azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

Os clientes de Número de Telefone também têm a opção de autenticar com a Autenticação do Diretório Ativo Azure. Com esta opção, `AZURE_CLIENT_SECRET` `AZURE_CLIENT_ID` é necessário `AZURE_TENANT_ID` configurar variáveis ambientais para a autenticação.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Gerir números de telefone

### <a name="search-for-available-phone-numbers"></a>Procurar números de telefone disponíveis

Para comprar números de telefone, primeiro deve procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, [as capacidades de número de telefone,](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services) [o tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)e a quantidade. Note que para o tipo de número de telefone gratuito, desde que o código de área seja opcional.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Comprar números de telefone

O resultado da procura de números de telefone é um `PhoneNumberSearchResult` . Isto contém um `SearchId` que pode ser passado para os números de compra API para adquirir os números na pesquisa. Note que ligar para os números de telefone de compra API resultará num encargo para a sua Conta Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionAsync();
```

### <a name="get-phone-numbers"></a>Obter números de telefone(s)

Depois de um número de compra, pode recuperá-lo do cliente.
```csharp
var getPhoneNumberResponse = await client.GetPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Também pode recuperar todos os números de telefone adquiridos.
``` csharp
var purchasedPhoneNumbers = client.GetPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Atualizar as capacidades dos números de telefone

Com um número adquirido, pode atualizar as capacidades.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Liberação do número de telefone

Pode libertar um número de telefone comprado.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionAsync();
````

## <a name="run-the-code"></a>Executar o código

Executar o pedido do seu diretório de candidaturas com o `dotnet run` comando.

```console
dotnet run
```

## <a name="sample-code"></a>Código de Exemplo

Você pode baixar o aplicativo de amostra do [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)
