---
title: Aprovisionar dispositivos do Windows para monitorização remota em C - Azure | Documentos da Microsoft
description: Descreve como ligar um dispositivo para o solution accelerator monitorização remota usando um aplicativo escrito em C em execução no Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182902"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Ligar o seu dispositivo para o acelerador de solução de monitorização remota (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra-lhe como ligar um dispositivo real para o acelerador de solução de monitorização remota.

Tal como acontece com aplicativos mais incorporados que são executadas em dispositivos restritos, o código de cliente para a aplicação de dispositivo é escrito em C. Neste tutorial, vai criar a aplicação de cliente de dispositivo num computador a executar o Windows.

Se preferir simular um dispositivo, veja [criar e teste de um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os passos neste guia de procedimentos siga os passos em [configurar o ambiente de desenvolvimento do Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) para adicionar as ferramentas de desenvolvimento necessárias e bibliotecas para o seu computador Windows.

## <a name="view-the-code"></a>Ver o código

O [código de exemplo](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) utilizados neste guia está disponível no repositório do GitHub de SDKs do Azure IoT C.

### <a name="download-the-source-code-and-prepare-the-project"></a>Baixe o código-fonte e preparar o projeto

Para preparar o projeto, [clonar o repositório de SDKs do Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) do GitHub.

O exemplo está localizado no **amostras/soluções/remote_monitoring_client** pasta.

Abra o **remote_monitoring.c** de ficheiros a **exemplos/soluções/remote_monitoring_client** pasta num editor de texto.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

1. Editar a **remote_monitoring.c** ficheiro para substituir o `<connectionstring>` com a cadeia de ligação do dispositivo que anotou no início deste guia de procedimentos quando adicionou um dispositivo para o solution accelerator.

1. Siga os passos em [criar o SDK de C no Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) para criar o SDK e a aplicação de cliente de monitorização remota.

1. No prompt de comando que utilizou para criar a solução, execute:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    A consola apresenta as mensagens como:

    - A aplicação envia telemetria de exemplo para o solution accelerator.
    - Responde a métodos invocados a partir do dashboard da solução.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
