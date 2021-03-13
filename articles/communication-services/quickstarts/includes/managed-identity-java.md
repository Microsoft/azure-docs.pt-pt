---
ms.openlocfilehash: f2e4bf603fa4cfb93c7ca51f64029ccaedcff727
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021516"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Adicione identidade gerida à sua solução de Serviços de Comunicação (Java)

### <a name="install-the-client-library-packages"></a>Instale os pacotes da biblioteca do cliente
No ficheiro pom.xml, adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.4</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>Use os pacotes da biblioteca do cliente

Adicione as `import` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Communication.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.core.credential.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;
import com.azure.identity.*;

import java.io.IOException;
import java.util.*;
```

Os exemplos abaixo estão a utilizar o [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential). Esta credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` e `AZURE_TENANT_ID` variáveis ambientais são necessárias para criar um `DefaultAzureCredential` objeto. Para criar uma aplicação registada no ambiente de desenvolvimento e configurar variáveis ambientais, consulte [Autorizar o acesso com identidade gerida.](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um símbolo com Identidade Gerida

O exemplo de código que se segue mostra como criar um objeto de cliente de serviço com identidade gerida.
Em seguida, use o cliente para emitir um símbolo para um novo utilizador:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com Identidade Gerida

O seguinte exemplo de código mostra como criar um objeto de cliente de serviço com identidade gerida e, em seguida, usar o cliente para enviar uma mensagem SMS:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SmsSendResult response = smsClient.send(
               "<from-phone-number>",
               "<to-phone-number>",
               "your message"
          );

          return response;
    }
```

