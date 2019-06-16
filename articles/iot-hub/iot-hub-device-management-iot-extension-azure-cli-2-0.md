---
title: Azure gestão de dispositivos de IoT com a extensão de IoT para a CLI do Azure | Documentos da Microsoft
description: Utilize a extensão de IoT para a ferramenta de CLI do Azure para gestão de dispositivos do IoT Hub do Azure, com os métodos diretos e opções de gestão de propriedades pretendidas do duplo.
author: chrissie926
manager: ''
keywords: gestão de dispositivos do iot do Azure, gestão de dispositivos do hub iot do azure, iot de gestão de dispositivos, gestão de dispositivos do hub iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 6b1029c5532e106c269b47e6e184b9c93faf8d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399628"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Utilizar a extensão de IoT para a CLI do Azure para gestão de dispositivos do IoT Hub do Azure

![Diagrama de ponto a ponto](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension) é uma nova extensão de IoT que adiciona às funcionalidades de código aberto do [CLI do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). A CLI do Azure inclui comandos para interagir com o Gestor de recursos do Azure e pontos finais de gestão. Por exemplo, pode utilizar a CLI do Azure para criar uma VM do Azure ou de um hub IoT. Uma extensão CLI permite que um serviço do Azure aumentar a CLI do Azure, dando que aceder às funcionalidades adicionais de serviços específicos. A extensão de IoT dá aos programadores de IoT o acesso de linha de comandos para os recursos de todos os IoT Hub do IoT Edge e serviço de aprovisionamento de dispositivo IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Opção de gestão          | Tarefa  |
|----------------------------|-----------|
| Métodos diretos             | Tornar um dispositivo atuar como iniciar ou interromper o envio de mensagens ou reiniciar o dispositivo.                                        |
| Propriedades pretendida do duplo    | Coloca um dispositivo em determinados Estados, como na definição de um LED verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Duplo de propriedades comunicadas   | Obter o Estado comunicado de um dispositivo. Por exemplo, o dispositivo comunica que o LED é intermitente agora.                                    |
| Etiquetas de duplo                  | Store metadados específicos do dispositivo na cloud. Por exemplo, a localização de implementação de uma máquina de venda automática.                         |
| Consultas de gémeos de dispositivo        | Consulte todos os dispositivos duplos para recuperar as condições arbitrárias, como identificar os dispositivos que estão disponíveis para utilização. |

Para obter mais explicações sobre as diferenças e as orientações sobre como utilizar estas opções, consulte [documentação de orientação do dispositivo-para-cloud comunicação](iot-hub-devguide-d2c-guidance.md) e [orientação de comunicação do Cloud-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

Os dispositivos duplos são documentos JSON que armazenam informações de estado dos dispositivos (metadados, configurações e condições). O IoT Hub cria um dispositivo duplo para cada dispositivo que se liga ao mesmo. Para obter mais informações sobre dispositivos duplos, consulte [introdução aos dispositivos duplos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que irá aprender

Aprenda a utilizar a extensão de IoT para a CLI do Azure com várias opções de gestão no computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute o CLI do Azure e a extensão de IoT para a CLI do Azure com várias opções de gestão.

## <a name="what-you-need"></a>Do que precisa

* Concluir o [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes incluem os seguintes requisitos::

  - Uma subscrição ativa do Azure.
  - Um hub IoT do Azure com a sua subscrição.
  - Uma aplicação de cliente que envia mensagens para o seu hub IoT do Azure.

* Certificar-se de que o dispositivo está em execução com a aplicação de cliente durante este tutorial.

* [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

* A CLI do Azure. Se quiser instalá-lo, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –version` para validar. 

* Instale a extensão de IoT. A forma mais simples consiste em executar `az extension add --name azure-cli-iot-ext`. [O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) descreve várias formas de instalar a extensão.

## <a name="log-in-to-your-azure-account"></a>Iniciar sessão na sua conta do Azure

Inicie sessão sua conta do Azure ao executar o seguinte comando:

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

## <a name="device-twin-desired-properties"></a>Propriedades dos dispositivos duplos pretendido

Definir um intervalo de propriedade pretendida = 3000, executando o seguinte comando:

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Esta propriedade pode ser lido a partir do seu dispositivo.

## <a name="device-twin-reported-properties"></a>Propriedades comunicadas do dispositivo duplo

Obter as propriedades reportadas do dispositivo, execute o seguinte comando:

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Um do duplo comunicado propriedades é $metadata. $lastUpdated que mostra a última vez que a aplicação de dispositivo atualizado o seu conjunto de propriedades comunicadas.

## <a name="device-twin-tags"></a>Etiquetas do dispositivo duplo

Apresenta as etiquetas e propriedades do dispositivo ao executar o seguinte comando:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adicionar uma função de campo = temperatura e humidade para o dispositivo ao executar o seguinte comando:

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Consultas de gémeos de dispositivo

Consultar os dispositivos com uma etiqueta de função = "temperatura e humidade" ao executar o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consultar todos os dispositivos, exceto aqueles com uma etiqueta de função = "temperatura e humidade" ao executar o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passos Seguintes

Já aprendeu a monitorizar mensagens do dispositivo para a cloud e enviar mensagens de cloud-para-dispositivo entre o seu dispositivo IoT e IoT Hub do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
