---
title: IoT DevKit com a nuvem – ligar IoT DevKit AZ3166 ao IoT Hub do Azure | Documentos da Microsoft
description: Neste tutorial, saiba como configurar e ligar IoT DevKit AZ3166 hub IoT do Azure, pelo que pode enviar dados para a plataforma de cloud do Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 941455e39a32405097563b043046866aeb5c7964
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351937"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ligar a IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Pode utilizar o [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e soluções de Internet das coisas (IoT) de protótipo que tiram partido dos serviços do Microsoft Azure. Ele inclui um quadro compatível com Arduino com periféricos avançados e sensores, um pacote de quadro de código-fonte aberto e crescente [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>O que fazer

Ligar o DevKit para um hub IoT do Azure que criar. Em seguida, coletar os dados de temperatura e humidade a partir de sensores e enviar os dados para o hub IoT.

Não tem um DevKit ainda? Experimente o [DevKit simulador](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [comprar um DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>O que irá aprender

* Como ligar a IoT DevKit a um ponto de acesso sem fios e prepare seu ambiente de desenvolvimento.
* Como criar um hub IoT e registar um dispositivo para o MXChip IoT DevKit.
* Como recolher dados de sensor ao executar um aplicativo de exemplo no MXChip IoT DevKit.
* Como enviar os dados de sensor ao seu hub IoT.

## <a name="what-you-need"></a>Do que precisa

* Um quadro de MXChip IoT DevKit com um cabo Micro USB. [Obter agora](https://aka.ms/iot-devkit-purchase).
* Um computador com Windows 10 ou macOS 10.10 +.
* Uma subscrição ativa do Azure. [Ativar uma conta gratuita de 30 dias avaliação Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Prepare o hardware

Conectar o seguinte hardware para o seu computador:

* DevKit quadro
* Cabo micro USB

![Hardware necessário](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Para ligar o DevKit ao seu computador, siga estes passos:

1. Ligar o fim USB para o seu computador.

2. Ligar o fim de Micro USB para o DevKit.

3. O LED verde para power confirma que a ligação.

   ![Ligações de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurar Wi-Fi

Projetos de IoT confiam na ligação à internet. Utilize as seguintes instruções para configurar o DevKit para ligar ao Wi-Fi.

### <a name="enter-ap-mode"></a>Entrar no modo do Pacífico

Premido o botão B, push e largue o botão de reposição e, em seguida, largue o botão B. Sua DevKit entra em modo AP para a configuração de Wi-Fi. A tela exibe o service set identifier (SSID) da DevKit e o endereço IP de portal de configuração.

![Repor o botão, o botão B e o SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![Definir o modo do Pacífico](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Ligar ao DevKit Pacífico

Agora, utilize outro dispositivo de Wi-Fi ativado (computador ou telemóvel) para ligar ao SSID DevKit (realçado na imagem anterior). Deixe a palavra-passe em branco.

![Botão Ligar e as informações de rede](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurar Wi-Fi para o DevKit

Abra o endereço IP mostrado no ecrã DevKit no seu computador ou o browser do telemóvel, selecione a rede Wi-Fi que pretende que o DevKit para ligar a e, em seguida, escreva a palavra-passe. Selecione **Ligar**.

![Caixa de palavra-passe e o botão Ligar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Quando a ligação tiver êxito, reinicia o DevKit dentro de alguns segundos. Em seguida, verá o nome do Wi-Fi e o endereço IP na tela:

![Nome do Wi-Fi e endereço IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Precisará de uma rede de 2,4 GHz para IoT DevKit trabalhar. O módulo de Wi-Fi sobre o IoT DevKit não é compatível com a rede de 5GHz. Verifique [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

Após a configuração de Wi-Fi, as suas credenciais serão mantidas no dispositivo para essa conexão, mesmo que o dispositivo está desligado. Por exemplo, se configurar o DevKit para Wi-Fi na sua casa e, em seguida, efetuar o DevKit para o office, terá de reconfigurar o modo do Pacífico (começando no passo na secção "Introduza o modo do Pacífico") para ligar o DevKit ao seu Wi-Fi do escritório.

## <a name="start-using-the-devkit"></a>Começar a utilizar o DevKit

A aplicação predefinida em execução no DevKit verifica a versão mais recente do firmware e exibe alguns dados de diagnóstico de sensor para.

### <a name="upgrade-to-the-latest-firmware"></a>Atualizar para o firmware mais recente

> [!NOTE]
> Desde a versão 1.1, DevKit permite ST-SAFE no carregador de inicialização. Tem de atualizar o firmware, se estiver a executar uma versão anterior à versão 1.1.

Se precisar de uma atualização de firmware, o ecrã mostrará as versões de firmware atual e mais recentes. Para atualizar, siga os [atualizar o firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) guia.

![Exibição de versões de firmware atual e mais recentes](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Este é um esforço de uso individual. Depois que comece a desenvolver o DevKit e carregar seu aplicativo, o firmware mais recente virão com a sua aplicação.

### <a name="test-various-sensors"></a>Testar vários sensores

Prima o botão B para testar os sensores. Continue o pressionamento e a liberação do botão B para percorrer cada sensor.

![Apresentação B do botão e o sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="install-azure-iot-tools"></a>Instalar as ferramentas de IoT do Azure

Recomendamos [ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) pacote de extensão para o Visual Studio Code para desenvolver no DevKit. Contém as ferramentas do Azure IoT [Bancada de trabalho de dispositivo do Azure IoT](https://aka.ms/iot-workbench) desenvolver e depurar em diversos dispositivos do IoT devkit e [o Kit de ferramentas do Azure IoT Hub](https://aka.ms/iot-toolkit) para gerir e interagir com o IoT Hub do Azure.

Pode ver estes [Channel 9](https://channel9.msdn.com/) vídeos para ter a descrição geral sobre o que fazer:
* [Introdução à nova extensão de IoT Bancada de trabalho do VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [O que há de novo na extensão do Kit de ferramentas de IoT para o VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

Siga estes passos para preparar o ambiente de desenvolvimento para DevKit:

1. Instale [Arduino IDE](https://www.arduino.cc/en/Main/Software). Ele fornece a coleção de ferramentas necessária para compilar e carregar o código de Arduino.
    * **Windows**: Utilize a versão do Windows Installer. Não instale a partir da App Store.
    * **macOS**: Arraste e largue o extraídos **Arduino.app** em `/Applications` pasta.
    * **Ubuntu**: Deszipe-o na pasta como `$HOME/Downloads/arduino-1.8.8`

2. Instale [Visual Studio Code](https://code.visualstudio.com/), um editor de código de origem entre plataformas com programação poderosas ferramentas, como a conclusão de código do IntelliSense e depuração.

3. Iniciar o VS Code, procure **Arduino** no mercado de extensão e instalá-lo. Esta extensão oferece experiências aprimoradas para o desenvolvimento com Arduino plataforma.
    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure **ferramentas do Azure IoT** no mercado de extensão e instalá-lo.
    ![Instalar as ferramentas de IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Instalar o pacote de extensão de ferramentas de IoT do Azure](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Configure código VS com Arduino definições.

    No Visual Studio Code, clique em **ficheiro > Preferências > definições**. Em seguida, clique no **...**  e **abrir Settings**.
    ![Instalar as ferramentas de IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Adicione o seguinte linhas para configurar Arduino dependendo da sua plataforma: 

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
    
        Substitua a **{username}** marcador de posição abaixo com seu nome de utilizador.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Arduino: Gestor de tabuleiro**. Procure **AZ3166** e instale a versão mais recente.
    ![Instalar o SDK de DevKit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar controladores de ST-ligação

[ST-ligação/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que IoT DevKit utiliza para comunicar com a sua máquina de desenvolvimento. Tem de instalá-lo no Windows, o código de dispositivo compilado para o DevKit de Flash. Siga os passos de específicas de SO para permitir o acesso de máquina para o seu dispositivo.

* **Windows**: Baixe e instale o controlador USB a partir [Web site de STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) para [ligação direta](https://aka.ms/stlink-v2-windows).
* **macOS**: Nenhum driver é necessário para macOS.
* **Ubuntu**: Execute os comandos no terminal e terminar sessão e inicie sessão para que a alteração de grupo entrar em vigor:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora foram todos configurados com a preparar e configurar o ambiente de desenvolvimento. Vamos criar o exemplo de "Hello World" para IoT: enviar dados de telemetria de temperatura hub IoT do Azure.

## <a name="build-your-first-project"></a>Crie seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abra o código de exemplo da Galeria de exemplos

1. Certificar-se de que é o IoT DevKit **não ligado** para o seu computador. Inicie o VS Code primeiro e, em seguida, ligue o DevKit para o seu computador.

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Abrir os exemplos...** . Em seguida, selecione **IoT DevKit** como quadro.

1. Na página de exemplos da bancada de trabalho de IoT, encontrar **começar** e clique em **exemplo aberto**. Em seguida, seleciona o caminho predefinido para transferir o código de exemplo.
    ![Exemplo aberto](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Aprovisionamento do Azure IoT Hub e dispositivos

1. Na janela novo projeto aberto, clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Aprovisionar serviços do Azure...** . Siga o guia passo a passo para concluir o aprovisionamento do seu IoT Hub do Azure e a criação de dispositivos no IoT Hub.
    ![Comando de aprovisionamento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se não tiver entrado no Azure. Siga a notificação pop-up para iniciar sessão.

1. Selecione a subscrição que pretende utilizar.
    ![Selecione sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie um novo [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Selecione o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos que especificou, siga o guia para selecionar ou criar um novo IoT Hub do Azure.
    ![Selecione os passos do IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione o IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, verá que o IoT Hub do Azure aprovisionados ![aprovisionado do IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no IoT Hub do Azure que aprovisionou.
    ![Selecione os passos de dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione o dispositivo de IoT aprovisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora tem o IoT Hub do Azure aprovisionados e dispositivo criado no mesmo. Também a cadeia de ligação do dispositivo será guardada no VS Code para configurar o IoT DevKit mais tarde.
    ![Aprovisionamento concluído](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compile o código de dispositivo

1. Na barra de status do canto inferior direito, veja a **MXCHIP AZ3166** é mostrado como quadro selecionado e a porta serial com **STMicroelectronics** é utilizado.
    ![Selecionar o quadro e COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique em `F1` para abrir a paleta de comandos, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Configure definições do dispositivo...** , em seguida, selecione **cadeia de ligação do dispositivo de configuração > selecione o IoT Hub dispositivo Connection String**.

1. No DevKit, mantenha premida **botão A**, push e de versão do **repor** botão e, em seguida, versão **botão A**. Sua DevKit entra em modo de configuração e guarda a cadeia de ligação.
    ![Cadeia de ligação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique em `F1` novamente, escreva e selecione **Bancada de trabalho de dispositivo do Azure IoT: Carregar o código de dispositivo**. Ele começa a compilação e carregar o código para DevKit.
    ![Carregamento de Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O DevKit reinicia e começa a execução do código.

> [!NOTE]
> Se houver quaisquer erros ou interrupções, sempre pode recuperar ao executar o comando novamente.

## <a name="test-the-project"></a>Testar o projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Ver a telemetria enviada para o IoT Hub do Azure

Clique no ícone de plug power na barra de status para abrir o Monitor de série: ![Monitor de série](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo está em execução com êxito quando vir os seguintes resultados:

* O Monitor Serial apresenta a mensagem enviada para o IoT Hub.
* O LED no MXChip IoT DevKit é intermitente.

![Saída de Serial monitor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Ver a telemetria recebida pelo IoT Hub do Azure

Pode usar [ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorizar mensagens do dispositivo para a nuvem (D2C) no IoT Hub.

1. Iniciar sessão [portal do Azure](https://portal.azure.com/), encontrar o IoT Hub que criou.
    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. Na **políticas de acesso partilhado** painel, clique nas **iothubowner política**e anote a cadeia de ligação do IoT hub.
    ![Cadeia de ligação do IoT Hub do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No VS Code, clique em `F1`, introduza e selecione **IoT Hub do Azure: Definir cadeia de ligação do IoT Hub**. Copie a cadeia de ligação para o mesmo.
    ![Definir cadeia de ligação do IoT Hub do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda a **dispositivos do AZURE IOT HUB** painel à direita, clique com botão direito no nome do dispositivo que criou e selecione **iniciar mensagens D2C monitorização**.
    ![Monitor D2C Message](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. Na **saída** painel, pode ver as mensagens D2C de entrada para o IoT Hub.
    ![Mensagens D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Rever o código

O `GetStarted.ino` é o arquivo de esboço Arduino principal.

![Mensagens D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo é enviada para o IoT Hub do Azure, abra o `utility.cpp` ficheiro na mesma pasta. Modo de exibição [referência da API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a utilizar sensores e periféricos no IoT DevKit.

O `DevKitMQTTClient` utilizada é um wrapper do **iothub_client** da [Microsoft Azure IoT SDKs e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) para interagir com o IoT Hub do Azure.

## <a name="problems-and-feedback"></a>Problemas e comentários

Se tiver problemas, pode verificar a existência de uma solução no [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou contacte-na partir de [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Pode também dar feedback para deixar um comentário nesta página.

## <a name="next-steps"></a>Passos Seguintes

Ligou com êxito um MXChip IoT DevKit ao IoT hub e enviou os dados de sensor capturada ao seu hub IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
