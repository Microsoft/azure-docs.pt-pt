---
title: Tutorial para segurança X.509 no Hub Azure IoT  Microsoft Docs
description: Inicie a segurança baseada em X.509 no seu hub Azure IoT num ambiente simulado.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 968241eff1bcab449f9a4def7a394a508461ec95
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271177"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Instale a segurança X.509 no seu hub Azure IoT

Este tutorial mostra os passos necessários para fixar o seu hub Azure IoT utilizando a Autenticação do *Certificado X.509.* Para fins de ilustração, utilizamos a ferramenta open-source OpenSSL para criar certificados localmente na sua máquina Windows. Recomendamos que utilize este tutorial apenas para efeitos de teste. Para o ambiente de produção, deve adquirir os certificados a uma autoridade de *certificados de raiz (CA)* .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer que tenha os seguintes recursos prontos:

* Criou um hub IoT com a sua assinatura Azure. Consulte [criar um hub IoT através do portal](iot-hub-create-through-portal.md) para passos detalhados.

* Tem o [Visual Studio 2017 ou o Visual Studio 2019](https://www.visualstudio.com/vs/) instalado.

## <a name="get-x509-ca-certificates"></a>Obtenha certificados Ca X.509

A segurança baseada em certificados X.509 no IoT Hub exige que comece com uma cadeia de [certificados X.509,](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)que inclui o certificado de raiz, bem como quaisquer certificados intermédios até ao certificado de folha.

Pode escolher qualquer uma das seguintes formas de obter os seus certificados:

* Comprar certificados X.509 a uma autoridade de *certificados de raiz (CA)* . Este método é recomendado para ambientes de produção.

* Crie os seus próprios certificados X.509 utilizando uma ferramenta de terceiros, como o [OpenSSL](https://www.openssl.org/). Esta técnica é boa para efeitos de teste e desenvolvimento. Consulte a [gestão dos certificados ca de teste para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para obter informações sobre a geração de certificados ca de teste utilizando powerShell ou Bash. O resto deste tutorial utiliza certificados CA de teste gerados seguindo as instruções de gestão dos certificados ca de [ensaio para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Gere um [certificado de CA intermédio X.509](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) assinado por um certificado ca raiz existente e carregue-o para o centro. Uma vez que o certificado intermédio é carregado e verificado, conforme instruído abaixo, pode ser utilizado no lugar de um certificado ca raiz mencionado abaixo. Ferramentas como openssL[(openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) e [openssl ca)](https://www.openssl.org/docs/man1.1.0/man1/ca.html)podem ser usadas para gerar e assinar um certificado ca intermédio.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registe os certificados X.509 CA no seu hub IoT

Estes passos mostram-lhe como adicionar uma nova Autoridade de Certificado sairá do seu hub IoT através do portal.

1. No portal Azure, navegue para o seu hub IoT e selecione **Definições** > **Certificados** para o hub.

1. Selecione **Adicionar** para adicionar um novo certificado.

1. No **Nome do Certificado,** introduza um nome de exibição amigável e selecione o ficheiro de certificado que criou na secção anterior a partir do seu computador.

1. Assim que receber uma notificação de que o seu certificado é carregado com sucesso, selecione **Save**.

    ![Carregar certificado](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   O seu certificado aparece na lista de certificados com estatuto de **Não verificado**.

1. Selecione o certificado que acabou de adicionar para exibir **Detalhes do Certificado**e, em seguida, selecione Generate Verification **Code**.

   ![Verificar certificado](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Copie o Código de **Verificação** para a pasta. Usa-o para validar a propriedade do certificado.

1. Siga o passo 3 na gestão dos [certificados CA de teste para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Este processo assina o seu código de verificação com a chave privada associada ao seu certificado De Ca X.509, que gera uma assinatura. Existem ferramentas disponíveis para realizar este processo de assinatura, por exemplo, openSSL. Este processo é conhecido como a [Prova de Posse.](https://tools.ietf.org/html/rfc5280#section-3.1)

1. Em **Detalhes do Certificado,** em Certificado de **Verificação .pem ou .cer file,** encontre e abra o ficheiro de assinatura. Em seguida, selecione **Verificar**.

   O estado do seu certificado altera-se a **Verificado**. Selecione **Refresh** se o certificado não atualizar automaticamente.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Crie um dispositivo X.509 para o seu hub IoT

1. No portal Azure, navegue até ao seu hub IoT e, em seguida, selecione **Explorers** > **dispositivos IoT**.

1. Selecione **Novo** para adicionar um novo dispositivo.

1. No **ID do dispositivo,** introduza um nome de exibição amigável. Para o tipo de **autenticação,** escolha **X.509 CA Assinado,** e, em seguida, selecione **Guardar**.

   ![Crie o dispositivo X.509 no portal](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticar o seu dispositivo X.509 com os certificados X.509

Para autenticar o seu dispositivo X.509, tem de assinar primeiro o dispositivo com o certificado CA. A assinatura de dispositivos de folhas é normalmente feita na fábrica, onde as ferramentas de fabrico foram ativadas em conformidade. À medida que o dispositivo vai de um fabricante para outro, a ação de assinatura de cada fabricante é capturada como um certificado intermédio dentro da cadeia. O resultado é uma cadeia de certificados do certificado CA para o certificado de folha do dispositivo. Passo 4 na gestão dos certificados ca de [teste para amostras e tutoriais](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) gera um certificado de dispositivo.

Em seguida, vamos mostrar-lhe C# como criar uma aplicação para simular o dispositivo X.509 registado para o seu hub IoT. Enviaremos valores de temperatura e humidade do dispositivo simulado para o seu centro. Neste tutorial, criaremos apenas a aplicação do dispositivo. É deixado como um exercício para os leitores criar a aplicação de serviço IoT Hub que enviará resposta aos eventos enviados por este dispositivo simulado. O C# pedido pressupõe que seguiu os passos na Gestão dos certificados ca de [teste para amostras e tutoriais.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)

1. Open Visual Studio, selecione **Criar um novo projeto,** e depois escolher o modelo de projeto **da App consola (.NET Framework).** Selecione **Seguinte**.

1. Em **Configurar o seu novo projeto,** nomeie o projeto *Simular O Dispositivo Simulax509*, e depois selecione **Criar**.

   ![Criar projeto de dispositivo X.509 no Estúdio Visual](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. No Solution Explorer, clique no projeto **SimulateX509Device** e, em seguida, selecione **Gerir pacotes NuGet**.

1. No **NuGet Package Manager,** **selecione Procurar** e escolha **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   ![Adicione o pacote SDK NuGet do dispositivo no Estúdio Visual](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do dispositivo Azure IoT e às suas dependências.

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Adicione os seguintes campos à classe **Programa:**

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Utilize o nome amigável do dispositivo utilizado na secção anterior no lugar de _<your_device_id>_

1. Adicione a seguinte função para criar números aleatórios para temperatura e humidade e envie estes valores para o centro:

    ```csharp
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

1. Por fim, adicione as seguintes linhas de código à função **Principal,** substituindo o _dispositivo_placeholders, _o seu nome-hub iot_e o ficheiro _absolute-path-to-your-device-pfx-file,_ conforme exigido pela sua configuração.

    ```csharp
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

   Este código liga-se ao seu hub IoT criando a cadeia de ligação para o seu dispositivo X.509. Uma vez ligado com sucesso, envia eventos de temperatura e humidade para o centro, e aguarda a sua resposta.

1. Executar a aplicação. Uma vez que esta aplicação acede a um ficheiro *.pfx,* poderá ter de executar esta aplicação como administrador.

   1. Construa a solução Visual Studio.

   1. Abra uma nova janela Command Prompt utilizando **run como administrador**.  

   1. Navegue para a pasta que contém a sua solução e, em seguida, navegue para o caminho *do bin/Debug* dentro da pasta de solução.

   1. Executar a aplicação **Simulax509Device.exe** a partir do pedido de comando.

   Deve ver o seu dispositivo conectando-se com sucesso ao centro e enviando os eventos.

   ![Executar aplicativo de dispositivo](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como garantir a sua solução IoT, consulte:

* [Boas Práticas de Segurança IoT](../iot-fundamentals/iot-security-best-practices.md)

* [Arquitetura de Segurança IoT](../iot-fundamentals/iot-security-architecture.md)

* [Proteger a sua implementação de IoT](../iot-fundamentals/iot-security-deployment.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementação de IA para dispositivos de borda com Borda Azure IoT](../iot-edge/tutorial-simulate-device-linux.md)
