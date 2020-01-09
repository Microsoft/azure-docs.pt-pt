---
title: Criar um hub IoT do Azure usando a API REST do provedor de recursos | Microsoft Docs
description: Saiba como usar a API REST do C# provedor de recursos para criar e gerenciar um hub IOT programaticamente.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429163"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um hub IoT usando a API REST do provedor de recursos (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar a [API REST do provedor de recursos do Hub IOT](https://docs.microsoft.com/rest/api/iothub/iothubresource) para criar e gerenciar hubs IOT do Azure programaticamente. Este tutorial mostra como usar a API REST do provedor de recursos do Hub IoT para criar um hub IoT a C# partir de um programa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.

* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Azure PowerShell 1,0](https://docs.microsoft.com/powershell/azure/install-Az-ps) ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um projeto C# de área de trabalho clássica do Visual Windows usando o modelo de projeto **aplicativo de console (.NET Framework)** . Nomeie o projeto **CreateIoTHubREST**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e clique em **gerenciar pacotes NuGet**.

3. No Gerenciador de pacotes NuGet, marque **incluir pré-lançamento**e na página **procurar** por **Microsoft. Azure. Management. ResourceManager**. Selecione o pacote, clique em **instalar**, em **examinar alterações** clique em **OK**e, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gerenciador de pacotes NuGet, procure **Microsoft. IdentityModel. clients. ActiveDirectory**.  Clique em **instalar**, em **examinar alterações** clique em **OK**e, em seguida, clique em **aceito** para aceitar a licença.

5. No Program.cs, substitua as instruções **using** existentes pelo seguinte código:

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

6. No Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de espaço reservado. Você fez uma observação de **ApplicationId**, **SubscriptionId**, **tenantid**e **senha** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa ao criar o Hub IOT. Você pode usar um grupo de recursos já existente ou um novo. **Nome do Hub IOT** é o nome do Hub IOT que você cria, como **MyIoTHub**. O nome do Hub IoT deve ser globalmente exclusivo. **Nome da implantação** é um nome para a implantação, como **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Usar a API REST do provedor de recursos para criar um hub IoT

Use a [API REST do provedor de recursos do Hub IOT](https://docs.microsoft.com/rest/api/iothub/iothubresource) para criar um hub IOT em seu grupo de recursos. Você também pode usar a API REST do provedor de recursos para fazer alterações em um hub IoT existente.

1. Adicione o seguinte método a Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o código a seguir ao método **CreateIoTHub** . Esse código cria um objeto **HttpClient** com o token de autenticação nos cabeçalhos:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o código a seguir ao método **CreateIoTHub** . Esse código descreve o Hub IoT para criar e gerar uma representação JSON. Para obter a lista atual de locais que dão suporte ao Hub IoT, consulte [status do Azure](https://azure.microsoft.com/status/):

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

4. Adicione o código a seguir ao método **CreateIoTHub** . Esse código envia a solicitação REST para o Azure. Em seguida, o código verifica a resposta e recupera a URL que você pode usar para monitorar o estado da tarefa de implantação:

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

5. Adicione o código a seguir ao final do método **CreateIoTHub** . Esse código usa o endereço **asyncStatusUri** recuperado na etapa anterior para aguardar a conclusão da implantação:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o código a seguir ao final do método **CreateIoTHub** . Esse código recupera as chaves do Hub IoT que você criou e as imprime no console:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar o aplicativo

Agora você pode concluir o aplicativo chamando o método **CreateIoTHub** antes de criá-lo e executá-lo.

1. Adicione o seguinte código ao final do método **Main** :

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique em **Compilar** e em **Compilar solução**. Corrija os erros.

3. Clique em **depurar** e **inicie a depuração** para executar o aplicativo. Pode levar vários minutos para a implantação ser executada.

4. Para verificar se o aplicativo adicionou o novo hub IoT, visite a [portal do Azure](https://portal.azure.com/) e exiba sua lista de recursos. Como alternativa, use o cmdlet **Get-AzResource** do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um hub IoT padrão S1 para o qual você é cobrado. Quando tiver terminado, você poderá excluir o Hub IoT por meio da [portal do Azure](https://portal.azure.com/) ou usando o cmdlet **Remove-AzResource** do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou um hub IoT usando a API REST do provedor de recursos, talvez você queira explorar ainda mais:

* Leia sobre os recursos da [API REST do provedor de recursos do Hub IOT](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Leia [Azure Resource Manager visão geral](../azure-resource-manager/management/overview.md) para saber mais sobre os recursos do Azure Resource Manager.

Para saber mais sobre como desenvolver para o Hub IoT, consulte os seguintes artigos:

* [Introdução ao SDK do C](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Implantando ia em dispositivos de borda com Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)