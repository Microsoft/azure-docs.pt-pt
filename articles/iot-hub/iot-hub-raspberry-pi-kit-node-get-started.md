---
title: Ligue raspberry Pi ao Azure IoT Hub na nuvem (Node.js)
description: Saiba como configurar e ligar Raspberry Pi ao Azure IoT Hub para Raspberry Pi para enviar dados para a plataforma cloud Azure neste tutorial.
author: wesmc7777
manager: eliotgra
keywords: azure iot framboesa pi, framboesa pi iot hub, framboesa pi enviar dados para a nuvem, framboesa pi para nuvem
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97802529"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Ligue raspberry Pi ao Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, começa-se por aprender o básico de trabalhar com Raspberry Pi que está a gerir Raspbian. Em seguida, aprende a ligar os seus dispositivos perfeitamente à nuvem utilizando o [Azure IoT Hub](about-iot-hub.md). Para as amostras do Núcleo IoT do Windows 10, aceda ao [Windows Dev Center](https://www.windowsondevices.com/).

Ainda não tem um kit? Experimente [o simulador online Raspberry Pi.](iot-hub-raspberry-pi-web-simulator-get-started.md) Ou comprar um kit novo [aqui.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>O que faz

* Criar um hub IoT.

* Registe um dispositivo para Pi no seu hub IoT.

* Instale Raspberry Pi.

* Execute uma aplicação de amostra no Pi para enviar dados de sensores para o seu hub IoT.

## <a name="what-you-learn"></a>O que irá aprender

* Como criar um hub Azure IoT e obter a sua nova cadeia de ligação do dispositivo.

* Como ligar Pi a um sensor BME280.

* Como recolher dados de sensores executando uma aplicação de amostra no Pi.

* Como enviar dados de sensores para o seu hub IoT.

## <a name="what-you-need"></a>O que precisa

![O que precisa](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Uma placa Raspberry Pi 2 ou Raspberry Pi 3.

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um monitor, um teclado USB e um rato que se conecta a Pi.

* Um Mac ou PC que está a executar Windows ou Linux.

* Uma ligação à internet.

* Um cartão microSD de 16 GB ou superior.

* Um adaptador USB-SD ou um cartão microSD para queimar a imagem do sistema operativo no cartão microSD.

* Uma fonte de alimentação de 5 volts de 2 amp com o cabo USB de 1,80m.

Os seguintes itens são opcionais:

* Um sensor de temperatura, pressão e humidade Adafruit BME280 montado.

* Uma tábua de pão.

* 6 fios de ponte F/M.

* Um LED difuso de 10 mm.

> [!NOTE]
> Se não tiver os itens opcionais, pode utilizar dados de sensores simulados.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Configurar Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instale o sistema operativo Raspbian para Pi

Prepare o cartão microSD para a instalação da imagem Raspbian.

1. Descarregue Raspbian.

   a. [Raspbian Buster com ambiente de trabalho](https://www.raspberrypi.org/software/) (o ficheiro .zip).

   b. Extraia a imagem Raspbian para uma pasta no seu computador.

2. Instale Raspbian no cartão microSD.

   a. [Descarregue e instale o utilitário de queimador de cartão Etcher SD](https://etcher.io/).

   b. Corra Etcher e selecione a imagem Raspbian que extraiu no passo 1.

   c. Selecione a unidade do cartão microSD. Etcher pode já ter selecionado a unidade correta.

   d. Clique em Flash para instalar Raspbian no cartão microSD.

   e. Retire o cartão microSD do computador quando a instalação estiver concluída. É seguro remover o cartão microSD diretamente porque o Etcher ejeta automaticamente ou desmonta o cartão microSD após a conclusão.

   f. Insira o cartão microSD em Pi.

### <a name="enable-ssh-and-i2c"></a>Ativar SSH e I2C

1. Ligue Pi ao monitor, teclado e rato.

2. Inicie o Pi e, em seguida, inscreva-se em Raspbian usando `pi` como nome de utilizador e como `raspberry` senha.

3. Clique no ícone de framboesa > **Configuração De**  >  **Framboesa Pi**.

   ![O menu Preferências Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. No separador **Interfaces,** desloque **o I2C** e **o SSH** para **ativar** e, em seguida, clique em **OK**. Se não tem sensores físicos e quer usar dados de sensores simulados, este passo é opcional.

   ![Ativar I2C e SSH em Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Para ativar o SSH e o I2C, pode encontrar mais documentos de referência sobre [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Ligue o sensor a Pi

Utilize a prancha de pão e os fios de ponte para ligar um LED e um BME280 a Pi da seguinte forma. Se não tiver o sensor, [salte esta secção.](#connect-pi-to-the-network)

![A framboesa Pi e a ligação sensorial](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

O sensor BME280 pode recolher dados de temperatura e humidade. O LED pisca quando o dispositivo envia uma mensagem para a nuvem.

Para os pinos dos sensores, utilize os seguintes fios:

| Início (Sensor & LED)     | Fim (Placa)            | Cor do cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3V PWR (Pin 1)       | Cabo branco   |
| GND (Pin 7G)             | GND (Pino 6)            | Cabo marrom   |
| SDI (Pin 10G)            | I2C1 SDA (Pino 3)       | Cabo vermelho     |
| SCK (Pin 8G)             | I2C1 SCL (Pin 5)       | Cabo laranja  |
| LED VDD (pin 18F)        | GPIO 24 (Pino 18)       | Cabo branco   |
| LED GND (Pin 17F)        | GND (Pino 20)           | Cabo preto   |

Clique para ver [Raspberry Pi 2 & mapeamentos de 3 pinos](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) para a sua referência.

Depois de ter ligado o BME280 com sucesso ao seu Raspberry Pi, deve ser como a imagem abaixo.

![Pi conectado e BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ligue Pi à rede

Ligue pi utilizando o cabo MICRO USB e a fonte de alimentação. Utilize o cabo Ethernet para ligar Pi à sua rede com fios ou siga as [instruções da Fundação Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/wireless/) para ligar pi à sua rede sem fios. Depois de o seu Pi ter sido conectado com sucesso à rede, precisa de tomar nota do [endereço IP do seu Pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Ligado à rede com fios](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Certifique-se de que o Pi está ligado à mesma rede que o seu computador. Por exemplo, se o seu computador estiver ligado a uma rede sem fios enquanto o Pi estiver ligado a uma rede com fios, poderá não ver o endereço IP na saída do devdisco.

## <a name="run-a-sample-application-on-pi"></a>Executar uma aplicação de amostra em Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Aplicação de amostra de clone e instalação dos pacotes pré-requisitos

1. Conecte-se ao seu Raspberry Pi com um dos seguintes clientes SSH do seu computador anfitrião:

   **Utilizadores do Windows**

   a. Faça o download e instale [o PuTTY](https://www.putty.org/) para windows.

   b. Copie o endereço IP do seu Pi na secção nome anfitrião (ou endereço IP) e selecione SSH como o tipo de ligação.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Utilizadores Mac e Ubuntu**

   Utilize o cliente SSH incorporado no Ubuntu ou no macOS. Talvez precises de correr `ssh pi@<ip address of pi>` para ligar o Pi via SSH.

   > [!NOTE]
   > O nome de utilizador predefinido é `pi` e a palavra-passe é `raspberry` .

2. Instale Node.js e NPM no seu Pi.

   Verifique primeiro a sua versão Node.js.

   ```bash
   node -v
   ```

   Se a versão for inferior a 10.x, ou se não houver Node.js no seu Pi, instale a versão mais recente.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Clone o pedido de amostra.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Instale todas as embalagens para a amostra. A instalação inclui o dispositivo Azure IoT SDK, a biblioteca de sensores BME280 e a biblioteca Wiring Pi.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Pode levar vários minutos para terminar este processo de instalação dependendo da sua ligação à rede.

### <a name="configure-the-sample-application"></a>Configurar o exemplo de aplicação

1. Abra o ficheiro config executando os seguintes comandos:

   ```bash
   nano config.json
   ```

   ![Arquivo Config](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Há dois itens neste ficheiro que pode configurar. O primeiro é , que define o intervalo de `interval` tempo (em milissegundos) entre as mensagens enviadas para a nuvem. O segundo é `simulatedData` , que é um valor booleano para usar ou não dados de sensores simulados.

   Se **não tiver o sensor,** deslote o `simulatedData` valor para fazer com que a `true` aplicação da amostra crie e utilize dados de sensores simulados.

   *Nota: O endereço i2c utilizado neste tutorial é 0x77 por defeito. Dependendo da sua configuração, também pode ser 0x76: se encontrar um erro i2c, tente alterar o valor para 118 e ver se funciona melhor. Para ver que endereço é usado pelo seu sensor, corra `sudo i2cdetect -y 1` em uma concha na framboesa pi*

2. Salve e saia digitando Control-O > Introduza > Control-X.

### <a name="run-the-sample-application"></a>Executar o exemplo de aplicação

Executar o pedido de amostra executando o seguinte comando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Certifique-se de que copia a cadeia de ligação do dispositivo nas cotações individuais.

Deverá ver o seguinte resultado, que mostra os dados do sensor e as mensagens que são enviadas ao seu hub IoT.

![Resultado – dados do sensor enviados do Raspberry Pi ao hub IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Leia as mensagens recebidas pelo seu hub

Uma forma de monitorizar as mensagens recebidas pelo seu hub IoT do seu dispositivo é utilizar as Ferramentas IoT Azure para Código do Estúdio Visual. Para saber mais, consulte [Use Azure IoT Tools for Visual Studio Code para enviar e receber mensagens entre o seu dispositivo e o IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Para mais formas de processar os dados enviados pelo seu dispositivo, continue para a secção seguinte.

## <a name="next-steps"></a>Passos seguintes

Executou uma aplicação de amostra para recolher dados de sensores e enviá-lo para o seu hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
