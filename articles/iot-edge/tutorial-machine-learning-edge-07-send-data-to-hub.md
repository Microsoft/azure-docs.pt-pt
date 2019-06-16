---
title: Enviar dados de dispositivos através do gateway transparente - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Utilize o seu computador de desenvolvimento como um dispositivo IoT Edge simulado para enviar dados para o IoT Hub o dispositivos ao aceder através de um dispositivo configurado como um gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 12793ff28bf13f26bc2cc3d436b644601fc48ac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081386"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: Enviar dados através do gateway transparente

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Neste artigo, utilizamos mais uma vez a máquina de desenvolvimento como um dispositivo simulado, mas em vez de enviar dados diretamente para o IoT Hub o dispositivo envia dados para o dispositivo do IoT Edge configurado como um gateway transparente.

Vamos monitorizar o funcionamento do dispositivo IoT Edge, enquanto o dispositivo está a enviar dados. Quando o dispositivo for concluído em execução, vamos examinar os dados em nossa conta de armazenamento para validar a tudo o que funcionou como esperado.

Este passo é, normalmente realizado por um desenvolvedor de cloud ou dispositivo.

## <a name="review-device-harness"></a>Rever a estrutura de dispositivo

Reutilizar o [DeviceHarness projeto](tutorial-machine-learning-edge-03-generate-data.md) para simular a jusante (ou folha) o dispositivo. Ligar ao gateway transparente requer duas coisas adicionais:

* Registar o certificado para que o dispositivo de downstream (neste caso, nossa máquina de desenvolvimento) confiar na autoridade de certificado a ser utilizada pelo runtime do IoT Edge.
* Adicione o nome de domínio completamente qualificado do gateway edge (FQDN) para a cadeia de ligação do dispositivo.

Examinar o código para ver como esses dois itens são implementadas.

1. No computador de desenvolvimento abra o Visual Studio Code.

2. Uso **arquivo** > **Abrir pasta...**  para abrir a unidade c:\\origem\\IoTEdgeAndMlSample\\DeviceHarness.

3. Ver o método InstallCertificate() em Program.cs.

4. Tenha em atenção que, se o código encontrar o caminho do certificado, ele chama o método CertificateManager.InstallCACert para instalar o certificado no computador.

5. Agora, veja o método GetIotHubDevice na classe TurbofanDevice.

6. Quando o usuário Especifica o FQDN do gateway, com o "-g" opção, que o valor é passado para esse método como gatewayFqdn, que obtém acrescentado à seqüência de conexão de dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Criar e executar os dispositivos de folha

1. Com o projeto de DeviceHarness estiver aberto no Visual Studio Code, compilar o projeto (Ctrl + Shift + B ou **Terminal** > **executar criar tarefa...** ) e selecione **criar** na caixa de diálogo.

2. Encontrar o nome de domínio completamente qualificado (FQDN) para o seu gateway edge ao navegar para a sua máquina de virtual de dispositivo do IoT Edge no portal e ao copiar o valor para **nome DNS** desde a descrição geral.

3. Abra o terminal do Visual Studio Code (**Terminal** > **novo terminal**) e execute o seguinte comando, substituindo `<edge_device_fqdn>` com o nome DNS que copiou a partir da máquina virtual:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. A aplicação tenta instalar o certificado no seu computador de desenvolvimento. Quando isso acontece, aceite o aviso de segurança.

5. Quando lhe for pedido para o clique de cadeia de ligação do IoT Hub nas reticências ( **...** ) no IoT Hub do Azure dispositivos painel e selecione **cadeia de ligação do Hub de IoT de cópia**. Cole o valor no terminal.

6. Verá a saída, como:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Tenha em atenção a adição de "GatewayHostName" para a cadeia de ligação do dispositivo, que faz com que o dispositivo comunique através do IoT Hub através do gateway transparente do IoT Edge.

## <a name="check-output"></a>Saída de verificação

### <a name="iot-edge-device-output"></a>Saída de dispositivo do IoT Edge

A saída do módulo avroFileWriter possa ser observada prontamente observando o dispositivo do IoT Edge.

1. SSH à sua máquina virtual do IoT Edge.

2. Procurar os ficheiros escritos no disco.

   ```bash
   find /data/avrofiles -type f
   ```

3. Saída do comando ficará semelhante ao seguinte exemplo:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Pode ter mais do que um único arquivo, dependendo do tempo da execução.

4. Preste atenção para os carimbos de data / hora. O módulo de avroFileWriter carrega os ficheiros para a cloud, assim que a hora da última modificação estiver mais de 10 minutos no passado (consulte modificadas\_ficheiro\_tempo limite em uploader.py no módulo avroFileWriter).

5. Depois de decorridos 10 minutos, o módulo deve carregar os ficheiros. Se o carregamento for bem-sucedido, elimina os ficheiros do disco.

### <a name="azure-storage"></a>Storage do Azure

Podemos pode observar os resultados de nosso dispositivo de folha a enviar dados ao observar as contas de armazenamento em que Esperamos que os dados para ser encaminhado.

1. Na máquina de desenvolvimento abra o Visual Studio Code.

2. No painel de "Armazenamento do AZURE" na janela de explorar, navegue pela árvore para encontrar a sua conta de armazenamento.

3. Expanda a **contentores de BLOBs** nó.

4. De trabalho que fizemos na parte anterior do tutorial, podemos esperar que o **ruldata** contentor deve conter mensagens com RUL. Expanda a **ruldata** nó.

5. Verá um ou mais ficheiros de blob com o nome, como: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Clique com o botão direito do rato em um dos ficheiros e escolha **transferir BLOBs** para guardar o ficheiro para o seu computador de desenvolvimento.

7. Em seguida expanda o **uploadturbofanfiles** nó. No artigo anterior, definimos esta localização como destino para ficheiros carregados pelo módulo de avroFileWriter.

8. Clique com o botão direito do rato nos ficheiros e escolha **transferir BLOBs** guardá-la no seu computador de desenvolvimento.

### <a name="read-avro-file-contents"></a>Conteúdo do arquivo de leitura Avro

Incluímos um utilitário de linha de comandos simples para ler um arquivo de Avro e retornar uma cadeia de caracteres do JSON das mensagens no ficheiro. Nesta secção, iremos instalar e executá-lo.

1. Abra um terminal no Visual Studio Code (**Terminal** > **novo terminal**).

2. Instale hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Utilizar hubavroreader para ler o ficheiro de Avro que transferiu a partir **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Tenha em atenção que se parece o corpo da mensagem, como esperado com o ID de dispositivo e prever a RUL.

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

5. Executar o mesmo comando, passando o ficheiro de Avro que transferiu a partir **uploadturbofanfiles**.

6. Conforme o esperado, essas mensagens contêm todos os dados de sensores e as definições operacionais da mensagem original. Estes dados poderiam ser usados para melhorar o modelo RUL no nosso dispositivo do edge.

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
           "EnqueuedTimeUtc": “<time>”
   }
   ```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser explorar os recursos utilizados por este tutorial ponto-a-ponto, aguarde até que termine para limpar os recursos que criou. Se não quiser continuar, utilize os seguintes passos para eliminá-los:

1. Eliminar os grupos de recursos criados para conter o desenvolvimento de VM, VM do IoT Edge, Hub IoT, conta de armazenamento, o serviço de área de trabalho do machine learning (e recursos criados: registo de contentor, o application insights, Cofre de chaves, conta de armazenamento).

2. Eliminar o projeto de machine learning na [blocos de notas do Azure](https://notebooks.azure.com).

3. Se clonou o repositório localmente, feche qualquer windows PowerShell ou o VS Code referir-se para o repositório local, em seguida, elimine o diretório do repositório.

4. Se tiver criado os certificados localmente, eliminar a unidade c: pasta\\edgeCertificates.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, nós usamos nossa máquina de desenvolvimento para simular um sensor de envio de dispositivos de folha e dados operacionais para nosso dispositivo de limite. Podemos validar que os módulos no dispositivo encaminhado, classificado, persistente e carregou os dados em primeiro lugar, examinando o funcionamento em tempo real do dispositivo de limite e, em seguida, analisando os ficheiros carregados para a conta de armazenamento.

Obter mais informações podem ser encontradas nas seguintes páginas:

* [Ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
* [Store dados na periferia com o armazenamento de Blobs do Azure no IoT Edge (pré-visualização)](how-to-store-data-blob.md)
