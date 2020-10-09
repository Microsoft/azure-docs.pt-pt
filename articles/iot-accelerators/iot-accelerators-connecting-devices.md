---
title: Provision dispositivos Windows para monitorização remota em C - Azure Microsoft Docs
description: Descreve como ligar um dispositivo ao acelerador de solução de monitorização remota utilizando uma aplicação escrita em C em execução no Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "61450235"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Ligue o seu dispositivo ao acelerador de solução de monitorização remota (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real ao acelerador de solução de monitorização remota.

Tal como acontece com a maioria das aplicações incorporadas que funcionam em dispositivos constrangidos, o código do cliente para a aplicação do dispositivo está escrito em C. Neste tutorial, constrói a aplicação do cliente do dispositivo numa máquina que executa o Windows.

Se preferir simular um dispositivo, consulte [criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar siga os passos na [configuração](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) do seu ambiente de desenvolvimento do Windows para adicionar as ferramentas e bibliotecas de desenvolvimento necessárias à sua máquina Windows.

## <a name="view-the-code"></a>Ver o código

O [código de amostra](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilizado neste guia está disponível no repositório Azure IoT C SDKs GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Descarregue o código fonte e prepare o projeto

Para preparar o projeto, [clone o repositório Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) do GitHub.

A amostra encontra-se na **pasta amostras/soluções/remote_monitoring_client.**

Abra o ficheiro **remote_monitoring.c** na pasta **samples/solutions/remote_monitoring_client** num editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Edite o ficheiro **remote_monitoring.c** para substituir `<connectionstring>` pela cadeia de ligação do dispositivo que observou no início deste guia quando adicionou um dispositivo ao acelerador de solução.

1. Siga os passos na [Build the C SDK in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) para construir o SDK e a aplicação do cliente de monitorização remota.

1. No pedido de comando que usou para construir a solução, corra:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    A consola apresenta mensagens como:

    - A aplicação envia telemetria de amostra para o acelerador de solução.
    - Responde aos métodos invocados do painel de instrumentos de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
