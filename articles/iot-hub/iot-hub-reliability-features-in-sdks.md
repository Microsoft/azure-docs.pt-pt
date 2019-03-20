---
title: Como gerir a conectividade e o sistema de mensagens confiável através de SDKs de dispositivo do IoT Hub do Azure
description: Saiba como melhorar a conectividade do dispositivo e de mensagens ao utilizar os SDKs do dispositivo do IoT Hub do Azure
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 72a30e86047dc79f8014ce4c6a38e5e370e1aab5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074935"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Gerir a conectividade e o sistema de mensagens confiável através de SDKs de dispositivo do IoT Hub do Azure

Este artigo fornece orientações de alto nível para ajudar a criar aplicativos de dispositivos que são mais flexíveis. Mostra como tirar partido das funcionalidades de mensagens fiáveis e conectividade no Azure IoT device SDKs. O objetivo deste guia é ajudar a gerir os seguintes cenários:

- Corrigir ligação de rede
- Alternar entre diferentes conexões de redes
- Restabelecer ligação devido a erros de ligação transitória do serviço

Detalhes de implementação podem variar por idioma. Para obter mais informações, consulte a documentação da API ou o SDK específico:

- [SDK de C/Python/iOS](https://github.com/azure/azure-iot-sdk-c)
- [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Nó de SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Conceber para resiliência

Dispositivos IoT frequentemente contam com ligações de rede instável ou não contínuos (por exemplo, GSM ou satélite). Podem ocorrer erros quando dispositivos interagirem com serviços baseados na nuvem, devido a falhas de disponibilidade e ao nível da infraestrutura ou transitória do serviço. Uma aplicação que é executado num dispositivo tem que gerir os mecanismos para ligação e restabelecimento de ligação e a lógica de repetição para enviar e receber mensagens. Além disso, os requisitos da estratégia de repetição são altamente dependem cenário de IoT do dispositivo, contexto, as capacidades.

Os SDKs do dispositivo do IoT Hub do Azure têm como objetivo para simplificar a ligar e comunicar da cloud para o dispositivo e dispositivo-para-cloud. Estes SDKs oferecem uma forma robusta para ligar ao IoT Hub do Azure e um conjunto abrangente de opções para enviar e receber mensagens. Os desenvolvedores também podem modificar a implementação existente para personalizar uma melhor estratégia de repetição para um determinado cenário.

Os recursos relevantes do SDK que oferecem suporte a conectividade e o sistema de mensagens confiável são abordados nas seções a seguir.

## <a name="connection-and-retry"></a>Ligação e tente novamente

Esta secção fornece uma descrição geral dos padrões de restabelecimento de ligação e tente novamente disponíveis ao gerir ligações. Ele detalha as orientações de implementação para utilizar uma política de repetição diferentes na sua aplicação de dispositivo e apresenta uma lista de APIs relevantes dos SDKs do dispositivo.

### <a name="error-patterns"></a>Padrões de erro
Falhas de ligação podem acontecer em muitos níveis:

- Erros de rede: desligado erros de resolução de socket e o nome
- Nível de protocolo erros para o transporte HTTP, AMQP e MQTT: desanexado ligações ou expirado sessões
- Erros de nível de aplicativo que resultam de qualquer um dos erros de locais: credenciais inválidas ou o comportamento de serviço (por exemplo, que excede a quota ou de limitação)

Os SDKs de dispositivo detectar erros em todos os três níveis. Erros relacionados com o sistema operacional e erros de hardware não são detetados e processados pelos SDKs do dispositivo. O design SDK baseia-se no [The transitório falhas manipulação orientações](/azure/architecture/best-practices/transient-faults#general-guidelines) do Centro de arquitetura do Azure.

### <a name="retry-patterns"></a>Repita os padrões de

Os passos seguintes descrevem o processo de repetição quando forem detetados erros de ligação:

1. O SDK Deteta o erro e o erro associado na rede, protocolo ou aplicação.
1. O SDK utiliza o filtro de erro para determinar o tipo de erro e decidir se uma repetição é necessária.
1. Se o SDK identifica uma **erro irrecuperável**, operações como a ligação, enviar e receber são parados. O SDK notifica o utilizador. Exemplos de erros irrecuperáveis incluem um erro de autenticação e um erro de ponto de extremidade ruim.
1. Se o SDK identifica uma **erro recuperável**, tentar novamente, de acordo com a política de repetição especificado até decorrido o tempo limite definido.  Tenha em atenção que o SDK utiliza **Exponential término com interferência** política de repetição por predefinição.
1. Quando o tempo limite definido expira, o SDK para tentar ligar ou enviar. Notifica o utilizador.
1. O SDK permite ao utilizador anexar um retorno de chamada para receber as alterações de estado da ligação.

Os SDKs fornecem que três políticas de repetição:

- **Término exponencial com interferência**: Esta política de repetição predefinida tende a ser agressivo no início e de lentidão ao longo do tempo até atingir um atraso máximo. O design se baseia [repita a orientação do Centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 
- **Repetição personalizada**: Para alguns idiomas SDK, pode criar uma política de repetição personalizada que é mais adequada para o seu cenário e, em seguida, inseri-lo para o RetryPolicy. Repetição personalizada não está disponível no SDK de C.
- **Nenhuma repetição**: Pode definir a política de repetição para "nenhuma repetição," que desativa a lógica de repetição. O SDK tenta ligar-se de uma vez e enviar uma mensagem de uma vez, partindo do princípio de que a ligação ser estabelecida. Esta política é normalmente utilizada em cenários com questões de largura de banda ou custo. Se escolher esta opção, as mensagens que não obedeçam a enviar são perdidas e não podem ser recuperadas.

### <a name="retry-policy-apis"></a>APIs de política de repetição

   | SDK | Método SetRetryPolicy | Implementações de política | Orientações de implementação |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Predefinido**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizada:** utilização disponível [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Nenhuma repetição:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementação de C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Predefinido**: [Classe de ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizada:** implementar [RetryPolicy interface](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nenhuma repetição:** [Classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementação de Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Predefinido**: [Classe de ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizada:** implementar [IRetryPolicy interface](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nenhuma repetição:** [Classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementação em c#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nó| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Predefinido**: [Classe de ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizada:** implementar [RetryPolicy interface](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nenhuma repetição:** [Classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementação de nó](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

Exemplos de código a seguir ilustram este fluxo:

#### <a name="net-implementation-guidance"></a>Orientações de implementação do .NET

O exemplo de código seguinte mostra como definir e definir a política de repetição predefinida:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Para evitar a utilização elevada da CPU, as repetições são limitadas se o código de falha imediatamente. Por exemplo, quando não existe nenhuma rede ou a rota para o destino. O tempo mínimo para executar a próxima repetição é de 1 segundo.

Se o serviço responde com um erro de limitação, a política de repetição é diferente e não pode ser alterada através da API pública:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

O mecanismo de repetição para após `DefaultOperationTimeoutInMilliseconds`, que está definido em 4 minutos.

#### <a name="other-languages-implementation-guidance"></a>Outras diretrizes de implementação de idiomas

Para obter exemplos de código em outros idiomas, reveja os seguintes documentos de implementação. O repositório contém exemplos que demonstram a utilização de APIs de política de repetição.

- [SDK de C/Python/iOS](https://github.com/azure/azure-iot-sdk-c)
- [SDK do .NET](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [SDK Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Nó de SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Passos Seguintes
- [Utilizar o dispositivo e SDKs de serviço](./iot-hub-devguide-sdks.md)
- [Utilizar o SDK do dispositivo IoT para C](./iot-hub-device-sdk-c-intro.md)
- [Desenvolver para dispositivos restritos](./iot-hub-devguide-develop-for-constrained-devices.md)
- [Desenvolver para dispositivos móveis](./iot-hub-how-to-develop-for-mobile-devices.md)
- [Resolver problemas de desliga do dispositivo](iot-hub-troubleshoot-connectivity.md)
