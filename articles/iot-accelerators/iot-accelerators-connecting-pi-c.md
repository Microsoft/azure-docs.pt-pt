---
title: Provisão Raspberry Pi à Monitorização Remota usando C - Azure / Microsoft Docs
description: Descreve como ligar um dispositivo Raspberry Pi ao acelerador de solução de monitorização remota utilizando uma aplicação escrita em C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "61454509"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Ligue o seu dispositivo Raspberry Pi ao acelerador de solução de monitorização remota (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real ao acelerador de solução de monitorização remota. Tal como acontece com a maioria das aplicações incorporadas que funcionam em dispositivos constrangidos, o código de cliente para a aplicação do dispositivo Raspberry Pi está escrito em C. Neste tutorial, você constrói a aplicação em um Raspberry Pi executando o Raspbian OS.

Se preferir simular um dispositivo, consulte [criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Hardware necessário

Um computador de secretária que lhe permite ligar remotamente à linha de comando do Raspberry Pi.

[Kit de arranque Microsoft IoT para raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial utiliza os seguintes itens do kit:

- Framboesa Pi 3
- Cartão MicroSD (com NOOBS)
- Um mini cabo USB
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de ambiente de trabalho necessário

Precisa de um cliente SSH na sua máquina de ambiente de trabalho para lhe permitir aceder remotamente à linha de comando do Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos a utilização [de PuTTY](https://www.putty.org/).
- A maioria das distribuições linux e Mac OS incluem o utilitário SSH de linha de comando. Para obter mais informações, consulte [SSH Usando Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software Raspberry Pi obrigatório

Este artigo pressupõe que instalou a versão mais recente do [Sistema Raspbian no seu Raspberry Pi.](https://www.raspberrypi.org/learning/software-guide/quickstart/)

Os seguintes passos mostram-lhe como preparar o seu Raspberry Pi para a construção de uma aplicação C que se conecta ao acelerador de solução:

1. Ligue-se ao seu Raspberry Pi usando **ssh**. Para mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site raspberry Pi](https://www.raspberrypi.org/).

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Para completar os passos neste guia de como guiar siga os passos na configuração do [seu ambiente de desenvolvimento Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para adicionar as ferramentas e bibliotecas de desenvolvimento necessárias ao seu Raspberry Pi.

## <a name="view-the-code"></a>Ver o código

O [código de amostra](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilizado neste guia está disponível no repositório Azure IoT C SDKs GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Descarregue o código fonte e prepare o projeto

Para preparar o projeto, clone ou descarregue o [repositório Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

A amostra encontra-se na **pasta amostras/soluções/remote_monitoring_client.**

Abra o ficheiro **remote_monitoring.c** na pasta **samples/solutions/remote_monitoring_client** num editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Os passos seguintes descrevem como usar *o CMake* para construir a aplicação do cliente. A aplicação do cliente de monitorização remota é construída como parte do processo de construção do SDK.

1. Edite o ficheiro **remote_monitoring.c** para substituir `<connectionstring>` pela cadeia de ligação do dispositivo que observou no início deste guia quando adicionou um dispositivo ao acelerador de solução.

1. Navegue até à raiz da sua cópia clonada do [repositório de repositório Azure IoT C SDKs](https://github.com/Azure/azure-iot-sdk-c) e execute os seguintes comandos para construir a aplicação do cliente:

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
    - Responde aos métodos invocados do painel de instrumentos de solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
