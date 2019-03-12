---
title: ESP8266 para a cloud - ligar o Feather HUZZAH ESP8266 ao IoT Hub do Azure | Documentos da Microsoft
description: Saiba como configurar e ligar Adafruit Feather HUZZAH ESP8266 hub de IoT do Azure para o mesmo para enviar dados para a plataforma de cloud do Azure neste tutorial.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: ae123638e26c9fb6c82666efddabf06874c7b7a8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539965"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Ligar o Adafruit Feather HUZZAH ESP8266 ao IoT Hub do Azure na cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Ligação entre DHT22 Feather HUZZAH ESP8266 e IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>O que fazer

Ligar o Adafruit Feather HUZZAH ESP8266 para um hub IoT que criou. Em seguida, execute um aplicativo de exemplo no ESP8266 para recolher os dados de temperatura e humidade a partir de um sensor DHT22. Por fim, que envia os dados de sensor ao seu hub IoT.

> [!NOTE]
> Se estiver a utilizar outros quadros ESP8266, ainda pode seguir estes passos para ligá-lo ao seu hub IoT. Dependendo do quadro de ESP8266 estiver a utilizar, poderá ter de reconfigurar o `LED_PIN`. Por exemplo, se estiver a utilizar ESP8266 de IA Thinker, pode alterá-lo partir `0` para `2`. Não tem ainda um kit? Obtenha-o a partir da [Web site do Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT e registar um dispositivo para Feather HUZZAH ESP8266
* Como ligar o Feather HUZZAH ESP8266 com o sensor e o seu computador
* Como recolher dados de sensor ao executar um aplicativo de exemplo no Feather HUZZAH ESP8266
* Como enviar os dados de sensor ao seu hub IoT

## <a name="what-you-need"></a>Do que precisa

![Partes necessários para o tutorial](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, terá das seguintes partes de seu Feather HUZZAH ESP8266 Starter Kit:

* O quadro de Feather HUZZAH ESP8266
* Uma USB Micro a cabo USB de um tipo

Também precisa do seguinte para o seu ambiente de desenvolvimento:

* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um Mac ou um PC com Windows ou Ubuntu.
* Uma rede sem fio Feather HUZZAH ESP8266 ligar a.
* Uma ligação à Internet para transferir a ferramenta de configuração.
* [Extensão do Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> A versão de Arduino IDE utilizada pela extensão do Visual Studio Code para Arduino tem de ser a versão 1.6.8 ou posterior. Versões anteriores não funcionam com a biblioteca de AzureIoT.

Os seguintes itens são opcionais no caso de não ter um sensor. Tem também a opção de utilizar dados de sensores simulados.

* Um sensor de temperatura e humidade Adafruit DHT22
* Um breadboard
* Cablagem de jumper M/M

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Ligar o Feather HUZZAH ESP8266 com o sensor e o seu computador

Nesta secção, vai ligar os sensores a seu quadro. Em seguida, conectar no seu dispositivo para o seu computador para uso posterior.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Ligar um sensor de temperatura e humidade DHT22 a Feather HUZZAH ESP8266

Utilize cabos, mas breadboard e jumper para estabelecer a ligação da seguinte forma. Se não tiver um sensor, ignore esta secção porque pode usar os dados de sensores simulados em vez disso.

![Referência de ligações](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Para pins de sensor, utilize a seguinte ligação:

| Iniciar (Sensor)           | Fim (quadro)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------  |
| VDD (Pin 31F)            | 3 v (afixar 58H)           | Cabo vermelho     |
| DADOS (Pin 32F)           | GPIO 2 (Pin 46A)       | Cabo azul    |
| GND (Pin 34F)            | GND (PIn 56I)          | Cabo preto   |

Para obter mais informações, consulte [configuração do sensor Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e [Adafruit Feather HUZZAH Esp8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Agora o Feather Huzzah ESP8266 devem estar ligados com um sensor de trabalho.

![Conecte DHT22 Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Ligar o Feather HUZZAH ESP8266 para o seu computador

Conforme mostrado a seguir, utilize a USB Micro a cabo USB de um tipo para ligar o Feather HUZZAH ESP8266 para o seu computador.

![Ligar o Feather Huzzah para o seu computador](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicionar permissões de porta serial (apenas Ubuntu)

Se usar o Ubuntu, certifique-se de que tem as permissões para operar no USB porta de Feather HUZZAH ESP8266. Para adicionar permissões de porta serial, siga estes passos:

1. Execute os seguintes comandos num terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Obtém uma os seguintes resultados:

   * crw-rw---1 raiz uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Na saída, tenha em atenção que `uucp` ou `dialout` é o nome do grupo proprietário da porta USB.

2. Adicione o utilizador ao grupo executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` é o nome de proprietário do grupo que obteve no passo anterior. `<username>` é o nome de utilizador do Ubuntu.

3. Termine a Ubuntu e, em seguida, inicie sessão novamente para que a alteração apareça.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Recolher dados de sensor e enviá-lo ao seu hub IoT

Nesta secção, implementar e executar uma aplicação de exemplo no Feather HUZZAH ESP8266. O aplicativo de exemplo pisca LED no Feather HUZZAH ESP8266 e envia os dados de temperatura e humidade recolhidos a partir do sensor DHT22 ao seu hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter a aplicação de exemplo a partir do GitHub

O aplicativo de exemplo está alojado no GitHub. Clone o repositório de exemplo que contém a aplicação de exemplo do GitHub. Para clonar o repositório de exemplo, siga estes passos:

1. Abra um prompt de comando ou uma janela de terminal.

2. Ir para uma pasta onde pretende que o aplicativo de exemplo para serem armazenados.

3. Execute o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Em seguida, instale o pacote para Feather HUZZAH ESP8266 no Visual Studio Code.

4. Abra a pasta onde o aplicativo de exemplo é armazenado.

5. Abra o ficheiro de app.ino na pasta de aplicação no Visual Studio Code.

   ![Abra a aplicação de exemplo no Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. No Visual Studio Code, introduza `F1`.

7. Tipo **Arduino** e selecione **Arduino: Gestor de tabuleiro**.

8. Na **Gestor de tabuleiro de Arduino** separador, clique em **URLs adicionais**.

   ![Gerenciador de área de Arduino de código VS](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. Na **definições de utilizador** janela, copie e cole o seguinte no final do ficheiro

   ```
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```
   
   ![Configurar o URL do pacote Arduino no VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Salve o arquivo e feche o **definições de utilizador** separador.

11. Clique em **índices de pacote de atualização**. Depois de concluída a atualização, procure **esp8266**.

12. Clique em **instalar** botão para esp8266.

   Gestor de quadros indica que ESP8266 com uma versão do 2.2.0 ou posterior está instalado.

   ![O pacote de esp8266 está instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Introduza `F1`, em seguida, escreva **Arduino** e selecione **Arduino: Configuração de quadro**.

14. Clique em caixa de **quadro selecionado:** e escreva **esp8266**, em seguida, selecione **Adafruit HUZZAH ESP8266 (esp8266)**.

   ![Selecionar o quadro de esp8266](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No Visual Studio Code, introduza `F1`, em seguida, escreva **Arduino** e selecione **Arduino: Gestor de biblioteca**.

2. Pesquisa para a biblioteca seguinte nomes individualmente. Para cada biblioteca que encontrar, clique em **instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Não tem um sensor DHT22 real?

O aplicativo de exemplo pode simular dados de temperatura e humidade no caso de não ter um sensor DHT22 real. Para configurar a aplicação de exemplo para utilizar dados simulados, siga estes passos:

1. Abra o `config.h` de ficheiros a `app` pasta.

2. Localize a seguinte linha de código e altere o valor de `false` para `true`:

   ```c
   define SIMULATED_DATA true
   ```
   
   ![Configurar a aplicação de exemplo para utilizar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Guarde o ficheiro.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implementar a aplicação de exemplo para Feather HUZZAH ESP8266

1. No Visual Studio Code, clique em **<Select Serial Port>** o estado da barra e, em seguida, clique na porta de série para Feather HUZZAH ESP8266.

2. Introduza `F1`, em seguida, escreva **Arduino** e selecione **Arduino: Carregar** para criar e implementar a aplicação de exemplo para Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Introduza as suas credenciais

Depois do carregamento for concluída com êxito, siga estes passos para introduzir as suas credenciais:

1. Abra Arduino IDE, clique em **ferramentas** > **Serial Monitor**.

2. Na janela serial monitor, tenha em atenção as duas listas suspensas no canto inferior direito.

3. Selecione **sem fim da linha** para a esquerda na lista pendente.

4. Selecione **bauds 115200** para a direita na lista pendente.

5. Na caixa de entrada na parte superior da janela serial monitor, introduza as seguintes informações se for solicitado a fornecê-los e, em seguida, clique em **enviar**.

   * Wi-Fi SSID
   * Palavra-passe do Wi-Fi
   * Cadeia de ligação do dispositivo

> [!Note]
> As informações de credenciais são armazenadas no EEPROM de Feather HUZZAH ESP8266. Se clicar no botão de reposição no quadro Feather HUZZAH ESP8266, o aplicativo de exemplo pergunta se deseja apagar as informações. Introduza `Y` ter informações apagadas. É-lhe perguntado para fornecer as informações de uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Certifique-se de que o aplicativo de exemplo está em execução com êxito

Se vir o seguinte resultado da janela de serial monitor e o LED piscando no Feather HUZZAH ESP8266, o aplicativo de exemplo está em execução com êxito.

![Saída final no Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="next-steps"></a>Passos Seguintes

Com êxito ligado um Feather HUZZAH ESP8266 ao seu hub IoT e enviados os dados de sensor capturada ao seu hub IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]