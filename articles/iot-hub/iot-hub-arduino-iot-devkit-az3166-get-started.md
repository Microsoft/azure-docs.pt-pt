---
title: Ligue ioT DevKit AZ3166 a um Hub Azure IoT
description: Neste tutorial, aprenda a configurar e ligar o IoT DevKit AZ3166 ao Azure IoT Hub para que possa enviar dados para a plataforma cloud Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 3e5ab1667ee0cda459785efa624bd7f4fc6818b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97562961"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ligue ioT DevKit AZ3166 a Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Pode utilizar o [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e protótipo de soluções Internet of Things (IoT) que tirem partido dos serviços microsoft Azure. Inclui um tabuleiro compatível com Arduino com periféricos e sensores ricos, um pacote de placa de código aberto e uma [rica galeria de amostras.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registar um dispositivo para o MXChip IoT DevKit.
* Como ligar o IoT DevKit à Wi-Fi e configurar a cadeia de ligação IoT Hub.
* Como enviar os dados de telemetria do sensor DevKit para o seu hub IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver aplicações para o IoT DevKit.

Ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um DevKit](https://aka.ms/iot-devkit-purchase).

Pode encontrar o código fonte para todos os tutoriais de DevKit da galeria de [amostras](/samples/browse/?term=mxchip)de código .

## <a name="what-you-need"></a>O que precisa

- Uma placa MXChip IoT DevKit com cabo Micro-USB. [Pegue agora.](https://aka.ms/iot-devkit-purchase)
- Um computador com o Windows 10, o macOS 10.10+ ou o Ubuntu 18.04+.
- Uma subscrição ativa do Azure. [Ative uma conta gratuita de 30 dias do Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Prepare o seu hardware

Ligue o seguinte hardware ao seu computador:

* Placa DevKit
* Cabo Micro-USB

![Hardware necessário](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para ligar o DevKit ao seu computador, siga estes passos:

1. Ligue a extremidade USB ao computador.

2. Ligue a extremidade micro USB ao DevKit.

3. O LED verde para alimentação confirma a ligação.

   ![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Quickstart: Enviar telemetria de DevKit para um IoT Hub

O quickstart utiliza firmware DevKit pré-compilado para enviar a telemetria para o IoT Hub. Antes de executá-lo, cria um hub IoT e regista um dispositivo com o hub.

### <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

   **MyNodeDevice**: O nome do dispositivo que está a registar. Use **MyNodeDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, tem de usar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se tiver um erro em `device-identity` funcionamento, instale a [extensão Azure IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Executar o seguinte comando para adicionar a extensão IoT do Microsoft Azure para Azure CLI à sua instância Cloud Shell. A Extensão IoT adiciona comandos específicos ao IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **Seu NomeIoTHubName**: Substitua este espaço reservado abaixo pelo nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se assemelha a:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria DevKit

O DevKit liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria de temperatura e humidade.

1. Descarregue a versão mais recente do [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) para IoT DevKit.

1. Certifique-se de que o IoT DevKit se liga ao computador via USB. Open File Explorer existe um dispositivo de armazenamento em massa USB chamado **AZ3166**.

    ![Abrir o Explorador do Windows](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arraste e deixe cair o firmware descarregado para o dispositivo de armazenamento em massa e piscará automaticamente.

    ![Copiar firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No DevKit, mantenha premido o botão **B,** pressione e solte o botão **Reset** e, em seguida, liberte o botão **B**. O seu DevKit entra no modo AP. Para confirmar, o ecrã apresenta o identificador de conjunto de serviço (SSID) do DevKit e o endereço IP do portal de configuração.

    ![Botão de reset, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Utilize um browser num dispositivo diferente com Wi-Fi (computador ou telemóvel) para ligar ao SSID do IoT DevKit apresentado no passo anterior. Se pedir uma senha, deixe-a vazia.

    ![Ligar SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Abra **192.168.0.1** no navegador. Selecione o Wi-Fi a que pretende que o IoT DevKit se ligue, escreva a palavra-passe Wi-Fi e, em seguida, cole a cadeia de ligação do dispositivo que fez notar anteriormente. Em seguida, clique em Guardar.

    ![UI de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O IoT DevKit suporta apenas a rede 2.4GHz. Verifique [as PERGUNTAS Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para mais detalhes.

1. A ligação wi-fi e a ligação do dispositivo serão armazenadas no IoT DevKit quando vir a página de resultados.

    ![Resultado da configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Depois de configurar Wi-Fi, as suas credenciais persistirão no dispositivo para essa ligação, mesmo que o dispositivo esteja desligado da tomada.

1. O IoT DevKit é reiniciado em alguns segundos. No ecrã DevKit, vê o endereço IP do DevKit seguindo os dados de telemetria, incluindo o valor da temperatura e da humidade com a contagem de mensagens enviada para o Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Envio de dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar os dados de telemetria enviados à Azure, execute o seguinte comando em Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estes passos para preparar o ambiente de desenvolvimento para o DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instale código de estúdio visual com pacote de extensão Azure IoT Tools

1. Instale [o Arduino IDE](https://www.arduino.cc/en/Main/Software). Fornece a ferramenta necessária para compilar e carregar o código Arduino.
    * **Windows**: Utilize a versão do Instalador do Windows. Não instale na App Store.
    * **macOS**: Arraste e deixe cair a **Arduino.app** extraída na `/Applications` pasta.
    * **Ubuntu**: Desaperte-o em pastas como `$HOME/Downloads/arduino-1.8.8`

2. Instale [o Visual Studio Code](https://code.visualstudio.com/), um editor de código fonte de plataforma cruzada com poderoso intellisense, suporte de conclusão de código e depuramento, bem como extensões ricas, pode ser instalado a partir do mercado.

3. Lançar código VS, procurar **Arduino** no mercado de extensão e instalá-lo. Esta extensão proporciona experiências melhoradas para o desenvolvimento na plataforma Arduino.

    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure [ferramentas Azure IoT](https://aka.ms/azure-iot-tools) no mercado de extensões e instale-as.

    ![Screenshot que mostra Azure IoT Tools no mercado de extensão.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou copiar e colar este URL numa janela do navegador: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > O pacote de extensão Azure IoT Tools contém a bancada [de trabalho do dispositivo Azure IoT](https://aka.ms/iot-workbench) que é usada para desenvolver e depurar vários dispositivos de devkit IoT. A [extensão Hub IoT do Azure](https://aka.ms/iot-toolkit), também incluída no pacote de extensões Azure IoT Tools, é utilizada para gerir e interagir com os Hubs IoT do Azure.

5. Configurar código VS com definições Arduino.

    No Código do Estúdio Visual, clique em **"Preferências > de Ficheiros" > Definições** (no macOS, **> Preferências de Código > Definições).** Em seguida, clique no ícone **Definições Abertas (JSON)** no canto superior direito da página *Definições.*

    ![Instalar ferramentas Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adicione as seguintes linhas para configurar o Arduino na sua plataforma: 

    * **Windows**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        substitua o marcador de posição **{username}** abaixo pelo seu nome de utilizador.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique `F1` para abrir a paleta de comando, escreva e selecione **Arduino: Board Manager**. Procure **por AZ3166** e instale a versão mais recente.

    ![Instalar o DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar controladores de ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o IoT DevKit utiliza para comunicar com a sua máquina de desenvolvimento. Tem de instalá-la no Windows para instalar a imagem do código do dispositivo compilado no DevKit. Sia os passos específicos do SO para permitir o acesso do computador ao seu dispositivo.

* **Windows**: Descarregue e instale o controlador USB do website da [STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Não é necessário nenhum condutor para o macOS.
* **Ubuntu**: execute os comandos no terminal e, em seguida, termine e volte a iniciar sessão para a alteração de grupo entrar em vigor:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora está pronto para preparar e configurar o seu ambiente de desenvolvimento. Vamos construir a amostra GetStarted que acabou de correr.

## <a name="build-your-first-project"></a>Construa o seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abrir o código de amostra da galeria de amostras

O IoT DevKit contém uma rica galeria de amostras que pode usar para aprender a ligar o DevKit a vários serviços Azure.

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, escreva e selecione **Azure IoT Device Workbench: Open Examples...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Examples, encontre **Get Start** e clique **em Abrir Amostra**. Em seguida, seleciona o caminho predefinido para descarregar o código de amostra.

    ![Amostra aberta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisão Azure IoT Hub e dispositivo

Em vez de aprovisionar o Azure IoT Hub e o dispositivo a partir do portal Azure, pode fazê-lo no Código VS sem sair do ambiente de desenvolvimento.

1. Na nova janela aberta do projeto, clique `F1` para abrir a paleta de comando, escreva e selecione **Azure IoT Device Workbench: Provision Azure Services...**. Siga o guia passo a passo para terminar de atrasar o seu Azure IoT Hub e criar o dispositivo IoT Hub.

    ![Comando de provisão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se não assinou no Azure. Siga a notificação pop-up para iniciar sessão.

1. Selecione a subscrição que pretende utilizar.

    ![Selecione sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie um novo [grupo de recursos.](../azure-resource-manager/management/overview.md#terminology)

    ![Selecionar o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos especificado, siga o guia para selecionar ou criar um novo Azure IoT Hub.

    ![Selecione passos do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, verá o Azure IoT Hub a provisionado.

    ![Hub IoT Provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Azure IoT Hub que a provisionou.

    ![Selecione passos do dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione dispositivo IoT Provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora, tem o Hub IoT do Azure aprovisionado e o dispositivo criado no mesmo. Também a cadeia de ligação do dispositivo será guardada no Código VS para configurar o IoT DevKit mais tarde.

    ![Provisão feita](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar código do dispositivo

1. Na barra de estado inferior direita, verifique se o **MXCHIP AZ3166** é apresentado como placa selecionada e a porta em série com **STMicroelectronics** é utilizada.

    ![Selecione placa e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique `F1` para abrir a paleta de comando, escreva e selecione **Azure IoT Device Workbench: Configurações do dispositivo de configuração...** e, em seguida, selecione **Config Device Connection String > Select IoT Hub Device Connection String**.

1. No DevKit, mantenha premido **o botão A**, pressione e solte o botão **de reset** e, em seguida, desbloqueie **o botão A**. O seu DevKit entra no modo de configuração e guarda a cadeia de ligação.

    ![Cadeia de ligação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique `F1` novamente, escreva e selecione **a bancada de trabalho do dispositivo Azure IoT: Código do dispositivo de upload**. Começa a compilar e a enviar o código para o DevKit.

    ![Upload Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O DevKit reinicia e começa a executar o código.

> [!NOTE]
> Se houver erros ou interrupções, pode sempre recuperar executando novamente o comando.

## <a name="test-the-project"></a>Testar o projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Ver a telemetria enviada para o Azure IoT Hub

Clique no ícone da ficha de alimentação na barra de estado para abrir o Monitor em Série:

![Monitor em série](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A aplicação da amostra está a funcionar com sucesso quando vê os seguintes resultados:

* O Monitor em Série exibe a mensagem enviada para o Hub IoT.
* O LED no MXChip IoT DevKit está a piscar.

![Saída do monitor de série](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Pode encontrar um erro durante os testes em que o LED não pisca, o portal Azure não mostra dados de entrada do dispositivo, mas o ecrã OLED do dispositivo mostra como **Running...**. Para resolver o problema, no portal Azure, vá ao dispositivo no hub IoT e envie uma mensagem para o dispositivo. Se vir a seguinte resposta no monitor em série no Código VS, é possível que a comunicação direta do dispositivo esteja bloqueada ao nível do router. Verifique as regras de firewall e router configuradas para os dispositivos de ligação. Além disso, certifique-se de que a porta de saída 1833 está aberta.
> 
> ERRO: mqtt_client.c (in454): Erro: ligação de abertura de falha ao ponto final  
> INFO: >>>Estado de ligação: desligado  
> ERRO: tlsio_mbedtls.c (in 604): IO aberto subjacente falhou  
> ERRO: mqtt_client.c (in 1042): Erro: io_open falhou  
> ERRO: iothubtransport_mqtt_common.c (in 2283): falha na ligação ao endereço atcsliothub.azure-devices.net.  
> INFO: >>>Reconectar.  
> INFO: Versão IoThub: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Ver a telemetria recebida por Azure IoT Hub

Pode utilizar [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorizar mensagens de dispositivo para nuvem (D2C) no IoT Hub.

1. Inscreva-se no [portal Azure,](https://portal.azure.com/)encontre o Hub IoT que criou.

    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **de políticas de acesso partilhado,** clique na política do **iothubowner** e escreva a cadeia de conexão do seu hub IoT.

    ![Cadeia de conexão Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Em Código VS, `F1` clique, escreva e selecione **Azure IoT Hub: set IoT Hub Connection String**. Copie o fio de ligação nele.

    ![Definir a cadeia de conexão Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel **de dispositivos AZURE IOT HUB** à esquerda, clique no nome do dispositivo que criou e selecione **Start Monitoring Built-in Event Endpoint**.

    ![Monitor D2C Mensagem](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No **painel OUTPUT,** pode ver as mensagens D2C recebidas no IoT Hub.

    ![Screenshot que mostra as mensagens D2C recebidas para o IoT Hub.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Rever o código

O `GetStarted.ino` ficheiro principal do esboço de Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada para o Azure IoT Hub, abra o `utility.cpp` ficheiro na mesma pasta. Consulte [a API Reference](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a usar sensores e periféricos no IoT DevKit.

O `DevKitMQTTClient` usado é um invólucro do **iothub_client** dos [Microsoft Azure IoT SDKs e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interagir com Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, pode verificar se existe uma solução nas [FAQ IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contactar-nos a partir do [Gitter.](https://gitter.im/Microsoft/azure-iot-developer-kit) Também pode nos dar feedback deixando um comentário nesta página.

## <a name="next-steps"></a>Passos seguintes

Ligou com sucesso um MXChip IoT DevKit ao seu hub IoT e enviou os dados do sensor capturado para o seu hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
