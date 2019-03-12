---
title: Raspberry Pi para cloud (node. js) - ligar o Raspberry Pi hub IoT do Azure | Documentos da Microsoft
description: Saiba como configurar e ligar o Raspberry Pi a IoT Hub do Azure para o Raspberry Pi enviar dados para a plataforma de cloud do Azure neste tutorial.
author: rangv
manager: ''
keywords: o Azure iot raspberry pi, hub de iot raspberry pi, enviar dados para a cloud de raspberry pi, raspberry pi com a nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: ebdc90dc35f891ea1811ce81c5bdc1b937c58a5c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529256"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Ligar o Raspberry Pi ao Hub IoT do Azure (node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começará aprender as noções básicas do trabalho com o Raspberry Pi com Raspbian. Em seguida, saiba como ligar forma totalmente integrada os seus dispositivos para a cloud, utilizando [IoT Hub do Azure](about-iot-hub.md). Para exemplos do Windows 10 IoT Core, vá para o [Windows Dev Center](https://www.windowsondevices.com/).

Não tem ainda um kit? Tente [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou comprar um novo kit [aqui](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>O que fazer

* Crie um hub IoT.
* Registe um dispositivo para o instalador de plataforma do seu hub IoT.
* Configure o Raspberry Pi.
* Execute uma aplicação de exemplo no instalador de plataforma para enviar dados de sensor ao seu hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a cadeia de ligação do novo dispositivo.
* Como ligar o Pi com um sensor BME280.
* Como recolher dados de sensor ao executar um aplicativo de exemplo no Pi.
* Como enviar dados de sensor ao seu hub IoT.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](./media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Um quadro Raspberry Pi 2 ou Raspberry Pi 3.
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Um monitor, um USB teclado e mouse que se liga a Pi.
* Um Mac ou o PC que está a executar o Windows ou Linux.
* Uma ligação à internet.
* Um 16 GB ou superior do cartão microSD.
* Um USB-adaptador ou microSD cartão SD para gravar a imagem de sistema operativo para o cartão microSD.
* Forneça uma potência de 2 e 5 volt com 6 pés micro um cabo USB.

Os seguintes itens são opcionais:

* Um montado Adafruit BME280 temperatura e humidade pressão sensor.
* Um breadboard.
* Cablagem de jumper 6 F/M.
* Um diffused LED de 10 mm.

> [!NOTE] 
> Se não tiver os itens opcionais, pode utilizar dados de sensores simulados.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter cadeia de ligação do IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar o Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema de operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Baixe Raspbian.

   a. [Transferir Raspbian Stretch](https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (o ficheiro. zip).

   > [!WARNING]
   > Utilize acima ligação para transferir `raspbian-2017-07-5` imagem zip. A versão mais recente de imagens de Raspbian tem alguns problemas conhecidos com o nó de conectando-Pi, que pode causar falhas nos passos seguintes.
 
   b. Extraia a imagem de Raspbian para uma pasta no seu computador.

2. Instale Raspbian ao cartão microSD.

   a. [Transferir e instalar o utilitário de gravador de cartão Etcher SD](https://etcher.io/).

   b. Execute Etcher e selecione a imagem de Raspbian extraído no passo 1.

   c. Selecione a unidade de cartão microSD. Etcher a poderá já ter selecionado a unidade correta.

   d. Clique em Flash para instalar Raspbian ao cartão microSD.

   e. Remova o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente, pois Etcher automaticamente ejeta ou desmonta o cartão microSD após a conclusão.

   f. Inserir o cartão microSD Pi.

### <a name="enable-ssh-and-i2c"></a>Ativar o SSH e I2C

1. Ligar o instalador de plataforma para o monitor, teclado e mouse. 

2. Iniciar o instalador de plataforma e, em seguida, inicie sessão no Raspbian utilizando `pi` como o nome de utilizador e `raspberry` como a palavra-passe.

3. Clique no ícone de Raspberry > **preferências** > **Raspberry Pi configuração**.

   ![O menu de preferências de Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

4. Sobre o **Interfaces** separador, defina **I2C** e **SSH** para **ativar**e, em seguida, clique em **OK**. Se não tiver físicos sensores e utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar I2C e SSH no Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Para ativar o SSH e I2C, pode encontrar mais documentos de referência sobre [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Ligar o sensor ao instalador de plataforma

Utilize cabos, mas breadboard e jumper para ligar um LED e um BME280 para Pi da seguinte forma. Se não tiver o sensor [ignorar esta secção](#connect-pi-to-the-network).

![A ligação Raspberry Pi e o sensor](./media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. O LED pisca quando o dispositivo envia uma mensagem para a cloud. 

Para pins de sensor, utilize a seguinte ligação:

| Iniciar (Sensor & LED)     | Fim (quadro)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5g)             | 3.3V PWR (Pin 1)       | Cabo branco   |
| GND (Pin 7G)             | GND (Pin 6)            | Cabo castanha   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Cabo vermelho     |
| SCK (Pin 8g)             | I2C1 SCL (Pin 5)       | Cor de laranja cabo  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Cabo branco   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Cabo preto   |

Clique para ver [Raspberry Pi 2 e 3 mapeamentos de pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para sua referência.

Depois de se ligar com êxito BME280 para o seu Raspberry Pi, deve ser, como abaixo da imagem.

![Instalador de plataforma ligado e BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ligar o Pi à rede

Ative o Pi usando o cabo USB do micro e a fonte de alimentação. Utilize o cabo Ethernet para ligar o instalador de plataforma à sua rede com fio ou seguir a [instruções da base do Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) para ligar o instalador de plataforma à sua rede sem fio. Depois de seu Pi foi ligado com êxito à rede, terá de tomar nota dos [endereço IP do seu Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ligado à rede com fio](./media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Certifique-se de que o instalador de plataforma está ligado à mesma rede que seu computador. Por exemplo, se o computador está ligado a uma rede sem fio, enquanto o instalador de plataforma está ligado a uma rede com fio, poderá não ver o endereço IP na saída devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de exemplo no Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Clonar o exemplo de aplicação e instalar os pacotes de pré-requisitos

1. Ligar-se para o seu Raspberry Pi com um dos clientes SSH seguintes a partir do computador anfitrião:
   
   **Utilizadores do Windows**
  
   a. Transfira e instale [PuTTY](https://www.putty.org/) para Windows. 

   b. Copie o endereço IP da seção de instalador de plataforma para o nome de anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac e os utilizadores do Ubuntu**
   
   Utilize o cliente SSH incorporado no Ubuntu ou macOS. Poderá ter de executar `ssh pi@<ip address of pi>` para ligar o instalador de plataforma através de SSH.

   > [!NOTE] 
   > O nome de utilizador predefinido é `pi` e a palavra-passe é `raspberry`.

2. Instale node. js e NPM para seu Pi.
   
   Em primeiro lugar, verifique sua versão do node. js. 
   
   ```bash
   node -v
   ```

   Se a versão é inferior à 4.x, ou se não houver nenhum node. js no seu Pi, instale a versão mais recente.

   ```bash
   curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Clone a aplicação de exemplo.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. Instale todos os pacotes para o exemplo. A instalação inclui o SDK de dispositivo do IoT do Azure, biblioteca de BME280 Sensor e biblioteca de preparar o Pi.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   >Poderá demorar vários minutos a concluir o processo de instalação, dependendo da sua ligação de rede.

### <a name="configure-the-sample-application"></a>Configurar a aplicação de exemplo

1. Abra o ficheiro de configuração ao executar os comandos seguintes:

   ```bash
   nano config.json
   ```

   ![Ficheiro de configuração](./media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Existem dois itens existentes neste ficheiro, que pode configurar. A primeira é `interval`, que define o intervalo de tempo (em milissegundos) entre as mensagens enviadas para a cloud. A segunda é `simulatedData`, que é um valor booleano para indicar se a utilizar dados de sensores simulados ou não.

   Se **não tem o sensor**, defina o `simulatedData` valor a `true` para tornar o aplicativo de exemplo, criar e utilizar dados de sensores simulados.

2. Guardar e sair, escrevendo o controle-O > Enter > controlo-X.

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Execute a aplicação de exemplo ao executar o seguinte comando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Certifique-se copiar-colar a cadeia de ligação do dispositivo para as aspas simples.


Deverá ver o resultado seguinte que mostra os dados de sensor e as mensagens que são enviadas ao seu hub IoT.

![Saída - dados de sensor enviados do Raspberry Pi ao seu hub IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Passos Seguintes

Executar uma aplicação de exemplo para recolher dados de sensor e enviá-lo ao seu hub IoT. Para ver as mensagens que seu Raspberry Pi enviado para o seu IoT hub ou enviar mensagens para o seu Raspberry Pi, consulte a [Utilize ferramentas de IoT do Azure para Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
