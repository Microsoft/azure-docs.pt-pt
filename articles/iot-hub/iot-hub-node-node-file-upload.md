---
title: Carregar arquivos de dispositivos para o Hub IoT do Azure com o nó | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para node. js. Os arquivos carregados são armazenados em um contêiner de blob do armazenamento do Azure.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 8747111921df494b8d5618dc8d6ece99fa821e47
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147633"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT (Node. js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial se baseia no código do tutorial [enviar mensagens da nuvem para o dispositivo com o Hub IOT](iot-hub-node-node-c2d.md) para mostrar como usar os [recursos de carregamento de arquivo do Hub IOT](iot-hub-devguide-file-upload.md) para carregar um arquivo no [armazenamento de BLOBs do Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça com segurança um dispositivo com um URI de blob do Azure para carregar um arquivo.

* Use as notificações de upload de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md) demonstra a funcionalidade básica de mensagens do dispositivo para a nuvem do Hub IOT. No entanto, em alguns cenários, não é possível mapear facilmente os dados que seus dispositivos enviam para as mensagens relativamente pequenas que o Hub IoT aceita. Por exemplo:

* Arquivos grandes que contêm imagens
* Os meus vídeos
* Dados de vibração amostrados em alta frequência
* Alguma forma de dados previamente processados.

Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como [Azure data Factory](../data-factory/introduction.md) ou a pilha do [Hadoop](../hdinsight/index.yml) . Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

No final deste tutorial, você executará dois aplicativos de console do node. js:

* **SimulatedDevice. js**, que carrega um arquivo no armazenamento usando um URI de SAS fornecido pelo Hub IOT.

* **ReadFileUploadNotification. js**, que recebe notificações de upload de arquivo do Hub IOT.

> [!NOTE]
> O Hub IoT dá suporte a várias plataformas de dispositivo e linguagens (incluindo C, .NET, JavaScript, Python e Java) por meio de SDKs do dispositivo IoT do Azure. Consulte o [Centro de desenvolvedores do Azure IoT] para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IoT do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Node. js versão 10.0. x ou posterior. [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node. js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você cria o aplicativo do dispositivo para carregar um arquivo no Hub IoT.

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

5. Adicione uma variável `deviceconnectionstring` e utilize-a para criar uma instância **Cliente**.  Substitua `{deviceconnectionstring}` pelo nome do dispositivo que você criou na seção *criar um hub IOT* :

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Para simplificar, a cadeia de conexão é incluída no código: essa não é uma prática recomendada e, dependendo do seu caso de uso e da arquitetura, talvez você queira considerar maneiras mais seguras de armazenar esse segredo.

6. Adicione o seguinte código para conectar o cliente:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Crie um retorno de chamada e use a função **uploadToBlob** para carregar o arquivo.

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

9. Copie um arquivo de imagem para `simulateddevice` a pasta e renomeie- `myimage.png`o.

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

Neste artigo, você cria um serviço de back-end para receber mensagens de notificação de upload de arquivo do Hub IoT criado em [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-node.md). Para receber mensagens de notificação de upload de arquivo, seu serviço precisa da permissão de **conexão de serviço** . Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo

Nesta seção, você cria um aplicativo de console do node. js que recebe mensagens de notificação de upload de arquivo do Hub IoT.

Você pode usar a cadeia de conexão **iothubowner** do Hub IOT para concluir esta seção. Você encontrará a cadeia de conexão no [portal do Azure](https://portal.azure.com/) na folha **política de acesso compartilhado** .

1. Crie uma pasta vazia com o nome ```fileuploadnotification```.  Na pasta ```fileuploadnotification```, crie um ficheiro package.json com o comando seguinte na sua linha de comandos.  Aceite todas as predefinições:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na ```fileuploadnotification``` pasta, execute o seguinte comando para instalar o pacote **do SDK do Azure-iothub** :

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **arquivo fileuploadnotification. js** na `fileuploadnotification` pasta.

4. Adicione as seguintes `require` instruções no início do arquivo **arquivo fileuploadnotification. js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicione uma variável `iothubconnectionstring` e utilize-a para criar uma instância **Cliente**.  Substitua o `{iothubconnectionstring}` valor do espaço reservado pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Para simplificar, a cadeia de conexão é incluída no código: essa não é uma prática recomendada e, dependendo do seu caso de uso e da arquitetura, talvez você queira considerar maneiras mais seguras de armazenar esse segredo.

6. Adicione o seguinte código para conectar o cliente:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Abra o cliente e use a função **getFileNotificationReceiver** para receber atualizações de status.

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

8. Salve e feche o arquivo **arquivo fileuploadnotification. js** .

## <a name="run-the-applications"></a>Executar as aplicações

Agora pode executar as aplicações.

Em um prompt de comando na `fileuploadnotification` pasta, execute o seguinte comando:

```cmd/sh
node FileUploadNotification.js
```

Em um prompt de comando na `simulateddevice` pasta, execute o seguinte comando:

```cmd/sh
node SimulatedDevice.js
```

A captura de tela a seguir mostra a saída do aplicativo **SimulatedDevice** :

![Saída do aplicativo Simulated-Device](./media/iot-hub-node-node-file-upload/simulated-device.png)

A captura de tela a seguir mostra a saída do aplicativo **arquivo fileuploadnotification** :

![Saída do aplicativo Read-file-upload-Notification](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento que você configurou:

![Arquivo carregado](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um hub IoT programaticamente](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK do C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
