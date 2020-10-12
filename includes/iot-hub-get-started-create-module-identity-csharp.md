---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883791"
---
## <a name="create-a-module-identity"></a>Criar uma identidade de módulo

Nesta secção, cria-se uma aplicação de consola .NET que cria uma identidade do dispositivo e uma identidade de módulo no registo de identidade no seu hub. Um dispositivo ou módulo não pode ligar-se ao hub a menos que tenha uma entrada no registo de identidade. Para obter mais informações, veja a secção [Identity Registry (Registo de Identidades) do Hub IoT developer guide (Guia do programador do Hub IoT)](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. O seu dispositivo e módulo utilizam estes valores para se identificarem quando envia mensagens dispositivo-a-nuvem para o IoT Hub. Os IDs são sensíveis às maiúsculas e minúsculas.

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, selecione Console App **(.NET Framework)**.

1. Selecione **Next** para abrir **o seu novo projeto**. Atribua ao projeto o nome *CreateIdentities* e atribua à solução o nome *IoTHubGetStarted*. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework.

    ![Insira o nome e a estrutura para a sua solução visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. No Estúdio Visual, open **Tools**  >  **NuGet Package Manager**  >  **Gerencie pacotes nuget para solução**. Selecione o separador **Procurar**.

1. Procurar **dispositivos Microsoft.Azure..** Selecione-o e, em seguida, **selecione Instalar**.

    ![Instalar a versão atual do serviço SDK do Azure IoT .NET](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Adicione as `using` seguintes declarações no topo do ficheiro **Program.cs:**

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Adicione os seguintes campos à classe **Programa**. Substitua o valor do marcador de posição pela cadeia de ligação do Hub IoT para o hub que criou na secção anterior.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Adicione o seguinte código à classe **Principal.**

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Adicione os seguintes métodos à classe **Programa**:

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

    O `AddDeviceAsync` método cria uma identidade do dispositivo com iD **myFirstDevice**. Se esse ID do dispositivo já existir no registo de identidade, o código simplesmente recupera as informações existentes do dispositivo. De seguida, a aplicação irá apresentar a chave primária para essa identidade. Utilize esta chave na aplicação do dispositivo simulado para se ligar ao seu hub.

    O `AddModuleAsync` método cria uma identidade de módulo com ID **myFirstModule** sob o dispositivo **myFirstDevice**. Se esse ID do módulo já existir no registo de identidade, o código simplesmente recupera as informações do módulo existentes. De seguida, a aplicação irá apresentar a chave primária para essa identidade. Utilize esta chave na aplicação do módulo simulado para se ligar ao seu hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Executar esta aplicação e tomar nota da chave do dispositivo e da chave do módulo.

> [!NOTE]
> O registo de identidade IoT Hub apenas armazena identidades de dispositivos e módulos para permitir um acesso seguro ao hub. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se a sua aplicação necessitar de armazenar outros metadados específicos do dispositivo, deverá utilizar uma loja específica para aplicações. Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, veja o [IoT Hub developer guide (Guia do programador do Hub IoT)](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
