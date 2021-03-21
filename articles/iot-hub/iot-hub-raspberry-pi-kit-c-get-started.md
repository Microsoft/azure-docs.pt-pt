---
title: Ligue raspberry Pi ao Azure IoT Hub usando C | Microsoft Docs
description: Saiba como configurar e ligar Raspberry Pi ao Azure IoT Hub para Raspberry Pi para enviar dados para a plataforma cloud Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97804059"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Ligue raspberry Pi ao Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começa-se por aprender o básico de trabalhar com Raspberry Pi que está a gerir Raspbian. Em seguida, aprende a ligar os seus dispositivos perfeitamente à nuvem utilizando o [Azure IoT Hub](about-iot-hub.md). Para as amostras do Núcleo IoT do Windows 10, aceda ao [Windows Dev Center](https://www.windowsondevices.com/).

Ainda não tem um kit? Experimente [o simulador online Raspberry Pi.](iot-hub-raspberry-pi-web-simulator-get-started.md) Ou comprar um kit novo [aqui.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que faz

* Criar um hub IoT.

* Registe um dispositivo para Pi no seu hub IoT.

* Configuração Raspberry Pi.

* Execute uma aplicação de amostra no Pi para enviar dados de sensores para o seu hub IoT.

Ligue raspberry Pi a um hub IoT que você cria. Em seguida, você executar uma aplicação de amostra em Pi para recolher dados de temperatura e humidade de um sensor BME280. Finalmente, envia os dados do sensor para o seu hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub Azure IoT e obter a sua nova cadeia de ligação do dispositivo.

* Como ligar Pi a um sensor BME280.

* Como recolher dados de sensores executando uma aplicação de amostra no Pi.

* Como enviar dados de sensores para o seu hub IoT.

## <a name="what-you-need"></a>O que precisa

![O que precisa](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* A placa Raspberry Pi 2 ou Raspberry Pi 3.

* Uma subscrição ativa do Azure. Se não tiver uma conta Azure, [crie uma conta de teste gratuita em](https://azure.microsoft.com/free/) apenas alguns minutos.

* Um monitor, um teclado USB e um rato que se ligam ao Pi.

* Um Mac ou um PC que esteja a executar Windows ou Linux.

* Uma ligação à Internet.

* Um cartão microSD de 16 GB ou superior.

* Um adaptador USB-SD ou um cartão microSD para queimar a imagem do sistema operativo no cartão microSD.

* Uma fonte de alimentação de 5 volts de 2 amp com o cabo USB de 1,80m.

Os seguintes itens são opcionais:

* Um sensor de temperatura, pressão e humidade Adafruit BME280 montado.

* Uma tábua de pão.

* 6 fios de ponte F/M.

* Um LED difuso de 10 mm.

> [!NOTE]
> Estes itens são opcionais porque a amostra de código suporta dados simulados do sensor.
>

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar Raspberry Pi

Agora prepara o Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instale o sistema operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Descarregue Raspbian.

   1. [Baixar Raspbian Stretch com Desktop](https://www.raspberrypi.org/software/) (o ficheiro .zip).

   2. Extraia a imagem Raspbian para uma pasta no seu computador.

2. Instale Raspbian no cartão microSD.

   1. [Descarregue e instale o utilitário de queimador de cartão Etcher SD](https://etcher.io/).

   2. Corra Etcher e selecione a imagem Raspbian que extraiu no passo 1.

   3. Selecione a unidade do cartão microSD. Note que etcher pode já ter selecionado a unidade correta.

   4. Clique em Flash para instalar Raspbian no cartão microSD.

   5. Retire o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente porque o Etcher ejeta automaticamente ou desmonta o cartão microSD após a conclusão.

   6. Insira o cartão microSD em Pi.

### <a name="enable-ssh-and-spi"></a>Ativar SSH e SPI

1. Ligue Pi ao monitor, teclado e rato, inicie o pi e inicie o teste em Raspbian utilizando `pi` como nome de utilizador e como `raspberry` senha.
 
2. Clique no ícone de framboesa > **Configuração De**  >  **Framboesa Pi**.

   ![O menu Preferências Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. No separador **Interfaces,** desloque **SPI** e **SSH** para **ativar** e, em seguida, clique em **OK**. Se não tem sensores físicos e quer usar dados de sensores simulados, este passo é opcional.

   ![Ativar SPI e SSH em Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para ativar o SSH e o SPI, pode encontrar mais documentos de referência em [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Ligue o sensor a Pi

Utilize a prancha de pão e os fios de ponte para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor, [salte esta secção.](#connect-pi-to-the-network)

![A framboesa Pi e a ligação sensorial](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. E o LED piscará se houver uma comunicação entre o dispositivo e a nuvem.

Para os pinos dos sensores, utilize os seguintes fios:

| Início (Sensor & LED)     | Fim (Placa)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (Pino 7)         | Cabo branco   |
| LED GND (Pin 6G)         | GND (Pino 6)            | Cabo preto   |
| VDD (pino 18F)            | 3.3V PWR (Pino 17)      | Cabo branco   |
| GND (Pin 20F)            | GND (Pino 20)           | Cabo preto   |
| SCK (Pin 21F)            | SPI0 SCLK (Pino 23)     | Cabo laranja  |
| SDO (Pin 22F)            | SPI0 MISO (Pino 21)     | Cabo amarelo  |
| SDI (Pin 23F)            | SPI0 MOSI (Pino 19)     | Cabo verde   |
| CS (Pin 24F)             | SPI0 CS (Pino 24)       | Cabo azul    |

Clique para ver [raspberry Pi 2 & mapeamentos de 3 pin](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) para a sua referência.

Depois de ter ligado o BME280 com sucesso ao seu Raspberry Pi, deve ser como a imagem abaixo.

![Pi conectado e BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ligue Pi à rede

Ligue pi utilizando o cabo MICRO USB e a fonte de alimentação. Utilize o cabo Ethernet para ligar Pi à sua rede com fios ou siga as [instruções da Fundação Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/) para ligar pi à sua rede sem fios. Depois de o seu Pi ter sido conectado com sucesso à rede, precisa de tomar nota do [endereço IP do seu Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Ligado à rede com fios](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de amostra em Pi

### <a name="sign-into-your-raspberry-pi"></a>Inscreva-se na sua Raspberry Pi

1. Utilize um dos seguintes clientes SSH do seu computador anfitrião para ligar ao seu Raspberry Pi.
   
   **Utilizadores do Windows**
   1. Faça o download e instale [o PuTTY](https://www.putty.org/) para windows. 
   1. Copie o endereço IP do seu Pi na secção nome anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utilizadores Mac e Ubuntu**

   Utilize o cliente SSH incorporado no Ubuntu ou no macOS. Talvez precises de correr `ssh pi@<ip address of pi>` para ligar o Pi via SSH.
   > [!NOTE]
   > O nome de utilizador predefinido é `pi` , e a palavra-passe é `raspberry` .


### <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Clonar a aplicação da amostra executando o seguinte comando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Executar script de configuração:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Se **não tiver um BME280 físico,** pode utilizar '---dados simulados' como parâmetro da linha de comando para simular dados de temperatura&humidade. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Construa e execute a aplicação da amostra

1. Construa a aplicação da amostra executando o seguinte comando:

   ```bash
   cmake . && make
   ```
   
   ![Construir saída](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Executar o pedido de amostra executando o seguinte comando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Certifique-se de que copia a cadeia de ligação do dispositivo nas cotações individuais.
   >

Deverá ver o seguinte resultado, que mostra os dados do sensor e as mensagens que são enviadas ao seu hub IoT.

![Resultado – dados do sensor enviados do Raspberry Pi ao hub IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leia as mensagens recebidas pelo seu hub

Uma forma de monitorizar as mensagens recebidas pelo seu hub IoT do seu dispositivo é utilizar as Ferramentas IoT Azure para Código do Estúdio Visual. Para saber mais, consulte [Use Azure IoT Tools for Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para mais formas de processar os dados enviados pelo seu dispositivo, continue para a secção seguinte.

## <a name="next-steps"></a>Passos seguintes

Executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]