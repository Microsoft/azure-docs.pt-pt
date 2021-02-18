---
title: incluir ficheiro
description: incluir ficheiro
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 01/14/2021
ms.openlocfilehash: 5e3b182be067e378fa3a9354d46a9888ea4710a9
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654994"
---
Nas secções seguintes, configura-se um terminal e utiliza o Azure CLI para criar um hub IoT. Para configurar um terminal que executa os comandos Azure CLI, pode utilizar o Azure Cloud Shell baseado no navegador ou utilizar um terminal local.
* Para utilizar a Cloud Shell, vá à secção seguinte: [Lance a Cloud Shell](#launch-the-cloud-shell). 
* Para utilizar um terminal local, salte a secção seguinte e vá [abrir um terminal local.](#open-a-local-terminal)

## <a name="launch-the-cloud-shell"></a>Lançar a Cloud Shell
Nesta secção, você cria uma sessão Cloud Shell e configura o seu ambiente terminal.

Inicie sessão no portal do Azure em https://portal.azure.com.  

Para lançar a Cloud Shell:

1. Selecione o botão **Cloud Shell** na barra de menu superior direita no portal Azure. 

    ![Botão Cloud Shell do portal Azure](media/iot-hub-include-create-hub-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se esta é a primeira vez que usas a Cloud Shell, isso leva-te a criar armazenamento, que é necessário para usar a Cloud Shell.  Selecione uma subscrição para criar uma conta de armazenamento e partilha de Ficheiros Microsoft Azure. 

2. Selecione o ambiente CLI preferido no dropdown **do ambiente Select.** Este quickstart usa o ambiente **Bash.** Todos os seguintes comandos CLI funcionam também no ambiente PowerShell. 

    ![Selecione ambiente CLI](media/iot-hub-include-create-hub-cli/cloud-shell-environment.png)

3. Salte a secção seguinte e vá [instalar a extensão Azure IoT](#install-the-azure-iot-extension). 

## <a name="open-a-local-terminal"></a>Abra um terminal local
Se optar por utilizar um terminal local em vez de Cloud Shell, complete esta secção.  

1. Abra um terminal local.
1. Executar o comando [de login az:](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login&preserve-view=true)

   ```azurecli
   az login
   ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador https://aka.ms/devicelogin e introduza o código de autorização exibido no seu terminal.

    Se não houver nenhum navegador web disponível ou o navegador web não abrir, utilize o fluxo de código do dispositivo com `az login --use-device-code` .

1. Inicie sessão com as credenciais da sua conta no browser.

    Para saber mais sobre os diferentes métodos de autenticação, veja [Iniciar sessão com a CLI do Azure]( /cli/azure/authenticate-azure-cli ).

1. Aceda à secção seguinte: [Instale a extensão Azure IoT](#install-the-azure-iot-extension). 

## <a name="install-the-azure-iot-extension"></a>Instale a extensão Azure IoT
Nesta secção, instale a extensão IoT do Microsoft Azure para O Azure CLI na sua concha CLI. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

> [!IMPORTANT]
> O terminal comanda no resto deste quickstart funciona o mesmo em Cloud Shell ou num terminal local. Para executar um comando, selecione **Copy** para copiar um bloco de código neste arranque rápido. Em seguida, cole-o na sua concha CLI e coloque-a.

Executar o comando [de adicionar extensão az.](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true) 

   ```azurecli
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Criar um hub IoT
Nesta secção, você usa Azure CLI para criar um hub IoT e um grupo de recursos.  Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um hub IoT funciona como um centro de mensagens central para comunicação bidis entre a sua aplicação IoT e os dispositivos. 

Para criar um hub IoT e um grupo de recursos:

1. Executar o [grupo az criar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true) comando para criar um grupo de recursos. O seguinte comando cria um grupo de recursos chamado *MyResourceGroup* na localização *leste.* 
    >[!NOTE]
    > Pode configurar opcionalmente um local alternativo. Para ver as localizações disponíveis, `az account list-locations` corra. Este tutorial usa *eastus* como mostrado no comando exemplo. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Executar o [hub az iot criar](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create&preserve-view=true) comando para criar um hub IoT. Pode levar alguns minutos para criar um hub IoT. 

    *Seu Nome YourIotHub.* Substitua este espaço reservado e os aparelhos circundantes no seguinte comando, utilizando o nome que escolheu para o seu hub IoT. Um nome de hub IoT deve ser globalmente único em Azure. Use o seu nome de hub IoT no resto deste quickstart onde quer que veja o espaço reservado.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado
Nesta secção, você cria um dispositivo IoT simulado que está ligado ao seu hub IoT. 

Para criar um dispositivo simulado:
1. Executar o comando [az iot hub dispositivo-identidade criar](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest#ext-azure-iot-az-iot-hub-device-identity-create&preserve-view=true) comando na sua concha CLI. Isto cria a identidade do dispositivo simulado. 

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    *myDevice*. Pode utilizar este nome diretamente para o ID do dispositivo simulado no resto deste artigo. Opcionalmente, use um nome diferente. 

    ```azurecli
    az iot hub device-identity create --device-id myDevice --hub-name {YourIoTHubName} 
    ```

1.  Executar o comando [de programa de programa de ligação az iot- identidade de ligação.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string?view=azure-cli-latest#ext_azure_iot_az_iot_hub_device_identity_connection_string_show&preserve-view=true) 

    ```azurecli
    az iot hub device-identity connection-string show --device-id myDevice --hub-name {YourIoTHubName}
    ```

    A saída da cadeia de ligação encontra-se no seguinte formato:

    ```Output
    HostName=<your IoT Hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<some value>
    ```

1. Guarde a cadeia de ligação num local seguro. 

> [!NOTE]
> Mantenha a casca CLI aberta. Será utilizado em passos posteriores.
