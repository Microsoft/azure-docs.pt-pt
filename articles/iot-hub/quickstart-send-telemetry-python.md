---
title: Guia de início rápido sobre como enviar telemetria para o Hub IoT do Azure (Python) | Microsoft Docs
description: Neste início rápido, irá executar um exemplo de aplicação Python para enviar telemetria simulada para um hub IoT e utilizar um utilitário para ler telemetria do mesmo.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: c92b019e15c6a9ee5b2d38e240ae4f9891621f72
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360206"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Início rápido: Enviar telemetria de um dispositivo para um hub IoT e lê-lo com um aplicativo de back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste guia de início rápido, irá enviar telemetria a partir de uma aplicação de dispositivo simulado, através do Hub IoT, para uma aplicação back-end para processamento.

Este guia de início rápido utiliza uma aplicação Python pré-escrita para enviar a telemetria e um utilitário de CLI para lê-la a partir do hub. Antes de executar estas duas aplicações, tem de criar um hub IoT e registar um dispositivo no hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

O aplicativo de exemplo executado neste guia de início rápido é escrito usando Python. Atualmente, o Microsoft Azure SDKs do IoT para Python dão suporte apenas a versões específicas do Python para cada plataforma. Para saber mais, confira o Leiame do [SDK do Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).

Este guia de início rápido pressupõe que você está usando um computador de desenvolvimento do Windows. Para sistemas Windows, há suporte apenas para [Python 3.6. x](https://www.python.org/downloads/release/python-368/) . O instalador do Python que escolher deve basear-se na arquitetura do sistema com o qual está a trabalhar. Se a arquitetura de CPU do sistema for de 32 bits, baixe o instalador x86; para a arquitetura de 64 bits, baixe o instalador x86-64. Além disso, verifique se o [Microsoft C++ Visual redistribuível para Visual Studio 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) está instalado para sua arquitetura (x86 ou x64).

Você pode baixar o Python para outras plataformas do [Python.org](https://www.python.org/downloads/).

Pode verificar qual a versão atual do Python no seu computador de desenvolvimento através de um dos seguintes comandos:

```python
python - -version
```

```python
python3 - -version
```

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Transfira o projeto Python de exemplo a partir de https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

    **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

    **MyPythonDevice**: Este é o nome fornecido para o dispositivo registrado. Utilize MyPythonDevice, conforme mostrado. Se escolher um nome diferente para o seu dispositivo, também irá precisar de utilizar esse nome através deste artigo, e atualize o nome do dispositivo em aplicações de exemplo antes de as executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão do dispositivo_ para o dispositivo registrado:

    **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto Python de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o ficheiro **SimulatedDevice.py** num editor de texto à sua escolha.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de ligação do dispositivo que anotou anteriormente. Em seguida, guarde as alterações feitas ao ficheiro **SimulatedDevice.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas exigidas para a aplicação de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar a aplicação de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-python/SimulatedDevice.png)
    
### <a name="to-avoid-the-import-iothubclient-error"></a>Para evitar o erro de importação de iothub_client
A versão atual do SDK do Azure IoT para Python é um wrapper em [nosso SDK do C](https://github.com/azure/azure-iot-sdk-c). Ele é gerado usando a biblioteca [Boost](https://www.boost.org/) . Por isso, ele vem com várias limitações significativas. Veja mais detalhes [aqui](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)

1. Verifique se você tem a versão correta do [Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues). Lembre-se de que apenas determinadas versões funcionam bem para esse exemplo. 
2. Verifique se você tem a versão certa do C++ tempo de execução [Microsoft Visual C++ redistribuível para Visual Studio 2019](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads). (Recomendamos o mais recente).
3. Verifique se você instalou o cliente iothub: `pip install azure-iothub-device-client`.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria a partir do seu hub

A extensão da CLI do Hub IoT pode ligar ao ponto final de **Eventos** do lado do serviço no seu Hub IoT. A extensão recebe as mensagens do dispositivo para a cloud enviadas a partir do seu dispositivo simulado. Uma aplicação back-end do Hub IoT é normalmente executada na cloud para receber e processar mensagens do dispositivo para a cloud.

Execute os seguintes comandos no Azure Cloud Shell, ao substituir `YourIoTHubName` pelo nome do hub IoT:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

A seguinte captura de ecrã mostra a saída à medida que a extensão recebe telemetria enviada pelo dispositivo simulado para o hub:

![Executar a aplicação back-end](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, configurou um hub IoT, registou um dispositivo, enviou telemetria simulada para o hub com uma aplicação Python e leu a telemetria do hub com uma simples aplicação back-end.

Para saber como controlar o seu dispositivo simulado a partir de uma aplicação back-end, continue para o guia de início rápido seguinte.

> [!div class="nextstepaction"]
> [Quickstart: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-python.md)
