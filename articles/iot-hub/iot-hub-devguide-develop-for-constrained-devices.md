---
title: Hub Azure IoT Desenvolver para dispositivos constrangidos usando IoT Hub C SDK
description: Guia do desenvolvedor - orientação sobre como desenvolver usando Azure IoT SDKs para dispositivos constrangidos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500007"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Desenvolver para dispositivos constrangidos utilizando Azure IoT C SDK

Azure IoT Hub C SDK está escrito em ANSI C (C99), o que torna-o adequado para operar uma variedade de plataformas com pequenas pegadas de disco e memória. A RAM recomendada é de pelo menos 64 KB, mas a pegada de memória exata depende do protocolo utilizado, do número de ligações abertas, bem como da plataforma visada.
> [!NOTE]
> * A Azure IoT C SDK publica regularmente informações sobre consumo de recursos para ajudar no desenvolvimento.  Visite o nosso [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) e reveja a última referência.
>

C SDK está disponível em forma de pacote a partir de apt-get, NuGet e MBED. Para direcionar os dispositivos constrangidos, pode querer construir o SDK localmente para a sua plataforma alvo. Esta documentação demonstra como remover certas características para diminuir a pegada do C SDK utilizando [cmake](https://cmake.org/). Além disso, esta documentação discute os modelos de programação de boas práticas para trabalhar com dispositivos constrangidos.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Construção do C SDK para dispositivos constrangidos

Construa o C SDK para dispositivos constrangidos.

> [!NOTE]
> O Incorporado C SDK é uma alternativa para dispositivos constrangidos que suportam a abordagem trazer a sua própria rede (BYON). Os desenvolvedores de IoT têm a liberdade de trazer o cliente MQTT, TLS, e tomada da sua escolha para criar uma solução de dispositivo. [Saiba mais sobre o C SDK incorporado.](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)

### <a name="prerequisites"></a>Pré-requisitos

Siga este [guia de configuração C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para preparar o seu ambiente de desenvolvimento para a construção do C SDK. Antes de chegar ao degrau para construir com cmake, você pode invocar bandeiras de cmake para remover características nãousadas.

### <a name="remove-additional-protocol-libraries"></a>Remover bibliotecas de protocolo adicionais

A C SDK suporta hoje cinco protocolos: MQTT, MQTT sobre WebSocket, AMQPs, AMQP sobre WebSocket e HTTPS. A maioria dos cenários requer um a dois protocolos em execução num cliente, daí que possa remover a biblioteca de protocolos que não está a usar do SDK. Informações adicionais sobre a escolha do protocolo de comunicação adequado para o seu cenário podem ser encontradas no [Protocolo de Comunicação IoT Hub](iot-hub-devguide-protocols.md). Por exemplo, O MQTT é um protocolo leve que é frequentemente mais adequado para dispositivos constrangidos.

Pode remover as bibliotecas AMQP e HTTP utilizando o seguinte comando cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Remover a capacidade de registo SDK

O C SDK fornece uma exploração extensiva ao longo de todo para ajudar na depuragem. Pode remover a capacidade de registo dos dispositivos de produção utilizando o seguinte comando cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Remova o upload para a capacidade do blob

Pode enviar ficheiros grandes para o Azure Storage utilizando a capacidade incorporada no SDK. O Azure IoT Hub atua como despachante para uma conta de armazenamento Azure associada. Pode utilizar esta funcionalidade para enviar ficheiros de mídia, grandes lotes de telemetria e registos. Pode obter mais informações no [upload de ficheiros com o IoT Hub](iot-hub-devguide-file-upload.md). Se a sua aplicação não necessitar desta funcionalidade, pode remover esta função utilizando o seguinte comando cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Tira de corrida no ambiente linux

Se os binários funcionarem no sistema Linux, pode aproveitar o comando da [tira](https://en.wikipedia.org/wiki/Strip_(Unix)) para reduzir o tamanho da aplicação final após a compilação.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos constrangidos

Em seguida, veja os modelos de programação para dispositivos constrangidos.

### <a name="avoid-using-the-serializer"></a>Evite utilizar o Serializer

O C SDK tem um [serializador C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)opcional, que permite utilizar tabelas de mapeamento declarativas para definir métodos e propriedades gémeas do dispositivo. O serializer foi concebido para simplificar o desenvolvimento, mas adiciona sobrecarga, o que não é ideal para dispositivos constrangidos. Neste caso, considere utilizar APIs de cliente primitivo e analisar o JSON utilizando um pároco leve, como [o pároco.](https://github.com/kgabis/parson)

### <a name="use-the-lower-layer-_ll_"></a>Utilize a camada inferior _(LL)_

O C SDK suporta dois modelos de programação. Um conjunto tem APIs com um infixo _LL,_ que significa camada inferior. Este conjunto de APIs tem um peso mais leve e não gira os fios dos trabalhadores, o que significa que o utilizador deve controlar manualmente o agendamento. Por exemplo, para o cliente do dispositivo, as APIs _ll_ podem ser encontradas neste [ficheiro de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Outro conjunto de APIs sem o índice _LL_ é chamado de camada de conveniência, onde um fio de trabalhador é girado automaticamente. Por exemplo, as APIs de camada de conveniência para o cliente do dispositivo podem ser encontradas neste [ficheiro de cabeçalho do cliente do dispositivo IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Para dispositivos constrangidos onde cada fio extra pode levar uma percentagem substancial de recursos do sistema, considere a utilização de APIs _LL._

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a arquitetura Azure IoT C SDK:
-    [Código fonte Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
-    [Dispositivo Azure IoT SDK para introdução C](iot-hub-device-sdk-c-intro.md)
