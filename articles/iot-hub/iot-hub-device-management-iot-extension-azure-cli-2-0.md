---
title: Gestão de dispositivos Azure IoT com extensão IoT para O ClI Azure Microsoft Docs
description: Utilize a extensão IoT para a ferramenta Azure CLI para a gestão de dispositivos Azure IoT Hub, com os métodos Direct e as opções de gestão de propriedades desejadas pela Twin.
author: chrissie926
manager: ''
keywords: gestão de dispositivos azure iot, gestão de dispositivos hub azure iot, gestão de dispositivos iot, gestão de dispositivos de hub iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239651"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Utilize a extensão IoT para o Azure CLI para a gestão do dispositivo Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma extensão IoT de código aberto que aumenta as capacidades do [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). O Azure CLI inclui comandos para interagir com o Gestor de Recursos Azure e pontos finais de gestão. Por exemplo, pode utilizar o Azure CLI para criar um VM Azure ou um hub IoT. Uma extensão CLI permite que um serviço Azure aumente o Azure CLI dando-lhe acesso a capacidades adicionais específicas do serviço. A extensão IoT dá aos desenvolvedores IoT acesso à linha de comando de todos os ioT Hub, IoT Edge e IoT Hub Device Provisioning Service.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa  |
|----------------------------|-----------|
| Métodos diretos             | Faça um dispositivo agir como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Propriedades desejadas twin    | Coloque um dispositivo em certos estados, tais como colocar um LED a verde ou definir o intervalo de envio de telemetria para 30 minutos.         |
| Propriedades reportadas twin   | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Etiquetas gémeas                  | Guarde metadados específicos do dispositivo na nuvem. Por exemplo, a localização de implantação de uma máquina de venda automática.                         |
| Consultas gémeas do dispositivo        | Consultar todos os gémeos do dispositivo para recuperar esses gémeos com condições arbitrárias, como identificar os dispositivos que estão disponíveis para uso. |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte a orientação de [comunicação Dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação Cloud-to-device](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub persiste um dispositivo gémeo para cada dispositivo que se liga ao mesmo. Para mais informações sobre gémeos dispositivos, consulte [Começar com gémeos dispositivos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a usar a extensão IoT para Azure CLI com várias opções de gestão na sua máquina de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Executar O ClI Azure e a extensão IoT para o Azure CLI com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

* Complete o tutorial de [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes itens cobrem os seguintes requisitos:

  - Uma subscrição ativa do Azure.
  - Um hub Azure IoT sob a sua assinatura.
  - Uma aplicação de cliente que envia mensagens para o seu hub Azure IoT.

* Certifique-se de que o seu dispositivo está a funcionar com a aplicação do cliente durante este tutorial.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

* O Azure CLI. Se precisar de instalá-lo, consulte [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az –version` para validar.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Instale a extensão IoT. A forma mais simples consiste em executar `az extension add --name azure-iot`. [O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) descreve várias formas de instalar a extensão.

## <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Inscreva-se na sua conta Azure executando o seguinte comando:

```azurecli
az login
```

## <a name="direct-methods"></a>Métodos diretos

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propriedades desejadas por gémeos dispositivos

Definir um intervalo de propriedade desejado = 3000 executando o seguinte comando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Esta propriedade pode ser lida a partir do seu dispositivo.

## <a name="device-twin-reported-properties"></a>Propriedades reportadas twin dispositivo

Obtenha as propriedades reportadas do dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Uma das propriedades reportadas é $metadata.$lastUpdated, o que mostra a última vez que a aplicação do dispositivo atualizou o seu conjunto de propriedades reportado.

## <a name="device-twin-tags"></a>Etiquetas duplas do dispositivo

Mostrar as etiquetas e propriedades do dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adicione uma função de campo = temperatura&humidade ao dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Consultas gémeas do dispositivo

Dispositivos de consulta com uma etiqueta de papel = "temperatura&humidade" executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulta de todos os dispositivos exceto aqueles com uma etiqueta de papel = "temperatura&humidade" executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passos seguintes

Aprendeu a monitorizar mensagens dispositivo-nuvem e a enviar mensagens cloud-to-device entre o seu dispositivo IoT e o Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
