---
title: Gerenciamento de dispositivo IoT do Azure com extensão de IoT para CLI do Azure | Microsoft Docs
description: Use a extensão de IoT para a ferramenta de CLI do Azure para o gerenciamento de dispositivos do Hub IoT do Azure, incluindo os métodos diretos e as opções de gerenciamento de propriedades desejadas do entrelaçamento.
author: chrissie926
manager: ''
keywords: gerenciamento de dispositivo IOT do Azure, gerenciamento de dispositivo do Hub IOT do Azure, IOT de gerenciamento de dispositivos, gerenciamento de dispositivos do Hub IOT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 93efd6e53470fb78bb6d823652437e7a37c33732
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640569"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Usar a extensão de IoT para CLI do Azure para o gerenciamento de dispositivos do Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão de IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) é uma nova extensão de IOT de software livre que adiciona aos recursos do [CLI do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). O CLI do Azure inclui comandos para interagir com os pontos de extremidade de Azure Resource Manager e de gerenciamento. Por exemplo, você pode usar CLI do Azure para criar uma VM do Azure ou um hub IoT. Uma extensão da CLI permite que um serviço do Azure aumente o CLI do Azure concedendo a você acesso a recursos adicionais específicos do serviço. A extensão de IoT fornece aos desenvolvedores de IoT acesso de linha de comando a todos os recursos do Hub IoT, IoT Edge e do serviço de provisionamento de dispositivos no Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gerenciamento          | Tarefa  |
|----------------------------|-----------|
| Métodos diretos             | Faça um dispositivo funcionar como iniciar ou parar de enviar mensagens ou reinicializar o dispositivo.                                        |
| Propriedades desejadas de entrelaçamento    | Coloque um dispositivo em determinados Estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Propriedades relatadas de entrelaçamento   | Obter o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando agora.                                    |
| Marcas de entrelaçamento                  | Armazene metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de venda.                         |
| Consultas do dispositivo.        | Consulte todos os dispositivos gêmeos para recuperar esses gêmeos com condições arbitrárias, como identificar os dispositivos que estão disponíveis para uso. |

Para obter uma explicação mais detalhada sobre as diferenças e orientações sobre como usar essas opções, consulte [diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e diretrizes de comunicação da [nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O Hub IoT persiste um dispositivo "r" para cada dispositivo que se conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte Introdução [ao dispositivo gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Você aprende a usar a extensão de IoT para CLI do Azure com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que você faz

Execute CLI do Azure e a extensão de IoT para CLI do Azure com várias opções de gerenciamento.

## <a name="what-you-need"></a>Do que precisa

* Conclua o tutorial do [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Esses itens abrangem os seguintes requisitos:

  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - Um aplicativo cliente que envia mensagens para o Hub IoT do Azure.

* Verifique se o dispositivo está em execução com o aplicativo cliente durante este tutorial.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

<!-- I'm not sure we need all this info, so comment out this include for now. Robin 7.26.2019
[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)] -->

* O CLI do Azure. Se você precisar instalá-lo, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –version` para validar.

* Instale a extensão de IoT. A forma mais simples consiste em executar `az extension add --name azure-cli-iot-ext`. [O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) descreve várias formas de instalar a extensão.

## <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Entre em sua conta do Azure executando o seguinte comando:

```bash
az login
```

## <a name="direct-methods"></a>Métodos diretos

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propriedades desejadas do dispositivo.

Defina um intervalo de propriedades desejado = 3000 executando o seguinte comando:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Essa propriedade pode ser lida em seu dispositivo.

## <a name="device-twin-reported-properties"></a>Propriedades relatadas do dispositivo.

Obtenha as propriedades relatadas do dispositivo executando o seguinte comando:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Uma das propriedades relatadas de entrelaçamento é $metadata. $lastUpdated, que mostra a última vez em que o aplicativo do dispositivo atualizou seu conjunto de propriedades relatadas.

## <a name="device-twin-tags"></a>Marcas de dispositivo

Exiba as marcas e propriedades do dispositivo executando o seguinte comando:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adicione uma função de campo = temperatura & umidade ao dispositivo executando o seguinte comando:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Consultas do dispositivo.

Consulte dispositivos com uma marca de função = ' temperatura & umidade ' executando o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos os dispositivos, exceto aqueles com uma marca de função = ' temperatura & umidade ' executando o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passos Seguintes

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
