---
title: Criar um hub de IoT do Azure com a API de REST do fornecedor de recursos | Documentos da Microsoft
description: Como utilizar a API de REST do fornecedor de recursos para criar um IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 7ace4c1827b340f6247d69902538fdaaf1243106
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727796"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Criar um hub IoT com o fornecedor de recursos REST API (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o [REST API do fornecedor de recursos de IoT Hub] [ lnk-rest-api] para criar e gerir hubs IoT do Azure através de programação. Este tutorial mostra-lhe como utilizar a API de REST do fornecedor de recursos de IoT Hub para criar um hub IoT a partir de um programa c#.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [O Azure Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [O Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows ambiente de trabalho clássico com o **aplicação de consola (.NET Framework)** modelo de projeto. Nomeie o projeto **CreateIoTHubREST**.

2. No Explorador de soluções, faça duplo clique no seu projeto e, em seguida, clique em **gerir pacotes NuGet**.

3. No Gestor de pacotes do NuGet, verifique **incluir pré-lançamento**e, no **procurar** procurar na página **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **instale**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gestor de pacotes do NuGet, procure **ActiveDirectory**.  Clique em **instale**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar a licença.

5. Em Program.cs, substitua o existente **usando** instruções com o código a seguir:

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

6. Em Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de marcador de posição. Anotou **ApplicationId**, **SubscriptionId**, **TenantId**, e **palavra-passe** anteriores neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que utilizar ao criar o hub IoT. Pode usar pré-existente ou um grupo de recursos. **Nome do IoT Hub** é o nome do IoT Hub, criar, como **MyIoTHub**. O nome do IoT hub tem de ser globalmente exclusivo. **Nome da implementação** é um nome para a implementação, tais como **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Utilize a API de REST do fornecedor de recursos para criar um hub IoT

Utilize o [REST API do fornecedor de recursos de IoT Hub] [ lnk-rest-api] para criar um hub IoT no seu grupo de recursos. Também pode utilizar a API de REST do fornecedor de recursos para fazer alterações para um hub IoT.

1. Adicione o seguinte método à Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Adicione o seguinte código para o **CreateIoTHub** método. Este código cria um **HttpClient** objeto com o token de autenticação nos cabeçalhos de:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código para o **CreateIoTHub** método. Esse código descreve o hub IoT para criar e gera uma representação JSON. Para obter a lista atual de localizações que suportam o IoT Hub, veja [estado do Azure][lnk-status]:

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

4. Adicione o seguinte código para o **CreateIoTHub** método. Este código envia a solicitação REST para o Azure. O código, em seguida, verifica a resposta e obtém o URL que pode utilizar para monitorizar o estado da tarefa de implementação:

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

5. Adicione o seguinte código ao final dos **CreateIoTHub** método. Este código utiliza a **asyncStatusUri** endereço obtidos no passo anterior para aguardar a conclusão da implementação:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Adicione o seguinte código ao final dos **CreateIoTHub** método. Este código obtém as chaves do hub IoT que criou e imprime-los para a consola:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Pode concluir a aplicação ao chamar o **CreateIoTHub** método antes da compilação e executá-lo.

1. Adicione o seguinte código ao final dos **Main** método:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Clique em **crie** e, em seguida **criar solução**. Corrigir os eventuais erros.

3. Clique em **depurar** e, em seguida **iniciar depuração** para executar a aplicação. Pode demorar alguns minutos até que a implementação para execução.

4. Para verificar se seu aplicativo adicionado o novo hub IoT, visite o [portal do Azure] [ lnk-azure-portal] e ver a sua lista de recursos. Em alternativa, utilize o **Get-AzResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard do IoT Hub que lhe é cobrada. Quando tiver terminado, pode eliminar o hub IoT através do [portal do Azure] [ lnk-azure-portal] ou utilizando o **Remove-AzResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos Seguintes
Agora que implementou um hub IoT com a API de REST do fornecedor de recursos, pode querer explorar ainda mais:

* Leia sobre as capacidades do [REST API do fornecedor de recursos de IoT Hub][lnk-rest-api].
* Leia [descrição geral do Azure Resource Manager] [ lnk-azure-rm-overview] para saber mais sobre as capacidades do Azure Resource Manager.

Para saber mais sobre o desenvolvimento para o IoT Hub, veja os artigos seguintes:

* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
