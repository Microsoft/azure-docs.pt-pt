---
title: Criar um hub Azure IoT utilizando o fornecedor de recursos REST API [ Microsoft Docs
description: Aprenda a usar o fornecedor de recursos C# REST API para criar e gerir um IoT Hub programáticamente.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429163"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um hub IoT utilizando o fornecedor de recursos REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar o fornecedor de [recursos IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) para criar e gerir os hubs Azure IoT programáticamente. Este tutorial mostra-lhe como usar o fornecedor de recursos IoT Hub REST API para criar um hub IoT a partir de um programa C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou mais tarde.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Prepare o seu projeto Estúdio Visual

1. No Estúdio Visual, crie um projeto visual C# Windows Classic Desktop utilizando o modelo de projeto **console App (.NET Framework).** Nomeie o projeto **CreateIoTHubREST**.

2. No Solution Explorer, clique à direita no seu projeto e, em seguida, clique em **Gerir pacotes NuGet**.

3. No NuGet Package Manager, consulte **Incluir pré-lançamento**, e na página **Browse** pesquisa para **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **Instalar**, em **Rever Alterações** clique **OK**e clique **em aceitar** as licenças.

4. No NuGet Package Manager, procure **microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **Instalar**, em **Rever Alterações** clique **OK**e clique **em aceitar** a licença.

5. Em Program.cs, substitua as declarações **existentes utilizando** o seguinte código:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Em Program.cs, adicione as seguintes variáveis estáticas substituindo os valores do espaço reservado. Fez uma nota de **ApplicationId,** **SubscriptionId,** **TenantId**e **Password** no início deste tutorial. **O nome** do grupo de recursos é o nome do grupo de recursos que usa quando cria o hub IoT. Pode utilizar um grupo de recursos pré-existente ou um novo grupo de recursos. **O nome IoT Hub** é o nome do IoT Hub que cria, como o **MyIoTHub.** O nome do seu centro ioT deve ser globalmente único. O nome de **implantação** é um nome para a implantação, como **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Use o fornecedor de recursos REST API para criar um hub IoT

Utilize o fornecedor de [recursos IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) para criar um hub IoT no seu grupo de recursos. Também pode utilizar o fornecedor de recursos REST API para fazer alterações num hub IoT existente.

1. Adicione o seguinte método para Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o seguinte código ao método **CreateIoTHub.** Este código cria um objeto **HttpClient** com o símbolo de autenticação nos cabeçalhos:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código ao método **CreateIoTHub.** Este código descreve o centro IoT para criar e gera uma representação JSON. Para a lista atual de locais que suportam o IoT Hub consulte [o Estado do Azure:](https://azure.microsoft.com/status/)

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código ao método **CreateIoTHub.** Este código submete o pedido de REST ao Azure. O código verifica então a resposta e recupera o URL que pode utilizar para monitorizar o estado da tarefa de implantação:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o seguinte código ao fim do método **CreateIoTHub.** Este código utiliza o endereço **asyncStatusUri** recuperado no passo anterior para esperar que a implementação esteja concluída:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código ao fim do método **CreateIoTHub.** Este código recupera as chaves do hub IoT que criou e imprime-as para a consola:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Complete e executar a aplicação

Agora pode completar a aplicação ligando para o método **CreateIoTHub** antes de construí-la e executá-la.

1. Adicione o seguinte código ao fim do método **Principal:**

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique em **Construir** e, em seguida, **construir solução**. Corrija todos os erros.

3. Clique em **Debug** e, em seguida, **comece a depurar** para executar a aplicação. Pode levar alguns minutos para o destacamento funcionar.

4. Para verificar se a sua aplicação adicionou o novo hub IoT, visite o [portal Azure](https://portal.azure.com/) e veja a sua lista de recursos. Em alternativa, utilize o cmdlet **Get-AzResource** PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona um Hub IoT Padrão S1 para o qual você é cobrado. Quando terminar, pode eliminar o hub IoT através do [portal Azure](https://portal.azure.com/) ou utilizando o cmdlet **Remove-AzResource** PowerShell quando terminar.

## <a name="next-steps"></a>Passos seguintes

Agora implementou um hub IoT usando o fornecedor de recursos REST API, você pode querer explorar mais:

* Leia sobre as capacidades do fornecedor de [recursos IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Leia a [visão geral do Gestor de Recursos azure](../azure-resource-manager/management/overview.md) para saber mais sobre as capacidades do Gestor de Recursos Azure.

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os seguintes artigos:

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)