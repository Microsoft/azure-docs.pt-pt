---
title: Gerir a conectividade IoT Hub & sDKs de mensagens fiáveis c/dispositivo
description: Saiba como melhorar a conectividade e as mensagens do seu dispositivo ao utilizar os SDKs do dispositivo Azure IoT Hub
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 1ca7219824a00a5af0bed7d42da75fc06ce2010d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284450"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Gerir a conectividade e mensagens fiáveis utilizando sDKs do dispositivo Azure IoT Hub

Este artigo fornece orientações de alto nível para ajudá-lo a projetar aplicações de dispositivos mais resistentes. Mostra como aproveitar a conectividade e funcionalidades de mensagens fiáveis em SDKs do dispositivo Azure IoT. O objetivo deste guia é ajudá-lo a gerir os seguintes cenários:

* Fixação de uma ligação de rede abandonada

* Alternar entre diferentes ligações de rede

* Reconectar-se devido a erros de ligação transitórios de serviço

Os detalhes da implementação podem variar de acordo com a linguagem. Para mais informações, consulte a documentação da API ou o SDK específico:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK de Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Fiabilidade ainda não implementada)

## <a name="designing-for-resiliency"></a>Conceber para resiliência

Os dispositivos IoT dependem frequentemente de ligações de rede não contínuas ou instáveis (por exemplo, GSM ou satélite). Erros podem ocorrer quando os dispositivos interagem com serviços baseados na nuvem devido à disponibilidade intermitente de serviços e falhas de nível de infraestrutura ou transitórias. Uma aplicação que funciona num dispositivo tem de gerir os mecanismos de ligação, reconexão e a lógica de retry para o envio e receção de mensagens. Além disso, os requisitos de estratégia de retry dependem fortemente do cenário ioT do dispositivo, contexto, capacidades.

Os SDKs do dispositivo Azure IoT Hub visam simplificar a ligação e comunicação de cloud-to-device e dispositivo-para-nuvem. Estes SDKs fornecem uma forma robusta de ligar ao Azure IoT Hub e um conjunto abrangente de opções para enviar e receber mensagens. Os desenvolvedores também podem modificar a implementação existente para personalizar uma melhor estratégia de retry para um determinado cenário.

As funcionalidades sdk relevantes que suportam conectividade e mensagens fiáveis estão cobertas nas seguintes secções.

## <a name="connection-and-retry"></a>Conexão e retentativa

Esta secção dá uma visão geral dos padrões de reconexão e de retenção disponíveis na gestão das ligações. Detalha a orientação de implementação para a utilização de uma política de retry diferente na aplicação do dispositivo e lista APIs relevantes dos SDKs do dispositivo.

### <a name="error-patterns"></a>Padrões de erro

Falhas de ligação podem ocorrer a vários níveis:

* Erros de rede: erros de tomada desligados e resolução de nomes

* Erros de nível protocole para transporte http, AMQP e MQTT: ligações separadas ou sessões caducadas

* Erros de nível de aplicação resultantes de erros locais: credenciais inválidas ou comportamento de serviço (por exemplo, exceder a quota ou estrangular)

Os SDKs do dispositivo detetam erros nos três níveis. Os erros relacionados com o OS e os erros de hardware não são detetados e tratados pelos SDKs do dispositivo. O design SDK baseia-se na [Orientação transitória](/azure/architecture/best-practices/transient-faults#general-guidelines) de manipulação de falhas do Centro de Arquitetura Azure.

### <a name="retry-patterns"></a>Padrões de retry

Os seguintes passos descrevem o processo de retry quando são detetados erros de ligação:

1. O SDK deteta o erro e o erro associado na rede, protocolo ou aplicação.

2. O SDK utiliza o filtro de erro para determinar o tipo de erro e decidir se é necessário um novo recurso.

3. Se o SDK identificar um **erro não recuperável,** operações como ligação, envio e receção são interrompidas. O SDK notifica o utilizador. Exemplos de erros não recuperáveis incluem um erro de autenticação e um erro de ponto final.

4. Se o SDK identificar um **erro recuperável,** retenta de acordo com a política de retry especificada até que o tempo limite definido decorrido.  Note que o SDK usa **back-off exponencial com** política de retry de nervos por padrão.
5. Quando o tempo definido expira, o SDK para de tentar ligar ou enviar. Notifica o utilizador.

6. O SDK permite ao utilizador anexar uma chamada para receber alterações no estado de ligação.

Os SDKs fornecem três políticas de retenção:

* **Recuo exponencial com nervosismo**: Esta política de retry padrão tende a ser agressiva no início e abrandar ao longo do tempo até atingir um atraso máximo. O design baseia-se na [orientação de Retry do Azure Architecture Center.](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) 

* **Retry personalizado**: Para alguns idiomas SDK, você pode desenhar uma política de retry personalizada que é mais adequada para o seu cenário e, em seguida, injetá-la na RetryPolicy. A retry personalizada não está disponível no C SDK, e não é atualmente suportada no Python SDK. O Python SDK reconecta-se conforme necessário.

* **Sem retry**: Pode definir a política de retry para "no retry", que desativa a lógica de retry. O SDK tenta ligar-se uma vez e enviar uma mensagem uma vez, assumindo que a ligação está estabelecida. Esta política é normalmente utilizada em cenários com largura de banda ou preocupações de custos. Se escolher esta opção, as mensagens que não enviam perdem-se e não podem ser recuperadas.

### <a name="retry-policy-apis"></a>APIs de política de retry

   | SDK | Método SetRetryPolicy | Implementações políticas | Orientação de implementação |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Predefinição**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizado:** use [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponível<BR>**Sem retry:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementação de C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [Política de SetRetry](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Padrão**: [ExponencialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizado:** implementar [interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Sem retry:** [Classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementação de Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Padrão**: [Aula exponencial backoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizado:** implementar [interface IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Sem retry:** [Classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementação de C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nó| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Padrão**: [ExponencialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizado:** implementar [interface RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Sem retry:** [Classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementação do nó](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Atualmente, não é suportado | Atualmente, não é suportado | Atualmente, não é suportado |

As seguintes amostras de código ilustram este fluxo:

#### <a name="net-implementation-guidance"></a>.NET orientação de implementação

A seguinte amostra de código mostra como definir e definir a política de retry padrão:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Para evitar uma utilização elevada do CPU, as repetições são estranguladas se o código falhar imediatamente. Por exemplo, quando não há rede ou rota para o destino. O tempo mínimo para executar a próxima tentativa é de 1 segundo.

Se o serviço responder com um erro de estrangulamento, a política de retry é diferente e não pode ser alterada através da API pública:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

O mecanismo de retry para depois `DefaultOperationTimeoutInMilliseconds`, que está atualmente definido em 4 minutos.

#### <a name="other-languages-implementation-guidance"></a>Orientação de implementação de outras línguas

Para amostras de código noutras línguas, reveja os seguintes documentos de implementação. O repositório contém amostras que demonstram a utilização de APIs políticas de retry.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK de Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o dispositivo e SDKs de serviço](./iot-hub-devguide-sdks.md)

* [Utilizar o SDK do dispositivo IoT para C](./iot-hub-device-sdk-c-intro.md)

* [Desenvolver para dispositivos restritos](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Desenvolver para dispositivos móveis](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Desligações do dispositivo de resolução de problemas](iot-hub-troubleshoot-connectivity.md)
