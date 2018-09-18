---
title: Aprovisionar dispositivos de Linux para monitorização remota em C - Azure | Documentos da Microsoft
description: Descreve como ligar um dispositivo para o solution accelerator monitorização remota usando um aplicativo escrito em C em execução no Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: fcc11347d06cde43c79ef4272f5c2ad87555c040
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734493"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Ligar o seu dispositivo para o acelerador de solução de monitorização remota (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo físico para o acelerador de solução de monitorização remota.

Tal como acontece com aplicativos mais incorporados que são executadas em dispositivos restritos, o código de cliente para a aplicação de dispositivo é escrito em C. Neste tutorial, vai criar a aplicação num computador a executar o Ubuntu (Linux).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos, precisa de um dispositivo com o Ubuntu versão 15.04 ou posterior. Antes de continuar, [configurar o ambiente de desenvolvimento do Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Ver o código

O [código de exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring) utilizados neste guia está disponível no repositório do GitHub de SDKs do Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixe o código-fonte e preparar o projeto

Para preparar o projeto, clonar ou transferir os [repositório Azure IoT SDKs de C](https://github.com/Azure/azure-iot-sdk-c) do GitHub.

O exemplo está localizado no **amostras/soluções/remote_monitoring** pasta.

Abra o **remote_monitoring.c** de ficheiros a **exemplos/soluções/remote_monitoring** pasta num editor de texto.

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
    ./samples/solutions/remote_monitoring/remote_monitoring_client
    ```

    A consola apresenta as mensagens como:

    - A aplicação envia telemetria de exemplo para o solution accelerator.
    - Responde a métodos invocados a partir do dashboard da solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
