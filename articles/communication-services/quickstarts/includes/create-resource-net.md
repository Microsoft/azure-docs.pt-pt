---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110899"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/dotnet/)
- A versão mais recente [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) para o seu sistema operativo.
- Obtenha a versão mais recente do [.NET Identity SDK](/dotnet/api/azure.identity).
- Obtenha a versão mais recente do [.NET Management SDK](../../concepts/sdk-options.md).

## <a name="installing-the-sdk"></a>Instalação do SDK

Em primeiro lugar, inclua o SDK de Gestão de Serviços de Comunicação no seu projeto C#:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>ID da subscrição

Precisa de saber a identificação da sua assinatura Azure. Isto pode ser adquirido no portal:

1.  Faça login na sua conta Azure
2.  Selecione subscrições na barra lateral esquerda
3.  Selecione qualquer subscrição necessária
4.  Clique em Visão Geral
5.  Selecione o seu ID de subscrição

Neste arranque rápido, assumimos que guardou o ID de subscrição numa variável ambiental chamada `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Autenticação

Para comunicar com os Serviços de Comunicação Azure, tem primeiro de se autenticar no Azure. Normalmente, fazes isto usando uma identidade principal de serviço.

### <a name="option-1-managed-identity"></a>Opção 1: Identidade gerida

Se o seu código estiver a funcionar como um serviço em Azure, a forma mais fácil de autenticar é adquirir uma identidade gerida da Azure. Saiba mais sobre [identidades geridas.](../../../active-directory/managed-identities-azure-resources/overview.md)

[Serviços Azure que suportam Identidades Geridas](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Identidade gerida atribuída pelo sistema](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Identidade gerida atribuída pelo utilizador](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

O ClientId da identidade gerida que criou deve ser passado para o `ManagedIdentityCredential` explicitamente.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Opção 2: Diretor de serviço

Em vez de utilizar uma identidade gerida, pode querer autenticar a Azure usando um principal de serviço que gere. Saiba mais utilizando documentação sobre [a criação e gestão de um diretor de serviços no Azure Ative Directory](../../../active-directory/develop/howto-create-service-principal-portal.md).

Depois de criar o seu principal serviço, terá de recolher as seguintes informações sobre o mesmo no portal Azure:

- **ID de Cliente**
- **Segredo do Cliente**
- **ID do inquilino**

Armazenar estes valores em variáveis ambientais `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` denominadas, e `AZURE_TENANT_ID` respectivamente. Pode então criar um cliente de gestão de Serviços de Comunicação como este:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Opção 3: Identidade do utilizador

Se quiser ligar para a Azure em nome de um utilizador interativo, em vez de utilizar uma identidade de serviço, pode utilizar o seguinte código para criar um cliente Azure Communication Services Management. Isto abrirá uma janela do navegador para solicitar ao utilizador as suas credenciais MSA ou AD AZure.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Gestão de Recursos de Serviços de Comunicação

### <a name="interacting-with-azure-resources"></a>Interagindo com os recursos da Azure

Agora que está autenticado, pode usar o seu cliente de gestão para fazer chamadas de API.

Para cada um dos seguintes exemplos, vamos atribuir os nossos recursos de Serviços de Comunicação a um grupo de recursos existente.

Se precisar de criar um grupo de recursos, pode fazê-lo utilizando o [portal Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md) ou o [Azure Resource Manager SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Criar e gerir um recurso de Serviços de Comunicação

A nossa instância do cliente SDK de Gestão de Serviços de Comunicação ``Azure.ResourceManager.Communication.CommunicationManagementClient`` pode ser utilizada para a realização de operações sobre recursos de Serviços de Comunicação.

#### <a name="create-a-communication-services-resource"></a>Criar um recurso do Communication Services

Ao criar um recurso de Serviços de Comunicação, especificará o nome do grupo de recursos e o nome do recurso. Note que o `Location` imóvel será sempre `global` , e durante a visualização pública o `DataLocation` valor deve ser `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Atualizar um recurso de Serviços de Comunicação

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Listar todos os recursos dos Serviços de Comunicação

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Eliminar um recurso de Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Gestão de chaves e cadeias de conexão

Todos os recursos de Serviços de Comunicação têm um par de chaves de acesso e cadeias de conexão correspondentes. Estas teclas podem ser acedidas com o SDK de gestão e depois utilizadas por outros SDKs de Serviços de Comunicação para se autenticarem nos Serviços de Comunicação da Azure.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Obtenha chaves de acesso para um recurso de Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Regenerar uma chave de acesso para um recurso de Serviços de Comunicação

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```