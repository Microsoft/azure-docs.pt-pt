---
title: Gerir a conectividade IoT Hub & mensagens confiáveis w/device SDKs
description: Saiba como melhorar a conectividade do seu dispositivo e mensagens ao utilizar os SDKs do dispositivo Azure IoT Hub
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: efd16f0e8dd6ba952b647fbfbd6f35fc05ffd4be
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615844"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Gerir conectividade e mensagens fiáveis utilizando SDKs de dispositivo Azure IoT Hub

Este artigo fornece orientações de alto nível para ajudá-lo a projetar aplicações de dispositivos que são mais resistentes. Mostra-lhe como tirar partido da conectividade e funcionalidades de mensagens confiáveis em SDKs de dispositivoS Azure IoT. O objetivo deste guia é ajudá-lo a gerir os seguintes cenários:

* Fixação de uma ligação de rede abandonada

* Alternação entre diferentes ligações de rede

* Reconectar-se devido a erros de ligação transitórios de serviço

Os detalhes da implementação podem variar por idioma. Para mais informações, consulte a documentação da API ou sDK específico:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK de Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Fiabilidade ainda não implementada)

## <a name="designing-for-resiliency"></a>Conceber para resiliência

Os dispositivos IoT dependem frequentemente de ligações de rede não contínuas ou instáveis (por exemplo, GSM ou satélite). Podem ocorrer erros quando os dispositivos interagem com serviços baseados na nuvem devido à disponibilidade intermitente de serviços e a falhas ao nível da infraestrutura ou a falhas transitórias. Uma aplicação que funciona num dispositivo tem de gerir os mecanismos de ligação, reencamamento e a lógica de reedição para o envio e receção de mensagens. Além disso, os requisitos de estratégia de reação dependem fortemente do cenário IoT do dispositivo, contexto, capacidades.

Os SDKs do dispositivo Azure IoT Hub visam simplificar a ligação e comunicação de nuvem-a-dispositivo e dispositivo-a-nuvem. Estes SDKs fornecem uma forma robusta de se conectar ao Azure IoT Hub e um conjunto abrangente de opções para o envio e receção de mensagens. Os desenvolvedores também podem modificar a implementação existente para personalizar uma melhor estratégia de relagem para um determinado cenário.

As funcionalidades SDK relevantes que suportam a conectividade e mensagens fiáveis estão cobertas nas seguintes secções.

## <a name="connection-and-retry"></a>Ligação e retíria

Esta secção fornece uma visão geral dos padrões de reenvolvição e retíria disponível na gestão de ligações. Detalha as orientações de implementação para a utilização de uma política de relemis diferente na aplicação do dispositivo e lista as APIs relevantes dos SDKs do dispositivo.

### <a name="error-patterns"></a>Padrões de erro

Falhas de ligação podem acontecer a vários níveis:

* Erros de rede: erros de tomada e resolução de nomes desligados

* Erros ao nível do protocolo para o transporte HTTP, AMQP e MQTT: ligações separadas ou sessões caducadas

* Erros ao nível da aplicação que resultam de erros locais: credenciais inválidas ou comportamento de serviço (por exemplo, excedendo a quota ou estrangulamento)

Os SDKs do dispositivo detetam erros nos três níveis. Erros e erros de hardware relacionados com os SO não são detetados e tratados pelos SDKs do dispositivo. O design SDK baseia-se na [Orientação de Manuseamento de Falhas Transitórias](/azure/architecture/best-practices/transient-faults#general-guidelines) do Centro de Arquitetura Azure.

### <a name="retry-patterns"></a>Padrões de relíndi

Os seguintes passos descrevem o processo de retenção quando são detetados erros de ligação:

1. O SDK deteta o erro e o erro associado na rede, protocolo ou aplicação.

2. O SDK utiliza o filtro de erro para determinar o tipo de erro e decide se é necessária uma nova tentativa.

3. Se o SDK identificar um **erro irrecuperável,** operações como ligação, envio e receção são interrompidas. O SDK notifica o utilizador. Exemplos de erros irrecuperáveis incluem um erro de autenticação e um erro de ponto final.

4. Se o SDK identificar um **erro recuperável,** retrai-se de acordo com a política de relemisão especificada até que o tempo limite definido desdufndido.  Note que o SDK usa **back-off exponencial com** política de retenção de nervosismo por padrão.
5. Quando o tempo limite definido expira, o SDK para de tentar ligar ou enviar. Notifica o utilizador.

6. O SDK permite ao utilizador anexar uma chamada para receber alterações no estado de ligação.

Os SDKs fornecem três políticas de repetição:

* **Recuo exponencial com nervosismo**: Esta política de retrocesso por defeito tende a ser agressiva no início e a abrandar ao longo do tempo até atingir um atraso máximo. O design baseia-se na [orientação retry do Azure Architecture Center.](/azure/architecture/best-practices/retry-service-specific) 

* **Ensaio personalizado**: Para alguns idiomas SDK, você pode desenhar uma política de relívia personalizada que seja mais adequada para o seu cenário e, em seguida, injetá-la na RetryPolicy. A redação personalizada não está disponível no C SDK, e não é atualmente suportada no Python SDK. O Python SDK reconecta-se conforme necessário.

* **Não redatória**: Pode reformular a política para "não voltar a tentar", o que desativa a lógica de re-tentar. O SDK tenta ligar uma vez e enviar uma mensagem uma vez, assumindo que a ligação está estabelecida. Esta política é normalmente usada em cenários com preocupações de largura de banda ou custos. Se escolher esta opção, as mensagens que não enviam perdem-se e não podem ser recuperadas.

### <a name="retry-policy-apis"></a>APIs de política de retíria

   | SDK | Método SetRetryPolicy | Implementações de políticas | Orientação de implementação |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Padrão**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizado:** use [a retrippolitidade](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponível<BR>**Sem reação:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementação C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy)        | **Padrão**: [Classe ExponencialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizado:** implementar [a interface RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Sem reação:** [Classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementação de Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DispositivoClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy) | **Padrão**: [Classe ExponencialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff)<BR>**Personalizado:** implementar [interface IRetryPolicy](/dotnet/api/microsoft.azure.devices.client.iretrypolicy)<BR>**Sem reação:** [Classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry) | [Implementação C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nó| [setRetryPolicy](/javascript/api/azure-iot-device/client) | **Padrão**: [Classe ExponencialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [Implementação do nó](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Atualmente, não é suportado | Atualmente, não é suportado | Atualmente, não é suportado |

As seguintes amostras de código ilustram este fluxo:

#### <a name="net-implementation-guidance"></a>.NET orientação de implementação

A seguinte amostra de código mostra como definir e definir a política de repetição predefinida:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Para evitar uma utilização elevada do CPU, as retrações são estranguladas se o código falhar imediatamente. Por exemplo, quando não há rede ou rota para o destino. O tempo mínimo para executar a próxima repetição é de 1 segundo.

Se o serviço responder com um erro de estrangulamento, a política de retenção é diferente e não pode ser alterada através da API pública:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

O mecanismo de retíria para depois de `DefaultOperationTimeoutInMilliseconds` , que está atualmente definido em 4 minutos.

#### <a name="other-languages-implementation-guidance"></a>Orientação de implementação de outras línguas

Para amostras de código noutras línguas, reveja os seguintes documentos de implementação. O repositório contém amostras que demonstram o uso de APIs de política de repetição.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [SDK de Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Fiabilidade ainda não implementada)

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o dispositivo e SDKs de serviço](./iot-hub-devguide-sdks.md)

* [Utilizar o SDK do dispositivo de IoT para C](./iot-hub-device-sdk-c-intro.md)

* [Desenvolver para dispositivos restritos](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Desenvolver para dispositivos móveis](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Desconexões de dispositivos de resolução de problemas](iot-hub-troubleshoot-connectivity.md)