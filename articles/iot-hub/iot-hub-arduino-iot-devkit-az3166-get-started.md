---
title: Ligue IoT DevKit AZ3166 a um Hub Azure IoT
description: Neste tutorial, aprenda a configurar e ligar o IoT DevKit AZ3166 ao Azure IoT Hub para que possa enviar dados para a plataforma cloud Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 0c7566df870b3c41b1f1dd7d4a4129ff9d9d9407
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481744"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ligue IoT DevKit AZ3166 ao Hub Azure IoT

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Pode utilizar o [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e protótipo soluções internet of things (IoT) que aproveitam os serviços do Microsoft Azure. Inclui uma placa compatível com Arduino com periféricos e sensores ricos, um pacote de placa de código aberto e uma rica galeria de [amostras.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registar um dispositivo para o MXChip IoT DevKit.
* Como ligar o IoT DevKit ao Wi-Fi e configurar a cadeia de ligação IoT Hub.
* Como enviar os dados de telemetria do sensor DevKit para o seu hub IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver a aplicação para o IoT DevKit.

Ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um DevKit](https://aka.ms/iot-devkit-purchase).

Pode encontrar o código fonte para todos os tutoriais de DevKit da [galeria de amostras](https://docs.microsoft.com/samples/browse/?term=mxchip)de código.

## <a name="what-you-need"></a>Do que precisa

* Uma placa MXChip IoT DevKit com um cabo Micro-USB. [Agora.](https://aka.ms/iot-devkit-purchase)
* Um computador que executa o Windows 10, macOS 10.10+ ou Ubuntu 18.04+.
* Uma subscrição ativa do Azure. [Ativar uma conta gratuita de 30 dias do Microsoft Azure.](https://azureinfo.microsoft.com/us-freetrial.html)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Prepare o seu hardware

Ligue o seguinte hardware ao seu computador:

* Placa DevKit
* Cabo micro-USB

![Hardware necessário](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para ligar o DevKit ao seu computador, siga estes passos:

1. Ligue a extremidade USB ao computador.

2. Ligue a extremidade Micro-USB ao DevKit.

3. O LED verde para alimentação confirma a ligação.

   ![Ligações de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Quickstart: Envie telemetria do DevKit para um Hub IoT

O quickstart usa firmware DevKit pré-compilado para enviar a telemetria para o IoT Hub. Antes de executá-lo, cria um hub IoT e regista um dispositivo com o hub.

### <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Executar o seguinte comando em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolher para o seu hub IoT.

   **MyNodeDevice**: O nome do dispositivo que está a registar. Utilize **o MyNodeDevice** como mostrado. Se escolher um nome diferente para o seu dispositivo, terá de utilizar esse nome ao longo deste artigo e atualizar o nome do dispositivo nas aplicações da amostra antes de os executar.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se tiver um `device-identity`erro em [execução, instale a extensão Azure IoT para o Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IoT adiciona comandos específicos ao IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua este espaço reservado abaixo com o nome que escolher para o seu hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria DevKit

O DevKit liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria de temperatura e humidade.

1. Descarregue a versão mais recente do [firmware GetStarted](https://aka.ms/devkit/prod/getstarted/latest) para IoT DevKit.

1. Certifique-se de que o IoT DevKit se liga ao computador via USB. Open File Explorer existe um dispositivo de armazenamento em massa USB chamado **AZ3166**.

    ![Explorador de janelas abertos](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arraste e deixe cair o firmware acabado de ser descarregado no dispositivo de armazenamento em massa e piscará automaticamente.

    ![Copiar firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No DevKit, segure o botão **B**, pressione e liberte o botão **Reset** e, em seguida, desbloqueie o botão **B**. O seu DevKit entra no modo AP. Para confirmar, o ecrã apresenta o identificador de conjunto de serviço (SSID) do DevKit e o endereço IP do portal de configuração.

    ![Botão de reset, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Utilize um navegador Web num dispositivo wi-fi diferente (computador ou telemóvel) para se ligar ao IoT DevKit SSID apresentado no passo anterior. Se pedir uma senha, deixe-a vazia.

    ![Ligar SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Aberto **192.168.0.1** no navegador. Selecione o Wi-Fi a que pretende que o IoT DevKit se conecte, escreva a palavra-passe Wi-Fi e, em seguida, colhe a cadeia de ligação do dispositivo de que tomou nota anteriormente. Em seguida, clique em Guardar.

    ![Configuração UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O IoT DevKit suporta apenas a rede de 2.4GHz. Consulte as [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para mais detalhes.

1. A linha de ligação Wi-Fi e de ligação ao dispositivo será armazenada no IoT DevKit quando vir a página de resultados.

    ![Resultado da configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Depois de configurado o Wi-Fi, as suas credenciais persistirão no dispositivo para essa ligação, mesmo que o dispositivo esteja desligado da tomada.

1. O IoT DevKit reinicia em poucos segundos. No ecrã DevKit, vê-se o endereço IP do DevKit seguindo os dados de telemetria, incluindo o valor da temperatura e humidade com a contagem de mensagens enviada para o Hub Azure IoT.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Envio de dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar os dados de telemetria enviados para o Azure, execute o seguinte comando em Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estes passos para preparar o ambiente de desenvolvimento para o DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instale o Código do Estúdio Visual com o pacote de extensão de ferramentas Azure IoT

1. Instale [o Arduino IDE.](https://www.arduino.cc/en/Main/Software) Fornece o instrumento necessário para a compilação e upload do código Arduino.
    * **Windows**: Utilize a versão do Instalador do Windows. Não instale a partir da App Store.
    * **macOS**: Arraste e deixe cair a `/Applications` **app Arduino.app** extraída na pasta.
    * **Ubuntu**: Desinsertá-lo em pasta como`$HOME/Downloads/arduino-1.8.8`

2. Instale o [Visual Studio Code](https://code.visualstudio.com/), um editor de código de origem de plataforma cruzada com poderoso intellisense, conclusão de código e suporte de depuração, bem como extensões ricas podem ser instalados a partir do mercado.

3. Lance o Código VS, procure a **Arduino** no mercado de extensões e instale-o. Esta extensão proporciona experiências melhoradas para o desenvolvimento na plataforma Arduino.

    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure [ferramentas Azure IoT](https://aka.ms/azure-iot-tools) no mercado de extensões e instale-as.

    ![Instalar ferramentas Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou use este URL direto:`vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > O pacote de extensão Azure IoT Tools contém a bancada de trabalho do [dispositivo Azure IoT](https://aka.ms/iot-workbench) que é usada para desenvolver e depurar em vários dispositivos ioT devkit. A [extensão Azure IoT Hub,](https://aka.ms/iot-toolkit)também incluída no pacote de extensão Azure IoT Tools, é usada para gerir e interagir com hubs Azure IoT.

5. Configure o Código VS com as definições de Arduino.

    No Código do Estúdio Visual, clique em **Definições de > preferências de ficheiro>** (em macOS, **Código > Preferências > Definições**). Em seguida, clique no ícone **Definições Abertas (JSON)** no canto superior direito da página *Definições.*

    ![Instalar ferramentas Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adicione as seguintes linhas para configurar o Arduino dependendo da sua plataforma: 

    * **Janelas:**

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu:**

        Substitua o espaço reservado **{username}** abaixo com o seu nome de utilizador.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique `F1` para abrir a paleta de comando, escreva e selecione **Arduino: Gestor de Quadros**. Procure o **AZ3166** e instale a versão mais recente.

    ![Instalar DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar controladores ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o IoT DevKit utiliza para comunicar com a sua máquina de desenvolvimento. É necessário instalá-lo no Windows para mostrar o código do dispositivo compilado ao DevKit. Siga os passos específicos do OS para permitir o acesso da máquina ao seu dispositivo.

* **Windows**: Descarregue e instale o controlador USB a partir do [site da STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Não é necessário condutor para o macOS.
* **Ubuntu**: Executar os comandos no terminal e assinar e assinar para que a mudança de grupo faça efeito:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora estão preparados para preparar e configurar o seu ambiente de desenvolvimento. Vamos construir a amostra getstarted que acabou de executar.

## <a name="build-your-first-project"></a>Construa o seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Código de amostra aberto da galeria de amostras

O IoT DevKit contém uma rica galeria de amostras que pode usar para aprender a ligar o DevKit a vários serviços Azure.

1. Certifique-se de que o seu IoT DevKit não está **ligado** ao computador. Inicie primeiro o Código VS e, em seguida, ligue o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comando, digite e selecione Bancada de Trabalho do **Dispositivo Azure IoT: Exemplos abertos...**. Em seguida, selecione **IoT DevKit** como placa.

1. Na página IoT Workbench Exemplos, encontre **Get Started** e clique em **Amostra Aberta**. Em seguida, seleciona o caminho predefinido para descarregar o código da amostra.

    ![Amostra aberta](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provision Azure IoT Hub e dispositivo

Em vez de fornecer hub Azure IoT e dispositivo do portal Azure, pode fazê-lo no Código VS sem sair do ambiente de desenvolvimento.

1. Na nova janela do `F1` projeto aberta, clique para abrir a paleta de comando, digite e selecione Bancada de Trabalho do **Dispositivo Azure IoT: Provision Azure Services...**. Siga o guia passo a passo para terminar o fornecimento do seu Hub Azure IoT e criar o dispositivo IoT Hub.

    ![Comando de provisão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se ainda não assinou em Azure. Siga a notificação pop-up para iniciar sessão.

1. Selecione a subscrição que pretende utilizar.

    ![Selecione sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie um novo grupo de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Selecionar grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos especificado, siga o guia para selecionar ou criar um novo Hub Azure IoT.

    ![Selecione passos Do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, verá o Hub Azure IoT provisionado.

    ![Hub IoT Provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Azure IoT Hub que forre.

    ![Selecione passos do dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione dispositivo IoT Provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora tem o Azure IoT Hub aprovisionado e dispositivo criado nele. Além disso, a cadeia de ligação do dispositivo será guardada no Código VS para configurar o IoT DevKit mais tarde.

    ![Provisão feita](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código do dispositivo

1. Na barra de estado inferior direito, verifique se o **MXCHIP AZ3166** é mostrado como placa selecionada e porta de série com **STMicroelectronics** é utilizado.

    ![Selecione placa e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique `F1` para abrir a paleta de comandos, digite e selecione a bancada de trabalho do **dispositivo Azure IoT: Configurar as definições**do dispositivo... e, em seguida, selecione a cadeia de ligação do **dispositivo Config > selecione ioT Hub Connection String**.

1. No DevKit, segure o **botão A**, pressione e liberte o botão **de reset** e, em seguida, desbloqueie o **botão A**. O seu DevKit entra no modo de configuração e guarda a cadeia de ligação.

    ![Cadeia de ligação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique `F1` novamente, escreva e selecione bancada de trabalho do **dispositivo Azure IoT: Carregar código do dispositivo**. Começa a compilar e a enviar o código para o DevKit.

    ![Upload de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O DevKit reinicia e começa a executar o código.

> [!NOTE]
> Se houver erros ou interrupções, pode sempre recuperar executando o comando novamente.

## <a name="test-the-project"></a>Testar o projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Veja a telemetria enviada para o Hub Azure IoT

Clique no ícone da ficha de alimentação na barra de estado para abrir o Monitor de Série:

![Monitor em série](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

A aplicação da amostra está a funcionar com sucesso quando vir os seguintes resultados:

* O Monitor de Série apresenta a mensagem enviada para o Centro IoT.
* O LED do MXChip IoT DevKit está a piscar.

![Saída do monitor de série](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Pode encontrar um erro durante os testes em que o LED não está a piscar, o portal Azure não mostra os dados de entrada do dispositivo, mas o ecrã OLED do dispositivo mostra como **Running...**. Para resolver o problema, no portal Azure, vá ao dispositivo no hub IoT e envie uma mensagem para o dispositivo. Se vir a seguinte resposta no monitor de série no Código VS, é possível que a comunicação direta do dispositivo esteja bloqueada ao nível do router. Verifique as regras de firewall e router que estão configuradas para os dispositivos de ligação. Além disso, certifique-se de que a porta de saída 1833 está aberta.
> 
> ERRO: mqtt_client.c (nn 454): Erro: ligação de abertura de falha ao ponto final  
> Estado de ligação INFO: >>>: desligado  
> ERRO: tlsio_mbedtls.c (n.º 604): Abertura subjacente da IO falhou  
> ERRO: mqtt_client.c (n1 1042): Erro: io_open falhou  
> ERRO: iothubtransport_mqtt_common.c (nn 2283): falha na ligação ao endereço atcsliothub.azure-devices.net.  
> INFO: >>>Reconectar.  
> INFORMAÇÃO: Versão IoThub: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Veja a telemetria recebida pelo Azure IoT Hub

Pode utilizar [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorizar as mensagens dispositivo-nuvem (D2C) no IoT Hub.

1. Assine no [portal Azure,](https://portal.azure.com/)encontre o Hub IoT que criou.

    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel de políticas de **acesso partilhado,** clique na **política iothubowner**, e escreva a cadeia de ligação do seu hub IoT.

    ![Fio de ligação Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No Código VS, clique em escrever `F1`, digite e selecione **Azure IoT Hub: Definir a cadeia de ligação do hub IoT**. Copie a cadeia de ligação nele.

    ![Linha de ligação Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel DE **DISPOSITIVOS HUB AZURE IOT** à direita, clique no nome do dispositivo que criou e selecione **Start Monitoring Built-in Event Endpoint**.

    ![Mensagem Monitor D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel **OUTPUT,** pode ver as mensagens D2C que chegam ao IoT Hub.

    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Rever o código

O `GetStarted.ino` é o arquivo principal do esboço arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada `utility.cpp` para o Hub Azure IoT, abra o ficheiro na mesma pasta. Ver [Referência API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a usar sensores e periféricos no IoT DevKit.

O `DevKitMQTTClient` usado é um invólucro do **iothub_client** dos [SDKs e bibliotecas do Microsoft Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interagir com o Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemas e feedback

Se encontrar problemas, pode verificar se há uma solução no [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-nos a partir de [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Pode também dar-nos feedback deixando um comentário nesta página.

## <a name="next-steps"></a>Passos seguintes

Ligou com sucesso um MXChip IoT DevKit ao seu hub IoT e enviou os dados do sensor capturados para o seu hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
