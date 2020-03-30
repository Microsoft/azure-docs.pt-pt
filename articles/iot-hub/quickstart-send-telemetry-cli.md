---
title: Envie telemetria para Azure IoT Hub (CLI) quickstart
description: Este quickstart mostra aos desenvolvedores novos no IoT Hub como começar usando o Azure CLI para criar um hub IoT, enviar telemetria e ver mensagens entre um dispositivo e o hub.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851412"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Quickstart: Envie a telemetria de um dispositivo para um hub IoT e monitorize-a com o Azure CLI

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste arranque rápido, utiliza-se o Azure CLI para criar um Hub IoT e um dispositivo simulado, enviar telemetria do dispositivo para o hub e enviar uma mensagem cloud-to-device. Também utiliza o portal Azure para visualizar as métricas do dispositivo. Este é um fluxo de trabalho básico para desenvolvedores que usam o CLI para interagir com uma aplicação IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Azure CLI. Você pode executar todos os comandos neste quickstart usando a Azure Cloud Shell, uma concha CLI interativa que corre no seu navegador. Se utilizar a Cloud Shell, não precisa de instalar nada. Se preferir utilizar o CLI localmente, este quickstart requer a versão Azure CLI 2.0.76 ou posterior. Execute az --version para localizar a versão. Para instalar ou atualizar, consulte [Instalar o Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

Independentemente de executar o CLI localmente ou na Cloud Shell, mantenha o portal aberto no seu navegador.  Usa-o mais tarde neste início rápido.

## <a name="launch-the-cloud-shell"></a>Lançar a Cloud Shell
Nesta secção, você lança uma instância da Casca de Nuvem Azure. Se utilizar o CLI localmente, salte para a secção [Prepare duas sessões CLI](#prepare-two-cli-sessions).

Para lançar a Cloud Shell:

1. Selecione o botão **Cloud Shell** na barra de menu salinos no portal Azure. 

    ![Botão Cloud Shell portal Azure](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se esta é a primeira vez que usa a Cloud Shell, isso leva-o a criar armazenamento, que é necessário para usar a Cloud Shell.  Selecione uma subscrição para criar uma conta de armazenamento e partilhar o Microsoft Azure Files. 

2. Selecione o seu ambiente CLI preferido no **downdown ambiente Select.** Este quickstart usa o ambiente **Bash.** Todos os comandos CLI seguintes também funcionam no ambiente Powershell. 

    ![Selecione ambiente CLI](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Preparar duas sessões de CLI

Nesta secção, prepare duas sessões Azure CLI. Se estiver a utilizar a Cloud Shell, executará as duas sessões em separados. Se utilizar um cliente CLI local, executará duas instâncias CLI separadas. Utilizará a primeira sessão como um dispositivo simulado e a segunda sessão para monitorizar e enviar mensagens. Para executar um comando, selecione **Copy** para copiar um bloco de código neste arranque rápido, cole-o na sua sessão de concha e execute-o.

O Azure CLI exige que esteja registado na sua conta Azure. Toda a comunicação entre a sua sessão de concha Seleção Azure CLI e o seu hub IoT é autenticada e encriptada. Como resultado, este arranque rápido não necessita de autenticação adicional que utilizaria com um dispositivo real, como uma cadeia de ligação.

*  Executar o comando [de adição de extensão az](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua concha CLI. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Depois de instalar a extensão Azure IOT, não precisa de a instalar novamente em nenhuma sessão da Cloud Shell. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Abra uma segunda sessão do CLI.  Se estiver a utilizar a Cloud Shell, **selecione Open new session**. Se estiver a usar o CLI localmente, abra uma segunda instância. 

    >[!div class="mx-imgBorder"]
    >![Abrir nova sessão cloud shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Criar um Hub IoT
Nesta secção, você usa o AZURE CLI para criar um grupo de recursos e um Hub IoT.  Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um IoT Hub funciona como um centro de mensagens central para a comunicação bidirecional entre a sua aplicação IoT e os dispositivos. 

> [!TIP]
> Opcionalmente, pode criar um grupo de recursos Azure, um Hub IoT, e outros recursos utilizando o [portal Azure,](iot-hub-create-through-portal.md) [Visual Studio Code,](iot-hub-create-use-iot-toolkit.md)ou outros métodos programáticos.  

1. Executar o [grupo AZ criar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) comando para criar um grupo de recursos. O seguinte comando cria um grupo de recursos chamado *MyResourceGroup* na localização *oriental.* 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Executar o [hub az iot criar](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) comando para criar um hub IoT. Pode levar alguns minutos para criar um centro de iot. 

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. Um nome de hub IoT deve ser globalmente único em Azure. Este espaço reservado é utilizado no resto deste quickstart para representar o seu nome de hub IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Criar e monitorizar um dispositivo
Nesta secção, cria-se um dispositivo simulado na primeira sessão do CLI. O dispositivo simulado envia telemetria do dispositivo para o seu hub IoT. Na segunda sessão do CLI, monitoriza eventos e telemetria e envia uma mensagem cloud-to-device para o dispositivo simulado.

Para criar e iniciar um dispositivo simulado:
1. Executar o [dispositivo-identidade do hub az iot criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) comando na primeira sessão CLI. Isto cria a identidade do dispositivo simulado. 

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    *simDevice*. Pode utilizar este nome diretamente para o dispositivo simulado no resto deste arranque rápido. Opcionalmente, use um nome diferente. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Executar o [dispositivo az iot simular](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) o comando na primeira sessão CLI.  Isto inicia o dispositivo simulado. O dispositivo envia telemetria para o seu hub IoT e recebe mensagens dele.  

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Para monitorizar um dispositivo:
1. Na segunda sessão do CLI, execute o comando de [monitores de eventos do centro az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) Isto começa a monitorizar o dispositivo simulado. A saída mostra a telemetria que o dispositivo simulado envia para o centro IoT.

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell monitoriza eventos](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Depois de monitorizar o dispositivo simulado na segunda sessão CLI, prima Ctrl+C para parar a monitorização. 

## <a name="use-the-cli-to-send-a-message"></a>Use o CLI para enviar uma mensagem
Nesta secção, utiliza a segunda sessão CLI para enviar uma mensagem para o dispositivo simulado.

1. Na primeira sessão do CLI, confirme se o dispositivo simulado está em funcionamento. Se o dispositivo tiver parado, execute o seguinte comando para o iniciar:

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Na segunda sessão do CLI, execute o comando de envio de [mensagens c2d do dispositivo az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) Isto envia uma mensagem cloud-to-device do seu hub IoT para o dispositivo simulado. A mensagem inclui uma corda e dois pares de valor-chave.  

    *YourIotHubName*. Substitua este espaço reservado abaixo pelo nome que escolheu para o seu hub IoT. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcionalmente, pode enviar mensagens cloud-to-device utilizando o portal Azure. Para isso, navegue na página de visão geral para o seu Hub IoT, selecione **Dispositivos IoT,** selecione o dispositivo simulado e selecione **Mensagem para Dispositivo**. 

1. Na primeira sessão do CLI, confirme que o dispositivo simulado recebeu a mensagem. 

    ![Mensagem cloud-to-device da Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Depois de ver a mensagem, feche a segunda sessão do CLI. Mantenha a primeira sessão do CLI aberta. Usa-o para limpar recursos num passo posterior.

## <a name="view-messaging-metrics-in-the-portal"></a>Ver métricas de mensagens no portal
O portal Azure permite-lhe gerir todos os aspetos do seu Hub IoT e dispositivos. Numa aplicação típica do IoT Hub que ingere a telemetria a partir de dispositivos, é melhor monitorizar dispositivos ou visualizar métricas na telemetria do dispositivo. 

Para visualizar as métricas de mensagens no portal Azure:
1. No menu de navegação à esquerda no portal, selecione **Todos os Recursos.** Isto lista todos os recursos na sua subscrição, incluindo o hub IoT que criou. 

1. Selecione o link no hub IoT que criou. O portal exibe a página de visão geral para o centro.

1. Selecione **Métricas** no painel esquerdo do seu Hub IoT. 

    ![Métricas de mensagens IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Introduza o seu nome de hub IoT no **Scope**.

2. Selecione *Iot Hub Standard Metrics* in **Metric Namespace**.

3. Selecione *o número total de mensagens utilizadas* em **Métricas**. 

4. Passe o ponteiro do rato sobre a área da linha do tempo em que o seu dispositivo enviou mensagens. O número total de mensagens num momento aparece no canto inferior esquerdo da linha do tempo.

    ![Ver métricas do Hub Azure IoT](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcionalmente, utilize a queda **métrica** para exibir outras métricas no seu dispositivo simulado. Por exemplo, entregas de *mensagens C2d concluídas* ou *dispositivos Totais (pré-visualização)*. 

## <a name="clean-up-resources"></a>Limpar recursos
Se já não precisar dos recursos Azure criados neste arranque rápido, pode utilizar o Azure CLI para os apagar.

Se continuar com o próximo artigo recomendado, pode manter os recursos que já criou e reutilizá-los. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Para eliminar um grupo de recursos por nome:
1. Executar o [grupo AZ eliminar](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) o comando. Isto remove o grupo de recursos, o IoT Hub e o registo do dispositivo que criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Executar o comando da lista de [grupos AZ](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) para confirmar que o grupo de recursos é eliminado.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes
Neste arranque rápido, usou o Azure CLI para criar um hub IoT, criar um dispositivo simulado, enviar telemetria, monitorizar a telemetria, enviar uma mensagem cloud-to-device e limpar recursos. Usou o portal Azure para visualizar métricas de mensagens no seu dispositivo.

Se for um desenvolvedor de dispositivos, o próximo passo sugerido é ver o quickstart de telemetria que utiliza o Dispositivo Azure IoT SDK para C. Opcionalmente, consulte um dos artigos de telemetria do Azure IoT Hub disponíveis na sua língua preferida ou SDK.

> [!div class="nextstepaction"]
> [Quickstart: Enviar telemetria de um dispositivo para um hub IoT (C)](quickstart-send-telemetry-c.md)
