---
title: Ligue raspberry Pi ao Hub Azure IoT usando C / Microsoft Docs
description: Saiba como configurar e ligar Raspberry Pi ao Hub Azure IoT para Raspberry Pi enviar dados para a plataforma de nuvem Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 03f9d58cab725335b0f4090ac1a7289c32c0af7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81640552"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Ligue raspberry Pi ao Hub Azure IoT (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começa-se por aprender o básico de trabalhar com Raspberry Pi que está a gerir o Raspbian. Em seguida, aprende a ligar os seus dispositivos à nuvem utilizando o [Azure IoT Hub](about-iot-hub.md). Para amostras do Windows 10 IoT Core, vá ao [Windows Dev Center](https://www.windowsondevices.com/).

Ainda não tem um kit? Experimente [o simulador online Raspberry Pi.](iot-hub-raspberry-pi-web-simulator-get-started.md) Ou comprar um kit novo [aqui.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que faz

* Criar um centro de ioT.

* Registe um dispositivo para pi no seu centro ioT.

* Preparar Raspberry Pi.

* Faça uma aplicação de amostra em Pi para enviar dados de sensores para o seu centro IoT.

Ligue raspberry Pi a um centro de IoT que você cria. Em seguida, executa uma aplicação de amostra em Pi para recolher dados de temperatura e humidade de um sensor BME280. Finalmente, envia os dados do sensor para o seu centro ioT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub Azure IoT e obter a sua nova cadeia de ligação ao dispositivo.

* Como ligar pi a um sensor BME280.

* Como recolher dados de sensores executando uma aplicação de amostra em Pi.

* Como enviar dados de sensores para o seu centro ioT.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* A prancha Raspberry Pi 2 ou Raspberry Pi 3.

* Uma subscrição ativa do Azure. Se não tiver uma conta Azure, [crie uma conta de teste Azure gratuita](https://azure.microsoft.com/free/) em poucos minutos.

* Um monitor, um teclado USB e um rato que se ligam ao Pi.

* Um Mac ou um PC que esteja a executar Windows ou Linux.

* Uma ligação à Internet.

* Um cartão de 16 GB ou acima do microSD.

* Um adaptador USB-SD ou um cartão microSD para queimar a imagem do sistema operativo no cartão microSD.

* Uma fonte de alimentação de 5 volts de 2 amp com o cabo USB de 1,80m.

Os seguintes itens são opcionais:

* Um sensor de temperatura, pressão e humidade Adafruit BME280 montado.

* Uma tábua de pão.

* 6 fios de salta F/M.

* Um LED de 10 mm difundido.

> [!NOTE]
> Estes itens são opcionais porque a amostra de código suporta dados de sensores simulados.
>

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar Raspberry Pi

Agora prepara o Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instale o sistema operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem raspbiana.

1. Baixe raspbian.

   1. [Baixe o Raspbian Stretch com desktop](https://www.raspberrypi.org/downloads/raspbian/) (o ficheiro .zip).

   2. Extraia a imagem raspbiana para uma pasta no seu computador.

2. Instale raspbian no cartão microSD.

   1. [Descarregue e instale o utilitário de queimador de cartão Etcher SD](https://etcher.io/).

   2. Execute etcher e selecione a imagem raspbiana que extraiu no passo 1.

   3. Selecione a unidade de cartão microSD. Note que etcher pode já ter selecionado a unidade correta.

   4. Clique em Flash para instalar Raspbian no cartão microSD.

   5. Retire o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente porque Etcher ejeta ou desmonta automaticamente o cartão microSD após a conclusão.

   6. Insira o cartão microSD em Pi.

### <a name="enable-ssh-and-spi"></a>Ativar SSH e SPI

1. Ligue Pi ao monitor, teclado e rato, inicie Pi e, `pi` em seguida, `raspberry` inicie o sessão na Raspbian usando como nome de utilizador e como palavra-passe.
 
2. Clique no ícone framboesa > **Preferências** > **De Framboesa Pi Configuração**.

   ![O menu Preferências Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. No separador **Interfaces,** coloque **SPI** e **SSH** para **ativar**, e, em seguida, clique **EM OK**. Se não tiver sensores físicos e pretender utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar SPI e SSH em Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para ativar sSH e SPI, pode encontrar mais documentos de referência sobre [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Ligue o sensor a Pi

Utilize os fios de pão e de salta para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor, [ignore esta secção.](#connect-pi-to-the-network)

![A Ligação Raspberry Pi e sensor](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. E o LED piscará se houver uma comunicação entre o dispositivo e a nuvem.

Para os pinos do sensor, utilize as seguintes cablagens:

| Iniciar (Sensor & LED)     | Fim (Placa)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Cabo branco   |
| LED GND (Pin 6G)         | GND (Pin o pino 6)            | Cabo preto   |
| VDD (Pin 18F)            | 3.3V PWR (Pin 17)      | Cabo branco   |
| GND (Pin 20F)            | GND (Pin 20)           | Cabo preto   |
| SCK (Pin 21F)            | SPI0 SCLK (Pino 23)     | Cabo laranja  |
| SDO (Pin 22F)            | SPI0 MISO (Pino 21)     | Cabo amarelo  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin19)     | Cabo verde   |
| CS (Pin o 24F)             | SPI0 CS (Pino 24)       | Cabo azul    |

Clique para ver [Raspberry Pi 2 & mapeamentos de 3 pin os mapeamentos](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para a sua referência.

Depois de ter ligado o BME280 com sucesso ao seu Raspberry Pi, deve ser como abaixo da imagem.

![Pi conectado e BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ligue Pi à rede

Ligue pi utilizando o cabo micro USB e a alimentação elétrica. Utilize o cabo Ethernet para ligar pi à sua rede com fios ou siga as [instruções da Fundação Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/) para ligar Pi à sua rede sem fios. Depois de o seu Pi ter sido ligado com sucesso à rede, precisa de tomar uma nota do [endereço IP do seu Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Ligado à rede com fios](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de amostra em Pi

### <a name="sign-into-your-raspberry-pi"></a>Assine o seu Raspberry Pi

1. Utilize um dos seguintes clientes SSH do seu computador anfitrião para se ligar ao seu Raspberry Pi.
   
   **Utilizadores do Windows**
   1. Descarregue e instale [PuTTY](https://www.putty.org/) para Windows. 
   1. Copie o endereço IP do seu Pi na secção nome do anfitrião (ou endereço IP) e selecione SSH como tipo de ligação.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utilizadores de Mac e Ubuntu**

   Utilize o cliente SSH incorporado em Ubuntu ou macOS. Talvez precise saquear `ssh pi@<ip address of pi>` para ligar Pi via SSH.
   > [!NOTE]
   > O nome de `pi` utilizador predefinido `raspberry`é , e a palavra-passe é .


### <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Clone a aplicação da amostra executando o seguinte comando:

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
   > Se **não tiver um BME280 físico,** pode utilizar '---dados simulados' como parâmetro de linha de comando para simular dados de temperatura&humidade. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Construir e executar a aplicação da amostra

1. Construa a aplicação da amostra executando o seguinte comando:

   ```bash
   cmake . && make
   ```
   
   ![Construir saída](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Executar a aplicação da amostra executando o seguinte comando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Certifique-se de copiar a cadeia de ligação do dispositivo às cotações individuais.
   >

Deverá ver o seguinte resultado, que mostra os dados do sensor e as mensagens que são enviadas ao seu hub IoT.

![Resultado – dados do sensor enviados do Raspberry Pi ao hub IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leia as mensagens recebidas pelo seu centro

Uma forma de monitorizar as mensagens recebidas pelo seu hub IoT do seu dispositivo é utilizar as Ferramentas Azure IoT para código de estúdio visual. Para saber mais, consulte [Use Ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para mais formas de processar os dados enviados pelo seu dispositivo, continue na secção seguinte.

## <a name="next-steps"></a>Passos seguintes

Executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu centro ioT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
