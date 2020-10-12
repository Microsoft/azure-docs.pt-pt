---
title: Gestão de dispositivos Azure IoT com extensão IoT para Azure CLI Microsoft Docs
description: Utilize a extensão IoT para a ferramenta Azure CLI para a gestão de dispositivos Azure IoT Hub, apresentando os métodos Diretos e as opções de gestão de propriedades desejadas pela Twin.
author: chrissie926
manager: ''
keywords: gestão de dispositivos azure iot, gestão de dispositivos azure iot hub, gestão de dispositivos, gestão de dispositivos iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 88c3d1f4213b161d5e322349a7f0e1bc1dd952e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80239651"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Utilize a extensão IoT para Azure CLI para a gestão de dispositivos Azure IoT Hub

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma extensão IoT de código aberto que adiciona às capacidades do [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). O Azure CLI inclui comandos para interagir com o Azure Resource Manager e os pontos finais de gestão. Por exemplo, pode utilizar o Azure CLI para criar um Azure VM ou um hub IoT. Uma extensão CLI permite que um serviço Azure aumente o CLI Azure, dando-lhe acesso a capacidades adicionais específicas do serviço. A extensão IoT dá aos desenvolvedores IoT acesso de linha de comando a todas as capacidades do Serviço de Fornecimento de Dispositivos IoT Hub e IoT.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa  |
|----------------------------|-----------|
| Métodos diretos             | Faça com que um dispositivo aja como iniciar ou parar de enviar mensagens ou reiniciar o dispositivo.                                        |
| Propriedades duplas desejadas    | Coloque um dispositivo em determinados estados, tais como a definição de um LED para verde ou a definição do intervalo de envio de telemetria para 30 minutos.         |
| Propriedades reportadas twin   | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo informa que o LED está a piscar agora.                                    |
| Etiquetas gémeas                  | Guarde metadados específicos do dispositivo na nuvem. Por exemplo, a localização de uma máquina de venda automática.                         |
| Consultas de gémeos do dispositivo        | Consultar todos os gémeos do dispositivo para recuperar os gémeos com condições arbitrárias, tais como identificar os dispositivos disponíveis para uso. |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre a utilização destas opções, consulte [a orientação de comunicação dispositivo-a-nuvem](iot-hub-devguide-d2c-guidance.md) e [a orientação de comunicação nuvem-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub persiste num dispositivo gémeo para cada dispositivo que se conecta ao mesmo. Para obter mais informações sobre os gémeos do dispositivo, consulte [Começar com os gémeos do dispositivo](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a usar a extensão IoT para Azure CLI com várias opções de gestão na sua máquina de desenvolvimento.

## <a name="what-you-do"></a>O que faz

Executar Azure CLI e a extensão IoT para Azure CLI com várias opções de gestão.

## <a name="what-you-need"></a>O que precisa

* Complete o tutorial [de simulador on-line Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes itens cobrem os seguintes requisitos:

  - Uma subscrição ativa do Azure.
  - Um hub Azure IoT sob a sua assinatura.
  - Uma aplicação de cliente que envia mensagens para o seu hub Azure IoT.

* Certifique-se de que o seu dispositivo está em funcionamento com a aplicação do cliente durante este tutorial.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

* O Azure CLI. Se precisar de o instalar, consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az –version` para validar.

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

## <a name="device-twin-desired-properties"></a>Propriedades desejadas twin dispositivo

Desacordo um intervalo de propriedade desejado = 3000 executando o seguinte comando:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Esta propriedade pode ser lida a partir do seu dispositivo.

## <a name="device-twin-reported-properties"></a>Propriedades reportadas de twin dispositivo

Obtenha as propriedades reportadas do dispositivo executando o seguinte comando:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Uma das propriedades geminadas reportadas é $metadata.$lastUpdated, o que mostra a última vez que a aplicação do dispositivo atualizou o seu conjunto de propriedades reportadas.

## <a name="device-twin-tags"></a>Etiquetas gémeas do dispositivo

Exiba as etiquetas e as propriedades do dispositivo executando o seguinte comando:

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

## <a name="device-twin-queries"></a>Consultas de gémeos do dispositivo

Dispositivos de consulta com uma etiqueta de papel = "temperatura&humidade" executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consultar todos os dispositivos, exceto os que têm uma etiqueta de papel = "temperatura&humidade" executando o seguinte comando:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passos seguintes

Aprendeu a monitorizar mensagens de dispositivo para nuvem e a enviar mensagens nuvem-a-dispositivo entre o seu dispositivo IoT e o Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
