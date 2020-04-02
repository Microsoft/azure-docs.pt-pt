---
title: Upload de ficheiros de dispositivos para Azure IoT Hub com Python Microsoft Docs
description: Como fazer o upload de ficheiros de um dispositivo para a nuvem utilizando o dispositivo Azure IoT SDK para Python. Os ficheiros carregados são armazenados num recipiente de armazenamento Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.openlocfilehash: 706e1920c6c4fe39e885fd3f5a631070545509ee
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529245"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para fazer o upload de um ficheiro para o [armazenamento de blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça um recipiente de armazenamento com segurança para carregar um ficheiro.

* Use o cliente Python para fazer upload de um ficheiro através do seu hub IoT.

A [telemetria Send de um dispositivo para um quickstart hub IoT](quickstart-send-telemetry-python.md) demonstra a funcionalidade básica de mensagens dispositivo-nuvem do IoT Hub. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo-nuvem que o IoT Hub aceita. Quando precisa de ficheiros de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial, executa a aplicação de consola Python:

* **FileUpload.py**, que envia um ficheiro para armazenamento utilizando o SDK do Dispositivo Python.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode estar bloqueado em alguns ambientes de rede corporativa e educativa. Para obter mais informações e formas de resolver este problema, consulte [A Ligação ao IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, cria a aplicação do dispositivo para fazer o upload de um ficheiro para o hub IoT.

1. No seu pedido de comando, execute o seguinte comando para instalar o pacote de **dispositivos azure-iot.** Usa este pacote para coordenar o upload do ficheiro com o teu hub IoT.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. No seu pedido de comando, execute o seguinte comando para instalar o pacote [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Usa este pacote para efetuar o upload do ficheiro.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Crie um ficheiro de teste que irá enviar para o armazenamento de blob.

1. Utilizando um editor de texto, crie um ficheiro **FileUpload.py** na sua pasta de trabalho.

1. Adicione as `import` seguintes declarações e variáveis no início do ficheiro **FileUpload.py.**

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. No seu ficheiro, substitua-a `[Device Connection String]` pela cadeia de ligação do seu dispositivo hub IoT. Substitua-o `[Full path to local file]` pelo caminho para o ficheiro de teste que criou ou qualquer ficheiro no seu dispositivo que pretenda carregar.

1. Criar uma função para carregar o ficheiro para o armazenamento de bolhas:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Esta função analisa a *estrutura blob_info* que lhe passou para criar um URL que utiliza para inicializar um [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Em seguida, envia o seu ficheiro para o armazenamento de blob Azure usando este cliente.

1. Adicione o seguinte código para ligar o cliente e fazer upload do ficheiro:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Este código cria um **IoTHubDeviceClient** assíncrono e utiliza as seguintes APIs para gerir o upload de ficheiros com o seu hub IoT:

    * **get_storage_info_for_blob** obtém informações do seu hub IoT sobre a conta de armazenamento ligada que criou anteriormente. Estas informações incluem o nome de anfitrião, o nome do contentor, o nome blob e um token SAS. A informação de armazenamento é passada para a função **store_blob** (criada na etapa anterior), para que o **BlobClient** nessa função possa autenticar com armazenamento Azure. O método **get_storage_info_for_blob** também devolve um correlation_id, que é utilizado no método **notify_blob_upload_status.** O correlation_id é a forma de marcar em que blob estás a trabalhar.

    * **notify_blob_upload_status** identifica o Hub IoT do estado da sua operação de armazenamento de bolhas. Passa-se o correlation_id obtido pelo método **get_storage_info_for_blob.** É usado pelo IoT Hub para notificar qualquer serviço que possa estar a ouvir uma notificação sobre o estado da tarefa de upload de ficheiros.

1. Guarde e feche o ficheiro **UploadFile.py.**

## <a name="run-the-application"></a>Executar a aplicação

Agora está pronto para executar a candidatura.

1. Num pedido de comando na sua pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A seguinte imagem mostra a saída da aplicação **FileUpload:**

    ![Saída da aplicação de dispositivo simulado](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento configurado:

    ![Ficheiro carregado](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Criar um hub IoT programática](iot-hub-rm-template-powershell.md)

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Saiba mais sobre o Armazenamento De Blob Azure com os seguintes links:

* [Documentação de armazenamento de Blob Azure](https://docs.microsoft.com/azure/storage/blobs/)

* [Armazenamento de Blob Azure para documentação da Python API](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
