---
title: Provision Linux dispositivos para monitorização remota em C - Azure Microsoft Docs
description: Descreve como ligar um dispositivo ao acelerador de solução de monitorização remota utilizando uma aplicação escrita em C em execução no Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61454504"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Ligue o seu dispositivo ao acelerador de solução de monitorização remota (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real ao acelerador de soluções de monitorização remota.

Tal como acontece com a maioria das aplicações incorporadas que funcionam em dispositivos constrangidos, o código cliente para a aplicação do dispositivo está escrito em C. Neste tutorial, você constrói a aplicação em uma máquina que executa Ubuntu (Linux).

Se preferir simular um dispositivo, consulte [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos neste guia de como guiar, precisa de um dispositivo que execute a versão 15.04 de Ubuntu ou mais tarde. Antes de prosseguir, instale o seu ambiente de [desenvolvimento Linux.](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)

## <a name="view-the-code"></a>Ver o código

O [código de amostra](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilizado neste guia está disponível no repositório Azure IoT C SDKs GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Descarregue o código fonte e prepare o projeto

Para preparar o projeto, clone ou descarregue o [repositório Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

A amostra está localizada nas **amostras/soluções/pasta remote_monitoring_client.**

Abra o ficheiro **remote_monitoring.c** na pasta **de amostras/soluções/remote_monitoring_client** num editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Os seguintes passos descrevem como usar o *CMake* para construir a aplicação do cliente. A aplicação do cliente de monitorização remota é construída como parte do processo de construção para o SDK.

1. Edite o ficheiro **remote_monitoring.c** para substituir `<connectionstring>` com a cadeia de ligação do dispositivo que observou no início deste guia de como orientar quando adicionou um dispositivo ao acelerador de soluções.

1. Navegue para a raiz da sua cópia clonada do [repositório de repositório sdks Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) e execute os seguintes comandos para construir a aplicação do cliente:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Executar a aplicação do cliente e enviar telemetria para ioT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    A consola apresenta mensagens como:

    - A aplicação envia telemetria de amostra para o acelerador de solução.
    - Responde aos métodos invocados a partir do painel de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
