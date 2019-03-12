---
title: Carregar ficheiros a partir de dispositivos no Hub de IoT do Azure com Python | Documentos da Microsoft
description: Como carregar ficheiros a partir de um dispositivo para a cloud com o Azure IoT device SDK para Python. Os ficheiros carregados são armazenados num contentor de BLOBs de armazenamento do Azure.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 0fe2b33bc5f9a0b599934c4cabb065d4c97ea61b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549889"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar ficheiros a partir do seu dispositivo para a cloud com o IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este artigo mostra como utilizar o [carregamento de recursos do IoT Hub do ficheiro](iot-hub-devguide-file-upload.md) para carregar um ficheiro para [armazenamento de Blobs do Azure](../storage/index.yml). Este tutorial mostra-lhe como:

- Fornece com segurança a um contentor de armazenamento para carregar um ficheiro.
- Utilize o cliente Python para carregar um ficheiro através do IoT hub.

O [enviar telemetria ao IoT Hub](quickstart-send-telemetry-python.md) início rápido demonstra a funcionalidade de mensagens dispositivo-para-cloud básica do IoT Hub. No entanto, em alguns cenários não pode facilmente mapear os dados de que seus dispositivos enviam nas mensagens dispositivo-para-cloud relativamente pequenas que aceita o IoT Hub. Quando precisar de upland arquivos de um dispositivo, pode continuar a utilizar a segurança e fiabilidade do IoT Hub.

> [!NOTE]
> SDK de Python do Hub IoT atualmente suporta apenas como a carregar arquivos baseados em caracteres **. txt** ficheiros.

No final deste tutorial, executar a aplicação de consola Python:

* **FileUpload.py**, que carrega um ficheiro para o armazenamento com o SDK de dispositivo Python.

> [!NOTE]
> IoT Hub suporta muitas plataformas de dispositivos e linguagens (incluindo C, .NET, Javascript, Python e Java) através de SDKs de dispositivo do IoT do Azure. Consulte a [Centro de Programadores do Azure IoT] para obter instruções passo a passo sobre como ligar o dispositivo ao IoT Hub do Azure.

Para concluir este tutorial, precisa do seguinte:

* [Python 2.x ou 3.x][lnk-python-download]. Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python][lnk-install-pip].
* Se estiver a utilizar o SO Windows, o [pacote redistribuível Visual C++][lnk-visual-c-redist], para permitir a utilização de DLLs nativas de Python.
* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
* Um hub IoT na sua conta do Azure, com uma identidade de dispositivo para testar a funcionalidade de carregamento de ficheiros. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Carregar um ficheiro a partir de uma aplicação de dispositivo

Nesta secção, vai criar a aplicação de dispositivo para carregar um ficheiro para o hub IoT.

1. Na sua linha de comandos, execute o seguinte comando para instalar o **azure-iothub-dispositivo-cliente** pacote:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Com um editor de texto, crie um ficheiro de teste que irá carregar para o armazenamento de Blobs. 

    > [!NOTE]
    > SDK de Python do Hub IoT atualmente suporta apenas como a carregar arquivos baseados em caracteres **. txt** ficheiros.

1. Com um editor de texto, crie uma **FileUpload.py** ficheiro na sua pasta de trabalho.

1. Adicione as seguintes `import` afirmações e variáveis no início do **FileUpload.py** ficheiro. 

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

1. No seu ficheiro, substitua `[Device Connection String]` com a cadeia de ligação do seu dispositivo do hub IoT. Substitua `[Full path to file]` com o caminho para o ficheiro de teste que criou ou qualquer ficheiro no seu dispositivo que pretende carregar. Substitua `[File name for storage]` com o nome que pretende dar ao seu ficheiro após foi carregada para o armazenamento de Blobs. 

1. Criar um retorno de chamada para o **upload_blob** função:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Adicione o seguinte código para ligar o cliente e carregar o ficheiro. Também incluem o `main` rotina:

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

1. Guarde e feche o **UploadFile.py** ficheiro.


## <a name="run-the-application"></a>Executar a aplicação

Agora, está pronto para executar a aplicação.

1. No prompt de comando na sua pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

1. Captura de ecrã seguinte mostra a saída do **FileUpload** aplicação:

    ![Saída da aplicação de dispositivo simulado](./media/iot-hub-python-python-file-upload/1.png)

1. Pode utilizar o portal para ver o ficheiro carregado no contentor de armazenamento que configurou:

    ![Ficheiro carregado](./media/iot-hub-python-python-file-upload/2.png)


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
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: https://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
