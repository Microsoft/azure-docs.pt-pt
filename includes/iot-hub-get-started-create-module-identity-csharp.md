---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883791"
---
## <a name="create-a-module-identity"></a>Criar uma identidade de módulo

Nesta seção, você cria um aplicativo de console .NET que cria uma identidade de dispositivo e uma identidade de módulo no registro de identidade em seu hub. Um dispositivo ou módulo não pode se conectar ao Hub, a menos que ele tenha uma entrada no registro de identidade. Para obter mais informações, consulte a [seção registro de identidade do guia do desenvolvedor do Hub IOT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Ao executar esta aplicação de consola, será gerado um ID e uma chave exclusivos para o dispositivo e o módulo. Seu dispositivo e módulo usam esses valores para se identificar quando enviam mensagens do dispositivo para a nuvem para o Hub IoT. Os IDs são sensíveis às maiúsculas e minúsculas.

1. Abra o Visual Studio e selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)** .

1. Selecione **Avançar** para abrir **configurar seu novo projeto**. Atribua ao projeto o nome *CreateIdentities* e atribua à solução o nome *IoTHubGetStarted*. Certifique-se de que tem a versão 4.6.1 ou posterior do .NET Framework.

    ![Insira o nome e a estrutura da sua solução do Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. No Visual Studio, abra **ferramentas** > **Gerenciador** > de pacotes NuGet**gerenciar pacotes NuGet para solução**. Selecione o separador **Procurar**.

1. Pesquise **Microsoft. Azure.** Devices. Selecione-o e, em seguida, selecione **instalar**.

    ![Instalar a versão atual do SDK do serviço .NET do Hub IoT do Azure](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

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

1. Adicione o código a seguir à classe **principal** .

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

    O `AddDeviceAsync` método cria uma identidade de dispositivo com a ID **myFirstDevice**. Se essa ID de dispositivo já existir no registro de identidade, o código simplesmente recuperará as informações do dispositivo existente. De seguida, a aplicação irá apresentar a chave primária para essa identidade. Você usa essa chave no aplicativo de dispositivo simulado para se conectar ao seu hub.

    O `AddModuleAsync` método cria uma identidade de módulo com a ID **myFirstModule** em Device **myFirstDevice**. Se essa ID de módulo já existir no registro de identidade, o código simplesmente recuperará as informações de módulo existentes. De seguida, a aplicação irá apresentar a chave primária para essa identidade. Você usa essa chave no aplicativo de módulo simulado para se conectar ao seu hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Execute este aplicativo e anote a chave do dispositivo e a chave do módulo.

> [!NOTE]
> O registro de identidade do Hub IoT armazena somente identidades de dispositivo e de módulo para habilitar o acesso seguro ao Hub. O registo de identidades armazena os IDs de dispositivo e as chaves para utilizar como credenciais de segurança. O registo de identidades também armazena um sinalizador ativado/desativado para cada dispositivo que pode utilizar para desativar o acesso a esse dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não existe nenhum sinalizador ativado/desativado para identidades de módulo. Para obter mais informações, consulte [Guia do desenvolvedor do Hub IOT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
