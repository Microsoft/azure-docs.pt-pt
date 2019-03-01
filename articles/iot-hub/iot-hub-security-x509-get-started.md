---
title: Tutorial para segurança X.509 no IoT Hub do Azure | Documentos da Microsoft
description: Comece a utilizar sobre a segurança de baseados em X.509 no seu hub IoT do Azure num ambiente simulado.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: bfacd71e4c5a018fb2d153e8b9683cfa8031a956
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010400"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Configurar a segurança X.509 no seu hub IoT do Azure

Este tutorial simula os passos necessários para proteger o seu hub IoT do Azure com o *autenticação de certificados X.509*. Para efeitos de ilustração, vamos mostrar como utilizar a ferramenta de código-fonte aberto OpenSSL para criar certificados localmente no seu computador Windows. Recomendamos que utilize este tutorial para apenas a fins de teste. Para o ambiente de produção, deverá comprar os certificados a partir de um *autoridade de certificação (AC) de raiz*. 

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer que tenha os seguintes recursos prontos:

- Criar um hub IoT com a sua subscrição do Azure. Ver [criar um hub IoT através do portal](iot-hub-create-through-portal.md) para obter passos detalhados. 
- Tiver [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/vs/) instalado no seu computador. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Obter certificados X.509 de AC
A segurança baseada em certificados X.509 no IoT Hub, tem de começar com uma [cadeia de certificados X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), que inclui o certificado de raiz, bem como quaisquer certificados intermédios até o certificado de folha. 

Pode escolher qualquer uma das formas seguintes para obter os certificados:
- Comprar certificados X.509 de um *autoridade de certificação (AC) de raiz*. Isto é recomendado para ambientes de produção.
OU,
- Crie seus próprios certificados X.509 com uma ferramenta de terceiros, como [OpenSSL](https://www.openssl.org/). Isso será bom para fins de teste e desenvolvimento. Ver [certificados de teste de Gerir AC para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para informações sobre como gerar certificados de AC com o PowerShell ou Bash de teste. O resto deste tutorial utiliza certificados de AC de teste gerados ao seguir as instruções em [certificados de teste de Gerir AC para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registar certificados de AC X.509 no seu hub IoT

Estes passos mostram como adicionar uma nova autoridade de certificação para o seu hub IoT através do portal.

1. No portal do Azure, navegue para o seu hub IoT e abra o **configurações** > **certificados** menu. 
2. Clique em **adicionar** para adicionar um novo certificado.
3. Introduza um nome a apresentar amigável para o certificado. Selecione o ficheiro de certificado de raiz com o nome *RootCA.cer* criado na secção anterior, a partir do seu computador. Clique em **Carregar**.
4. Depois de obter uma notificação que o certificado é carregado com êxito, clique em **guardar**.

    ![Carregar certificado](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Isto irá mostrar o seu certificado no **Explorador de certificados** lista. Tenha em atenção a **STATUS** deste certificado é *verificado*.

5. Clique no certificado que adicionou no passo anterior.

6. Na **detalhes do certificado** painel, clique em **gerar código de verificação**.

7. Ele cria um **código de verificação** para validar a propriedade do certificado. Copie o código para a área de transferência. 

   ![Verificar o certificado](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Agora, tem de iniciar sessão isso *código de verificação* com o representante de chave privada com o certificado X.509 de AC, que gera uma assinatura. Existem ferramentas disponíveis para efetuar este processo de assinatura, por exemplo, OpenSSL. Isso é conhecido como o [prova de posse](https://tools.ietf.org/html/rfc5280#section-3.1). Passo 3 [certificados de teste de Gerir AC para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um código de verificação.
 
9. Carregar a assinatura resultante da etapa 8 acima ao seu hub IoT no portal. Na **detalhes do certificado** painel no portal do Azure, navegue para o **ficheiro. pem ou. cer do certificado de verificação**e selecione a assinatura, por exemplo, *VerifyCert4.cer*criadas pelo exemplo PowerShell comando com o _Explorador de ficheiros_ ícone além-lo.

10. Assim que o certificado é carregado com êxito, clique em **Verifique se**. O **STATUS** das suas modificações de certificado **_verificado_** no **certificados** painel. Clique em **atualizar** se não atualizar automaticamente.

   ![Carregar a verificação de certificado](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Criar um dispositivo X.509 para o seu hub IoT

1. No portal do Azure, navegue até ao seu IoT hub **exploradores > dispositivos IoT** página.

2. Clique em **+ adicionar** para adicionar um novo dispositivo.

3. Dê um nome a apresentar amigável para o **ID do dispositivo**e selecione **_assinado de AC X.509_** como o **tipo de autenticação**. Clique em **Guardar**.

   ![Criar dispositivo X.509 no portal](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticar o dispositivo X.509 com os certificados X.509

Para autenticar o seu dispositivo X.509, precisa primeiro, inicie sessão no dispositivo com o certificado da AC. Assinatura de dispositivos de folha normalmente é feito da fábrica de produção, em que as ferramentas de fabrico foram ativadas adequadamente. Como o dispositivo passar de um fabricante para outro, a ação de assinatura de cada fabricante é capturada como um certificado intermédio dentro da cadeia. O resultado final é uma cadeia de certificados a partir do certificado de AC para o certificado de folha do dispositivo. Passo 4 [certificados de teste de Gerir AC para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um certificado de dispositivo.

Em seguida, mostraremos como criar uma aplicação c# para simular o dispositivo X.509 registado para o seu hub IoT. Iremos enviar valores de temperatura e humidade do dispositivo simulado ao seu hub. Observe que, neste tutorial, iremos criar a aplicação de dispositivo. Ele é deixado como um exercício para os leitores para criar a aplicação de serviço do IoT Hub que irá enviar a resposta a eventos enviados por esse dispositivo. Aplicação c# parte do princípio de que seguiu os passos em [certificados de teste de Gerir AC para exemplos e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. No Visual Studio, crie um novo projeto do Visual c# Windows ambiente de trabalho clássico com o modelo de projeto de aplicativo de Console. Nomeie o projeto **SimulateX509Device**.
   ![Criar o projeto para dispositivos X.509 no Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. No Solution Explorer, clique com botão direito a **SimulateX509Device** projeto e, em seguida, clique em **gerir pacotes NuGet...** . Na janela do Gestor de pacotes NuGet, selecione **navegue** e procure **microsoft.azure.devices.client**. Selecione **instale** para instalar o **Microsoft.Azure.Devices.Client** empacotamento e a aceitar os termos de utilização. Este procedimento transfere, instala e adiciona uma referência para o pacote de NuGet do SDK de dispositivo do IoT do Azure e as respetivas dependências.
   ![Adicionar o pacote NuGet do SDK de dispositivo no Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Adicione as seguintes linhas de código na parte superior a *Program.cs* ficheiro:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Adicione as seguintes linhas de código dentro do **programa** classe:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Utilize o nome amigável do dispositivo que utilizou na secção anterior, em vez de _< your_device_id >_ marcador de posição.

5. Adicione a seguinte função para criar números aleatórios de temperatura e humidade e enviar esses valores para o hub:
    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Por fim, adicione as seguintes linhas de código para o **Main** função, substituindo os marcadores de posição _id do dispositivo_, _nome da-iot-hub_ e  _Absolute-path-to-your-Device-PFX-File_ conforme requerido pelo seu programa de configuração.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
   Este código liga-se ao seu hub IoT através da criação de cadeia de ligação do seu dispositivo X.509. Assim que estiver ligado com êxito, ele, em seguida, envia eventos de temperatura e humidade para o hub e aguarda a resposta. 
7. Uma vez que esta aplicação acede uma *. pfx* ficheiro, poderá ter de executá-lo na *administrador* modo. Compile a solução do Visual Studio. Abra uma nova janela de comando como um **administrador**e navegue para a pasta que contém esta solução. Navegue para o *bin/Debug* caminho dentro da pasta de solução. Executar o aplicativo **SimulateX509Device.exe** partir a _administrador_ janela de comando. Deverá ver o seu dispositivo para ligar ao hub e enviar os eventos com êxito. 
   ![Executar a aplicação de dispositivo](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Consulte também
Para saber mais sobre como proteger a sua solução de IoT, veja:

* [Práticas recomendadas de segurança de IoT][lnk-security-best-practices]
* [Arquitetura de segurança de IoT][lnk-security-architecture]
* [Proteger a sua implementação de IoT][lnk-security-deployment]

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

[lnk-security-best-practices]: ../iot-fundamentals/iot-security-best-practices.md
[lnk-security-architecture]: ../iot-fundamentals/iot-security-architecture.md
[lnk-security-deployment]: ../iot-fundamentals/iot-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
