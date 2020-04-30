---
title: Upload de ficheiros de dispositivos para Azure IoT Hub com Nó [ Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para Node.js. Os ficheiros carregados são armazenados num recipiente de armazenamento Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732256"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código no envio de [mensagens cloud-to-device com tutorial IoT Hub](iot-hub-node-node-c2d.md) para lhe mostrar como usar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para fazer o upload de um ficheiro para o armazenamento de [blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça de forma segura um dispositivo com um blob URI Azure para o upload de um ficheiro.

* Utilize as notificações de upload de ficheiros IoT Hub para desencadear o processamento do ficheiro na parte de trás da sua aplicação.

A [telemetria Send de um dispositivo para um quickstart hub IoT](quickstart-send-telemetry-node.md) demonstra a funcionalidade básica de mensagens dispositivo-nuvem do IoT Hub. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo-nuvem que o IoT Hub aceita. Por exemplo:

* Ficheiros grandes que contêm imagens
* Vídeos
* Dados de vibração recolhidos em alta frequência
* Alguma forma de dados pré-processados.

Estes ficheiros são normalmente processados na nuvem utilizando ferramentas como a [Azure Data Factory](../data-factory/introduction.md) ou a stack [Hadoop.](../hdinsight/index.yml) Quando precisa de ficheiros de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola Node.js:

* **SimuladoDevice.js**, que envia um ficheiro para armazenamento utilizando um SAS URI fornecido pelo seu hub IoT.

* **ReadFileUploadNotification.js**, que recebe notificações de upload de ficheiros do seu hub IoT.

> [!NOTE]
> O IoT Hub suporta muitas plataformas e idiomas de dispositivos (incluindo C, .NET, Javascript, Python e Java) através de SDKs do dispositivo Azure IoT. Consulte o Centro de Desenvolvimento [Azure IoT] para obter instruções passo a passo sobre como ligar o seu dispositivo ao Hub Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou mais tarde. [Prepare o seu ambiente](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) de desenvolvimento descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, cria a aplicação do dispositivo para fazer o upload de um ficheiro para o hub IoT.

1. Crie uma pasta vazia com o nome ```simulateddevice```.  Na pasta ```simulateddevice```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Na linha de comandos da pasta ```simulateddevice```, execute o comando seguinte para instalar o pacote SDK do Serviço **azure-iot-device** e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Com um editor de texto, crie um ficheiro **SimulatedDevice.js** na pasta ```simulateddevice```.

4. Adicione as seguintes instruções ```require``` no início do ficheiro **SimulatedDevice.js**:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Adicione uma variável `deviceconnectionstring` e utilize-a para criar uma instância **Cliente**.  Substitua `{deviceconnectionstring}` pelo nome do dispositivo que criou na secção *Criar um Hub IoT:*

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de ligação está incluída no código: esta não é uma prática recomendada e dependendo do seu caso de uso e arquitetura, você pode considerar formas mais seguras de armazenar este segredo.

6. Adicione o seguinte código para ligar o cliente:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Crie uma chamada e utilize a função **uploadToBlob** para fazer o upload do ficheiro.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Guarde e feche o ficheiro **SimulatedDevice.js**.

9. Copie um ficheiro `simulateddevice` de imagem `myimage.png`para a pasta e mude o nome.

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de ligação do hub IoT

Neste artigo cria um serviço de backend para receber mensagens de notificação de envio de ficheiros do hub IoT que criou no [Envio de telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Para receber mensagens de notificação de upload de ficheiros, o seu serviço necessita da permissão de ligação do **serviço.** Por padrão, cada Hub IoT é criado com uma política de acesso partilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receba uma notificação de upload de ficheiro

Nesta secção, cria uma aplicação de consola Node.js que recebe mensagens de notificação de upload de ficheiros do IoT Hub.

Pode utilizar a cadeia de ligação **iothubowner** do seu Hub IoT para completar esta secção. Encontrará a cadeia de ligação no [portal Azure](https://portal.azure.com/) na lâmina da política de **acesso partilhado.**

1. Crie uma pasta vazia com o nome ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. Ao seu pedido ```fileuploadnotification``` de comando na pasta, execute o seguinte comando para instalar o pacote SDK **azure-iothub:**

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um `fileuploadnotification` ficheiro **FileUploadNotification.js** na pasta.

4. Adicione as `require` seguintes declarações no início do ficheiro **FileUploadNotification.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicione uma variável `iothubconnectionstring` e utilize-a para criar uma instância **Cliente**.  Substitua `{iothubconnectionstring}` o valor do espaço reservado pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de ligação do [hub IoT:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de ligação está incluída no código: esta não é uma prática recomendada e dependendo do seu caso de uso e arquitetura, você pode considerar formas mais seguras de armazenar este segredo.

6. Adicione o seguinte código para ligar o cliente:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Abra o cliente e utilize a função **getFileNotificationReceiver** para receber atualizações de estado.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Guarde e feche o ficheiro **FileUploadNotification.js.**

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Num pedido de `fileuploadnotification` comando na pasta, execute o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Num pedido de `simulateddevice` comando na pasta, execute o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

A seguinte imagem mostra a saída da aplicação **SimuladDevice:**

![Saída da aplicação de dispositivo simulado](./media/iot-hub-node-node-file-upload/simulated-device.png)

A seguinte imagem mostra a saída da aplicação **FileUploadNotification:**

![Saída da aplicação de notificação de leitura-upload de ficheiros](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento configurado:

![Ficheiro carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Criar um hub IoT programática](iot-hub-rm-template-powershell.md)

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
