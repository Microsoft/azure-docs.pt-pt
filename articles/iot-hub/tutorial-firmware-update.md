---
title: Atualizar o firmware do dispositivo através do Hub IoT do Azure | Microsoft Docs
description: Implemente um processo de atualização de firmware do dispositivo através de tarefas e dispositivos duplos.
services: iot-hub
author: dominicbetts
manager: timlt
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2019
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: c387a7c10b962cfd27a7563c00ce58f58e97b9d5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671833"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Tutorial: Implementar um processo de atualização de firmware do dispositivo

Pode ter de atualizar o firmware nos dispositivos ligados ao hub IoT. Por exemplo, pode querer adicionar novas funcionalidades ao firmware ou aplicar patches de segurança. Em muitos cenários de IoT, não é prático visitar fisicamente e, em seguida, aplicar manualmente as atualizações de firmware aos dispositivos. Este tutorial mostra como pode iniciar e monitorizar o processo de atualização de firmware remotamente através de uma aplicação de back-end ligada ao seu hub.

Para criar e monitorizar o processo de atualização de firmware, a aplicação de back-end neste tutorial cria uma _configuração_ no hub IoT. A [gestão de dispositivos automática](iot-hub-auto-device-config.md) do Hub IoT utiliza esta configuração para atualizar um conjunto de _propriedades pretendidas dos dispositivos duplos_ em todos os dispositivos chiller. As propriedades pretendidas especificam os detalhes da atualização de firmware necessária. Embora os dispositivos chiller estejam a executar o processo de atualização de firmware, reportam o respetivo estado à aplicação de back-end através das _propriedades reportadas dos dispositivos duplos_. A aplicação de back-end pode utilizar a configuração para monitorizar as propriedades reportadas enviadas a partir do dispositivo e controlar o processo de atualização de firmware para conclusão:

![Processo de atualização de firmware](media/tutorial-firmware-update/Process.png)

Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub IoT e adicionar um dispositivo de teste ao registo de identidades do dispositivo.
> * Criar uma configuração para definir a atualização de firmware.
> * Simular o processo de atualização de firmware num dispositivo.
> * Receber atualizações de estado do dispositivo à medida que a atualização de firmware avança.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

As duas aplicações de exemplo que executa neste guia de início rápido são escritas com Node.js. Precisa do Node.js. v4.X.X ou posterior no seu computador de desenvolvimento.

Pode transferir o Node.js para múltiplas plataformas em [nodejs.org](https://nodejs.org).

Pode verificar qual a versão atual do Node.js no seu computador de desenvolvimento através do seguinte comando:

```cmd/sh
node --version
```

Transfira o projeto Node.js de exemplo de https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip e extraia o arquivo ZIP.

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

Para concluir este tutorial, a sua subscrição do Azure tem de ter um hub IoT com um dispositivo adicionado ao registo de identidades do dispositivo. A entrada no registo de identidades de dispositivos permite que o dispositivo simulado que executará neste tutorial se ligue ao seu hub.

Se ainda não tiver um hub do IoT configurado na sua subscrição, pode configurar um com o seguinte script da CLI. Este script utiliza o nome **tutorial-iot-hub** para o hub do IoT. Quando o executar, deve substituir este nome pelo seu próprio nome exclusivo. O script cria o grupo de recursos e o hub na região **E.U.A. Central**, que pode alterar para uma região mais próxima de si. O script obtém a cadeia de ligação do serviço do hub IoT, que irá utilizar na aplicação de exemplo de back-end para ligar ao seu hub IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --hub-name $hubname -o table

```

Este tutorial utiliza um dispositivo simulado chamado **MyFirmwareUpdateDevice**. O script seguinte adiciona este dispositivo ao registo de identidades do dispositivo, define um valor de etiqueta e obtém a respetiva cadeia de ligação:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Se executar estes comandos numa linha de comandos do Windows ou do Powershell, veja a página [azure-iot-cli-extension tips](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
) para obter informações sobre como citar cadeias JSON.

## <a name="start-the-firmware-update"></a>Iniciar a atualização de firmware

Vai criar uma [configuração de gestão de dispositivos automática](iot-hub-auto-device-config.md#create-a-configuration) na aplicação de back-end para iniciar o processo de atualização de firmware em todos os dispositivos marcados com um **devicetype** de chiller. Nesta secção, saberá como:

* Criar uma configuração a partir de uma aplicação de back-end.
* Monitorizar a conclusão da tarefa.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Utilizar as propriedades pretendidas para iniciar a atualização de firmware da aplicação de back-end

Para ver o código da aplicação de back-end que cria a configuração, navegue para a pasta **iot-hub/Tutorials/FirmwareUpdate** do projeto Node.js de exemplo que transferiu. Em seguida, abra o ficheiro ServiceClient.js num editor de texto.

A aplicação de back-end cria a seguinte configuração:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

A configuração inclui as seguintes secções:

* `content` especifica as propriedades pretendidas de firmware enviadas para os dispositivos selecionados.
* `metrics` especifica as consultas a executar que reportam o estado da atualização de firmware.
* `targetCondition` seleciona os dispositivos para receber a atualização de firmware.
* `priorty` define a prioridade relativa desta configuração para outras configurações.

A aplicação de back-end utiliza o código seguinte para criar a configuração para definir as propriedades pretendidas:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Depois de criar a configuração, a aplicação monitoriza a atualização de firmware:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Uma configuração reporta dois tipos de métricas:

* Métricas de sistema que reportam quantos dispositivos são visados e quantos dispositivos têm a atualização aplicada.
* Métricas personalizadas geradas pelas consultas adicionadas à configuração. Neste tutorial, as consultas reportam quantos dispositivos estão em cada fase do processo de atualização.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Responder ao pedido de atualização de firmware no dispositivo

Para ver o código do dispositivo simulado que processa as propriedades pretendidas de firmware enviadas a partir da aplicação de back-end, navegue para a pasta **iot-hub/Tutorials/FirmwareUpdate** no projeto Node.js de exemplo que transferiu. Em seguida, abra o ficheiro SimulatedDevice.js num editor de texto.

A aplicação do dispositivo simulado cria um processador de atualizações para as propriedades pretendidas **properties.desired.firmware** no dispositivo duplo. No processador, efetua algumas verificações básicas nas propriedades pretendidas antes de iniciar o processo de atualização:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Atualizar o firmware

A função **initiateFirmwareUpdateFlow** executa a atualização. Esta função utiliza a função **waterfall** para executar as fases do processo de atualização na sequência. Neste exemplo, a atualização de firmware tem quatro fases. A primeira fase transfere a imagem, a segunda fase verifica a imagem através de uma soma de verificação, a terceira fase aplica a imagem e a última fase reinicia o dispositivo:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Durante o processo de atualização, o dispositivo simulado reporta o progresso através das propriedades reportadas:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

O fragmento seguinte mostra a implementação da fase de transferência. Durante a fase de transferência, o dispositivo simulado utiliza as propriedades reportadas para enviar informações de estado para a aplicação de back-end:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Executar o exemplo

Nesta secção, vai executar duas aplicações de exemplo para observar de que forma uma aplicação de back-end cria a configuração para gerir o processo de atualização de firmware no dispositivo simulado.

Para executar as aplicações de back-end e de dispositivo simulado, precisa das cadeias de ligação do serviço e do dispositivo. Tomou nota das cadeias de ligação quando criou os recursos no início deste tutorial.

Para executar a aplicação do dispositivo simulado, abra uma janela da linha de comandos ou shell e navegue para a pasta **iot-hub/Tutorials/FirmwareUpdate** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar a aplicação de back-end, abra outra janela da linha de comandos ou shell. Navegue para a pasta **iot-hub/Tutorials/FirmwareUpdate** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A captura de ecrã seguinte mostra o resultado da aplicação do dispositivo simulado e mostra como responde à atualização das propriedades pretendidas de firmware a partir da aplicação de back-end:

![Dispositivo simulado](./media/tutorial-firmware-update/SimulatedDevice.png)

A captura de ecrã seguinte mostra o resultado da aplicação de back-end e realça como esta cria a configuração para atualizar as propriedades pretendidas de firmware:

![Aplicação de back-end](./media/tutorial-firmware-update/BackEnd1.png)

A captura de ecrã seguinte mostra o resultado da aplicação de back-end e realça como esta monitoriza as métricas de atualização de firmware a partir do dispositivo simulado:

![Aplicação de back-end](./media/tutorial-firmware-update/BackEnd2.png)

Devido à latência no registo de identidade do dispositivo Hub IoT, poderá não ver todas as atualizações de estado enviadas para a aplicação de back-end. Também pode ver as métricas no portal na secção **Gestão de dispositivos automática -> Configuração do dispositivo IoT** do seu hub IoT:

![Ver a configuração no portal](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se tenciona concluir o próximo tutorial, saia do grupo de recursos e do hub do IoT e reutilize-os mais tarde.

Se já não precisar do Hub IoT, elimine-o, bem como ao grupo de recursos, no portal. Para tal, selecione o grupo de recursos **tutorial-iot-hub-rg** que contém o seu hub do IoT e clique em **Eliminar**.

Em alternativa, utilize a CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a implementar um processo de atualização de firmware para os seus dispositivos ligados. Avance para o próximo tutorial para aprender a utilizar as ferramentas do portal do Azure IoT Hub e comandos da CLI do Azure para testar a conectividade do dispositivo.

> [!div class="nextstepaction"]
[Utilize um dispositivo simulado para testar a conectividade com o seu hub IoT](tutorial-connectivity.md)
