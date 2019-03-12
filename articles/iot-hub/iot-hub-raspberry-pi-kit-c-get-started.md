---
title: Ligar o Raspberry Pi ao IoT Hub do Azure com C | Documentos da Microsoft
description: Saiba como configurar e ligar o Raspberry Pi a IoT Hub do Azure para o Raspberry Pi enviar dados para a plataforma de cloud do Azure
author: rangv
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: rangv
ms.openlocfilehash: 562dc32f6dd40e6b0103fcc255ceafe829e2265d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529655"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Ligar o Raspberry Pi a IoT Hub (C) do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começará aprender as noções básicas do trabalho com o Raspberry Pi com Raspbian. Em seguida, saiba como ligar forma totalmente integrada os seus dispositivos para a cloud, utilizando [IoT Hub do Azure](about-iot-hub.md). Para exemplos do Windows 10 IoT Core, vá para o [Windows Dev Center](https://www.windowsondevices.com/).

Não tem ainda um kit? Tente [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou comprar um novo kit [aqui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>O que fazer

* Crie um hub IoT.
* Registe um dispositivo para o instalador de plataforma do seu hub IoT.
* Configure o Raspberry Pi.
* Execute uma aplicação de exemplo no instalador de plataforma para enviar dados de sensor ao seu hub IoT.

Ligar o Raspberry Pi para um hub IoT que criou. Em seguida, executar um aplicativo de exemplo no Pi para recolher dados de temperatura e humidade de um sensor BME280. Por fim, que envia os dados de sensor ao seu hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo.
* Como ligar o Pi com um sensor BME280.
* Como recolher dados de sensor ao executar um aplicativo de exemplo no Pi.
* Como enviar dados de sensor ao seu hub IoT.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](./media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* O quadro Raspberry Pi 2 ou Raspberry Pi 3.
* Uma subscrição ativa do Azure. Se não tiver uma conta do Azure, [criar uma conta gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um monitor, um USB teclado e mouse que se ligam a Pi.
* Um Mac ou um PC que está a executar o Windows ou Linux.
* Uma ligação à Internet.
* Um 16 GB ou superior do cartão microSD.
* Um USB-adaptador ou microSD cartão SD para gravar a imagem de sistema operativo para o cartão microSD.
* Forneça uma potência de 2 e 5 volt com 6 pés micro um cabo USB.

Os seguintes itens são opcionais:

* Um montado Adafruit BME280 temperatura e humidade pressão sensor.
* Um breadboard.
* Cablagem de jumper 6 F/M.
* Um diffused LED de 10 mm.

> [!NOTE] 
> Estes itens são opcionais, uma vez que o suporte de exemplo de código simulated dados de sensor.
>

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="setup-raspberry-pi"></a>Configurar o Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema de operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Baixe Raspbian.
   1. [Transferir Raspbian Stretch com o Desktop](https://www.raspberrypi.org/downloads/raspbian/) (o ficheiro. zip).
   1. Extraia a imagem de Raspbian para uma pasta no seu computador.
1. Instale Raspbian ao cartão microSD.
   1. [Transferir e instalar o utilitário de gravador de cartão Etcher SD](https://etcher.io/).
   1. Execute Etcher e selecione a imagem de Raspbian extraído no passo 1.
   1. Selecione a unidade de cartão microSD. Tenha em atenção que Etcher poderá ter já selecionado da unidade correta.
   1. Clique em Flash para instalar Raspbian ao cartão microSD.
   1. Remova o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente, pois Etcher automaticamente ejeta ou desmonta o cartão microSD após a conclusão.
   1. Inserir o cartão microSD Pi.

### <a name="enable-ssh-and-spi"></a>Ativar o SSH e SPI

1. Ligar o instalador de plataforma para o monitor, teclado e mouse, iniciar o instalador de plataforma e, em seguida, inicie sessão no Raspbian utilizando `pi` como o nome de utilizador e `raspberry` como a palavra-passe.
1. Clique no ícone de Raspberry > **preferências** > **Raspberry Pi configuração**.

   ![O menu de preferências de Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Sobre o **Interfaces** separador, defina **SPI** e **SSH** para **ativar**e, em seguida, clique em **OK**. Se não tiver físicos sensores e utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar SPI e SSH no Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Para ativar o SSH e SPI, pode encontrar mais documentos de referência sobre [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Ligar o sensor ao instalador de plataforma

Utilize cabos, mas breadboard e jumper para ligar um LED e um BME280 para Pi da seguinte forma. Se não tiver o sensor [ignorar esta secção](#connect-pi-to-the-network).

![A ligação Raspberry Pi e o sensor](./media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. E o LED será blink se houver uma comunicação entre o dispositivo e a cloud. 

Para pins de sensor, utilize a seguinte ligação:

| Iniciar (Sensor & LED)     | Fim (quadro)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5g)         | GPIO 4 (Pin 7)         | Cabo branco   |
| LED GND (Pin 6G)         | GND (Pin 6)            | Cabo preto   |
| VDD (Pin 18F)            | 3.3V PWR (17 de Pin)      | Cabo branco   |
| GND (Pin 20F)            | GND (Pin 20)           | Cabo preto   |
| SCK (Pin 21F)            | SPI0 SCLK (23 de Pin)     | Cor de laranja cabo  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Cabo amarelo  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Cabo verde   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Cabo azul    |

Clique para ver [Raspberry Pi 2 e 3 mapeamentos de Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para sua referência.

Depois de se ligar com êxito BME280 para o seu Raspberry Pi, deve ser, como abaixo da imagem.

![Instalador de plataforma ligado e BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ligar o Pi à rede

Ative o Pi usando o cabo USB do micro e a fonte de alimentação. Utilize o cabo Ethernet para ligar o instalador de plataforma à sua rede com fio ou seguir a [instruções da base do Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) para ligar o instalador de plataforma à sua rede sem fio. Depois de seu Pi foi ligado com êxito à rede, terá de tomar nota dos [endereço IP do seu Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ligado à rede com fio](./media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de exemplo no Pi

### <a name="login-to-your-raspberry-pi"></a>Início de sessão para o seu Raspberry Pi

1. Utilize um dos seguintes clientes SSH do computador anfitrião para ligar ao seu Raspberry Pi.
   
   **Utilizadores do Windows**
   1. Transfira e instale [PuTTY](https://www.putty.org/) para Windows. 
   1. Copie o endereço IP da seção de instalador de plataforma para o nome de anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac e os utilizadores do Ubuntu**
   
   Utilize o cliente SSH incorporado no Ubuntu ou macOS. Poderá ter de executar `ssh pi@<ip address of pi>` para ligar o instalador de plataforma através de SSH.
   > [!NOTE] 
   O nome de utilizador predefinido é `pi` , e a palavra-passe é `raspberry`.


### <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Clone a aplicação de exemplo ao executar o seguinte comando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Execute o script de configuração:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Se **não tem um BME280 físico**, pode utilizar "– dados simulados ' como parâmetro de linha de comandos para simular dados de temperatura e humidade. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Criar e executar o aplicativo de exemplo

1. Crie a aplicação de exemplo ao executar o seguinte comando:

   ```bash
   cmake . && make
   ```
   
   ![Criar a saída](./media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Execute a aplicação de exemplo ao executar o seguinte comando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Certifique-se copiar-colar a cadeia de ligação do dispositivo para as aspas simples.
   >

Deverá ver o resultado seguinte que mostra os dados de sensor e as mensagens que são enviadas ao seu hub IoT.

![Saída - dados de sensor enviados do Raspberry Pi ao seu hub IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Passos Seguintes

Executar uma aplicação de exemplo para recolher dados de sensor e enviá-lo ao seu hub IoT. Para ver as mensagens que seu Raspberry Pi enviado para o seu IoT hub ou enviar mensagens para o seu Raspberry Pi, consulte a [Utilize ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
