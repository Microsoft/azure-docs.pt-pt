---
title: Upload de ficheiros de dispositivos para Azure IoT Hub com Python Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para Python. Os ficheiros carregados são armazenados num recipiente de armazenamento Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110746"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para fazer o upload de um ficheiro para o [armazenamento de blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça um recipiente de armazenamento com segurança para carregar um ficheiro.

* Use o cliente Python para fazer upload de um ficheiro através do seu hub IoT.

A [telemetria Send de um dispositivo para um quickstart hub IoT](quickstart-send-telemetry-python.md) demonstra a funcionalidade básica de mensagens dispositivo-nuvem do IoT Hub. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo-nuvem que o IoT Hub aceita. Quando precisa de ficheiros de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

> [!NOTE]
> Atualmente, o IoT Hub Python SDK apenas suporta o upload de ficheiros baseados em caracteres, tais como ficheiros **.txt.**

No final deste tutorial executa a aplicação de consola Python:

* **FileUpload.py**, que envia um ficheiro para armazenamento utilizando o SDK do Dispositivo Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Este guia utiliza o SDK V1 Python deprecated, uma vez que a funcionalidade de upload de ficheiros ainda não foi implementada no novo V2 SDK.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, cria a aplicação do dispositivo para fazer o upload de um ficheiro para o hub IoT.

1. Ao seu pedido de comando, execute o seguinte comando para instalar o pacote **azure-iothub-dispositivo-cliente:**

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Utilizando um editor de texto, crie um ficheiro de teste que irá enviar para o armazenamento de blob.

    > [!NOTE]
    > Atualmente, o IoT Hub Python SDK apenas suporta o upload de ficheiros baseados em caracteres, tais como ficheiros **.txt.**

3. Utilizando um editor de texto, crie um ficheiro **FileUpload.py** na sua pasta de trabalho.

4. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **FileUpload.py.** 

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

5. No seu ficheiro, substitua-a `[Device Connection String]` pela cadeia de ligação do seu dispositivo hub IoT. Substitua-o `[Full path to file]` pelo caminho para o ficheiro de teste que criou, ou qualquer ficheiro no seu dispositivo que pretenda carregar. Substitua-o `[File name for storage]` pelo nome que pretende dar ao seu ficheiro depois de ser enviado para o armazenamento de bolhas. 

6. Criar um callback para a função **upload_blob:**

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adicione o seguinte código para ligar o cliente e faça upload do ficheiro. Inclua `main` também a rotina:

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

8. Guarde e feche o ficheiro **UploadFile.py.**

## <a name="run-the-application"></a>Executar a aplicação

Agora está pronto para executar a candidatura.

1. Num pedido de comando na sua pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A seguinte imagem mostra a saída da aplicação **FileUpload:**

    ![Saída da aplicação de dispositivo simulado](./media/iot-hub-python-python-file-upload/1.png)

3. Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento configurado:

    ![Ficheiro carregado](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Criar um hub IoT programática](iot-hub-rm-template-powershell.md)

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)
