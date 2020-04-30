---
title: Hub Azure IoT Desenvolvem-se para dispositivos constrangidos usando IoT Hub C SDK
description: Guia de desenvolvimento - orientação sobre como desenvolver utilizando SDKs Azure IoT para dispositivos constrangidos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9010ff582f05e81e17e280e20f180ceccf0e746f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733192"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Desenvolver dispositivos constrangidos usando O SDK Azure IoT C

O Azure IoT Hub C SDK está escrito em ANSI C (C99), o que o torna adequado para operar uma variedade de plataformas com pequeno disco e pegada de memória. A RAM recomendada é de pelo menos 64 KB, mas a pegada de memória exata depende do protocolo utilizado, do número de ligações abertas, bem como da plataforma visada.
> [!NOTE]
> * O Azure IoT C SDK publica regularmente informações sobre consumo de recursos para ajudar no desenvolvimento.  Visite o nosso [repositório GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) e reveja o mais recente referencial.
>

C SDK está disponível em formato de pacote de apt-get, NuGet e MBED. Para direcionar dispositivos constrangidos, pode querer construir o SDK localmente para a sua plataforma alvo. Esta documentação demonstra como remover certas funcionalidades para diminuir a pegada do C SDK usando [cmake](https://cmake.org/). Além disso, esta documentação discute os modelos de programação de boas práticas para trabalhar com dispositivos constrangidos.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Construção do C SDK para dispositivos constrangidos

Construa o C SDK para dispositivos constrangidos.

### <a name="prerequisites"></a>Pré-requisitos

Siga este guia de [configuração C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para preparar o seu ambiente de desenvolvimento para a construção do C SDK. Antes de chegar ao degrau para construir com cmake, você pode invocar bandeiras de cmake para remover características não usadas.

### <a name="remove-additional-protocol-libraries"></a>Remover bibliotecas de protocolos adicionais

C SDK suporta cinco protocolos hoje: MQTT, MQTT sobre WebSocket, AMQPs, AMQP sobre WebSocket e HTTPS. A maioria dos cenários requer um a dois protocolos em execução de um cliente, pelo que pode remover a biblioteca de protocolos que não está a usar do SDK. Informações adicionais sobre a escolha do protocolo de comunicação adequado para o seu cenário podem ser encontradas em Escolha um protocolo de [comunicação IoT Hub](iot-hub-devguide-protocols.md). Por exemplo, o MQTT é um protocolo leve que é muitas vezes mais adequado para dispositivos constrangidos.

Pode remover as bibliotecas AMQP e HTTP utilizando o seguinte comando de cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Remover a capacidade de exploração madeireira SDK

O C SDK fornece uma extensa exploração madeireira em toda a parte para ajudar na depuração. Pode remover a capacidade de exploração de dispositivos de produção utilizando o seguinte comando de cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Remova o upload para a capacidade blob

Pode fazer o upload de ficheiros grandes para o Armazenamento Azure utilizando a capacidade incorporada no SDK. O Azure IoT Hub funciona como despachante para uma conta de Armazenamento Azure associada. Pode utilizar esta funcionalidade para enviar ficheiros de mídia, grandes lotes de telemetria e registos. Pode obter mais informações no [upload de ficheiros com o IoT Hub](iot-hub-devguide-file-upload.md). Se a sua aplicação não necessitar desta funcionalidade, pode remover esta funcionalidade utilizando o seguinte comando cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Tira de corrida no ambiente linux

Se os seus binários funcionarem no sistema Linux, pode alavancar o comando de [tiras](https://en.wikipedia.org/wiki/Strip_(Unix)) para reduzir o tamanho da aplicação final após a compilação.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos constrangidos

Em seguida, olhe para os modelos de programação para dispositivos limitados.

### <a name="avoid-using-the-serializer"></a>Evite utilizar o Serializer

O C SDK tem um [serializador C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)opcional, que permite utilizar tabelas de mapeamento declarativas para definir métodos e propriedades gémeas do dispositivo. O serializador foi concebido para simplificar o desenvolvimento, mas adiciona sobrecarga, o que não é ideal para dispositivos constrangidos. Neste caso, considere usar APIs de cliente primitivo e parse JSON usando um parser leve como [o pároco](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Utilize a camada inferior _(LL)_

O C SDK suporta dois modelos de programação. Um conjunto tem APIs com um infixo _LL,_ que significa camada inferior. Este conjunto de APIs tem um peso mais leve e não gira os fios do trabalhador, o que significa que o utilizador deve controlar manualmente o agendamento. Por exemplo, para o cliente do dispositivo, as APIs _LL_ podem ser encontradas neste ficheiro de [cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Outro conjunto de APIs sem o índice _LL_ é chamado de camada de conveniência, onde um fio de trabalhador é fiado automaticamente. Por exemplo, a camada de conveniência APIs para o cliente do dispositivo pode ser encontrada neste ficheiro de [cabeçalho do Cliente do Dispositivo IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Para dispositivos constrangidos onde cada fio extra pode assumir uma percentagem substancial de recursos do sistema, considere usar _APIs LL._

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a arquitetura Azure IoT C SDK:
-    [Código fonte Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/)
-    [Dispositivo Azure IoT SDK para introdução em C](iot-hub-device-sdk-c-intro.md)
