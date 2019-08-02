---
title: Carregar arquivos de dispositivos para o Hub IoT do Azure com Python | Microsoft Docs
description: Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para Python. Os arquivos carregados são armazenados em um contêiner de blob do armazenamento do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: a529933cf4af572deacab1ae3c615ec0a0eca68f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667875"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar os [recursos de upload de arquivo do Hub IOT](iot-hub-devguide-file-upload.md) para carregar um arquivo no [armazenamento de BLOBs do Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça com segurança um contêiner de armazenamento para carregar um arquivo.

* Use o cliente Python para carregar um arquivo por meio do Hub IoT.

O guia de início rápido [Enviar telemetria de um dispositivo para um hub IOT](quickstart-send-telemetry-python.md) demonstra a funcionalidade básica de mensagens do dispositivo para a nuvem do Hub IOT. No entanto, em alguns cenários, não é possível mapear facilmente os dados que seus dispositivos enviam para as mensagens relativamente pequenas que o Hub IoT aceita. Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

> [!NOTE]
> O SDK do Python do Hub IoT atualmente dá suporte apenas ao carregamento de arquivos baseados em caracteres, como arquivos **. txt** .

Ao final deste tutorial, você executará o aplicativo de console do Python:

* **FileUpload.py**, que carrega um arquivo no armazenamento usando o SDK do dispositivo Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Veja a seguir as instruções de instalação para os pré-requisitos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você cria o aplicativo do dispositivo para carregar um arquivo no Hub IoT.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **Azure-iothub-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Usando um editor de texto, crie um arquivo de teste que será carregado no armazenamento de BLOBs.

    > [!NOTE]
    > O SDK do Python do Hub IoT atualmente dá suporte apenas ao carregamento de arquivos baseados em caracteres, como arquivos **. txt** .

3. Usando um editor de texto, crie um arquivo **FileUpload.py** em sua pasta de trabalho.

4. Adicione as seguintes `import` instruções e variáveis ao início do arquivo **FileUpload.py** . 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Em seu arquivo, substitua `[Device Connection String]` pela cadeia de conexão do dispositivo do Hub IOT. Substitua `[Full path to file]` pelo caminho para o arquivo de teste que você criou ou qualquer arquivo no dispositivo que você deseja carregar. Substitua `[File name for storage]` pelo nome que você deseja dar ao seu arquivo depois que ele for carregado no armazenamento de BLOBs. 

6. Crie um retorno de chamada para a função **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adicione o código a seguir para conectar o cliente e carregar o arquivo. Inclua também a `main` rotina:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Salve e feche o arquivo **UploadFile.py** .

## <a name="run-the-application"></a>Executar a aplicação

Agora você está pronto para executar o aplicativo.

1. Em um prompt de comando em sua pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A captura de tela a seguir mostra a saída do aplicativo FileUpload:

    ![Saída do aplicativo Simulated-Device](./media/iot-hub-python-python-file-upload/1.png)

3. Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento que você configurou:

    ![Arquivo carregado](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um hub IoT programaticamente](iot-hub-rm-template-powershell.md)

* [Introdução ao SDK do C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
