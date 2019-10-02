---
title: IoT DevKit para nuvem – conectar IoT DevKit AZ3166 ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, saiba como configurar e conectar o IoT DevKit AZ3166 ao Hub IoT do Azure para que ele possa enviar dados para a plataforma de nuvem do Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: e8a186fbcb04dc29fcf57a2353adcf89ce46b119
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677940"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Conectar IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [MXChip IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e protótipos Internet das coisas soluções (IOT) que aproveitam os serviços Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos e sensores avançados, um pacote de placa de software livre e uma [Galeria de amostras](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)avançada.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registrar um dispositivo para o MXChip IoT DevKit.
* Como conectar o IoT DevKit ao Wi-Fi e configurar a cadeia de conexão do Hub IoT.
* Como enviar os dados de telemetria do sensor DevKit para o Hub IoT.
* Como preparar o ambiente de desenvolvimento e desenvolver o aplicativo para o DevKit de IoT.

Ainda não tem um DevKit? Experimente o [simulador do devkit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um devkit](https://aka.ms/iot-devkit-purchase).

Você pode encontrar o código-fonte para todos os tutoriais do DevKit da [Galeria de exemplos de código](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Do que precisa

* Uma placa MXChip IoT DevKit com um cabo micro USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
* Um computador que executa o Windows 10, macOS 10.10s + ou Ubuntu 18.04 +.
* Uma subscrição ativa do Azure. [Ative uma avaliação gratuita de 30 dias Microsoft Azure conta](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Preparar o hardware

Conecte o seguinte hardware ao seu computador:

* Placa DevKit
* Cabo micro USB

![Hardware necessário](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para conectar o DevKit ao seu computador, siga estas etapas:

1. Conecte a extremidade USB ao seu computador.

2. Conecte a extremidade micro-USB ao DevKit.

3. O LED verde para energia confirma a conexão.

   ![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Início rápido: Enviar telemetria do DevKit para um hub IoT

O início rápido usa o firmware DevKit pré-compilado para enviar a telemetria para o Hub IoT. Antes de executá-lo, você cria um hub IoT e registra um dispositivo com o Hub.

### <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registar um dispositivo

É necessário registar um dispositivo no hub IoT antes de o mesmo se poder ligar. Neste início rápido, vai utilizar o Azure Cloud Shell para registar um dispositivo simulado.

1. Execute o comando a seguir em Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

   **MyNodeDevice**: O nome do dispositivo que você está registrando. Use **MyNodeDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, precisará usar esse nome em todo este artigo e atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Se você receber um erro em `device-identity`execução, instale a [extensão do Azure IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) para obter mais detalhes.
  
1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de ligação do dispositivo_ que acabou de registar:

   **YourIoTHubName**: Substitua esse espaço reservado abaixo pelo nome que você escolher para o Hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anote a cadeia de ligação do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Irá utilizar este valor mais adiante no guia de início rápido.

### <a name="send-devkit-telemetry"></a>Enviar telemetria DevKit

O DevKit se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia a telemetria de temperatura e umidade.

1. Baixe a versão mais recente do [firmware getstarted](https://aka.ms/devkit/prod/getstarted/latest) para IOT devkit.

1. Verifique se o IoT DevKit se conecte ao seu computador via USB. Abra o explorador de arquivos há um dispositivo de armazenamento em massa USB chamado **AZ3166**.

    ![Abrir o Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Arraste e solte o firmware que acabou de ser baixado no dispositivo de armazenamento em massa e ele será atualizado automaticamente.

    ![Copiar firmware](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. No DevKit, mantenha o botão **b**, pressione e solte o botão **Redefinir** e, em seguida, solte o botão **b**. Seu DevKit entra no modo AP. Para confirmar, a tela exibe o SSID (identificador de conjunto de serviços) do DevKit e o endereço IP do portal de configuração.

    ![Botão redefinir, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Definir modo AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Use um navegador da Web em um dispositivo habilitado para Wi-Fi diferente (computador ou telefone celular) para se conectar ao SSID DevKit do IoT exibido na etapa anterior. Se ele solicitar uma senha, deixe-a vazia.

    ![Conectar SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Abra **192.168.0.1** no navegador. Selecione o Wi-Fi ao qual você deseja que o IoT DevKit se conecte, digite a senha Wi-Fi e cole a cadeia de conexão do dispositivo anotada anteriormente. Em seguida, clique em Guardar.

    ![Interface do usuário de configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > O IoT DevKit só dá suporte à rede de 2,4 GHz. Consulte as [perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

1. As informações de WiFi e a cadeia de conexão do dispositivo serão armazenadas no DevKit de IoT quando você vir a página resultado.

    ![Resultado da configuração](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Depois que o Wi-Fi estiver configurado, suas credenciais serão mantidas no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado.

1. O IoT DevKit reinicia em alguns segundos. Na tela DevKit, você vê o endereço IP do DevKit segue os dados telemétricos, incluindo temperatura e valor de umidade com contagem de mensagens enviar para o Hub IoT do Azure.

    ![IP WiFi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Envio de dados](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Para verificar os dados de telemetria enviados ao Azure, execute o seguinte comando no Azure Cloud Shell:

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Siga estas etapas para preparar o ambiente de desenvolvimento para o DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalar Visual Studio Code com o pacote de extensão das ferramentas do Azure IoT

1. Instale o [ARDUINO IDE](https://www.arduino.cc/en/Main/Software). Ele fornece o ferramentas necessário para compilar e carregar o código Arduino.
    * **Windows**: Use Windows Installer versão. Não instale da loja de aplicativos.
    * **macOS**: Arraste e solte o **Arduino. app** extraído `/Applications` na pasta.
    * **Ubuntu**: Descompacte-o na pasta, como`$HOME/Downloads/arduino-1.8.8`

2. Instale o [Visual Studio Code](https://code.visualstudio.com/), um editor de código-fonte de plataforma cruzada com o IntelliSense poderoso, a conclusão de código e o suporte à depuração, bem como as extensões avançadas podem ser instaladas do Marketplace.

3. Inicie VS Code, procure **Arduino** no Marketplace de extensão e instale-o. Essa extensão fornece experiências aprimoradas para o desenvolvimento na plataforma Arduino.

    ![Instalar o Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure por [Ferramentas do Azure IOT](https://aka.ms/azure-iot-tools) no Marketplace de extensão e instale-a.

    ![Instalar as ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Ou use este link direto:
    > [!div class="nextstepaction"]
    > [Instalar o pacote de extensão das ferramentas do Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > O pacote de extensão das ferramentas do Azure IoT contém o [Azure IOT Device Workbench](https://aka.ms/iot-workbench) , que é usado para desenvolver e depurar em vários dispositivos IOT devkit. O [Kit de ferramentas do Hub IOT do Azure](https://aka.ms/iot-toolkit), também incluído com o pacote de extensão das ferramentas do Azure IOT, é usado para gerenciar e interagir com os hubs IOT do Azure.

5. Defina VS Code com as configurações de Arduino.

    Em Visual Studio Code, clique em **arquivo > preferências > configurações** (no MacOS, **código > Preferências > configurações**). Em seguida, clique no ícone de **configurações abertas (JSON)** no canto superior direito da página de *configurações* .

    ![Instalar as ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Adicione as seguintes linhas para configurar o Arduino dependendo da sua plataforma: 

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

        Substitua o espaço reservado **{username}** abaixo pelo seu nome de usuário.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique `F1` para abrir a paleta de comandos, digite e **selecione Arduino: Gerenciador**de placa. Procure **AZ3166** e instale a versão mais recente.

    ![Instalar o SDK do DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar drivers ST-link

O [St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o IOT devkit usa para se comunicar com seu computador de desenvolvimento. Você precisa instalá-lo no Windows para atualizar o código do dispositivo compilado para o DevKit. Siga as etapas específicas do sistema operacional para permitir que o computador acesse seu dispositivo.

* **Windows**: Baixe e instale o driver USB no [site do STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Nenhum driver é necessário para o macOS.
* **Ubuntu**: Execute os comandos no terminal e saia e entre para que a alteração do grupo entre em vigor:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora você está pronto para preparar e configurar seu ambiente de desenvolvimento. Vamos criar o exemplo getstarted que você acabou de executar.

## <a name="build-your-first-project"></a>Crie seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abrir código de exemplo da Galeria de exemplo

O IoT DevKit contém uma rica Galeria de exemplos que você pode usar para aprender a conectar o DevKit a vários serviços do Azure.

1. Verifique se o DevKit de IoT **não está conectado** ao seu computador. Inicie VS Code primeiro e, em seguida, conecte o DevKit ao seu computador.

1. Clique `F1` para abrir a paleta de comandos, digite e **selecione Azure IOT Device Workbench: Abrir exemplos...** . Em seguida, selecione **IOT devkit** como placa.

1. Na página exemplos do IoT Workbench, encontre **introdução** e clique em **abrir exemplo**. Em seguida, seleciona o caminho padrão para baixar o código de exemplo.

    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisionar o dispositivo e o Hub IoT do Azure

Em vez de provisionar o Hub IoT do Azure e o dispositivo do portal do Azure, você pode fazer isso no VS Code sem sair do ambiente de desenvolvimento.

1. Na janela novo projeto aberto, clique `F1` para abrir a paleta de comandos, digite e selecione **Azure IOT Device Workbench: Provisionar serviços do Azure...** . Siga o guia passo a passo para concluir o provisionamento do Hub IoT do Azure e a criação do dispositivo Hub IoT.

    ![Comando de provisionamento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se você não tiver entrado no Azure. Siga a notificação de pop-up para entrar.

1. Selecione a assinatura que você deseja usar.

    ![Selecionar Sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie um novo [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Selecionar grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos que você especificou, siga o guia para selecionar ou criar um novo hub IoT do Azure.

    ![Selecionar etapas do Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecionar Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela saída, você verá o Hub IoT do Azure provisionado.

    ![Hub IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Hub IoT do Azure que você provisionou.

    ![Selecionar etapas do dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecionar dispositivo IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora você tem o Hub IoT do Azure provisionado e o dispositivo criado nele. Além disso, a cadeia de conexão do dispositivo será salva em VS Code para configurar o IoT DevKit mais tarde.

    ![Provisionamento concluído](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código do dispositivo

1. Na barra de status inferior direita, marque a **MXCHIP AZ3166** é mostrada como a placa selecionada e a porta serial com **STMicroelectronics** é usada.

    ![Selecionar placa e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique `F1` para abrir a paleta de comandos, digite e **selecione Azure IOT Device Workbench: Definir configurações do dispositivo...** e, em seguida, selecione **configuração cadeia de conexão do dispositivo > Selecionar cadeia de conexão do dispositivo do Hub IOT.**

1. No DevKit, mantenha o **botão a**, pressione e solte o botão **Redefinir** e, em seguida, solte o **botão a**. Seu DevKit entra no modo de configuração e salva a cadeia de conexão.

    ![Cadeia de ligação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique `F1` novamente, digite e selecione **Azure IOT Device Workbench: Carregue o código**do dispositivo. Ele inicia a compilação e carrega o código para DevKit.

    ![Upload de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O DevKit reinicia e inicia a execução do código.

> [!NOTE]
> Se houver erros ou interrupções, você sempre poderá recuperar executando o comando novamente.

## <a name="test-the-project"></a>Testar o projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Exibir a telemetria enviada ao Hub IoT do Azure

Clique no ícone de plugue de energia na barra de status para abrir o monitor serial:

![Monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo está sendo executado com êxito quando você vê os seguintes resultados:

* O monitor serial exibe a mensagem enviada ao Hub IoT.
* O LED no DevKit IoT MXChip está piscando.

![Saída do monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar as [Ferramentas do Azure IOT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorar mensagens de D2C (dispositivo para nuvem) no Hub IOT.

1. [Portal do Azure](https://portal.azure.com/)de entrada, localize o Hub IOT que você criou.

    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **políticas de acesso compartilhado** , clique na **política iothubowner**e anote a cadeia de conexão do Hub IOT.

    ![Cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. Em vs Code, clique `F1`em, digite e **selecione Hub IOT do Azure: Defina a cadeia**de conexão do Hub IOT. Copie a cadeia de conexão nela.

    ![Definir cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel **dispositivos do Hub IOT do Azure** à direita, clique com o botão direito do mouse no nome do dispositivo criado e selecione **Iniciar Monitoramento do ponto de extremidade do evento interno**.

    ![Monitorar mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel de **saída** , você pode ver as mensagens D2C de entrada para o Hub IOT.

    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Rever o código

O `GetStarted.ino` é o principal arquivo de esboço Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada ao Hub IOT do Azure `utility.cpp` , abra o arquivo na mesma pasta. Veja [referência de API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para saber como usar sensores e periféricos no IOT devkit.

O `DevKitMQTTClient` usado é um wrapper do **iothub_client** do [Microsoft Azure SDKs e bibliotecas de IOT para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) interagir com o Hub IOT do Azure.

## <a name="problems-and-feedback"></a>Problemas e comentários

Se você tiver problemas, poderá verificar uma solução nas [perguntas frequentes do IOT devkit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entrar em contato conosco no [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários deixando um comentário nesta página.

## <a name="next-steps"></a>Passos seguintes

Você conectou um DevKit MXChip IoT com êxito ao Hub IoT e enviou os dados capturados do sensor para o Hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
