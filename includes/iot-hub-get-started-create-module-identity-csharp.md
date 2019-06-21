---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184912"
---
## <a name="create-a-module-identity"></a>Criar uma identidade de módulo

Nesta secção, vai criar uma aplicação de consola .NET que cria uma identidade de dispositivo e uma identidade de módulo no registo de identidade do seu hub IoT. Não é possível ligar um dispositivo ou módulo ao hub IoT, exceto se tiver uma entrada no registo de identidade. Para obter mais informações, consulte a [secção de registo de identidade do Guia do programador do IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O dispositivo e o módulo utilizam estes valores para se identificarem quando enviam mensagens do dispositivo para cloud para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.


1. **Crie um projeto do Visual Studio** - No Visual Studio, adicione um projeto Visual C# - Windows Classic Desktop à nova solução ao utilizar o modelo de projeto **Console App (.NET Framework)** . Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework. Atribua ao projeto o nome **CreateIdentities** e atribua à solução o nome **IoTHubGetStarted**.

    ![Criar uma solução do Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Instale o SDK de serviço .NET do Hub IoT do Azure V1.16.0-preview-001** - O módulo de identidade e o módulo duplo encontram-se em modo de pré-visualização pública. Só está disponível em SDKs de pré-lançamento do serviço IoT Hub. No Visual Studio, abra tools > Nuget package manager > manage Nuget packages for solution. Procure Microsoft.Azure.Devices. Certifique-se de que selecionou a caixa de verificação Include prerelease. Selecione a versão 1.16.0-preview-001 e instale. Agora, tem acesso a todas as funcionalidades de módulo. 

    ![Instalar o SDK de serviço .NET do Hub IoT do Azure V1.16.0-preview-001](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Adicione o seguinte código para o **Main** classe.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Adicione os seguintes métodos à classe **Programa**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    O método AddDeviceAsync() cria uma identidade de dispositivo com o ID **myFirstDevice**. (Se o ID desse dispositivo já existir no registo de identidade, o código apenas obtém as informações do dispositivo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do dispositivo simulado para ligar ao seu hub IoT.

    O método AddModuleAsync() cria uma identidade de módulo com o ID **myFirstModule** no dispositivo **myFirstDevice**. (Se o ID desse módulo já existir no registo de identidade, o código apenas obtém as informações do módulo existente.) De seguida, a aplicação irá apresentar a chave primária para essa identidade. Esta chave vai ser utilizada na aplicação do módulo simulado para ligar ao seu hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Execute esta aplicação e tome nota da chave do dispositivo e do módulo.

> [!NOTE]
> O registo de identidade do Hub IoT apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub IoT. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação tiver de armazenar outros metadados específicos do dispositivo, deverá utilizar um armazenamento específico da aplicação. Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, consulte [Guia do programador do IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
