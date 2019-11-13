---
title: Enviar mensagens para um servidor MQTT usando a biblioteca de cliente MQTT do Azure
description: Saiba como usar a biblioteca de cliente do MQTT para enviar mensagens para um agente do MQTT. Saiba também como configurar seu mXChip IoT DevKit para ser um cliente MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 14feb02fbac800cd37da882a1872beb8269d9938
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954232"
---
# <a name="send-messages-to-an-mqtt-server"></a>Enviar mensagens para um servidor MQTT

Os sistemas de Internet das Coisas (IoT) geralmente lidam com conexões de Internet intermitentes, de baixa qualidade ou lentas. MQTT é um protocolo de conectividade M2M (máquina a máquina), que foi desenvolvido com esses desafios em mente. 

A biblioteca de cliente MQTT usada aqui faz parte do projeto [Eclipse PAHO](https://www.eclipse.org/paho/) , que fornece APIs para usar o MQTT em vários meios de transporte.

## <a name="what-you-learn"></a>O que irá aprender

Neste projeto, você aprende:
- Como usar a biblioteca de cliente do MQTT para enviar mensagens para um agente do MQTT.
- Como configurar seu MXChip IOT DevKit como um cliente MQTT.

## <a name="what-you-need"></a>Do que precisa

Conclua o [Guia de introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

## <a name="open-the-project-folder"></a>Abrir a pasta do projeto

1. Se o DevKit já estiver conectado ao seu computador, desconecte-o.

2. Iniciar VS Code.

3. Conecte o DevKit ao seu computador.

## <a name="open-the-mqttclient-sample"></a>Abrir o exemplo de MQTTClient

Expanda a seção **exemplos do ARDUINO** no lado esquerdo, navegue até **exemplos para MXCHIP AZ3166 > MQTT**e selecione **MQTTClient**. Uma nova janela VS Code é aberta com uma pasta do projeto.

> [!NOTE]
> Você também pode abrir o exemplo da paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino**e, em seguida, localize e selecione **Arduino: exemplos**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Criar e carregar o esboço de Arduino no DevKit

Digite `Ctrl+P` (macOS: `Cmd+P`) para executar `task device-upload`. Quando o upload for concluído, o DevKit reiniciará e executará o esboço.

![dispositivo-carregar](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Você pode receber uma mensagem de erro "erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote do quadro não é atualizado corretamente. Para resolver esse erro, consulte a [seção de desenvolvimento das perguntas frequentes sobre o IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testar o projeto

Em VS Code, siga este procedimento para abrir e configurar o monitor serial:

1. Clique na `COM[X]` palavra na barra de status para definir a porta COM à direita com `STMicroelectronics`: ![set-com-Port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Clique no ícone de plugue de energia na barra de status para abrir o monitor serial: ![](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg) de monitor serial
  
3. Na barra de status, clique no número que representa a taxa de transmissão e defina-a como `115200`: ![definir-taxa de baud](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

O monitor serial exibe todas as mensagens enviadas pelo esboço de exemplo. O esboço conecta o DevKit ao Wi-Fi. Depois que a conexão Wi-Fi for bem-sucedida, o esboço enviará uma mensagem para o agente do MQTT. Depois disso, o exemplo envia repetidamente duas mensagens "iot.eclipse.org" usando QoS 0 e QoS 1, respectivamente.

![saída serial](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, consulte as [perguntas frequentes do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se usando os seguintes canais:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Consulte também

* [Conectar IoT DevKit AZ3166 ao Hub IoT do Azure na nuvem](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Agite, agite para um tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como configurar seu MXChip IOT DevKit como um cliente MQTT e usar a biblioteca de cliente do MQTT para enviar mensagens para um agente do MQTT, aqui estão as próximas etapas sugeridas:

* [Visão geral do acelerador de solução de monitoramento remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo IoT Central do Azure](/azure/iot-central/core/howto-connect-devkit)
