---
title: Faça upload de ficheiros de dispositivos para Azure IoT Hub com python | Microsoft Docs
description: Como carregar ficheiros de um dispositivo para a nuvem usando o dispositivo Azure IoT SDK para Python. Os ficheiros carregados são armazenados num recipiente de bolhas de armazenamento Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501146"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como usar as [capacidades de upload de ficheiros do IoT Hub](iot-hub-devguide-file-upload.md) para enviar um ficheiro para [o armazenamento de blob Azure](../storage/index.yml). Este tutorial mostra-lhe como:

* Forneça de forma segura um recipiente de armazenamento para o upload de um ficheiro.

* Use o cliente Python para fazer o upload de um ficheiro através do seu hub IoT.

O [telemetria Enviar de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md) quickstart demonstra a funcionalidade básica de mensagens dispositivo-a-nuvem do IoT Hub. No entanto, em alguns cenários não é possível mapear facilmente os dados que os seus dispositivos enviam para as mensagens relativamente pequenas de dispositivo para nuvem que o IoT Hub aceita. Quando precisa de criar ficheiros a partir de um dispositivo, ainda pode utilizar a segurança e a fiabilidade do IoT Hub.

No final deste tutorial, você executou a aplicação de consola Python:

* **FileUpload.py**, que envia um ficheiro para armazenamento usando o Python Device SDK.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Certifique-se de que a porta 8883 está aberta na sua firewall. A amostra do dispositivo neste artigo utiliza o protocolo MQTT, que comunica sobre a porta 8883. Este porto pode ser bloqueado em alguns ambientes de rede corporativa e educacional. Para obter mais informações e formas de contornar esta questão, consulte [Connecting to IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Faça upload de um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, cria-se a aplicação do dispositivo para fazer o upload de um ficheiro para o hub IoT.

1. No seu comando, executar o seguinte comando para instalar o pacote **de dispositivo azure-iot.** Utilize este pacote para coordenar o upload do ficheiro com o seu hub IoT.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. No seu comando, executar o seguinte comando para instalar o pacote [**azure.storage.blob.**](https://pypi.org/project/azure-storage-blob/) Utilize este pacote para efetuar o upload do ficheiro.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Crie um ficheiro de teste que irá carregar para o armazenamento de bolhas.

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

1. No seu ficheiro, `[Device Connection String]` substitua-o pela cadeia de ligação do seu dispositivo de hub IoT. `[Full path to local file]`Substitua-o pelo caminho para o ficheiro de teste que criou ou por qualquer ficheiro no seu dispositivo que pretenda carregar.

1. Crie uma função para carregar o ficheiro para o armazenamento de bolhas:

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

    Esta função analisa a estrutura *blob_info* que passou para criar um URL que utiliza para inicializar [um azure.storage.blob.BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient). Em seguida, envia o seu ficheiro para o armazenamento de blob Azure usando este cliente.

1. Adicione o seguinte código para ligar o cliente e fazer o upload do ficheiro:

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

    Este código cria um **IoTHubDeviceClient** assíncronos e utiliza as seguintes APIs para gerir o upload de ficheiros com o seu hub IoT:

    * **get_storage_info_for_blob** obtém informações do seu hub IoT sobre a Conta de Armazenamento ligada que criou anteriormente. Esta informação inclui o nome de anfitrião, nome do recipiente, nome blob e um token SAS. A informação de armazenamento é transmitida para a função **store_blob** (criada no passo anterior), para que o **BlobClient** nessa função possa autenticar com armazenamento Azure. O método **get_storage_info_for_blob** também devolve uma correlation_id, que é utilizada no método **notify_blob_upload_status.** O correlation_id é a forma do IoT Hub marcar em que bolha estás a trabalhar.

    * **notify_blob_upload_status** notifica o IoT Hub do estado da sua operação de armazenamento de bolhas. Passa-se o correlation_id obtido pelo método **get_storage_info_for_blob.** É usado pelo IoT Hub para notificar qualquer serviço que possa estar a ouvir uma notificação sobre o estado da tarefa de upload de ficheiros.

1. Guarde e feche o ficheiro **UploadFile.py.**

## <a name="run-the-application"></a>Executar a aplicação

Agora estás pronto para fazer a candidatura.

1. Numa instrução de comando na sua pasta de trabalho, executar o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

2. A imagem que se segue mostra a saída da aplicação **FileUpload:**

    ![Saída a partir de aplicação de dispositivo simulado](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Pode utilizar o portal para visualizar o ficheiro carregado no recipiente de armazenamento que configura:

    ![Ficheiro carregado](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar as capacidades de upload de ficheiros do IoT Hub para simplificar os uploads de ficheiros a partir de dispositivos. Pode continuar a explorar funcionalidades e cenários do hub IoT com os seguintes artigos:

* [Crie um hub IoT programáticamente](iot-hub-rm-template-powershell.md)

* [Introdução à C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Saiba mais sobre o Azure Blob Storage com os seguintes links:

* [Documentação de armazenamento Azure Blob](../storage/blobs/index.yml)

* [Armazenamento Azure Blob para documentação python API](/python/api/overview/azure/storage-blob-readme)