---
ms.openlocfilehash: 3ec650f82b95cdfabc923e92325959c63f2846de
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110765"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Adicionar identidade gerida à sua solução de Serviços de Comunicação (JS)

### <a name="install-the-sdk-packages"></a>Instalar os pacotes SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Utilize os pacotes SDK

Adicione as `import` seguintes diretivas ao seu código para utilizar os SDKs de armazenamento Azure Identity e Azure.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

Para registar a aplicação no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um símbolo com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para emitir um símbolo para um novo utilizador:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para enviar uma mensagem SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
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

