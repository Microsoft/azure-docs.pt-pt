---
title: Ligue raspberry Pi ao Hub Azure IoT na nuvem (Node.js)
description: Saiba como configurar e ligar Raspberry Pi ao Azure IoT Hub para Raspberry Pi enviar dados para a plataforma de nuvem Azure neste tutorial.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi enviar dados para a nuvem, framboesa pi para a nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 7c32ae73f065aa5cd1d0dabec421d354684fbb3c
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371511"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Ligue raspberry Pi ao Hub Azure IoT (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começa-se por aprender o básico de trabalhar com Raspberry Pi que está a gerir o Raspbian. Em seguida, aprende a ligar os seus dispositivos à nuvem utilizando o [Azure IoT Hub](about-iot-hub.md). Para amostras do Windows 10 IoT Core, vá ao [Windows Dev Center](https://www.windowsondevices.com/).

Ainda não tem um kit? Experimente [o simulador online Raspberry Pi.](iot-hub-raspberry-pi-web-simulator-get-started.md) Ou comprar um kit novo [aqui.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que faz

* Criar um centro de ioT.

* Registe um dispositivo para pi no seu centro ioT.

* Prepara o Raspberry Pi.

* Faça uma aplicação de amostra em Pi para enviar dados de sensores para o seu centro IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub Azure IoT e obter a sua nova cadeia de ligação ao dispositivo.

* Como ligar pi a um sensor BME280.

* Como recolher dados de sensores executando uma aplicação de amostra em Pi.

* Como enviar dados de sensores para o seu centro ioT.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Uma placa de Framboesa Pi 2 ou Raspberry Pi 3.

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um monitor, um teclado USB e um rato que se conecta ao Pi.

* Um Mac ou PC que está a executar Windows ou Linux.

* Uma ligação à internet.

* Um cartão de 16 GB ou acima do microSD.

* Um adaptador USB-SD ou um cartão microSD para queimar a imagem do sistema operativo no cartão microSD.

* Uma fonte de alimentação de 5 volts de 2 amp com o cabo USB de 1,80m.

Os seguintes itens são opcionais:

* Um sensor de temperatura, pressão e humidade Adafruit BME280 montado.

* Uma tábua de pão.

* 6 fios de salta F/M.

* Um LED de 10 mm difundido.

> [!NOTE]
> Se não tiver os itens opcionais, pode utilizar dados de sensores simulados.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registe um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instale o sistema operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem raspbiana.

1. Baixe raspbian.

   a. [Raspbian Buster com desktop](https://www.raspberrypi.org/downloads/raspbian/) (o ficheiro .zip).

   b. Extraia a imagem raspbiana para uma pasta no seu computador.

2. Instale raspbian no cartão microSD.

   a. [Descarregue e instale o utilitário de queimador de cartão Etcher SD](https://etcher.io/).

   b. Execute etcher e selecione a imagem raspbiana que extraiu no passo 1.

   c. Selecione a unidade de cartão microSD. Etcher pode já ter selecionado a unidade correta.

   d. Clique em Flash para instalar Raspbian no cartão microSD.

   e. Retire o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente porque Etcher ejeta ou desmonta automaticamente o cartão microSD após a conclusão.

   f. Insira o cartão microSD em Pi.

### <a name="enable-ssh-and-i2c"></a>Ativar SSH e I2C

1. Ligue pi ao monitor, teclado e rato.

2. Inicie pi e, em seguida, assine em Raspbian usando `pi` como nome de utilizador e `raspberry` como palavra-passe.

3. Clique no ícone framboesa > **Preferências** > **Configuração Raspberry Pi**.

   ![O menu Preferências Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. No separador **Interfaces,** coloque **I2C** e **SSH** para **ativar**, e, em seguida, clique **em OK**. Se não tiver sensores físicos e pretender utilizar dados de sensores simulados, este passo é opcional.

   ![Ativar I2C e SSH em Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para ativar o SSH e o I2C, pode encontrar mais documentos de referência sobre [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Ligue o sensor a Pi

Utilize os fios de pão e de salta para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor, [ignore esta secção.](#connect-pi-to-the-network)

![A Ligação Raspberry Pi e sensor](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. O LED pisca quando o dispositivo envia uma mensagem para a nuvem.

Para os pinos do sensor, utilize as seguintes cablagens:

| Início (Sensor e LED)     | Fim (Placa)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3V PWR (Pin 1)       | Cabo branco   |
| GND (Pin 7G)             | GND (Pin o pino 6)            | Cabo marrom   |
| SDI (Pin 10G)            | I2C1 SDA (Pino 3)       | Cabo vermelho     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Cabo laranja  |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | Cabo branco   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Cabo preto   |

Clique para ver [mapeamentos de pinos Raspberry Pi 2 e 3](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) para a sua referência.

Depois de ter ligado o BME280 com sucesso ao seu Raspberry Pi, deve ser como abaixo da imagem.

![Pi conectado e BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ligue Pi à rede

Ligue pi utilizando o cabo micro USB e a alimentação elétrica. Utilize o cabo Ethernet para ligar pi à sua rede com fios ou siga as [instruções da Fundação Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) para ligar Pi à sua rede sem fios. Depois de o seu Pi ter sido ligado com sucesso à rede, precisa de tomar uma nota do [endereço IP do seu Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ligado à rede com fios](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Certifique-se de que pi está ligado à mesma rede que o seu computador. Por exemplo, se o seu computador estiver ligado a uma rede sem fios enquanto pi estiver ligado a uma rede com fios, poderá não ver o endereço IP na saída de devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de amostra em Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Aplicação de amostras de clonagem e instalação dos pacotes pré-requisitos

1. Ligue-se ao seu Raspberry Pi com um dos seguintes clientes SSH do seu computador anfitrião:

   **Utilizadores do Windows**

   a. Descarregue e instale [PuTTY](https://www.putty.org/) para Windows.

   b. Copie o endereço IP do seu Pi na secção nome do anfitrião (ou endereço IP) e selecione SSH como tipo de ligação.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utilizadores de Mac e Ubuntu**

   Utilize o cliente SSH incorporado em Ubuntu ou macOS. Talvez precise sê-`ssh pi@<ip address of pi>` para ligar pi via SSH.

   > [!NOTE]
   > O nome de utilizador predefinido é `pi` e a palavra-passe é `raspberry`.

2. Instale o Nó.js e o NPM no seu Pi.

   Primeiro verifique a sua versão Node.js.

   ```bash
   node -v
   ```

   Se a versão for inferior a 10.x, ou se não houver Node.js no seu Pi, instale a versão mais recente.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Clone a aplicação da amostra.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Instale todos os pacotes para a amostra. A instalação inclui dispositivo SDK, biblioteca sensor BME280 e biblioteca Wiring Pi.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Pode levar vários minutos para terminar este processo de instalação dependendo da ligação da sua rede.

### <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Abra o ficheiro config executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Arquivo Config](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Há dois itens neste ficheiro que pode configurar. O primeiro é `interval`, que define o intervalo de tempo (em milissegundos) entre mensagens enviadas para a nuvem. A segunda é `simulatedData`, que é um valor booleano para usar dados de sensores simulados ou não.

   Se **não tiver o sensor,** detete o valor `simulatedData` para `true` para fazer com que a aplicação da amostra crie e utilize dados de sensores simulados.

   *Nota: O endereço i2c utilizado neste tutorial é 0x77 por padrão. Dependendo da sua configuração, também pode ser 0x76: se encontrar um erro i2c, tente alterar o valor para 118 e veja se funciona melhor. Para ver qual o endereço usado pelo seu sensor, corra `sudo i2cdetect -y 1` numa concha na framboesa pi*

2. Poupe e saia digitando Control-O > Enter > Control-X.

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Executar a aplicação da amostra executando o seguinte comando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Certifique-se de copiar a cadeia de ligação do dispositivo às cotações individuais.

Deve ver a seguinte saída que mostra os dados do sensor e as mensagens que são enviadas para o seu hub IoT.

![Saída - dados do sensor enviados de Raspberry Pi para o seu hub IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leia as mensagens recebidas pelo seu centro

Uma forma de monitorizar as mensagens recebidas pelo seu hub IoT do seu dispositivo é utilizar as Ferramentas Azure IoT para código de estúdio visual. Para saber mais, consulte [Use Ferramentas Azure IoT para o Código do Estúdio Visual para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para mais formas de processar os dados enviados pelo seu dispositivo, continue na secção seguinte.

## <a name="next-steps"></a>Passos seguintes

Executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu centro ioT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
