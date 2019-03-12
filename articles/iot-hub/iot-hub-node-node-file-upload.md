---
title: Carregar ficheiros a partir de dispositivos no Hub de IoT do Azure com o nó | Documentos da Microsoft
description: Como carregar ficheiros a partir de um dispositivo para a cloud com o Azure IoT device SDK para node. js. Os ficheiros carregados são armazenados num contentor de BLOBs de armazenamento do Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: f110fe84ab09e930947411a79c950af21cc5334c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544525"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial baseia-se sobre o código no [enviar mensagens da Cloud para o dispositivo com o IoT Hub](iot-hub-node-node-c2d.md) tutorial para mostrar a como usar o [ficheiro capacidades de carregamento do IoT Hub](iot-hub-devguide-file-upload.md) para carregar um ficheiro para [BLOBs do Azure armazenamento](../storage/index.yml). Este tutorial mostra-lhe como:

- Fornecer com segurança a um dispositivo do Azure URI de blob para carregar um ficheiro.
- Utilize as notificações de carregamento de arquivo do IoT Hub para acionar a processar o ficheiro no seu back-end de aplicação.

O [introdução ao IoT Hub](quickstart-send-telemetry-node.md) tutorial demonstra a funcionalidade de mensagens dispositivo-para-cloud básica do IoT Hub. No entanto, em alguns cenários não pode facilmente mapear os dados de que seus dispositivos enviam nas mensagens dispositivo-para-cloud relativamente pequenas que aceita o IoT Hub. Por exemplo:

* Ficheiros grandes que contêm imagens
* Vídeos
* Dados de vibração amostrados em alta frequência
* Alguma forma de pré-processados dados.

Estes ficheiros são normalmente processada na cloud com ferramentas, como de lote [do Azure Data Factory](../data-factory/introduction.md) ou o [Hadoop](../hdinsight/index.yml) pilha. Quando precisar de upland arquivos de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

No final deste tutorial executa duas aplicações de consola node. js:

* **Simulateddevice. js**, que carrega um ficheiro para o armazenamento com um URI de SAS fornecido pelo IoT hub.
* **ReadFileUploadNotification.js**, que recebe notificações de carregamento de ficheiros do seu hub IoT.

> [!NOTE]
> IoT Hub suporta muitas plataformas de dispositivos e linguagens (incluindo C, .NET, Javascript, Python e Java) através de SDKs de dispositivo do IoT do Azure. Consulte a [Centro de Programadores do Azure IoT] para obter instruções passo a passo sobre como ligar o dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* Versão 4.0.x ou posterior do Node.js.
* Uma conta ativa do Azure. (Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, vai criar a aplicação de dispositivo para carregar um ficheiro para o hub IoT.

1. Crie uma pasta vazia com o nome ```simulateddevice```.  Na pasta ```simulateddevice```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

1. Na linha de comandos da pasta ```simulateddevice```, execute o comando seguinte para instalar o pacote SDK do Serviço **azure-iot-device** e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Com um editor de texto, crie um ficheiro **SimulatedDevice.js** na pasta ```simulateddevice```.

1. Adicione as seguintes instruções ```require``` no início do ficheiro **SimulatedDevice.js**:

    ```javascript
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Adicione uma variável ```deviceconnectionstring``` e utilize-a para criar uma instância **Cliente**.  Substitua ```{deviceconnectionstring}``` com o nome do dispositivo que criou o _criar um IoT Hub_ secção:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Para simplificar a cadeia de ligação está incluída no código: não é uma prática recomendada e consoante os casos de utilização e a arquitetura talvez queira considerar maneiras mais seguras de armazenar este segredo.

1. Adicione o seguinte código para ligar o cliente:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Crie uma chamada de retorno e utilize o **uploadToBlob** função para carregar o ficheiro.

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

1. Guarde e feche o ficheiro **SimulatedDevice.js**.

1. Copiar um ficheiro de imagem para o `simulateddevice` pasta e mude o nome `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de carregamento do ficheiro

Nesta secção, vai criar uma aplicação de consola node. js que recebe mensagens de notificação de carregamento de arquivo do IoT Hub.

Pode utilizar o **iothubowner** cadeia de ligação do IoT Hub para concluir esta secção. Encontrará a cadeia de ligação no [portal do Azure](https://portal.azure.com/) sobre o **política de acesso partilhado** painel.

1. Crie uma pasta vazia com o nome ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

1. Sua linha de comandos do ```fileuploadnotification``` pasta, execute o seguinte comando para instalar o **azure-iothub** pacote do SDK:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Com um editor de texto, crie uma **FileUploadNotification.js** de ficheiros a ```fileuploadnotification``` pasta.

1. Adicione as seguintes ```require``` instruções no início do **FileUploadNotification.js** ficheiro:

    ```javascript
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Adicione uma variável ```iothubconnectionstring``` e utilize-a para criar uma instância **Cliente**.  Substitua ```{iothubconnectionstring}``` com a cadeia de ligação para o hub IoT que criou na _criar um IoT Hub_ secção:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Para simplificar a cadeia de ligação está incluída no código: não é uma prática recomendada e consoante os casos de utilização e a arquitetura talvez queira considerar maneiras mais seguras de armazenar este segredo.

1. Adicione o seguinte código para ligar o cliente:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Abra o cliente e utilizar o **getFileNotificationReceiver** função para receber atualizações de estado.

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

1. Guarde e feche o **FileUploadNotification.js** ficheiro.

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Uma linha de comandos a `fileuploadnotification` pasta, execute o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Uma linha de comandos a `simulateddevice` pasta, execute o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

Captura de ecrã seguinte mostra a saída do **SimulatedDevice** aplicação:

![Saída da aplicação de dispositivo simulado](./media/iot-hub-node-node-file-upload/simulated-device.png)

Captura de ecrã seguinte mostra a saída do **FileUploadNotification** aplicação:

![Saída da aplicação de carregamento-notificação de leitura-ficheiro](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Pode utilizar o portal para ver o ficheiro carregado no contentor de armazenamento que configurou:

![Ficheiro carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar as capacidades de carregamento de arquivo do IoT Hub para simplificar os carregamentos de ficheiros a partir de dispositivos. Pode continuar a explorar as funcionalidades do hub IoT e cenários com os seguintes artigos:

* [Criar um hub IoT através de programação][lnk-create-hub]
* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

<!-- Links -->
[Centro de Programadores do Azure IoT]: https://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
