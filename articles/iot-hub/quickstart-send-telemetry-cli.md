---
title: Quickstart - Enviar telemetria para Azure IoT Hub (CLI) quickstart
description: Este quickstart mostra aos desenvolvedores novos no IoT Hub como começar usando o Azure CLI para criar um hub IoT, enviar telemetria e ver mensagens entre um dispositivo e o hub.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 4671880490a9ce9e29f49ede0e7687bdcf639a7e
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199803"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Quickstart: Enviar telemetria de um dispositivo para um hub IoT e monitorizá-lo com o Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste arranque rápido, você usa o CLI Azure para criar um Hub IoT e um dispositivo simulado, enviar telemetria do dispositivo para o centro e enviar uma mensagem nuvem-para-dispositivo. Também usa o portal Azure para visualizar as métricas do dispositivo. Este é um fluxo de trabalho básico para desenvolvedores que usam o CLI para interagir com uma aplicação IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Pode executar todos os comandos neste quickstart usando o Azure Cloud Shell, uma concha CLI interativa que funciona no seu navegador. Se utilizar a Cloud Shell, não precisa de instalar nada. Se preferir utilizar o CLI localmente, este quickstart requer a versão 2.0.76 ou posterior do Azure CLI. Execute az --version para localizar a versão. Para instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

Independentemente de executar o CLI localmente ou na Cloud Shell, mantenha o portal aberto no seu navegador.  Usa-o mais tarde neste arranque rápido.

## <a name="launch-the-cloud-shell"></a>Lançar a Cloud Shell
Nesta secção, você lança uma instância da Azure Cloud Shell. Se utilizar o CLI localmente, salte para a secção [Preparar duas sessões CLI](#prepare-two-cli-sessions).

Para lançar a Cloud Shell:

1. Selecione o botão **Cloud Shell** na barra de menu superior direita no portal Azure. 

    ![Botão Cloud Shell do portal Azure](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se esta é a primeira vez que usas a Cloud Shell, isso leva-te a criar armazenamento, que é necessário para usar a Cloud Shell.  Selecione uma subscrição para criar uma conta de armazenamento e partilha de Ficheiros Microsoft Azure. 

2. Selecione o ambiente CLI preferido no dropdown **do ambiente Select.** Este quickstart usa o ambiente **Bash.** Todos os seguintes comandos CLI funcionam também no ambiente PowerShell. 

    ![Selecione ambiente CLI](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Preparar duas sessões CLI

Nesta secção, prepare duas sessões Azure CLI. Se estiver a utilizar o Cloud Shell, executará as duas sessões em separado separados separados separados. Se utilizar um cliente CLI local, executará duas instâncias CLI separadas. Utilizará a primeira sessão como um dispositivo simulado e a segunda sessão para monitorizar e enviar mensagens. Para executar um comando, selecione **Copy** para copiar um bloco de código neste arranque rápido, cole-o na sua sessão de concha e execute-o.

O Azure CLI requer que você esteja registado na sua conta Azure. Toda a comunicação entre a sua sessão de concha Azure CLI e o seu hub IoT é autenticada e encriptada. Como resultado, este quickstart não necessita de autenticação adicional que utilizaria com um dispositivo real, como uma cadeia de ligação.

*  Executar o comando [de adicionar extensão az](/cli/azure/extension#az-extension-add) para adicionar a extensão IoT do Microsoft Azure para Azure CLI à sua concha CLI. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Depois de instalar a extensão Azure IOT, não precisa de a instalar novamente em nenhuma sessão cloud Shell. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Abra uma segunda sessão de CLI.  Se estiver a utilizar o Cloud Shell, selecione **Abrir nova sessão**. Se estiver a usar o CLI localmente, abra uma segunda instância. 

    >[!div class="mx-imgBorder"]
    >![Abrir nova sessão cloud shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Criar um Hub IoT
Nesta secção, você usa o CLI Azure para criar um grupo de recursos e um Hub IoT.  Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um Hub IoT funciona como um centro de mensagens central para comunicação bidis entre a sua aplicação IoT e os dispositivos. 

> [!TIP]
> Opcionalmente, pode criar um grupo de recursos Azure, um Hub IoT e outros recursos utilizando o [portal Azure](iot-hub-create-through-portal.md), [Visual Studio Code,](iot-hub-create-use-iot-toolkit.md)ou outros métodos programáticos.  

1. Executar o [grupo az criar](/cli/azure/group#az-group-create) comando para criar um grupo de recursos. O seguinte comando cria um grupo de recursos chamado *MyResourceGroup* na localização *leste.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Executar o [hub az iot criar](/cli/azure/iot/hub#az-iot-hub-create) comando para criar um hub IoT. Pode levar alguns minutos para criar um hub IoT. 

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. Um nome de hub IoT deve ser globalmente único em Azure. Este espaço reservado é utilizado no resto deste quickstart para representar o seu nome de hub IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Criar e monitorizar um dispositivo
Nesta secção, cria-se um dispositivo simulado na primeira sessão de CLI. O dispositivo simulado envia telemetria do dispositivo para o seu hub IoT. Na segunda sessão de CLI, monitoriza eventos e telemetria e envia uma mensagem nuvem-dispositivo para o dispositivo simulado.

Para criar e iniciar um dispositivo simulado:
1. Executar o comando [az iot hub dispositivo-identidade criar](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) comando na primeira sessão CLI. Isto cria a identidade do dispositivo simulado. 

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    *simDevice*. Pode utilizar este nome diretamente para o dispositivo simulado no resto deste arranque rápido. Opcionalmente, use um nome diferente. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Executar o [comando simulação do dispositivo az iot](/cli/azure/ext/azure-iot/iot/device#ext-azure-iot-az-iot-device-simulate) na primeira sessão CLI.  Isto inicia o dispositivo simulado. O dispositivo envia telemetria para o seu hub IoT e recebe mensagens do mesmo.  

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Para monitorizar um dispositivo:
1. Na segunda sessão de CLI, executar o comando [az iot hub monitor-events.](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) Isto começa a monitorizar o dispositivo simulado. A saída mostra a telemetria que o dispositivo simulado envia para o hub IoT.

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell monitoriza eventos](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Depois de monitorizar o dispositivo simulado na segunda sessão de CLI, prima Ctrl+C para parar a monitorização. 

## <a name="use-the-cli-to-send-a-message"></a>Use o CLI para enviar uma mensagem
Nesta secção, utilize a segunda sessão de CLI para enviar uma mensagem ao dispositivo simulado.

1. Na primeira sessão de CLI, confirme que o dispositivo simulado está em funcionamento. Se o dispositivo tiver parado, executar o seguinte comando para o iniciar:

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Na segunda sessão de CLI, executar o [comando de envio de mensagem c2d do dispositivo az iot.](/cli/azure/ext/azure-iot/iot/device/c2d-message#ext-azure-iot-az-iot-device-c2d-message-send) Isto envia uma mensagem nuvem-para-dispositivo do seu hub IoT para o dispositivo simulado. A mensagem inclui uma corda e dois pares de valores-chave.  

    *Seu Nome YourIotHub.* Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcionalmente, pode enviar mensagens nuvem-para-dispositivo utilizando o portal Azure. Para isso, navegue na página geral para o seu IoT Hub, selecione **Dispositivos IoT,** selecione o dispositivo simulado e selecione **mensagem para dispositivo**. 

1. Na primeira sessão do CLI, confirme que o dispositivo simulado recebeu a mensagem. 

    ![Mensagem nuvem-para-dispositivo cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Depois de ver a mensagem, feche a segunda sessão de CLI. Mantenha aberta a primeira sessão CLI. Usa-se para limpar recursos num passo posterior.

## <a name="view-messaging-metrics-in-the-portal"></a>Ver métricas de mensagens no portal
O portal Azure permite-lhe gerir todos os aspetos do seu Hub E dispositivos IoT. Numa aplicação típica do IoT Hub que ingere telemetria a partir de dispositivos, é melhor monitorizar os dispositivos ou ver métricas na telemetria do dispositivo. 

Para visualizar métricas de mensagens no portal Azure:
1. No menu de navegação à esquerda no portal, selecione **Todos os Recursos**. Isto lista todos os recursos da sua subscrição, incluindo o hub IoT que criou. 

1. Selecione o link no hub IoT que criou. O portal apresenta a página geral do hub.

1. Selecione **métricas** no painel esquerdo do seu Hub IoT. 

    ![Métricas de mensagens IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Insira o nome do seu hub IoT no **Âmbito**.

2. Selecione *Iot Hub Standard Metrics* in **Metric Namespace**.

3. Selecione *o número total de mensagens utilizadas* na **Métrica.** 

4. Passe o ponteiro do rato sobre a área da linha temporal em que o seu dispositivo enviou mensagens. O número total de mensagens num ponto do tempo aparece no canto inferior esquerdo da linha do tempo.

    ![Ver métricas do Hub Azure IoT](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcionalmente, utilize o **dropdown métrico** para exibir outras métricas no seu dispositivo simulado. Por exemplo, *entregas de mensagens C2d concluídas* ou *dispositivos totais (pré-visualização)*. 

## <a name="clean-up-resources"></a>Limpar os recursos
Se já não precisar dos recursos Azure criados neste arranque rápido, pode utilizar o CLI Azure para os eliminar.

Se continuar com o próximo artigo recomendado, pode manter os recursos que já criou e reutilizá-los. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Para eliminar um grupo de recursos por nome:
1. Executar o [comando de eliminação do grupo az.](/cli/azure/group#az-group-delete) Isto remove o grupo de recursos, o Hub IoT e o registo do dispositivo que criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Executar o comando [da lista de grupos az](/cli/azure/group#az-group-list) para confirmar que o grupo de recursos é eliminado.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, você usou o CLI Azure para criar um hub IoT, criar um dispositivo simulado, enviar telemetria, monitorizar a telemetria, enviar uma mensagem nuvem-para-dispositivo e limpar recursos. Usou o portal Azure para visualizar as métricas de mensagens no seu dispositivo.

Se for um desenvolvedor de dispositivos, o próximo passo sugerido é ver o quickstart da telemetria que utiliza o Azure IoT Device SDK para C. Opcionalmente, consulte um dos artigos de telemetria Azure IoT Hub no seu idioma preferido ou SDK.

> [!div class="nextstepaction"]
> [Quickstart: Enviar telemetria de um dispositivo para um hub IoT (C)](quickstart-send-telemetry-c.md)