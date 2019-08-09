---
title: O Hub IoT do Azure desenvolve para dispositivos restritos usando o SDK C do Hub IoT | Microsoft Docs
description: Guia do desenvolvedor-diretrizes sobre como desenvolver usando SDKs de IoT do Azure para dispositivos restritos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.openlocfilehash: d69fe6b845d3af04e42ee91daa9359dcb9a88fc5
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880972"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Desenvolver para dispositivos restritos usando o SDK do Azure IoT C

O SDK C do Hub IoT do Azure é escrito em ANSI C (C99), o que o torna adequado para operar uma variedade de plataformas com espaço pequeno de disco e memória. A RAM recomendada é de pelo menos 64 KB, mas a superfície exata da memória depende do protocolo usado, do número de conexões abertas, bem como da plataforma de destino.
> [!NOTE]
> * O SDK do Azure IoT C publica regularmente informações de consumo de recursos para ajudar no desenvolvimento.  Visite nosso [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) e examine o parâmetro de comparação mais recente.
>

O SDK do C está disponível na forma de pacote de apt-get, NuGet e MBED. Para direcionar dispositivos restritos, talvez você queira compilar o SDK localmente para a plataforma de destino. Esta documentação demonstra como remover certos recursos para reduzir a superfície do SDK do C usando o [CMake](https://cmake.org/). Além disso, esta documentação aborda os modelos de programação de práticas recomendadas para trabalhar com dispositivos restritos.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Criando o SDK do C para dispositivos restritos

Crie o SDK do C para dispositivos restritos.

### <a name="prerequisites"></a>Pré-requisitos

Siga este [Guia de instalação do SDK do c](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para preparar seu ambiente de desenvolvimento para criar o SDK do c. Antes de ir para a etapa de criação com o CMake, você pode invocar sinalizadores de CMake para remover recursos não utilizados.

### <a name="remove-additional-protocol-libraries"></a>Remover bibliotecas de protocolo adicionais

O SDK do C dá suporte a cinco protocolos hoje: MQTT, MQTT sobre WebSocket, AMQPs, AMQP por WebSocket e HTTPS. A maioria dos cenários requer um a dois protocolos em execução em um cliente, portanto, você pode remover a biblioteca de protocolos que você não está usando do SDK. Informações adicionais sobre como escolher o protocolo de comunicação apropriado para seu cenário podem ser encontradas em [escolher um protocolo de comunicação do Hub IOT](iot-hub-devguide-protocols.md). Por exemplo, MQTT é um protocolo leve que geralmente é mais adequado para dispositivos restritos.

Você pode remover as bibliotecas AMQP e HTTP usando o seguinte comando CMake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Remover o recurso de log do SDK

O SDK do C fornece o registro em log extensivo para ajudar na depuração. Você pode remover o recurso de registro em log para dispositivos de produção usando o seguinte comando CMake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Remover o carregamento do recurso de BLOB

Você pode carregar arquivos grandes no armazenamento do Azure usando a funcionalidade interna no SDK. O Hub IoT do Azure atua como um Dispatcher para uma conta de armazenamento do Azure associada. Você pode usar esse recurso para enviar arquivos de mídia, lotes de telemetria grandes e logs. Você pode obter mais informações em [carregando arquivos com o Hub IOT](iot-hub-devguide-file-upload.md). Se seu aplicativo não exigir essa funcionalidade, você poderá remover esse recurso usando o seguinte comando CMake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Executando strip no ambiente Linux

Se seus binários forem executados no sistema Linux, você poderá aproveitar o [comando strip](https://en.wikipedia.org/wiki/Strip_(Unix)) para reduzir o tamanho do aplicativo final após a compilação.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos restritos

Em seguida, examine os modelos de programação para dispositivos restritos.

### <a name="avoid-using-the-serializer"></a>Evite usar o serializador

O SDK do C tem um [serializador do SDK do c](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)opcional, que permite que você use tabelas de mapeamento declarativo para definir métodos e propriedades de dispositivo. O serializador foi projetado para simplificar o desenvolvimento, mas adiciona sobrecarga, o que não é ideal para dispositivos restritos. Nesse caso, considere o uso de APIs de cliente primitivas e a análise do JSON usando um analisador leve, como o [Parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-_ll_"></a>Usar a camada inferior (_ll_)

O SDK do C dá suporte a dois modelos de programação. Um conjunto tem APIs com um infixo _ll_ , que representa a camada inferior. Esse conjunto de APIs é um peso mais leve e não gira threads de trabalho, o que significa que o usuário deve controlar manualmente o agendamento. Por exemplo, para o cliente do dispositivo, as APIs _ll_ podem ser encontradas neste [arquivo de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Outro conjunto de APIs sem o índice _ll_ é chamado de camada de conveniência, em que um thread de trabalho é girado automaticamente. Por exemplo, as APIs da camada de conveniência para o cliente do dispositivo podem ser encontradas neste [arquivo de cabeçalho do cliente do dispositivo IOT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Para dispositivos restritos em que cada thread extra pode assumir um percentual substancial de recursos do sistema, considere o uso de APIs _ll_ .

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a arquitetura do SDK do Azure IoT C:
-   [Código-fonte do SDK do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/)
-   [Introdução ao SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md)
