---
title: O Azure IoT Hub desenvolver para restrita dispositivos com o SDK de C do IoT Hub | Documentos da Microsoft
description: Guia do desenvolvedor – documentação de orientação sobre como desenvolver com os SDKs do Azure para dispositivos restritos.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 7788bca621a59ec8cdfe36edf73a99efca8c460c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61320952"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Desenvolver para dispositivos restritos com o SDK de C do Azure IoT

SDK de C de Hub IoT do Azure é escrito em ANSI C (C99), que a torna adequada para operar uma variedade de plataformas com pouco espaço de disco e memória. A quantidade de RAM recomendada é de, pelo menos, de 64 KB, mas os requisitos de espaço de memória exato depende do protocolo utilizado, o número de ligações abertas, bem como a plataforma de destino.
> [!NOTE]
> * SDK de C do Azure IoT publica regularmente informações de consumo de recursos para ajudá-lo desenvolvimento.  Visite nosso [repositório do GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) e reveja o parâmetro de comparação mais recente.
>

SDK de C está disponível em formato de pacote do apt-get, NuGet e MBED. Para filtrar dispositivos restritos, pode querer criar o SDK localmente para a sua plataforma de destino. Esta documentação demonstra como remover determinados recursos para reduzir os requisitos de espaço de utilizar o SDK de C [cmake](https://cmake.org/). Além disso, esta documentação descreve a melhor prática de modelos para trabalhar com dispositivos restritos de programação.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Criar o SDK de C para dispositivos restritos

Crie o SDK de C para dispositivos restritos.

### <a name="prerequisites"></a>Pré-requisitos

Siga este [guia de configuração do SDK de C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) para preparar o ambiente de desenvolvimento para criar o SDK de C. Antes de obter para o passo para criar com cmake, é possível invocar sinalizadores de cmake para remover recursos não utilizados.

### <a name="remove-additional-protocol-libraries"></a>Remover as bibliotecas de protocolo adicionais

SDK de C suporta protocolos de cinco hoje: MQTT, MQTT ao longo do WebSocket, AMQPs, AMQP sobre WebSocket e HTTPS. A maioria dos cenários necessitam de protocolos de um ou dois em execução num cliente, por conseguinte, pode remover a biblioteca de protocolo que não estiver a utilizar o SDK. Informações adicionais sobre como escolher o protocolo de comunicação apropriados para seu cenário pode ser encontrado na [escolher um protocolo de comunicação do IoT Hub](iot-hub-devguide-protocols.md). Por exemplo, MQTT é um protocolo simples que é, muitas vezes, mais adequado para dispositivos restritos.

Pode remover as bibliotecas AMQP e HTTP com o seguinte comando de cmake:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Remover o recurso de registro de SDK

O SDK de C mostra o log extenso em todo para ajudar na depuração. Pode remover o recurso de Registro para os dispositivos de produção com o seguinte comando de cmake:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Remover o carregamento para a capacidade de BLOBs

Pode carregar ficheiros grandes para o armazenamento do Azure com o recurso interno no SDK. O IoT Hub do Azure funciona como um dispatcher para uma conta de armazenamento do Azure associada. Pode utilizar esta funcionalidade para enviar os ficheiros de multimédia, lotes grandes de telemetria e registos. Pode obter mais informações em [carregamento de ficheiros com o IoT Hub](iot-hub-devguide-file-upload.md). Se seu aplicativo não exija essa funcionalidade, pode remover esta funcionalidade com o seguinte comando de cmake:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Faixa em execução no ambiente do Linux

Se executam seus binários no sistema Linux, poderá aproveitar o [faixa comando](https://en.wikipedia.org/wiki/Strip_(Unix)) para reduzir o tamanho do aplicativo final após a compilação.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos restritos

Em seguida, ver modelos para dispositivos restritos de programação.

### <a name="avoid-using-the-serializer"></a>Evite usar o serializador

O SDK de C tem opcional [serializador do SDK de C](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), que permite que usar tabelas de mapeamento declarativo para definir métodos e propriedades de twin do dispositivo. O serializador foi concebido para simplificar o desenvolvimento, mas acrescenta sobrecarga, que não é ideal para dispositivos restritos. Neste caso, considere a utilização de APIs de cliente primitivas e analisar JSON utilizando um analisador simples, tal como [parson](https://github.com/kgabis/parson).

### <a name="use-the-lower-layer-ll"></a>Utilizar a camada inferior (_odas_)

O SDK de C suporta dois modelos de programação. Um conjunto tem APIs com um _odas_ infix, que significa camada inferior. Este conjunto de APIs é mais claro de importância e não criar threads de trabalho, que significa que o utilizador tem manualmente de controle do agendamento. Por exemplo, para o cliente de dispositivo, o _odas_ APIs que pode ser encontradas neste [arquivo de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Outro conjunto de APIs sem o _odas_ índice denomina-se a camada de conveniência, onde um thread de trabalho é preparado automaticamente. Por exemplo, a camada de conveniência APIs para o cliente do dispositivo pode ser encontrada neste [o arquivo de cabeçalho de cliente do dispositivo IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Para dispositivos restritos em que cada thread adicional pode demorar uma porcentagem considerável de recursos do sistema, considere utilizar _odas_ APIs.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a arquitetura do SDK de C do Azure IoT:
-   [Código de origem do SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [O Azure IoT-device SDK para C introdução](iot-hub-device-sdk-c-intro.md)
