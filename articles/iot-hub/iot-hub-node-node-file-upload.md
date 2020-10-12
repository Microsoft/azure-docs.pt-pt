---
title: Faça upload de ficheiros de dispositivos para Azure IoT Hub com Nó Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para Node.js. Os ficheiros carregados são armazenados num recipiente de bolhas de armazenamento Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: db4f78e14696c421adaedd16b0b3f8d598f12846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251903"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se no código no [enviar mensagens nuvem-para-dispositivo com tutorial IoT Hub](iot-hub-node-node-c2d.md) para lhe mostrar como utilizar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para enviar um ficheiro para o armazenamento de [blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça de forma segura um dispositivo com um URI blob Azure para o upload de um ficheiro.

* Utilize as notificações de upload de ficheiros IoT Hub para desencadear o processamento do ficheiro na parte de trás da sua aplicação.

O [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) quickstart demonstra a funcionalidade básica de mensagens dispositivo-a-nuvem do IoT Hub. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo para nuvem que o IoT Hub aceita. Por exemplo:

* Grandes ficheiros que contêm imagens
* Vídeos
* Dados de vibração recolhidos em alta frequência
* Alguma forma de dados pré-processados.

Estes ficheiros são normalmente processados na nuvem utilizando ferramentas como [a Azure Data Factory](../data-factory/introduction.md) ou a pilha [Hadoop.](../hdinsight/index.yml) Quando precisa de criar ficheiros a partir de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial, executam duas aplicações Node.js para consolas:

* **SimulatedDevice.js**, que envia um ficheiro para armazenamento usando um SAS URI fornecido pelo seu hub IoT.

* **ReadFileUploadNotification.js**, que recebe notificações de upload de ficheiros do seu hub IoT.

> [!NOTE]
> O IoT Hub suporta muitas plataformas e idiomas de dispositivos (incluindo C, .NET, Javascript, Python e Java) através de SDKs de dispositivoS Azure IoT. Consulte o [Azure IoT Developer Center] para obter instruções passo a passo sobre como ligar o seu dispositivo ao Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Prepare o seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar Node.js para este tutorial no Windows ou linux.

* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, cria-se a aplicação do dispositivo para fazer o upload de um ficheiro para o hub IoT.

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

5. Adicione uma variável `deviceconnectionstring` e utilize-a para criar uma instância **Cliente**.  `{deviceconnectionstring}`Substitua-o pelo nome do dispositivo que criou na secção *Criar um Hub IoT:*

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de ligação está incluída no código: esta não é uma prática recomendada e, dependendo da sua caixa de utilização e arquitetura, poderá considerar formas mais seguras de armazenar este segredo.

6. Adicione o seguinte código para ligar o cliente:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Crie uma chamada e use a função **uploadToBlob** para carregar o ficheiro.

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

9. Copie um ficheiro de imagem para a `simulateddevice` pasta e rebatize-o `myimage.png` .

## <a name="get-the-iot-hub-connection-string"></a>Obtenha a cadeia de conexão do hub IoT

Neste artigo cria-se um serviço de backend para receber mensagens de notificação de upload de ficheiros do hub IoT que criou em [Enviar por email o artigo Telemetria para um hub IoT](quickstart-send-telemetry-node.md). Para receber mensagens de notificação de upload de ficheiros, o seu serviço necessita da permissão **de ligação do serviço.** Por padrão, cada IoT Hub é criado com uma política de acesso compartilhado chamada **serviço** que concede esta permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de ficheiros

Nesta secção, cria-se uma aplicação de consola Node.js que recebe mensagens de notificação de upload de ficheiros do IoT Hub.

Pode utilizar a cadeia de ligação **iothubowner** do seu Hub IoT para completar esta secção. Encontrará a cadeia de ligação no [portal Azure](https://portal.azure.com/) na lâmina de **política de acesso partilhado.**

1. Crie uma pasta vazia com o nome ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. No seu comando, na ```fileuploadnotification``` pasta, executar o seguinte comando para instalar o pacote **Azure-iothub** SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um ficheiro **FileUploadNotification.js** na `fileuploadnotification` pasta.

4. Adicione as `require` seguintes declarações no início do ficheiro **FileUploadNotification.js:**

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicione uma variável `iothubconnectionstring` e utilize-a para criar uma instância **Cliente**.  Substitua o `{iothubconnectionstring}` valor do espaço reservado pela cadeia de ligação do hub IoT que copiou anteriormente na cadeia de [ligação do hub IoT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Por uma questão de simplicidade, a cadeia de ligação está incluída no código: esta não é uma prática recomendada e, dependendo da sua caixa de utilização e arquitetura, poderá considerar formas mais seguras de armazenar este segredo.

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

Num pedido de comando na `fileuploadnotification` pasta, executar o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Num pedido de comando na `simulateddevice` pasta, executar o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

A imagem que se segue mostra a saída da aplicação **SimulatedDevice:**

![Saída a partir de aplicação de dispositivo simulado](./media/iot-hub-node-node-file-upload/simulated-device.png)

A imagem que se segue mostra a saída da app **FileUploadNotification:**

![Saída da aplicação de read-file-upload-notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento que configura:

![Ficheiro carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Crie um hub IoT programáticamente](iot-hub-rm-template-powershell.md)

* [Introdução à C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
