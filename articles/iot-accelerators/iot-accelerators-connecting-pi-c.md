---
title: Aprovisionar o Raspberry Pi a monitorização remota com C - Azure | Documentos da Microsoft
description: Descreve como ligar um dispositivo de Raspberry Pi do solution Accelerator monitorização remota usando um aplicativo escrito em C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180505"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Ligar o seu dispositivo de Raspberry Pi para o acelerador de solução de monitorização remota (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real para o acelerador de solução de monitorização remota. Tal como acontece com aplicativos mais incorporados que são executadas em dispositivos restritos, o código de cliente para a aplicação de dispositivo Raspberry Pi é escrito em C. Neste tutorial, vai criar o aplicativo num Raspberry Pi a executar o SO Raspbian.

Se preferir simular um dispositivo, veja [criar e teste de um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Hardware necessário

Um computador de secretária que lhe permite ligar remotamente à linha de comandos no Raspberry Pi.

[Microsoft IoT Starter Kit para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial utiliza os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de ambiente de trabalho necessárias

Terá de cliente SSH no seu computador desktop que lhe permite aceder remotamente a linha de comandos no Raspberry Pi.

- Windows não incluem um cliente SSH. Recomendamos que utilize [PuTTY](https://www.putty.org/).
- A maioria das distribuições de Linux e Mac OS incluem o utilitário da linha de comandos de SSH. Para obter mais informações, consulte [SSH através de Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software necessário de Raspberry Pi

Este artigo pressupõe que instalou a versão mais recente do [Raspbian SO no seu Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Os passos seguintes mostram como preparar o seu Raspberry Pi para a criação de uma aplicação de C que liga ao solution accelerator:

1. Ligar ao seu Raspberry Pi com **ssh**. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) sobre o [site Raspberry Pi](https://www.raspberrypi.org/).

1. Utilize o seguinte comando para atualizar o seu Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Para concluir os passos neste guia de procedimentos siga os passos em [configurar o ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para adicionar as ferramentas de desenvolvimento necessárias e bibliotecas para o seu Raspberry Pi.

## <a name="view-the-code"></a>Ver o código

O [código de exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilizados neste guia está disponível no repositório do GitHub de SDKs do Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixe o código-fonte e preparar o projeto

Para preparar o projeto, clonar ou transferir os [repositório Azure IoT SDKs de C](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

O exemplo está localizado no **amostras/soluções/remote_monitoring_client** pasta.

Abra o **remote_monitoring.c** de ficheiros a **exemplos/soluções/remote_monitoring_client** pasta num editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Os passos seguintes descrevem como utilizar *CMake* para criar a aplicação de cliente. A aplicação de cliente de monitorização remota foi desenvolvida como parte do processo de compilação para o SDK.

1. Editar a **remote_monitoring.c** ficheiro para substituir o `<connectionstring>` com a cadeia de ligação do dispositivo que anotou no início deste guia de procedimentos quando adicionou um dispositivo para o solution accelerator.

1. Navegue para a raiz da sua cópia clonada a [repositório Azure IoT SDKs de C](https://github.com/Azure/azure-iot-sdk-c) repositório e execute os seguintes comandos para criar a aplicação de cliente:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Execute a aplicação de cliente e enviar telemetria ao IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    A consola apresenta as mensagens como:

    - A aplicação envia telemetria de exemplo para o solution accelerator.
    - Responde a métodos invocados a partir do dashboard da solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
