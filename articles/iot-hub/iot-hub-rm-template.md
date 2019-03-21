---
title: Criar um IoT Hub do Azure através de um modelo (.NET) | Documentos da Microsoft
description: Como utilizar um modelo Azure Resource Manager para criar um IoT Hub com um programa c#.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 4acf7a8bbc02a10468004092152b1d7c5c4d621f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075923"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Criar um hub IoT com o modelo Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o Azure Resource Manager para criar e gerir hubs IoT do Azure através de programação. Este tutorial mostra-lhe como utilizar um modelo Azure Resource Manager para criar um hub IoT a partir de um programa c#.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [O Azure Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo explica como utilizar o modelo de implementação Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Uma [conta de armazenamento do Azure] [ lnk-storage-account] onde pode armazenar os ficheiros de modelo do Azure Resource Manager.
* [O Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar o projeto do Visual Studio

1. No Visual Studio, crie um projeto do Visual c# Windows ambiente de trabalho clássico com o **aplicação de consola (.NET Framework)** modelo de projeto. Nomeie o projeto **CreateIoTHub**.

2. No Explorador de soluções, faça duplo clique no seu projeto e, em seguida, clique em **gerir pacotes NuGet**.

3. No Gestor de pacotes do NuGet, verifique **incluir pré-lançamento**e, no **procurar** procurar na página **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **instale**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gestor de pacotes do NuGet, procure **ActiveDirectory**.  Clique em **instale**, na **rever alterações** clique **OK**, em seguida, clique em **aceito** para aceitar a licença.

5. Em Program.cs, substitua o existente **usando** instruções com o código a seguir:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Em Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de marcador de posição. Anotou **ApplicationId**, **SubscriptionId**, **TenantId**, e **palavra-passe** anteriores neste tutorial. **O nome da sua conta de armazenamento do Azure** é o nome da conta do Storage do Azure onde armazena os ficheiros de modelo do Azure Resource Manager. **Nome do grupo de recursos** é o nome do grupo de recursos que utilizar ao criar o hub IoT. O nome pode ser um grupo de recursos novo ou já existente. **Nome da implementação** é um nome para a implementação, tais como **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Submeter um modelo para criar um hub IoT

Utilize um ficheiro de modelo e o parâmetro JSON para criar um hub IoT no grupo de recursos. Também pode utilizar um modelo Azure Resource Manager para efetuar alterações a um hub IoT.

1. No Explorador de soluções, faça duplo clique no seu projeto, clique em **Add**e, em seguida, clique em **Novo Item**. Adicionar um ficheiro JSON chamado **Template** ao seu projeto.

2. Para adicionar uma standard do hub IoT para o **E.U.A. Leste** região, substitua o conteúdo do **Template** com a seguinte definição de recurso. Para obter a lista atual de regiões que suportam o IoT Hub, veja [estado do Azure][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. No Explorador de soluções, faça duplo clique no seu projeto, clique em **Add**e, em seguida, clique em **Novo Item**. Adicionar um ficheiro JSON chamado **Parameters. JSON** ao seu projeto.

4. Substitua o conteúdo do **Parameters. JSON** com as seguintes informações de parâmetro que define um nome para o novo hub IoT, tais como **{suas iniciais} mynewiothub**. O nome do hub IoT deve ser globalmente exclusivo, pelo que deve incluir o nome ou iniciais:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. Na **Explorador de servidores**, ligar à sua subscrição do Azure e na sua conta de armazenamento do Azure, criar um contentor chamado **modelos**. Na **propriedades** painel, defina o **acesso de leitura público** permissões para o **modelos** contentor para **Blob**.

6. No **Explorador de servidores**, clique com o botão direito no **modelos** contentor e, em seguida, clique **contentor de BLOBs de modo de exibição**. Clique no **carregar Blob** botão, selecione os dois arquivos **Parameters. JSON** e **templates.json**e, em seguida, clique em **aberto** para carregar o Ficheiros de JSON para o **modelos** contentor. Os URLs dos blobs que contém os dados JSON são:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adicione o seguinte método à Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adicione o seguinte código para o **CreateIoTHub** método para enviar os ficheiros de modelo e o parâmetro para o Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Adicione o seguinte código para o **CreateIoTHub** método que exibe o estado e as chaves do novo hub IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar a aplicação

Pode concluir a aplicação ao chamar o **CreateIoTHub** método antes da compilação e executá-lo.

1. Adicione o seguinte código ao final dos **Main** método:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Clique em **crie** e, em seguida **criar solução**. Corrigir os eventuais erros.

3. Clique em **depurar** e, em seguida **iniciar depuração** para executar a aplicação. Pode demorar alguns minutos até que a implementação para execução.

4. Para verificar a sua aplicação adicionada novo hub IoT, visite o [portal do Azure] [ lnk-azure-portal] e ver a sua lista de recursos. Em alternativa, utilize o **Get-AzResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard do IoT Hub que lhe é cobrada. Pode eliminar o hub IoT através da [portal do Azure] [ lnk-azure-portal] ou utilizando o **Remove-AzResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos Seguintes
Agora que implementou um hub IoT com um modelo Azure Resource Manager com um programa c#, pode querer explorar ainda mais:

* Leia sobre as capacidades do [REST API do fornecedor de recursos de IoT Hub][lnk-rest-api].
* Leia [descrição geral do Azure Resource Manager] [ lnk-azure-rm-overview] para saber mais sobre as capacidades do Azure Resource Manager.
* Para a sintaxe JSON e propriedades a utilizar em modelos, consulte [tipos de recursos de Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

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
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
