---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657676"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerida à sua solução de Serviços de Comunicação (.Net)

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione as `using` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` e `AZURE_TENANT_ID` variáveis ambientais são necessárias para criar um `DefaultAzureCredential` objeto. Para criar uma aplicação registada no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida.](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um símbolo com Identidade Gerida

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com fichas do Azure Ative Directory.

Em seguida, use o cliente para emitir um símbolo para um novo utilizador:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de serviço SMS com identidade gerida e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre a autenticação](../concepts/authentication.md)

Também pode querer:

- [Saiba mais sobre o controlo de acesso baseado em funções da Azure](../../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criação de fichas de acesso ao utilizador](../../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../telephony-sms/send.md)
- [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
