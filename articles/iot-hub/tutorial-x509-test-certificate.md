---
title: Tutorial - Capacidade de teste de certificados X.509 para autenticar dispositivos num Hub Azure IoT | Microsoft Docs
description: Tutorial - Teste os seus certificados X.509 para autenticar no Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 91eea344914120a396ba9465ec504a37f5844d4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630707"
---
# <a name="tutorial-testing-certificate-authentication"></a>Tutorial: Autenticação de certificado de teste

Pode utilizar o seguinte exemplo de código C# para testar que o seu certificado pode autenticar o seu dispositivo no seu Hub IoT. Note que deve fazer o seguinte antes de executar o código de teste:

* Crie um certificado de CA de raiz ou ac subordinado.
* Faça o upload do seu certificado de CA para o seu IoT Hub.
* Prove que possui o certificado de aptidão.
* Adicione um dispositivo ao seu Hub IoT.
* Crie um certificado de dispositivo com o mesmo ID do dispositivo que o seu dispositivo.

## <a name="code-example"></a>Exemplo de código

O exemplo de código que se segue mostra como criar uma aplicação C# para simular o dispositivo X.509 registado no seu hub IoT. O exemplo envia valores de temperatura e humidade do dispositivo simulado para o seu hub. Neste tutorial, criaremos apenas a aplicação do dispositivo. É deixado como um exercício para os leitores criar a aplicação de serviço IoT Hub que enviará respostas aos eventos enviados por este dispositivo simulado.

1. Abra o Estúdio Visual, **selecione Criar um novo projeto** e, em seguida, escolha o modelo de projeto da App consola **(.NET Framework).** Selecione **Seguinte**.

1. Em **Configurar o seu novo projeto**, nomeie o projeto *SimulateX509Device* e, em seguida, selecione **Create**.

   ![Criar projeto de dispositivo X.509 no Estúdio Visual](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. No Solution Explorer, clique com o botão direito no projeto **SimulateX509Device** e, em seguida, **selecione Gerir pacotes NuGet**.

1. No Gestor de **Pacotes NuGet**, selecione **Procurar** e escolher **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

   ![Adicione o pacote SDK NuGet do dispositivo no Estúdio Visual](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Este passo descarrega, instala e adiciona uma referência ao pacote SDK NuGet do dispositivo Azure IoT e às suas dependências.

    Inserir e executar o seguinte código:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

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
         }
    }
}
```