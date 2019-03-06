---
title: Sincronizar estados de dispositivos a partir do Hub IoT do Azure | Microsoft Docs
description: Utilize dispositivos duplos para sincronizar estados entre os seus dispositivos e o seu hub do IoT
services: iot-hub
documentationcenter: ''
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.custom: mvc
ms.openlocfilehash: 02159f58b5e1db3de92e88dae3f6eba38b24e3c4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450860"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Tutorial: Configurar os seus dispositivos através de um serviço de back-end

Para além de receber dados telemétricos dos seus dispositivos, poderá ter de os configurar a partir do seu serviço de back-end. Quando enviar uma configuração pretendida para os seus dispositivos, também poderá querer receber atualizações de conformidade e de estado desses dispositivos. Por exemplo, poderá definir um intervalo pretendido para a temperatura de um dispositivo ou recolher informações sobre a versão do firmware dos seus dispositivos.

Para sincronizar as informações de estado entre um dispositivo e um hub do IoT, deve utilizar _dispositivos duplos_. Um [dispositivo duplo](iot-hub-devguide-device-twins.md) é um documento JSON que é associado a um dispositivo específico e é armazenado pelo Hub IoT na nuvem, onde pode efetuar [consultas](iot-hub-devguide-query-language.md) sobre o mesmo. Um dispositivo duplo contém _propriedades pretendidas_, _propriedades reportadas_ e _etiquetas_. Uma propriedade pretendida é definida por uma aplicação de back-end e é lida por um dispositivo. Uma propriedade reportada é definida por um dispositivo e é lida por uma aplicação de back-end. Uma etiqueta é definida por uma aplicação de back-end e nunca é enviada para um dispositivo. As etiquetas são utilizadas para organizar os seus dispositivos. Este tutorial mostra-lhe como utilizar as propriedades pretendidas e reportadas para sincronizar as informações de estado:

![Resumo dos dispositivos duplos](media/tutorial-device-twins/DeviceTwins.png)

Neste tutorial, vai realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub do IoT e adicionar um dispositivo de teste ao registo de identidades.
> * Utilizar as propriedades pretendidas para enviar informações de estado para o seu dispositivo simulado.
> * Utilizar as propriedades reportadas para receber informações de estado do seu dispositivo simulado.

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

Para concluir este tutorial, a sua subscrição do Azure tem de conter um hub do IoT com um dispositivo adicionado ao registo de identidades de dispositivos. A entrada no registo de identidades de dispositivos permite que o dispositivo simulado que executará neste tutorial se ligue ao seu hub.

Se ainda não tiver um hub IoT que configurar na sua subscrição, pode configurar um com o seguinte script CLI. Este script utiliza o nome **tutorial-iot-hub** para o hub do IoT. Quando o executar, deve substituir este nome pelo seu próprio nome exclusivo. O script cria o grupo de recursos e o hub na região **E.U.A. Central**, que pode alterar para uma região mais próxima de si. O script obtém a cadeia de ligação do serviço do hub do IoT, que irá utilizar no exemplo de back-end para ligar ao seu hub do IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --hub-name $hubname -o table

```

Este tutorial utiliza um dispositivo simulado chamado **MyTwinDevice**. O seguinte script adiciona este dispositivo ao seu registo de identidades e obtém a respetiva cadeia de ligação:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Enviar informações de estado

As propriedades pretendidas são utilizadas para enviar informações de estado de uma aplicação de back-end para um dispositivo. Nesta secção, saberá como:

* Receber e processar as propriedades pretendidas num dispositivo.
* Enviar as propriedades pretendidas a partir de uma aplicação de back-end.

Para ver o código de exemplo do dispositivo simulado que recebe as propriedades pretendidas, navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto de exemplo Node.js que transferiu. Em seguida, abra o ficheiro SimulatedDevice.js num editor de texto.

As secções seguintes descrevem o código que é executado no dispositivo simulado que responde às alterações de propriedades pretendidas enviadas a partir da aplicação de back-end:

### <a name="retrieve-the-device-twin-object"></a>Obter o objeto do dispositivo duplo

O seguinte código liga o seu hub do IoT através de uma cadeia de ligação do dispositivo:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

O seguinte código obtém um duplo a partir do objeto de cliente:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Propriedades pretendidas de exemplo

Pode estruturar as suas propriedades pretendidas da forma que for mais conveniente para a sua aplicação. Este exemplo utiliza uma propriedade de nível superior chamada **fanOn** e agrupa as restantes propriedades em **componentes** separados. O seguinte fragmento de código JSON apresenta a estrutura das propriedades pretendidas que este tutorial utiliza:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Criar processadores

Pode criar processadores para atualizações de propriedades pretendidas que correspondam a atualizações de diferentes níveis na hierarquia de JSON. Por exemplo, este processador monitoriza todas as alterações de propriedades pretendidas enviadas para o dispositivo a partir de uma aplicação de back-end. A variável **delta** contém as propriedades pretendidas enviadas a partir da solução de back-end:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

O seguinte processador só reage a alterações feitas à propriedade pretendida **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Processadores para múltiplas propriedades

No JSON de propriedades pretendidas de exemplo apresentado anteriormente, o nó **climate** em **componentes** contém duas propriedades, **minTemperature** e **maxTemperature**.

O objeto **duplo** local de um dispositivo armazena um conjunto completo de propriedades pretendidas e reportadas. A variável **delta** enviada do back-end pode atualizar apenas um subconjunto de propriedades pretendidas. No seguinte fragmento de código, se o dispositivo simulado receber uma atualização a uma das propriedades, **minTemperature** ou **maxTemperature**, utilizará o valor no objeto duplo local do outro valor para configurar o dispositivo:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

O objeto **duplo** local armazena um conjunto completo de propriedades pretendidas e reportadas. A variável **delta** enviada do back-end pode atualizar apenas um subconjunto de propriedades pretendidas.

### <a name="handle-insert-update-and-delete-operations"></a>Processar operações de inserção, atualização e eliminação

As propriedades pretendidas enviadas a partir do back-end não indicam qual a operação que está a ser realizada numa propriedade pretendida específica. O seu código tem de deduzir qual é a operação, com base no conjunto atual de propriedades pretendidas armazenadas localmente e nas alterações enviadas a partir do hub.

O seguinte fragmento de código mostra como o dispositivo simulado processa operações de inserção, atualização e eliminação na lista de **componentes** nas propriedades pretendidas. Pode ver como utilizar valores **nulos** para indicar que um componente deve ser eliminado:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Enviar propriedades pretendidas para um dispositivo a partir do back-end

Já viu como um dispositivo implementa processadores para receber atualizações de propriedades pretendidas. Esta secção mostra-lhe como enviar alterações de propriedades pretendidas para um dispositivo a partir de uma aplicação de back-end.

Para ver o código de exemplo do dispositivo simulado que recebe as propriedades pretendidas, navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto de exemplo Node.js que transferiu. Em seguida, abra o ficheiro ServiceClient.js num editor de texto.

O seguinte fragmento de código mostra-lhe como ligar ao registo de identidades de dispositivos e aceder ao duplo de um dispositivo específico:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

O seguinte fragmento de código mostra diferentes *patches* de propriedades pretendidas que a aplicação de back-end envia para o dispositivo:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

O seguinte fragmento de código mostra como a aplicação de back-end envia uma atualização de propriedades pretendidas para um dispositivo:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Executar as aplicações

Nesta secção, irá executar duas aplicações de exemplo para ver uma aplicação de back-end a enviar atualizações de propriedades pretendidas para uma aplicação de dispositivo simulado.

Para executar as aplicações de back-end e de dispositivo simulado, precisa das cadeias de ligação do serviço e do dispositivo. Tomou nota das cadeias de ligação quando criou os recursos no início deste tutorial.

Para executar a aplicação de dispositivo simulado, abra uma janela da linha de comandos ou shell e navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar a aplicação de back-end, abra outra janela da linha de comandos ou shell. Navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A seguinte captura de ecrã mostra os dados de saída da aplicação de dispositivo simulado e realça como esta processa uma atualização feita à propriedade pretendida **maxTemperature**. Pode ver como o processador de nível superior e os processadores de componentes de clima são executados:

![Dispositivo simulado](./media/tutorial-device-twins/SimulatedDevice1.png)

A seguinte captura de ecrã mostra os dados de saída da aplicação de back-end e realça como esta envia uma atualização feita à propriedade pretendida **maxTemperature**:

![Aplicação de back-end](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Receber informações de estado

A sua aplicação de back-end recebe as informações de estado de um dispositivo na forma de propriedades reportadas. O dispositivo define as propriedades reportadas e envia-as para o seu hub. A aplicação de back-end consegue ler os valores atuais das propriedades reportadas do dispositivo duplo armazenado no seu hub.

### <a name="send-reported-properties-from-a-device"></a>Enviar propriedades reportadas a partir de um dispositivo

Pode enviar as atualizações feitas aos valores das propriedades reportadas como um patch. O seguinte fragmento de código mostra um modelo do patch que o dispositivo simulado envia. O dispositivo simulado atualiza os campos no patch antes de o enviar para o hub:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

O dispositivo simulado utiliza a seguinte função para enviar o patch que contém as propriedades reportadas para o hub:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Processar propriedades reportadas

Uma aplicação de back-end acede aos valores atuais das propriedades reportadas de um dispositivo através do dispositivo duplo. O seguinte fragmento de código mostra-lhe como a aplicação de back-end lê os valores das propriedades reportadas do dispositivo simulado:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Executar as aplicações

Nesta secção, irá executar duas aplicações de exemplo para ver uma aplicação de dispositivo simulado a enviar atualizações de propriedades reportadas para uma aplicação de back-end.

Executará as mesmas duas aplicações de exemplo que executou para ver como as propriedades pretendidas são enviadas para um dispositivo.

Para executar as aplicações de back-end e de dispositivo simulado, precisa das cadeias de ligação do serviço e do dispositivo. Tomou nota das cadeias de ligação quando criou os recursos no início deste tutorial.

Para executar a aplicação de dispositivo simulado, abra uma janela da linha de comandos ou shell e navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Para executar a aplicação de back-end, abra outra janela da linha de comandos ou shell. Navegue para a pasta **iot-hub/Tutorials/DeviceTwins** no projeto Node.js que transferiu. Em seguida, execute os seguintes comandos:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A seguinte captura de ecrã mostra os dados de saída da aplicação de dispositivo simulado e realça como esta envia uma atualização de propriedades reportadas para o seu hub:

![Dispositivo simulado](./media/tutorial-device-twins/SimulatedDevice2.png)

Captura de ecrã seguinte mostra a saída da aplicação de back-end e destaca como ele recebe e processa uma propriedade comunicada de atualização de um dispositivo:

![Aplicação de back-end](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se tenciona concluir o próximo tutorial, saia do grupo de recursos e do hub do IoT e reutilize-os mais tarde.

Se já não precisar do Hub IoT, elimine-o, bem como ao grupo de recursos, no portal. Para tal, selecione o grupo de recursos **tutorial-iot-hub-rg** que contém o seu hub do IoT e clique em **Eliminar**.

Em alternativa, utilize a CLI:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a sincronizar informações de estado entre os seus dispositivos e o seu hub do IoT. Avance para o próximo tutorial para saber como utilizar dispositivos duplos para implementar um processo de atualização de firmware.

> [!div class="nextstepaction"]
[Implementar um processo de atualização de firmware do dispositivo](tutorial-firmware-update.md)
