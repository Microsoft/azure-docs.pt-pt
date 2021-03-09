---
ms.openlocfilehash: a51121b9dd9c7dcb894399fd9ad5f49cc5e07f3a
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486614"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Adicionar identidade gerida à sua solução de Serviços de Comunicação (JS)

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione as `import` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient } from "@azure/communication-sms";
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

Para registar a aplicação no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um símbolo com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para emitir um símbolo para um novo utilizador:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para enviar uma mensagem SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

