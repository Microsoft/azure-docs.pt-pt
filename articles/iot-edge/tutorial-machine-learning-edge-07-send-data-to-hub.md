---
title: 'Tutorial: enviar dados de dispositivo por meio de Machine Learning de gateway transparentes em Azure IoT Edge'
description: 'Tutorial: usar seu computador de desenvolvimento como um dispositivo de IoT Edge simulado para enviar dados ao Hub IoT através de um dispositivo configurado como um gateway transparente.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a1974a9d9e635bad4094e38c2c194ff49993611
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113958"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Tutorial: enviar dados por meio do gateway transparente

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, usamos novamente o computador de desenvolvimento como um dispositivo simulado, mas em vez de enviar dados diretamente para o Hub IoT, o dispositivo envia dados para o dispositivo IoT Edge configurado como um gateway transparente.

Monitoramos a operação do dispositivo IoT Edge enquanto o dispositivo simulado está enviando dados. Após a conclusão da execução do dispositivo, examinaremos os dados em nossa conta de armazenamento para validar tudo o que funcionou conforme o esperado.

Essa etapa normalmente é executada por um desenvolvedor de dispositivo ou nuvem.

## <a name="review-device-harness"></a>Examinar o equipamento de dispositivo

Reutilize o [projeto DeviceHarness](tutorial-machine-learning-edge-03-generate-data.md) para simular o dispositivo downstream (ou folha). Conectar-se ao gateway transparente requer duas coisas adicionais:

* Registre o certificado para fazer com que o dispositivo downstream (nesse caso, nosso computador de desenvolvimento) confie na autoridade de certificação que está sendo usada pelo IoT Edge Runtime.
* Adicione o FQDN (nome de domínio totalmente qualificado) do gateway de borda à cadeia de conexão do dispositivo.

Examine o código para ver como esses dois itens são implementados.

1. No computador de desenvolvimento, abra Visual Studio Code.

2. Usar **arquivo** > **abrir pasta...** para abrir C:\\fonte\\IoTEdgeAndMlSample\\DeviceHarness.

3. Examine o método InstallCertificate () em Program.cs.

4. Observe que, se o código encontrar o caminho do certificado, ele chamará o método Certificatemanager. InstallCACert para instalar o certificado no computador.

5. Agora, examine o método GetIotHubDevice na classe TurbofanDevice.

6. Quando o usuário especifica o FQDN do gateway usando a opção "-g", esse valor é passado para esse método como gatewayFqdn, que é anexado à cadeia de conexão do dispositivo.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Compilar e executar dispositivo de folha

1. Com o projeto DeviceHarness ainda aberto no Visual Studio Code, compile o projeto (Ctrl + Shift + B ou **Terminal** > **Executar tarefa de compilação...** ) e selecione **Compilar** na caixa de diálogo.

2. Localize o FQDN (nome de domínio totalmente qualificado) do seu gateway de borda navegando até a máquina virtual do dispositivo IoT Edge no portal e copiando o valor do **nome DNS** da visão geral.

3. Abra o terminal do Visual Studio Code (**terminal** > **novo terminal**) e execute o comando a seguir, substituindo `<edge_device_fqdn>` pelo nome DNS que você copiou da máquina virtual:

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

4. O aplicativo tenta instalar o certificado em seu computador de desenvolvimento. Em caso afirmativo, aceite o aviso de segurança.

5. Quando for solicitada a cadeia de conexão do Hub IoT, clique nas reticências ( **...** ) no painel dispositivos do Hub IOT do Azure e selecione **copiar cadeia de conexão do Hub IOT**. Cole o valor no terminal.

6. Você verá uma saída como:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observe a adição de "GatewayHostName" à cadeia de conexão do dispositivo, que faz com que o dispositivo se comunique através do Hub IoT por meio do IoT Edge gateway transparente.

## <a name="check-output"></a>Verificar saída

### <a name="iot-edge-device-output"></a>Saída do dispositivo IoT Edge

A saída do módulo avroFileWriter pode ser prontamente observada examinando o dispositivo IoT Edge.

1. SSH em sua máquina virtual de IoT Edge.

2. Procure arquivos gravados em disco.

   ```bash
   find /data/avrofiles -type f
   ```

3. A saída do comando se parecerá com o exemplo a seguir:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Você pode ter mais de um único arquivo, dependendo do tempo da execução.

4. Preste atenção aos carimbos de data/hora. O módulo avroFileWriter carrega os arquivos para a nuvem quando a hora da última modificação é mais de 10 minutos no passado (consulte o arquivo\_modificado\_tempo limite em uploader.py no módulo avroFileWriter).

5. Depois que os 10 minutos tiverem decorrido, o módulo deverá carregar os arquivos. Se o upload for bem-sucedido, ele excluirá os arquivos do disco.

### <a name="azure-storage"></a>Storage do Azure

Podemos observar os resultados do nosso dispositivo de folha enviando dados examinando as contas de armazenamento em que esperamos que os dados sejam roteados.

1. No computador de desenvolvimento, abra Visual Studio Code.

2. No painel "armazenamento do AZURE" na janela explorar, navegue na árvore para localizar sua conta de armazenamento.

3. Expanda o nó **contêineres de blob** .

4. Do trabalho que fizemos na parte anterior do tutorial, esperamos que o contêiner **ruldata** deva conter mensagens com RUL. Expanda o nó **ruldata** .

5. Você verá um ou mais arquivos de blob nomeados como: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>`.

6. Clique com o botão direito do mouse em um dos arquivos e escolha **baixar blob** para salvar o arquivo em seu computador de desenvolvimento.

7. Em seguida, expanda o nó **uploadturbofanfiles** . No artigo anterior, definimos esse local como o destino para os arquivos carregados pelo módulo avroFileWriter.

8. Clique com o botão direito do mouse nos arquivos e escolha **baixar blob** para salvá-lo em seu computador de desenvolvimento.

### <a name="read-avro-file-contents"></a>Ler conteúdo do arquivo Avro

Incluímos um utilitário de linha de comando simples para ler um arquivo Avro e retornar uma cadeia de caracteres JSON das mensagens no arquivo. Nesta seção, vamos instalá-lo e executá-lo.

1. Abra um terminal no Visual Studio Code (**terminal** > **novo terminal**).

2. Instalar o hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

3. Use hubavroreader para ler o arquivo Avro que você baixou do **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

4. Observe que o corpo da mensagem é semelhante ao esperado com a ID do dispositivo e a RUL prevista.

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

5. Execute o mesmo comando passando o arquivo Avro que você baixou de **uploadturbofanfiles**.

6. Conforme esperado, essas mensagens contêm todos os dados do sensor e as configurações operacionais da mensagem original. Esses dados podem ser usados para melhorar o modelo RUL em nosso dispositivo de borda.

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

Se você planeja explorar os recursos usados por este tutorial de ponta a ponta, aguarde até que você tenha terminado de limpar os recursos que criou. Se você não planeja continuar, use as seguintes etapas para excluí-las:

1. Exclua os grupos de recursos criados para manter a VM de desenvolvimento, IoT Edge VM, Hub IoT, conta de armazenamento, serviço de espaço de trabalho do Machine Learning (e recursos criados: registro de contêiner, Application insights, cofre de chaves, conta de armazenamento).

2. Exclua o projeto de Machine Learning nos [blocos de anotações do Azure](https://notebooks.azure.com).

3. Se você clonou o repositório localmente, feche qualquer PowerShell ou VS Code Windows fazendo referência ao repositório local e, em seguida, exclua o diretório do repositório.

4. Se você tiver criado certificados localmente, exclua a pasta c:\\edgeCertificates.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos nossa máquina de desenvolvimento para simular um dispositivo de folha enviando dados de sensor e operacionais ao nosso dispositivo de borda. Validamos que os módulos no dispositivo roteados, classificados, persistiram e carregaram os dados primeiro examinando a operação em tempo real do dispositivo de borda e, em seguida, examinando os arquivos carregados na conta de armazenamento.

Mais informações podem ser encontradas nas seguintes páginas:

* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
* [Armazenar dados na borda com o armazenamento de BLOBs do Azure no IoT Edge (versão prévia)](how-to-store-data-blob.md)
