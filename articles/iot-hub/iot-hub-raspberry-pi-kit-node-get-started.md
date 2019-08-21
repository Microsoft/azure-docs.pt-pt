---
title: Raspberry Pi para nuvem (Node. js) – conectar o Raspberry Pi ao Hub IoT do Azure | Microsoft Docs
description: Saiba como configurar e conectar o Raspberry Pi ao Hub IoT do Azure para o Raspberry Pi para enviar dados para a plataforma de nuvem do Azure neste tutorial.
author: wesmc7777
manager: philmea
keywords: Azure IOT Raspberry Pi, Hub IOT do Raspberry Pi, Raspberry Pi enviar dados para a nuvem, Raspberry Pi para nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: wesmc
ms.openlocfilehash: e7346fa0f9cc977755c441077a50707dd207019f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638284"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Conectar o Raspberry Pi ao Hub IoT do Azure (Node. js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com o Raspberry Pi que está executando o Raspbian. Em seguida, você aprende a conectar seus dispositivos diretamente à nuvem usando o [Hub IOT do Azure](about-iot-hub.md). Para exemplos do Windows 10 IoT Core, vá para o [centro de desenvolvimento do Windows](https://www.windowsondevices.com/).

Ainda não tem um kit? Experimente o [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou compre um novo kit [aqui](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>O que você faz

* Crie um hub IoT.

* Registrar um dispositivo para PI em seu hub IoT.

* Configurar o Raspberry Pi.

* Execute um aplicativo de exemplo no PI para enviar dados de sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub IoT do Azure e obter a nova cadeia de conexão do dispositivo.

* Como conectar o PI a um sensor BME280.

* Como coletar dados de sensor executando um aplicativo de exemplo no PI.

* Como enviar dados de sensor para o Hub IoT.

## <a name="what-you-need"></a>Do que precisa

![Do que precisa](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Uma placa Raspberry Pi 2 ou Raspberry Pi 3.

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um monitor, um teclado USB e um mouse que se conectam ao PI.

* Um Mac ou PC que esteja executando Windows ou Linux.

* Uma conexão com a Internet.

* Um cartão microSD de 16 GB ou superior.

* Um adaptador USB-SD ou placa microSD para gravar a imagem do sistema operacional no cartão microSD.

* Uma fonte de alimentação de 5 volts com o cabo micro USB de 6 pés.

Os seguintes itens são opcionais:

* Um sensor de temperatura, pressão e umidade Adafruit BME280.

* Um placa universal.

* 6 cabos de jumper F/M.

* Um LED de 10 mm difuso.

> [!NOTE]
> Se você não tiver os itens opcionais, poderá usar os dados simulados do sensor.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no Hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar o Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema operacional Raspbian para PI

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Baixe o Raspbian.

   a. [Raspbian Buster com desktop](https://www.raspberrypi.org/downloads/raspbian/) (o arquivo. zip).

   b. Extraia a imagem Raspbian para uma pasta no seu computador.

2. Instale o Raspbian no cartão microSD.

   a. [Baixe e instale o utilitário gravador de cartão SD do entalhe](https://etcher.io/).

   b. Execute o entalhe e selecione a imagem Raspbian que você extraiu na etapa 1.

   c. Selecione a unidade do cartão microSD. O gravador pode já ter selecionado a unidade correta.

   d. Clique em Flash para instalar o Raspbian no cartão microSD.

   e. Remova o cartão microSD do computador quando a instalação for concluída. É seguro remover o cartão microSD diretamente porque o Entalheer ejeta ou desmonta automaticamente o cartão microSD após a conclusão.

   f. Insira o cartão microSD no PI.

### <a name="enable-ssh-and-i2c"></a>Habilitar SSH e I2C

1. Conecte o PI ao monitor, ao teclado e ao mouse.

2. Inicie o PI e entre no Raspbian usando `pi` como o nome de usuário e `raspberry` como a senha.

3. Clique no ícone de Raspberry > **preferências** > **Raspberry configuração de PI**.

   ![O menu de preferências do Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Na guia **interfaces** , defina **I2C** e **SSH** como **habilitar**e clique em **OK**. Se você não tiver sensores físicos e quiser usar dados simulados de sensor, essa etapa será opcional.

   ![Habilitar I2C e SSH no Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para habilitar o SSH e o I2C, você pode encontrar mais documentos de referência em [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Conectar o sensor ao PI

Use os cabos placa universal e jumper para conectar um LED e um BME280 ao PI da seguinte maneira. Se você não tiver o sensor, [ignore esta seção](#connect-pi-to-the-network).

![O Raspberry Pi e a conexão do sensor](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode coletar dados de temperatura e umidade. O LED pisca quando o dispositivo envia uma mensagem para a nuvem. 

Para Pins de sensor, use a seguinte fiação:

| Iniciar (LED de & do sensor)     | Fim (quadro)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN 5G)             | 3.3 v PWR (pino 1)       | Cabo branco   |
| GND (PIN 7G)             | GND (pino 6)            | Cabo marrom   |
| SDI (PIN 10G)            | I2C1 SDA (pino 3)       | Cabo vermelho     |
| SCK (pino 8G)             | SCL I2C1 (pino 5)       | Cabo laranja  |
| LED VDD (PIN 18F)        | GPIO 24 (pino 18)       | Cabo branco   |
| LED GND (pino 17F)        | GND (pino 20)           | Cabo preto   |

Clique para exibir [mapeamentos de PIN do Raspberry Pi 2 & 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para sua referência.

Depois de conectar o BME280 ao seu Raspberry Pi com êxito, deve ser como a imagem abaixo.

![PI e BME280 conectados](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Conectar o PI à rede

Ative o PI usando o cabo micro USB e a fonte de alimentação. Use o cabo Ethernet para conectar o PI à sua rede com fio ou siga as [instruções do Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) para conectar o PI à sua rede sem fio. Depois que o PI tiver sido conectado com êxito à rede, você precisará anotar o [endereço IP do seu PI](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Conectado à rede com fio](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Verifique se o PI está conectado à mesma rede que o seu computador. Por exemplo, se o computador estiver conectado a uma rede sem fio enquanto o PI estiver conectado a uma rede com fio, talvez você não veja o endereço IP na saída devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar um aplicativo de exemplo no PI

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Clonar aplicativo de exemplo e instalar os pacotes de pré-requisito

1. Conecte-se ao seu Raspberry Pi com um dos seguintes clientes SSH do seu computador host:

   **Usuários do Windows**
  
   a. Baixe e instale [](https://www.putty.org/) o inacabamento para o Windows. 

   b. Copie o endereço IP do PI na seção nome do host (ou endereço IP) e selecione SSH como o tipo de conexão.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Usuários do Mac e do Ubuntu**

   Use o cliente SSH interno no Ubuntu ou no macOS. Talvez seja necessário executar `ssh pi@<ip address of pi>` o para conectar o PI via SSH.

   > [!NOTE]
   > O nome de usuário `pi` padrão é e a `raspberry`senha é.

2. Instale o Node. js e o NPM em seu PI.

   Primeiro verifique a versão do node. js.

   ```bash
   node -v
   ```

   Se a versão for inferior a 11. x ou se não houver node. js em seu PI, instale a versão mais recente.

   ```bash
   curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Clone o aplicativo de exemplo.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. Instale todos os pacotes para o exemplo. A instalação inclui o SDK do dispositivo IoT do Azure, a biblioteca de sensor BME280 e a biblioteca PI de fiação.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```

   > [!NOTE]
   >Pode levar vários minutos para concluir esse processo de instalação, dependendo de sua conexão de rede.

### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Abra o arquivo de configuração executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Ficheiro de configuração](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Há dois itens neste arquivo que você pode configurar. O primeiro é `interval`, que define o intervalo de tempo (em milissegundos) entre as mensagens enviadas para a nuvem. O segundo é `simulatedData`, que é um valor booliano para se usar ou não dados de sensor simulados.

   Se você **não tiver o sensor**, defina o `simulatedData` valor como `true` para fazer com que o aplicativo de exemplo crie e use dados simulados de sensor.

2. Salve e saia digitando Control-O > Insira > Control-X.

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Execute o aplicativo de exemplo executando o seguinte comando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Certifique-se de copiar e colar a cadeia de conexão do dispositivo nas aspas simples.

Você deve ver a saída a seguir que mostra os dados do sensor e as mensagens que são enviadas para o Hub IoT.

![Saída-dados de sensor enviados do Raspberry Pi para o Hub IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Ler as mensagens recebidas pelo seu hub

Uma maneira de monitorar as mensagens recebidas pelo Hub IoT do seu dispositivo é usar as ferramentas de IoT do Azure para Visual Studio Code. Para saber mais, confira [usar as ferramentas de IOT do Azure para Visual Studio Code para enviar e receber mensagens entre o dispositivo e o Hub IOT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para obter mais maneiras de processar dados enviados pelo seu dispositivo, continue na próxima seção.

## <a name="next-steps"></a>Passos Seguintes

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los para o Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
