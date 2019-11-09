---
title: Enviar telemetria para o início rápido do Hub IoT do Azure (CLI)
description: Este guia de início rápido mostra os desenvolvedores novos para o Hub IoT como começar usando o CLI do Azure para criar um hub IoT, enviar telemetria e exibir mensagens entre um dispositivo e o Hub.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: d926f1c286c4288784d429f13b22436d7c8ae7e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73849277"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Início rápido: enviar telemetria de um dispositivo para um hub IoT e monitorá-lo com o CLI do Azure

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que lhe permite ingerir elevados volumes de telemetria dos seus dispositivos IoT para a cloud para armazenamento ou processamento. Neste guia de início rápido, você usa o CLI do Azure para criar um hub IoT e um dispositivo simulado, enviar telemetria do dispositivo para o Hub e enviar uma mensagem da nuvem para o dispositivo. Você também usa o portal do Azure para visualizar as métricas do dispositivo. Esse é um fluxo de trabalho básico para desenvolvedores que usam a CLI para interagir com um aplicativo de Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos
- Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- CLI do Azure. Você pode executar todos os comandos neste guia de início rápido usando o Azure Cloud Shell, um shell de CLI interativo que é executado em seu navegador. Se você usar o Cloud Shell, não precisará instalar nada. Se você preferir usar a CLI localmente, este início rápido exigirá CLI do Azure versão 2.0.76 ou posterior. Execute az --version para localizar a versão. Para instalar ou atualizar, consulte [instalar CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

Independentemente de você executar a CLI localmente ou na Cloud Shell, mantenha o portal aberto em seu navegador.  Você o usará posteriormente neste guia de início rápido.

## <a name="launch-the-cloud-shell"></a>Iniciar o Cloud Shell
Nesta seção, você inicia uma instância do Azure Cloud Shell. Se você usar a CLI localmente, pule para a seção [preparar duas sessões da CLI](#prepare-two-cli-sessions).

Para iniciar o Cloud Shell:
1. Selecione o botão **Cloud Shell** na barra de menus superior direita na portal do Azure. 

    ![Botão de Cloud Shell de portal do Azure](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Se esta for a primeira vez que você usou o Cloud Shell, ele solicitará que você crie o armazenamento, que é necessário para usar o Cloud Shell.  Selecione uma assinatura para criar uma conta de armazenamento e Microsoft Azure compartilhamento de arquivos. 

1. Selecione o ambiente da CLI preferencial na lista suspensa **selecionar ambiente** . Este guia de início rápido usa o ambiente **bash** . Todos os comandos da CLI a seguir também funcionam no ambiente do PowerShell. 

    ![Selecionar ambiente da CLI](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Preparar duas sessões da CLI
Nesta seção, você prepara duas sessões de CLI do Azure. Na Cloud Shell, você executará as duas sessões em guias separadas do navegador. Em um cliente CLI local, você executará duas instâncias separadas da CLI. Você usará a primeira sessão como um dispositivo simulado e a segunda sessão para monitorar e enviar mensagens. Para executar um comando, selecione **copiar** para copiar um bloco de código neste guia de início rápido, Cole-o na sessão do Shell e execute-o.

CLI do Azure exige que você esteja conectado à sua conta do Azure. Toda a comunicação entre a sessão do CLI do Azure Shell e o Hub IoT é autenticada e criptografada. Como resultado, este guia de início rápido não precisa de autenticação adicional que você usaria com um dispositivo real, como uma cadeia de conexão.

1. Execute o comando [AZ Extension Add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) para adicionar a extensão Microsoft Azure IoT para CLI do Azure ao Shell da CLI. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

   ```azurecli
   az extension add --name azure-cli-iot-ext
   ```
    Depois de instalar a extensão de IOT do Azure, você não precisará instalá-la novamente em nenhuma sessão de Cloud Shell. 

1. Abra uma segunda sessão da CLI.  Se você estiver usando o Cloud Shell, selecione **abrir nova sessão**. Se você estiver usando a CLI localmente, abra uma segunda instância. 

    ![Abrir nova sessão de Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>Criar um Hub IoT
Nesta seção, você usa o CLI do Azure para criar um grupo de recursos e um hub IoT.  Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um hub IoT atua como um hub de mensagens central para comunicação bidirecional entre o aplicativo IoT e os dispositivos. 

> [!TIP]
> Opcionalmente, você pode criar um grupo de recursos do Azure, um hub IoT e outros recursos usando o [portal do Azure](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)ou outros métodos programáticos.  

1. Execute o comando [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) para criar um grupo de recursos. O comando a seguir cria um grupo de recursos chamado *MyResource* Group na localização *lesteus* . 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Execute o comando [AZ IOT Hub Create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) para criar um hub IOT.

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. Um nome de Hub IoT deve ser globalmente exclusivo no Azure. Esse espaço reservado é usado no restante deste guia de início rápido para representar o nome do Hub IoT.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Criar e monitorar um dispositivo
Nesta seção, você criará um dispositivo simulado na primeira sessão da CLI. O dispositivo simulado envia telemetria de dispositivo para o Hub IoT. Na segunda sessão da CLI, você monitora eventos e telemetria e envia uma mensagem da nuvem para o dispositivo para o dispositivo simulado.

Para criar e iniciar um dispositivo simulado:
1. Execute o comando [AZ IOT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) na primeira sessão da CLI. Isso cria a identidade do dispositivo simulado. 

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. 

    *simDevice*. Você pode usar esse nome diretamente para o dispositivo simulado no restante deste guia de início rápido. Opcionalmente, use um nome diferente. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Execute o comando [AZ IOT Device Simulate](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) na primeira sessão da CLI.  Isso inicia o dispositivo simulado. O dispositivo envia telemetria para o Hub IoT e recebe mensagens dele.  

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Para monitorar um dispositivo:
1. Na segunda sessão da CLI, execute o comando [AZ IOT Hub monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) . Isso inicia o monitoramento do dispositivo simulado. A saída mostra a telemetria que o dispositivo simulado envia para o Hub IoT.

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Eventos do monitor de Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Depois de monitorar o dispositivo simulado na segunda sessão da CLI, pressione CTRL + C para interromper o monitoramento. 

## <a name="use-the-cli-to-send-a-message"></a>Usar a CLI para enviar uma mensagem
Nesta seção, você usará a segunda sessão da CLI para enviar uma mensagem ao dispositivo simulado.

1. Na primeira sessão da CLI, confirme se o dispositivo simulado está em execução. Se o dispositivo tiver sido interrompido, execute o seguinte comando para iniciá-lo:

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Na segunda sessão da CLI, execute o comando [AZ IOT Device C2D-Message Send](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) . Isso envia uma mensagem da nuvem para o dispositivo do Hub IoT para o dispositivo simulado. A mensagem inclui uma cadeia de caracteres e dois pares chave-valor.  

    *Nomedoseuhubiot*. Substitua esse espaço reservado abaixo pelo nome escolhido para o Hub IoT. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Opcionalmente, você pode enviar mensagens da nuvem para o dispositivo usando o portal do Azure. Para fazer isso, navegue até a página Visão geral do Hub IoT, selecione **dispositivos IOT**, selecione o dispositivo simulado e selecione **mensagem para dispositivo**. 

1. Na primeira sessão da CLI, confirme se o dispositivo simulado recebeu a mensagem. 

    ![Cloud Shell mensagem da nuvem para o dispositivo](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Depois de exibir a mensagem, feche as duas sessões da CLI. 

## <a name="view-messaging-metrics-in-the-portal"></a>Exibir métricas de mensagens no portal
O portal do Azure permite que você gerencie todos os aspectos de seu hub IoT e dispositivos. Em um aplicativo de Hub IoT típico que ingere telemetria de dispositivos, talvez você queira monitorar dispositivos ou exibir métricas na telemetria do dispositivo. 

Para visualizar as métricas de mensagens no portal do Azure:
1. Na **Home** Page do portal, selecione **todos os recursos**. 

1. Na lista de grupos de recursos, selecione o Hub IoT que você criou. 

1. Selecione **métricas** no painel esquerdo do Hub IOT. 

    ![Métricas de mensagens do Hub IoT](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Insira o nome do Hub IoT no **escopo**.

2. Selecione *métricas padrão do Hub IOT* no **namespace de métrica**.

3. Selecione o *número total de mensagens usadas* na **métrica**. 

4. Passe o ponteiro do mouse sobre a área da linha do tempo na qual o dispositivo enviou mensagens. O número total de mensagens em um ponto no tempo é exibido no canto inferior esquerdo da linha do tempo.

    ![Exibir métricas do Hub IoT do Azure](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Opcionalmente, use a lista suspensa **métrica** para exibir outras métricas em seu dispositivo simulado. Por exemplo, *entregas de mensagens C2d concluídas* ou *total de dispositivos (visualização)* . 

## <a name="clean-up-resources"></a>Limpar recursos
Se você não precisar mais dos recursos do Azure criados neste guia de início rápido, poderá usar o CLI do Azure para excluí-los.

Se você continuar com o próximo artigo recomendado, poderá manter os recursos já criados e reutilizá-los. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

Para eliminar um grupo de recursos por nome:
1. Execute o comando [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Isso remove o grupo de recursos, o Hub IoT e o registro de dispositivo que você criou.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Execute o comando [AZ Group List](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) para confirmar se o grupo de recursos foi excluído.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você usou o CLI do Azure para criar um hub IoT, criar um dispositivo simulado, enviar telemetria, monitorar a telemetria, enviar uma mensagem da nuvem para o dispositivo e limpar os recursos. Você usou o portal do Azure para visualizar as métricas de mensagens em seu dispositivo.

Se você for um desenvolvedor de dispositivos, a próxima etapa sugerida será ver o início rápido de telemetria que usa o SDK do dispositivo IoT do Azure para C. opcionalmente, consulte um dos artigos de início rápido da telemetria do Hub IoT do Azure disponíveis em seu idioma ou SDK preferido.

> [!div class="nextstepaction"]
> [Início rápido: enviar telemetria de um dispositivo para um hub IoT (C)](quickstart-send-telemetry-c.md)
