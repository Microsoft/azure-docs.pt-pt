---
title: Crie um hub Azure IoT utilizando o fornecedor de recursos REST API | Microsoft Docs
description: Saiba como utilizar o fornecedor de recursos C# REST API para criar e gerir programaticamente um Hub IoT.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144423"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um hub IoT utilizando o fornecedor de recursos REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar o [fornecedor de recursos IoT Hub REST API](/rest/api/iothub/iothubresource) para criar e gerir hubs Azure IoT programáticamente. Este tutorial mostra-lhe como usar o fornecedor de recursos IoT Hub REST API para criar um hub IoT a partir de um programa C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Azure PowerShell 1.0](/powershell/azure/install-Az-ps) ou mais tarde.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Prepare o seu projeto Visual Studio

1. No Visual Studio, crie um projeto visual C# Windows Classic Desktop utilizando o modelo de projeto de consola **(.NET Framework).** Nomeie o projeto **CreateIoTHubREST**.

2. No Solution Explorer, clique com o botão direito no seu projeto e, em seguida, clique em **Gerir Pacotes NuGet**.

3. No NuGet Package Manager, consulte **incluir pré-relançação,** e na pesquisa de página **Browse** para **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **Instalar,** em **Alterações de Revisão** clique em **OK** e clique em **I Accept** to accept the licenses.

4. No NuGet Package Manager, procure **microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **Instalar**, em **Alterações de Revisão** clique **EM OK** e clique em **I Accept** to accept the license.

5. No Programa.cs, substitua as declarações **existentes com** o seguinte código:

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

6. No Programa.cs, adicione as seguintes variáveis estáticas substituindo os valores do espaço reservado. Fez uma nota de **ApplicationId,** **SubscriptionId,** **TenantId** e **Password** no início deste tutorial. **O nome** do grupo de recursos é o nome do grupo de recursos que utiliza quando cria o hub IoT. Pode utilizar um grupo de recursos pré-existente ou novo. **IoT Hub é** o nome do IoT Hub que cria, como **MyIoTHub**. O nome do seu hub IoT deve ser globalmente único. **O nome de implantação** é um nome para a implantação, como **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Utilize o fornecedor de recursos REST API para criar um hub IoT

Utilize o [fornecedor de recursos IoT Hub REST API](/rest/api/iothub/iothubresource) para criar um hub IoT no seu grupo de recursos. Também pode utilizar o fornecedor de recursos REST API para escruquisar alterações num hub IoT existente.

1. Adicione o seguinte método ao Programa.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o seguinte código ao método **CreateIoTHub.** Este código cria um objeto **HttpClient** com o token de autenticação nos cabeçalhos:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código ao método **CreateIoTHub.** Este código descreve o hub IoT para criar e gerar uma representação JSON. Para a lista atual de locais que suportam ioT Hub ver [Estado de Azure:](https://azure.microsoft.com/status/)

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

4. Adicione o seguinte código ao método **CreateIoTHub.** Este código submete o pedido DE REST ao Azure. Em seguida, o código verifica a resposta e recupera o URL que pode utilizar para monitorizar o estado da tarefa de implantação:

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

5. Adicione o seguinte código ao fim do método **CreateIoTHub.** Este código utiliza o endereço **asyncStatusUri** recuperado no passo anterior para aguardar que a implementação esteja concluída:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código ao fim do método **CreateIoTHub.** Este código recupera as chaves do hub IoT que criou e imprime-as na consola:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Complete e execute a aplicação

Agora pode completar a aplicação ligando para o método **CreateIoTHub** antes de a construir e executar.

1. Adicione o seguinte código ao fim do método **Principal:**

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique **em Construir** e, em seguida, Construir **Solução**. Corrija quaisquer erros.

3. Clique em **Debug** e **comece a depurar** para executar a aplicação. Pode levar vários minutos para a colocação funcionar.

4. Para verificar se a sua aplicação adicionou o novo hub IoT, visite o [portal Azure](https://portal.azure.com/) e veja a sua lista de recursos. Em alternativa, utilize o **cmdlet Get-AzResource** PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona um Hub IoT Standard S1 para o qual está faturado. Quando terminar, pode eliminar o hub IoT através do [portal Azure](https://portal.azure.com/) ou utilizando o cmdlet **Remove-AzResource** PowerShell quando terminar.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou um hub IoT utilizando o fornecedor de recursos REST API, talvez queira explorar mais:

* Leia sobre as capacidades do fornecedor de [recursos IoT Hub REST API](/rest/api/iothub/iothubresource).

* Leia [a visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md) para saber mais sobre as capacidades do Azure Resource Manager.

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os seguintes artigos:

* [Introdução à C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)