---
title: 'Tutorial: Envie dados do dispositivo através de gateway transparente - Machine Learning on Azure IoT Edge'
description: Este tutorial mostra como pode usar a sua máquina de desenvolvimento como um dispositivo IoT Edge simulado para enviar dados para o IoT Hub, passando por um dispositivo configurado como uma porta de entrada transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 50f339b257110f0a5dc0ac08b9f40043ee384afb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706900"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: Enviar dados através de gateway transparente

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, voltamos a usar a máquina de desenvolvimento como um dispositivo simulado, mas em vez de enviar dados diretamente para o IoT Hub, o dispositivo envia dados para o dispositivo IoT Edge configurado como uma porta de entrada transparente.

Monitorizamos o funcionamento do dispositivo IoT Edge enquanto o dispositivo simulado está a enviar dados. Uma vez que o dispositivo esteja terminado de funcionamento, olhamos para os dados na nossa conta de armazenamento para validar tudo o que funcionou como esperado.

Este passo é normalmente realizado por um desenvolvedor de nuvem ou dispositivo.

## <a name="review-device-harness"></a>Rever o arnês do dispositivo

Reutilizar o [projeto DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) para simular o dispositivo a jusante (ou folha). A ligação ao gateway transparente requer duas coisas adicionais:

* Registe o certificado para que o dispositivo a jusante (neste caso a nossa máquina de desenvolvimento) confie na autoridade de certificados que está a ser utilizada pelo tempo de execução ioT Edge.
* Adicione o nome de domínio totalmente qualificado (FQDN) à cadeia de ligação do dispositivo.

Veja o código para ver como estes dois itens são implementados.

1. Na sua máquina de desenvolvimento abrir o Código do Estúdio Visual.

2. Utilize a pasta aberta do\\\\\\ **ficheiro...** > **Open Folder...** para abrir C: fonte IoTEdgeAndMlSample DeviceHarness.

3. Veja o método de Instalação de Certificados em Program.cs.

4. Note que se o código encontrar o caminho do certificado, chama o método CertificateManager.InstallCACert para instalar o certificado na máquina.

5. Agora veja o método GetIotHubDevice na classe TurbofanDevice.

6. Quando o utilizador especifica o FQDN do gateway utilizando a opção "-g", esse valor é passado para este método como gatewayFqdn, que é anexado à cadeia de ligação do dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Construir e executar dispositivo de folha

1. Com o projeto DeviceHarness ainda aberto no Código do Estúdio Visual, construa o projeto (Ctrl + Shift + B ou **Terminal** > **Run Build Task...**) e selecione **Build** a partir do diálogo.

2. Encontre o nome de domínio totalmente qualificado (FQDN) para o seu portal de entrada de borda, navegando para a sua máquina virtual do dispositivo IoT Edge no portal e copiando o valor para o **nome DNS** a partir da visão geral.

3. Abra o terminal de**Terminal** > Código do Estúdio Visual ( Terminal `<edge_device_fqdn>` **Novo)** e execute o seguinte comando, substituindo o nome DNS que copiou da máquina virtual:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. A aplicação tenta instalar o certificado na sua máquina de desenvolvimento. Quando acontecer, aceite o aviso de segurança.

5. Quando solicitado para a cadeia de ligação IoT Hub clique na elipse **(...**) no painel de dispositivos Azure IoT Hub e selecione **Copy IoT Hub Connection String**. Cola o valor no terminal.

6. Verá a saída como:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Note a adição do "GatewayHostName" à cadeia de ligação do dispositivo, o que faz com que o dispositivo se comunique através do IoT Hub através do portal transparente IoT Edge.

## <a name="check-output"></a>Verificar a saída

### <a name="iot-edge-device-output"></a>Saída do dispositivo IoT Edge

A saída do módulo AvroFileWriter pode ser facilmente observada olhando para o dispositivo IoT Edge.

1. SSH na sua máquina virtual IoT Edge.

2. Procure ficheiros escritos em disco.

   ```bash
   find /data/avrofiles -type f
   ```

3. A saída do comando será o seguinte exemplo:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Pode ter mais do que um único ficheiro, dependendo do tempo da execução.

4. Preste atenção aos selos temporais. O módulo avroFileWriter envia os ficheiros para a nuvem uma vez que o último\_\_tempo de modificação é superior a 10 minutos no passado (ver TEMPO DE FICHEIRO MODIFICADO em uploader.py no módulo avroFileWriter).

5. Uma vez decorridos os 10 minutos, o módulo deve carregar os ficheiros. Se o upload for bem sucedido, elimina os ficheiros do disco.

### <a name="azure-storage"></a>Storage do Azure

Podemos observar os resultados do nosso dispositivo de folhas enviando dados olhando para as contas de armazenamento onde esperamos que os dados sejam encaminhados.

1. Na máquina de desenvolvimento abre o Código do Estúdio Visual.

2. No painel "ARMAZENAMENTO AZURE" na janela de exploração, navegue na árvore para encontrar a sua conta de armazenamento.

3. Expanda o nó **blob containers.**

4. Pelo trabalho que fizemos na parte anterior do tutorial, esperamos que o recipiente **ruldata** contenha mensagens com RUL. Expanda o nó **ruldata.**

5. Verá um ou mais ficheiros blob chamados como: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Clique num dos ficheiros e escolha **download Blob** para guardar o ficheiro para a sua máquina de desenvolvimento.

7. Em seguida, expanda o nó **de uploadturbofanfiles.** No artigo anterior, definimos esta localização como o alvo para ficheiros carregados pelo módulo avroFileWriter.

8. Clique nos ficheiros e escolha **download Blob** para guardá-lo para a sua máquina de desenvolvimento.

### <a name="read-avro-file-contents"></a>Ler conteúdo de ficheiros Avro

Incluímos um simples utilitário de linha de comando para ler um ficheiro Avro e devolver uma série jSON das mensagens no ficheiro. Nesta secção, vamos instalá-la e executá-la.

1. Abra um terminal em Visual Studio Code (**Terminal** > **Novo terminal).**

2. Instale hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Utilize o hubavroreader para ler o ficheiro Avro que descarregou a partir de **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Note que o corpo da mensagem parece como esperávamos com identificação do dispositivo e rul previsto.

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

5. Execute o mesmo comando passando o ficheiro Avro que descarregou a partir de **uploadturbofanfiles**.

6. Como esperado, estas mensagens contêm todos os dados do sensor e configurações operacionais da mensagem original. Estes dados poderiam ser utilizados para melhorar o modelo RUL no nosso dispositivo de borda.

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

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia explorar os recursos utilizados por este tutorial de ponta a ponta, espere até que esteja feito para limpar os recursos que criou. Se não pretender continuar, utilize os seguintes passos para os eliminar:

1. Eliminar o(s) grupo de recursos criado para deter o Dev VM, IoT Edge VM, IoT Hub, conta de armazenamento, serviço de espaço de trabalho de aprendizagem automática (e recursos criados: registo de contentores, insights de aplicação, cofre chave, conta de armazenamento).

2. Elimine o projeto de aprendizagem automática em [cadernos Azure.](https://notebooks.azure.com)

3. Se clonou o repo localmente, feche quaisquer janelas de Código PowerShell ou VS referindo-se ao repo local, então elimine o repo diretório.

4. Se criou certificados localmente, elimine a pasta c:\\edgeCertificates.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos a nossa máquina de desenvolvimento para simular um dispositivo de folha sondar sensores e dados operacionais para o nosso dispositivo de borda. Validámos que os módulos do dispositivo foram encaminhados, classificados, persumidos e carregamos os dados primeiro examinando o funcionamento em tempo real do dispositivo de borda e, em seguida, olhando para os ficheiros enviados para a conta de armazenamento.

Mais informações podem ser encontradas nas seguintes páginas:

* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
* [Armazenar dados na borda com armazenamento de Blob Azure no IoT Edge (pré-visualização)](how-to-store-data-blob.md)
