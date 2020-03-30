---
title: Crie um Hub Azure IoT usando um modelo (.NET) [ Microsoft Docs
description: Como usar um modelo de Gestor de Recursos Azure para criar um Hub IoT com um programa C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976738"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Crie um hub IoT usando o modelo de Gestor de Recursos Azure (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o Azure Resource Manager para criar e gerir os hubs Azure IoT programáticamente. Este tutorial mostra-lhe como usar um modelo de Gestor de Recursos Azure para criar um hub IoT a partir de um programa C#.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e classic](../azure-resource-manager/management/deployment-models.md).  Este artigo cobre a utilização do modelo de implementação do Gestor de Recursos Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Uma [conta de Armazenamento Azure][lnk-storage-account] onde pode armazenar os seus ficheiros de modelo de Gestor de Recursos Azure.
* [Azure PowerShell 1.0][lnk-powershell-install] ou mais tarde.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Prepare o seu projeto Estúdio Visual

1. No Estúdio Visual, crie um projeto visual C# Windows Classic Desktop utilizando o modelo de projeto **console App (.NET Framework).** Nomeie o projeto **CreateIoTHub**.

2. No Solution Explorer, clique à direita no seu projeto e, em seguida, clique em **Gerir pacotes NuGet**.

3. No NuGet Package Manager, consulte **Incluir pré-lançamento**, e na página **Browse** pesquisa para **Microsoft.Azure.Management.ResourceManager**. Selecione o pacote, clique em **Instalar**, em **Rever Alterações** clique **OK**e clique **em aceitar** as licenças.

4. No NuGet Package Manager, procure **microsoft.IdentityModel.Clients.ActiveDirectory**.  Clique em **Instalar**, em **Rever Alterações** clique **OK**e clique **em aceitar** a licença.

5. Em Program.cs, substitua as declarações **existentes utilizando** o seguinte código:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Em Program.cs, adicione as seguintes variáveis estáticas substituindo os valores do espaço reservado. Fez uma nota de **ApplicationId,** **SubscriptionId,** **TenantId**e **Password** no início deste tutorial. O nome da **sua conta Azure Storage** é o nome da conta Azure Storage onde armazena os ficheiros do modelo Do Gestor de Recursos Azure. **O nome** do grupo de recursos é o nome do grupo de recursos que usa quando cria o hub IoT. O nome pode ser um grupo de recursos pré-existente ou novo. O nome de **implantação** é um nome para a implantação, como **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Submeta um modelo para criar um hub IoT

Utilize um modelo JSON e um ficheiro de parâmetro para criar um hub IoT no seu grupo de recursos. Também pode usar um modelo de Gestor de Recursos Azure para fazer alterações num hub IoT existente.

1. No Solution Explorer, clique à direita no seu projeto, clique em **Adicionar**, e depois clique em **Novo Item**. Adicione um ficheiro JSON chamado **template.json** ao seu projeto.

2. Para adicionar um hub IoT padrão à região **leste dos EUA,** substitua o conteúdo do **template.json** com a seguinte definição de recursos. Para a lista atual de regiões que suportam o Hub IoT ver [Estado Azure:][lnk-status]

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

3. No Solution Explorer, clique à direita no seu projeto, clique em **Adicionar**, e depois clique em **Novo Item**. Adicione um ficheiro JSON chamado **parâmetros.json** ao seu projeto.

4. Substitua o conteúdo dos **parâmetros.json** pelas seguintes informações de parâmetros que estabelecem um nome para o novo hub IoT, como **{as suas iniciais}mynewiothub**. O nome do hub IoT deve ser globalmente único, pelo que deve incluir o seu nome ou iniciais:

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

5. No **Server Explorer,** ligue-se à sua subscrição Azure e na sua conta de Armazenamento Azure crie um recipiente chamado **modelos**. No painel **Propriedades,** detete as permissões de acesso ao **acesso** ao público para o recipiente de **modelos** para **Blob**.

6. No **Server Explorer,** clique à direita no recipiente dos **modelos** e, em seguida, clique em **Ver Recipiente Blob**. Clique no botão **Upload Blob,** selecione os dois ficheiros, **parameters.json** e **templates.json**e, em seguida, clique em **Abrir** para carregar os ficheiros JSON para o recipiente de **modelos.** Os URLs das bolhas que contêm os dados jSON são:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adicione o seguinte método para Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adicione o seguinte código ao método **CreateIoTHub** para submeter o modelo e os ficheiros de parâmetros ao Gestor de Recursos Azure:

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

9. Adicione o seguinte código ao método **CreateIoTHub** que apresenta o estado e as teclas para o novo hub IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Complete e executar a aplicação

Agora pode completar a aplicação ligando para o método **CreateIoTHub** antes de construí-la e executá-la.

1. Adicione o seguinte código ao fim do método **Principal:**

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Clique em **Construir** e, em seguida, **construir solução**. Corrija todos os erros.

3. Clique em **Debug** e, em seguida, **comece a depurar** para executar a aplicação. Pode levar alguns minutos para o destacamento funcionar.

4. Para verificar se a sua aplicação adicionou o novo hub IoT, visite o [portal Azure][lnk-azure-portal] e veja a sua lista de recursos. Em alternativa, utilize o cmdlet **Get-AzResource** PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona um Hub IoT Padrão S1 para o qual você é cobrado. Pode eliminar o hub IoT através do [portal Azure][lnk-azure-portal] ou utilizando o cmdlet **Remove-AzResource** PowerShell quando terminar.

## <a name="next-steps"></a>Passos seguintes
Agora implementou um hub IoT usando um modelo de Gestor de Recursos Azure com um programa C#, você pode querer explorar mais:

* Leia sobre as capacidades do fornecedor de [recursos IoT Hub REST API][lnk-rest-api].
* Leia a [visão geral do Gestor de Recursos azure][lnk-azure-rm-overview] para saber mais sobre as capacidades do Gestor de Recursos Azure.
* Para que a sintaxe e as propriedades da JSON utilizem em modelos, consulte os tipos de [recursos da Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os seguintes artigos:

* [Introdução ao C SDK][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
