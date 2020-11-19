---
title: Resolução de problemas nos Serviços de Comunicação da Azure
description: Saiba como recolher as informações necessárias para resolver a sua solução de Serviços de Comunicação.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 775075765c8c8eaa94541c0f094c1f7743fe59d9
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886792"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Resolução de problemas nos Serviços de Comunicação da Azure

Este documento irá ajudá-lo a resolver problemas que poderá experimentar dentro da sua solução de Serviços de Comunicação. Se estiver a resolver problemas de SMS, pode [ativar o relatório de entrega com](../quickstarts/telephony-sms/handle-sms-events.md) a Grade de Eventos para capturar detalhes de entrega de SMS.

## <a name="getting-help"></a>Obter ajuda

Encorajamos os desenvolvedores a apresentarem perguntas, sugerirem funcionalidades e reportarem problemas como problemas no [repositório](https://github.com/Azure/communication)dos Serviços de Comunicação GitHub. Outros fóruns incluem:

* [Perguntas e Respostas da Microsoft](/answers/questions/topics/single/101418.html)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Dependendo do seu plano de [suporte à](https://azure.microsoft.com/support/plans/) subscrição Azure, poderá enviar um bilhete de apoio diretamente através do [portal Azure.](https://azure.microsoft.com/support/create-ticket/)

Para ajudá-lo a resolver determinados tipos de problemas, pode ser-lhe pedido qualquer uma das seguintes informações:

* **MS-CV ID**: Este ID é usado para resolver chamadas e mensagens de resolução de problemas. 
* **ID de chamada**: Este ID é usado para identificar chamadas de Serviços de Comunicação.
* **ID de mensagem SMS**: Este ID é utilizado para identificar mensagens SMS.
* **Registos de** chamadas : Estes registos contêm informações detalhadas que podem ser usadas para resolver problemas de chamadas e problemas de rede.


## <a name="access-your-ms-cv-id"></a>Aceda ao seu ID MS-CV

O ID MS-CV pode ser acedido configurando diagnósticos na instância do `clientOptions` objeto ao rubricar as bibliotecas do seu cliente. Os diagnósticos podem ser configurados para qualquer uma das bibliotecas de clientes Azure, incluindo chat, administração e chamada VoIP.

### <a name="client-options-example"></a>Exemplo de opções de cliente

Os seguintes fragmentos de código demonstram a configuração do diagnóstico. Quando as bibliotecas do cliente forem utilizadas com diagnósticos ativados, os detalhes de diagnóstico serão emitidos ao ouvinte do evento configurado:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Aceda ao seu ID de chamada

Ao apresentar um pedido de apoio através do portal Azure relacionado com questões de chamadas, poderá ser-lhe pedido que forneça identificação da chamada a que se refere. Isto pode ser acedido através da biblioteca do cliente chamador:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Aceda ao seu ID de mensagem SMS

Para problemas de SMS, pode recolher o ID da mensagem a partir do objeto de resposta.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="enable-and-access-call-logs"></a>Ativar e aceder a registos de chamadas




# <a name="javascript"></a>[JavaScript](#tab/javascript)

O seguinte código pode ser usado para configurar `AzureLogger` registos de saída para a consola utilizando a biblioteca cliente JavaScript:

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Ao desenvolver para iOS, os seus registos são armazenados em `.blog` ficheiros. Note que não pode ver os registos diretamente porque estão encriptados.

Estes podem ser acedidos abrindo Xcode. Aceda a dispositivos > Windows e simuladores > dispositivos. Selecione o seu dispositivo. Em Aplicações Instaladas, selecione a sua aplicação e clique em "Descarregar o recipiente". 

Isto vai dar-lhe um `xcappdata` ficheiro. Clique com o botão direito neste ficheiro e selecione "Mostrar conteúdo do pacote". Em seguida, verá os `.blog` ficheiros que pode anexar ao seu pedido de suporte Azure.

# <a name="android"></a>[Android](#tab/android)

Ao desenvolver para Android, os seus registos são armazenados em `.blog` ficheiros. Note que não pode ver os registos diretamente porque estão encriptados.

No Android Studio, navegue para o Explorador de Ficheiros de Dispositivos selecionando Ver > Ferramenta Windows > Device File Explorer a partir do simulador e do dispositivo. O `.blog` ficheiro será localizado dentro do diretório da sua aplicação, que deve parecer algo como `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Pode anexar este ficheiro ao seu pedido de apoio. 
   

---


## <a name="related-information"></a>Informações relacionadas
- [Registos e diagnósticos](logging-and-diagnostics.md)
- [Métricas](metrics.md)