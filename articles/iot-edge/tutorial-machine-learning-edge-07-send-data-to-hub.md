---
title: 'Tutorial: Enviar dados do dispositivo via gateway transparente - Machine Learning on Azure IoT Edge'
description: Este tutorial mostra como pode usar a sua máquina de desenvolvimento como um dispositivo IoT Edge simulado para enviar dados para o IoT Hub através de um dispositivo configurado como um gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 50df3424892594a6817d481aa4a3d540a342854f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932324"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: Enviar dados via gateway transparente

Neste artigo, mais uma vez usamos o VM de desenvolvimento como um dispositivo simulado. No entanto, em vez de enviar dados diretamente para o IoT Hub, o dispositivo envia dados para o dispositivo IoT Edge configurados como um gateway transparente.

Monitorizamos o funcionamento do dispositivo IoT Edge enquanto o dispositivo simulado está a enviar dados. Uma vez que o dispositivo esteja terminado de funcionar, olhamos para os dados na nossa conta de armazenamento para validar tudo o que funciona como esperado.

Este passo é normalmente realizado por um desenvolvedor de nuvem ou dispositivo.

Nesta secção do tutorial, aprende-se a:

> [!div class="checklist"]
>
> * Construa e execute um dispositivo de folha.
> * Verifique se os dados gerados estão a ser armazenados no seu armazenamento Azure Blob.
> * Validar que o modelo de aprendizagem automática classificou os dados do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se já chegou a este artigo diretamente, visite o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="review-device-harness"></a>Arnês do dispositivo de revisão

Reutilizar o [projeto DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) para simular o dispositivo a jusante (ou folha). A ligação ao gateway transparente requer duas coisas adicionais:

* Registe o certificado para fazer com que o dispositivo IoT a jusante confie na autoridade de certificados utilizada pelo tempo de execução IoT Edge. No nosso caso, o dispositivo a jusante é o VM de desenvolvimento.
* Adicione o nome de domínio totalmente qualificado (FQDN) ao fio de ligação do dispositivo.

Veja o código para ver como estes dois itens são implementados.

1. Na sua máquina de desenvolvimento abra o Código do Estúdio Visual.

1. Utilize a pasta open **de**  >  **ficheiros...** para abrir C: \\ fonte \\ IoTEdgeAndMlSample \\ DeviceHarness.

1. Veja o método InstallCertificate() em Program.cs.

1. Note que se o código encontrar o caminho do certificado, chama o método CertificateManager.InstallCACert para instalar o certificado na máquina.

1. Agora veja o método GetIotHubDevice na classe TurbofanDevice.

1. Quando o utilizador especifica o FQDN do gateway utilizando a opção "-g", esse valor é passado para este método como a `gatewayFqdn` variável, que é anexada à cadeia de ligação do dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Construir e executar dispositivo de folha

1. Com o projeto DeviceHarness ainda aberto no Código do Estúdio Visual, construa o projeto. No menu **Terminal,** selecione **Run Build Task** e selecione **Build.**

1. Encontre o nome de domínio totalmente qualificado (FQDN) para o seu gateway de borda navegando para o seu dispositivo IoT Edge (Linux VM) no portal Azure e copiando o valor para o **nome DNS** na página geral.

1. Inicie o seu dispositivo IoT (Linux VM) se ainda não estiver em funcionamento.

1. Abra o terminal visual Studio Code. A partir do menu **Terminal,** selecione **Novo Terminal** e execute o seguinte comando, substituindo pelo `<edge_device_fqdn>` nome DNS que copiou do dispositivo IoT Edge (Linux VM):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. A aplicação tenta instalar o certificado na sua máquina de desenvolvimento. Quando isso acontecer, aceite o aviso de segurança.

1. Quando solicitado para a cadeia de ligação IoT Hub, clique na elipse **(...**) no painel de dispositivos Azure IoT Hub e selecione **copy IoT Hub Connection String**. Cole o valor no terminal.

1. Verá a saída como:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Note a adição do "GatewayHostName" à cadeia de ligação do dispositivo, o que faz com que o dispositivo comunique através do IoT Hub através do gateway transparente IoT Edge.

## <a name="check-output"></a>Verificar saída

### <a name="iot-edge-device-output"></a>Saída do dispositivo IoT Edge

A saída do módulo avroFileWriter pode ser facilmente observada olhando para o dispositivo IoT Edge.

1. SSH na sua máquina virtual IoT Edge.

1. Procure ficheiros escritos no disco.

   ```bash
   find /data/avrofiles -type f
   ```

1. A saída do comando será o seguinte exemplo:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Pode ter mais do que um único ficheiro dependendo do tempo de execução.

1. Preste atenção nas datas. O módulo avroFileWriter carrega os ficheiros para a nuvem uma vez que a última hora de modificação é superior a 10 minutos no passado (ver TIMEOUT DE FICHEIRO MODIFICADO \_ em uploader.py no módulo \_ AvroFileWriter).

1. Uma vez decorridos os 10 minutos, o módulo deve carregar os ficheiros. Se o upload for bem sucedido, elimina os ficheiros do disco.

### <a name="azure-storage"></a>Storage do Azure

Podemos observar os resultados do nosso dispositivo de folha enviando dados olhando para as contas de armazenamento onde esperamos que os dados sejam encaminhados.

1. Na máquina de desenvolvimento abre o Código do Estúdio Visual.

1. No painel "AZURE STORAGE" na janela de exploração, navegue pela árvore para encontrar a sua conta de armazenamento.

1. Expanda o nó **de recipientes Blob.**

1. Pelo trabalho que fizemos na parte anterior do tutorial, esperamos que o recipiente **de ruldata** contenha mensagens com a RUL. Expanda o nó **de ruldata.**

1. Verá um ou mais ficheiros blob chamados: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Clique em um dos ficheiros e escolha **Download Blob** para guardar o ficheiro na sua máquina de desenvolvimento.

1. Em seguida, expandir o **nó uploadturbofanfiles.** No artigo anterior, definimos esta localização como o alvo para ficheiros carregados pelo módulo avroFileWriter.

1. Clique no direito nos ficheiros e escolha **Baixar Blob** para guardá-lo na sua máquina de desenvolvimento.

### <a name="read-avro-file-contents"></a>Leia o conteúdo do ficheiro Avro

Incluímos um simples utilitário de linha de comando para ler um ficheiro Avro e devolver uma sequência JSON das mensagens no ficheiro. Nesta secção, vamos instalá-lo e executá-lo.

1. Abra um terminal em **Terminal** Visual Studio Code  >  **(Terminal Novo terminal).**

1. Instalar o hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Utilize o hubavroreader para ler o ficheiro Avro que descarregou a partir de **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Note que o corpo da mensagem parece como esperávamos com o ID do dispositivo e previu RUL.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Executar o mesmo comando passando o ficheiro Avro que descarregou a partir de **uploadturbofanfiles**.

1. Como esperado, estas mensagens contêm todos os dados do sensor e configurações operacionais da mensagem original. Estes dados podem ser usados para melhorar o modelo RUL no nosso dispositivo de borda.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia explorar os recursos utilizados por este tutorial de ponta a ponta, espere até terminar para limpar os recursos que criou. Caso contrário, utilize os seguintes passos para os eliminar:

1. Eliminar o(s) grupo de recursos criado para deter o Dev VM, IoT Edge VM, IoT Hub, conta de armazenamento, serviço de espaço de trabalho de machine learning (e recursos criados: registo de contentores, Insights de Aplicação, cofre chave, conta de armazenamento).

1. Elimine o projeto de aprendizagem automática em [Cadernos Azure.](https://notebooks.azure.com)

1. Se clonou o repo localmente, feche quaisquer janelas do Código PowerShell ou VS referentes ao repo local e, em seguida, apague o diretório de repo.

1. Se criou certificados localmente, elimine a pasta c: \\ edgeCertificates.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos o nosso VM de desenvolvimento para simular um dispositivo de folha enviando sensores e dados operacionais para o nosso dispositivo IoT Edge. Validámos que os módulos do dispositivo foram encaminhados, classificados, persistidos e carregados os dados examinando o funcionamento em tempo real do dispositivo edge e olhando para os ficheiros enviados para a conta de armazenamento.

Para continuar a aprender sobre as capacidades do IoT Edge, experimente este tutorial a seguir:

> [!div class="nextstepaction"]
> [Criar uma hierarquia de dispositivos IoT Edge (Pré-visualização)](tutorial-nested-iot-edge.md?view=iotedge-2020-11&preserve-view=true)