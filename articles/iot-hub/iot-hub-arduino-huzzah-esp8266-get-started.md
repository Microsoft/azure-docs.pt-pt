---
title: ESP8266 para a nuvem conectar difusão HUZZAH ESP8266 ao Hub IoT do Azure | Microsoft Docs
description: Saiba como configurar e conectar o Adafruit difusa HUZZAH ESP8266 ao Hub IoT do Azure para que ele envie dados para a plataforma de nuvem do Azure neste tutorial.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 8e97a979c37af8ade51b4ff6ca4b2c5b4eec126e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232727"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Conectar Adafruit difusão HUZZAH ESP8266 ao Hub IoT do Azure na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexão entre DHT22, difusa HUZZAH ESP8266 e Hub IoT](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>O que você faz

Conecte Adafruit difusão HUZZAH ESP8266 a um hub IoT criado por você. Em seguida, você executa um aplicativo de exemplo no ESP8266 para coletar os dados de temperatura e umidade de um sensor de DHT22. Por fim, você envia os dados do sensor para o Hub IoT.

> [!NOTE]
> Se você estiver usando outras placas ESP8266, ainda poderá seguir estas etapas para conectá-lo ao Hub IoT. Dependendo da placa ESP8266 que você estiver usando, talvez seja necessário reconfigurar o `LED_PIN`. Por exemplo, se você estiver usando ESP8266 do ia-thinkr, você pode alterá- `0` lo `2`de para. Ainda não tem um kit? Obtenha-o no [site do Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registrar um dispositivo para difusão HUZZAH ESP8266
* Como conectar a difusão HUZZAH ESP8266 com o sensor e seu computador
* Como coletar dados de sensor executando um aplicativo de exemplo em difusão HUZZAH ESP8266
* Como enviar os dados do sensor para o Hub IoT

## <a name="what-you-need"></a>Do que precisa

![Partes necessárias para o tutorial](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu kit de início do HUZZAH ESP8266:

* A placa ESP8266 de difusão HUZZAH
* Um micro USB para digitar um cabo USB

Você também precisa dos seguintes itens para seu ambiente de desenvolvimento:

* Uma subscrição ativa do Azure. Se você não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou PC que esteja executando o Windows ou Ubuntu.
* [GIT](https://git-scm.com/download)
* [Arduino](https://www.arduino.cc/en/main/software#download)
* [Driver Adafruit huzzah ESP8266 para USB](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
* Uma rede sem fio para difusão HUZZAH ESP8266 para se conectar ao.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [Extensão de Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> A versão do IDE Arduino usada pela extensão de Visual Studio Code para Arduino deve ser a versão 1.6.8 ou posterior. As versões anteriores não funcionam com a biblioteca AzureIoT.

Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulados.

* Um sensor de temperatura e umidade Adafruit DHT22
* Um placa universal
* Cabos de jumper de m/M

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Conectar difusão HUZZAH ESP8266 com o sensor e seu computador

Nesta seção, você conectará os sensores à sua placa. Em seguida, conecte seu dispositivo ao computador para uso adicional.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Conectar um sensor de temperatura e umidade DHT22 para difusão HUZZAH ESP8266

Use os cabos de jumper e placa universal para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulados em vez disso.

![Referência de conexões](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Para Pins de sensor, use a seguinte fiação:

| Início (sensor)           | Fim (quadro)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------  |
| VDD (PIN 31F)            | 3V (pino 58H)           | Cabo vermelho     |
| DADOS (PIN 32F)           | GPIO 2 (PIN 46A)       | Cabo azul    |
| GND (PIN 34F)            | GND (PIn 56I)          | Cabo preto   |

Para obter mais informações, consulte [instalação do sensor ADAFRUIT DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e [Adafruit difusa HUZZAH Esp8266 pinagens](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Agora, seu huzzah de difusão ESP8266 deve estar conectado a um sensor de trabalho.

![Conectar DHT22 com difusão Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Conectar difusão HUZZAH ESP8266 ao seu computador

Conforme mostrado a seguir, use o micro USB para digitar um cabo USB para conectar a difusão HUZZAH ESP8266 ao seu computador.

![Conectar o huzzah de difusão ao seu computador](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicionar permissões de porta serial (somente Ubuntu)

Se você usar o Ubuntu, verifique se tem as permissões para operar na porta USB de difusão HUZZAH ESP8266. Para adicionar permissões de porta serial, siga estas etapas:

1. Execute os seguintes comandos em um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Você Obtém uma das seguintes saídas:

   * CRW-RW----1 raiz UUCP xxxxxxxx
   * CRW-RW----1 conexão de discagem raiz xxxxxxxx

   Na saída, observe que `uucp` ou `dialout` é o nome do proprietário do grupo da porta USB.

2. Adicione o usuário ao grupo executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`é o nome do proprietário do grupo que você obteve na etapa anterior. `<username>`é o nome de usuário do Ubuntu.

3. Saia do Ubuntu e, em seguida, entre novamente para que a alteração apareça.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o Hub IoT

Nesta seção, você implantará e executará um aplicativo de exemplo em difusão HUZZAH ESP8266. O aplicativo de exemplo pisca o LED em difusão HUZZAH ESP8266 e envia os dados de temperatura e umidade coletados do sensor DHT22 para o Hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter o aplicativo de exemplo do GitHub

O aplicativo de exemplo é hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.

2. Vá para uma pasta onde você deseja que o aplicativo de exemplo seja armazenado.

3. Execute o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Em seguida, instale o pacote para difusão HUZZAH ESP8266 no Visual Studio Code.

4. Abra a pasta onde o aplicativo de exemplo está armazenado.

5. Abra o arquivo app. ino na pasta do aplicativo no Visual Studio Code.

   ![Abra o aplicativo de exemplo no Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. Na Visual Studio Code, digite `F1`.

7. Digite **Arduino** e selecione **Arduino: Gerenciador**de placa.

8. Na guia **Gerenciador da placa Arduino** , clique em **URLs adicionais**.

   ![VS Code o Gerenciador da placa Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. Na janela **configurações do usuário** , copie e cole o seguinte no final do arquivo

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Configurar a URL do pacote Arduino no VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Salve o arquivo e feche a guia **configurações do usuário** .

11. Clique em **Atualizar índices de pacote**. Após a conclusão da atualização, procure **esp8266**.

12. Clique no botão **instalar** para esp8266.

    O Gerenciador de placas indica que o ESP8266 com uma versão do 2.2.0 ou posterior está instalado.

    ![O pacote esp8266 está instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Insira `F1`, em seguida  , digite Arduino **e selecione Arduino: Configuração**do quadro.

14. Clique na caixa do **quadro selecionado:** e digite **esp8266**, em seguida, selecione **Adafruit HUZZAH esp8266 (esp8266)** .

    ![Selecione a placa esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. Na Visual Studio Code, digite `F1`e, em seguida, digite **Arduino** e selecione **Arduino: Gerenciador**de biblioteca.

2. Pesquise os nomes de biblioteca a seguir um a um. Para cada biblioteca que você encontrar, clique em **instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Não tem um sensor de DHT22 real?

O aplicativo de exemplo pode simular dados de temperatura e umidade caso você não tenha um sensor de DHT22 real. Para configurar o aplicativo de exemplo para usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo `app` na pasta.

2. Localize a seguinte linha de código e altere o valor de `false` para `true`:

   ```c
   define SIMULATED_DATA true
   ```

   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Guarde o ficheiro.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implantar o aplicativo de exemplo para difundir HUZZAH ESP8266

1. Na Visual Studio Code, clique em  **\<selecionar porta serial >** na barra de status e, em seguida, clique na porta serial para difusão HUZZAH ESP8266.

2. Insira `F1`, em seguida  , digite Arduino **e selecione Arduino: Carregue** para compilar e implantar o aplicativo de exemplo para difundir HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Insira suas credenciais

Depois que o carregamento for concluído com êxito, siga estas etapas para inserir suas credenciais:

1. Abra o IDE do Arduino, clique em **ferramentas** > **Serial Monitor**.

2. Na janela monitor serial, observe as duas listas suspensas no canto inferior direito.

3. Selecione **sem fim de linha** para a lista suspensa à esquerda.

4. Selecione **taxa de transmissão de 115200** para a lista suspensa à direita.

5. Na caixa de entrada localizada na parte superior da janela do monitor serial, insira as informações a seguir se você for solicitado a fornecê-las e, em seguida, clique em **Enviar**.

   * SSID de Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão do dispositivo

> [!Note]
> As informações de credenciais são armazenadas no EEPROM de difusão HUZZAH ESP8266. Se você clicar no botão redefinir na placa HUZZAH ESP8266 de difusão, o aplicativo de exemplo perguntará se você deseja apagar as informações. Insira `Y` para que as informações sejam apagadas. Você será solicitado a fornecer as informações uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verifique se o aplicativo de exemplo está sendo executado com êxito

Se você vir a seguinte saída da janela do monitor serial e o LED piscando em difusão HUZZAH ESP8266, o aplicativo de exemplo será executado com êxito.

![Saída final no IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo seu hub

Uma maneira de monitorar as mensagens recebidas pelo Hub IoT do seu dispositivo é usar as ferramentas de IoT do Azure para Visual Studio Code. Para saber mais, confira [usar as ferramentas de IOT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IOT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais maneiras de processar dados enviados pelo seu dispositivo, continue na próxima seção.

## <a name="next-steps"></a>Passos Seguintes

Você conectou um ESP8266 de difusão HUZZAH com êxito ao Hub IoT e enviou os dados de sensor capturados para o Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]