---
title: Como gerenciar conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure
description: Saiba como melhorar a conectividade do dispositivo e as mensagens ao usar os SDKs do dispositivo do Hub IoT do Azure
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: b5fe47bf066568960f9819a780a1281bedd1902b
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999994"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Gerenciar conectividade e mensagens confiáveis usando SDKs de dispositivo do Hub IoT do Azure

Este artigo fornece diretrizes de alto nível para ajudá-lo a criar aplicativos de dispositivo que são mais resilientes. Ele mostra como aproveitar os recursos de conectividade e de mensagens confiáveis nos SDKs do dispositivo IoT do Azure. O objetivo deste guia é ajudá-lo a gerenciar os seguintes cenários:

* Corrigindo uma conexão de rede descartada

* Alternando entre diferentes conexões de rede

* Reconectando devido a erros de conexão transitória do serviço

Os detalhes da implementação podem variar por idioma. Para obter mais informações, consulte a documentação da API ou o SDK específico:

* [SDK do C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK do node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [SDK do Python](https://github.com/Azure/azure-iot-sdk-python) (Confiabilidade ainda não implementada)

## <a name="designing-for-resiliency"></a>Conceber para resiliência

Os dispositivos IoT geralmente dependem de conexões de rede não contínuas ou instáveis (por exemplo, GSM ou satélite). Os erros podem ocorrer quando os dispositivos interagem com serviços baseados em nuvem devido à disponibilidade intermitente do serviço e às falhas transitórias ou em nível de infraestrutura. Um aplicativo que é executado em um dispositivo precisa gerenciar os mecanismos para conexão, reconexão e a lógica de repetição para enviar e receber mensagens. Além disso, os requisitos de estratégia de repetição dependem muito do cenário de IoT do dispositivo, do contexto, dos recursos.

Os SDKs de dispositivo do Hub IoT do Azure visam simplificar a conexão e a comunicação da nuvem para o dispositivo e do dispositivo para a nuvem. Esses SDKs fornecem uma maneira robusta de se conectar ao Hub IoT do Azure e um conjunto abrangente de opções para enviar e receber mensagens. Os desenvolvedores também podem modificar a implementação existente para personalizar uma estratégia de repetição melhor para um determinado cenário.

Os recursos relevantes do SDK que dão suporte à conectividade e ao sistema de mensagens confiável são abordados nas seções a seguir.

## <a name="connection-and-retry"></a>Conexão e tentar novamente

Esta seção fornece uma visão geral dos padrões de reconexão e de repetição disponíveis ao gerenciar conexões. Ele detalha as diretrizes de implementação para usar uma política de repetição diferente no aplicativo do dispositivo e lista as APIs relevantes dos SDKs do dispositivo.

### <a name="error-patterns"></a>Padrões de erro

As falhas de conexão podem ocorrer em muitos níveis:

* Erros de rede: erros de soquete e resolução de nome desconectados

* Erros de nível de protocolo para transporte HTTP, AMQP e MQTT: links desanexados ou sessões expiradas

* Erros de nível de aplicativo que resultam de erros locais: credenciais inválidas ou comportamento de serviço (por exemplo, excedendo a cota ou limitação)

Os SDKs de dispositivo detectam erros em todos os três níveis. Erros relacionados ao so e erros de hardware não são detectados e manipulados pelos SDKs do dispositivo. O design do SDK é baseado nas [diretrizes de tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults#general-guidelines) do centro de arquitetura do Azure.

### <a name="retry-patterns"></a>Padrões de repetição

As etapas a seguir descrevem o processo de repetição quando erros de conexão são detectados:

1. O SDK detecta o erro e o erro associado na rede, no protocolo ou no aplicativo.

2. O SDK usa o filtro de erro para determinar o tipo de erro e decidir se é necessária uma nova tentativa.

3. Se o SDK identificar um **erro irrecuperável**, operações como conexão, envio e recebimento serão interrompidas. O SDK notifica o usuário. Exemplos de erros irrecuperáveis incluem um erro de autenticação e um erro de ponto de extremidade insatisfatório.

4. Se o SDK identificar um **erro recuperável**, ele tentará de acordo com a política de repetição especificada até que o tempo limite definido decorra.  Observe que o SDK usa **retirada exponencial com a** política de repetição de variação por padrão.
5. Quando o tempo limite definido expira, o SDK para de tentar se conectar ou enviar. Ele notifica o usuário.

6. O SDK permite que o usuário anexe um retorno de chamada para receber alterações de status de conexão.

Os SDKs fornecem três políticas de repetição:

* **Retirada exponencial com tremulação**: Essa política de repetição padrão tende a ser agressiva no início e fica mais lenta ao longo do tempo até atingir um atraso máximo. O design é baseado nas [diretrizes de repetição da centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Repetição personalizada**: Para algumas linguagens de SDK, você pode criar uma política de repetição personalizada mais adequada para seu cenário e, em seguida, injeta-la no RetryPolicy. A repetição personalizada não está disponível no SDK do C.

* **Sem repetição**: Você pode definir a política de repetição como "sem repetição", que desabilita a lógica de repetição. O SDK tenta se conectar uma vez e envia uma mensagem uma vez, supondo que a conexão seja estabelecida. Essa política é normalmente usada em cenários com largura de banda ou preocupações de custo. Se você escolher essa opção, as mensagens que falharem no envio serão perdidas e não poderão ser recuperadas.

### <a name="retry-policy-apis"></a>APIs de política de repetição

   | SDK | Método SetRetryPolicy | Implementações de política | Orientações de implementação |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Padrão**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizado:** usar [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponíveis<BR>**Sem repetição:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementação de C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Padrão**: [Classe ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizado:** implementar a [interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Sem repetição:** [Classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementação de Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Padrão**: [Classe ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizado:** implementar a [interface IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Sem repetição:** [Classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#implementação](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nó| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Padrão**: [Classe ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizado:** implementar a [interface RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Sem repetição:** [Classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementação de nó](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Brevemente | Brevemente | Brevemente

Os exemplos de código a seguir ilustram esse fluxo:

#### <a name="net-implementation-guidance"></a>Diretrizes de implementação do .NET

O exemplo de código a seguir mostra como definir e definir a política de repetição padrão:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Para evitar o alto uso da CPU, as novas tentativas serão limitadas se o código falhar imediatamente. Por exemplo, quando não há rede ou rota para o destino. O tempo mínimo para executar a próxima tentativa é de 1 segundo.

Se o serviço responder com um erro de limitação, a política de repetição será diferente e não poderá ser alterada por meio da API pública:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

O mecanismo de repetição é `DefaultOperationTimeoutInMilliseconds`interrompido após, que está definido atualmente como 4 minutos.

#### <a name="other-languages-implementation-guidance"></a>Diretrizes de implementação de outras linguagens

Para obter exemplos de código em outros idiomas, examine os documentos de implementação a seguir. O repositório contém exemplos que demonstram o uso de APIs de política de repetição.

* [SDK do C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK do node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Passos Seguintes

* [Utilizar o dispositivo e SDKs de serviço](./iot-hub-devguide-sdks.md)

* [Utilizar o SDK do dispositivo IoT para C](./iot-hub-device-sdk-c-intro.md)

* [Desenvolver para dispositivos restritos](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Desenvolver para dispositivos móveis](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Solucionar problemas de desconexões de dispositivo](iot-hub-troubleshoot-connectivity.md)
