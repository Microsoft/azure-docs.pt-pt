---
ms.openlocfilehash: c1b74b43c6ef884c68282dcaaae8dfc9a5541453
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621993"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerida à sua solução de Serviços de Comunicação (.NET)

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione as `using` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` e `AZURE_TENANT_ID` variáveis ambientais são necessárias para criar um `DefaultAzureCredential` objeto. Para criar uma aplicação registada no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida.](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um símbolo com Identidade Gerida

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com fichas do Azure Ative Directory.

Em seguida, use o cliente para emitir um símbolo para um novo utilizador:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço SMS com identidade gerida e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

