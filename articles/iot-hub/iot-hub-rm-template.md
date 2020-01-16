---
title: Criar um hub IoT do Azure usando um modelo (.NET) | Microsoft Docs
description: Como usar um modelo de Azure Resource Manager para criar um hub IoT com um C# programa.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976738"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Criar um hub IoT usando o modelo de Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Você pode usar Azure Resource Manager para criar e gerenciar hubs IoT do Azure programaticamente. Este tutorial mostra como usar um modelo de Azure Resource Manager para criar um hub IoT a partir de C# um programa.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Azure Resource Manager e clássico](../azure-resource-manager/management/deployment-models.md).  Este artigo aborda o uso do modelo de implantação Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Uma [conta de armazenamento do Azure][lnk-storage-account] em que você pode armazenar seus arquivos de modelo de Azure Resource Manager.
* [Azure PowerShell 1,0][lnk-powershell-install] ou posterior.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um projeto C# de área de trabalho clássica do Visual Windows usando o modelo de projeto **aplicativo de console (.NET Framework)** . Nomeie o projeto **CreateIoTHub**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e clique em **gerenciar pacotes NuGet**.

3. No Gerenciador de pacotes NuGet, marque **incluir pré-lançamento**e na página **procurar** por **Microsoft. Azure. Management. ResourceManager**. Selecione o pacote, clique em **instalar**, em **examinar alterações** clique em **OK**e, em seguida, clique em **aceito** para aceitar as licenças.

4. No Gerenciador de pacotes NuGet, procure **Microsoft. IdentityModel. clients. ActiveDirectory**.  Clique em **instalar**, em **examinar alterações** clique em **OK**e, em seguida, clique em **aceito** para aceitar a licença.

5. No Program.cs, substitua as instruções **using** existentes pelo seguinte código:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. No Program.cs, adicione as seguintes variáveis estáticas, substituindo os valores de espaço reservado. Você fez uma observação de **ApplicationId**, **SubscriptionId**, **tenantid**e **senha** anteriormente neste tutorial. **O nome da conta de armazenamento do Azure** é o nome da conta de armazenamento do Azure em que você armazena seus arquivos de modelo de Azure Resource Manager. **Nome do grupo de recursos** é o nome do grupo de recursos que você usa ao criar o Hub IOT. O nome pode ser um grupo de recursos já existente ou um novo. **Nome da implantação** é um nome para a implantação, como **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Enviar um modelo para criar um hub IoT

Use um modelo JSON e um arquivo de parâmetro para criar um hub IoT em seu grupo de recursos. Você também pode usar um modelo de Azure Resource Manager para fazer alterações em um hub IoT existente.

1. Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto, clique em **Adicionar**e em **novo item**. Adicione um arquivo JSON chamado **Template. JSON** ao seu projeto.

2. Para adicionar um hub IoT padrão à região **leste dos EUA** , substitua o conteúdo de **Template. JSON** pela definição de recurso a seguir. Para obter a lista atual de regiões que dão suporte ao Hub IoT, consulte [status do Azure][lnk-status]:

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

3. Em Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto, clique em **Adicionar**e em **novo item**. Adicione um arquivo JSON chamado **Parameters. JSON** ao seu projeto.

4. Substitua o conteúdo de **Parameters. JSON** pelas seguintes informações de parâmetro que definem um nome para o novo hub IOT, como **{suas iniciais} mynewiothub**. O nome do Hub IoT deve ser globalmente exclusivo, portanto, ele deve incluir seu nome ou suas iniciais:

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

5. No **Gerenciador de servidores**, conecte-se à sua assinatura do Azure e, em sua conta de armazenamento do Azure, crie um contêiner chamado **modelos**. No painel **Propriedades** , defina as permissões de **acesso de leitura público** para o contêiner **modelos** como **blob**.

6. Em **Gerenciador de servidores**, clique com o botão direito do mouse no contêiner **modelos** e clique em **Exibir contêiner de BLOBs**. Clique no botão **carregar blob** , selecione os dois arquivos, **Parameters. JSON** e **templates. JSON**e, em seguida, clique em **abrir** para carregar os arquivos JSON para o contêiner **modelos** . As URLs dos BLOBs que contêm os dados JSON são:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adicione o seguinte método a Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adicione o seguinte código ao método **CreateIoTHub** para enviar os arquivos de modelo e parâmetro para o Azure Resource Manager:

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

9. Adicione o seguinte código ao método **CreateIoTHub** que exibe o status e as chaves para o novo hub IOT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Concluir e executar o aplicativo

Agora você pode concluir o aplicativo chamando o método **CreateIoTHub** antes de criá-lo e executá-lo.

1. Adicione o seguinte código ao final do método **Main** :

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Clique em **Compilar** e em **Compilar solução**. Corrija os erros.

3. Clique em **depurar** e **inicie a depuração** para executar o aplicativo. Pode levar vários minutos para a implantação ser executada.

4. Para verificar se o aplicativo adicionou o novo hub IoT, visite a [portal do Azure][lnk-azure-portal] e exiba sua lista de recursos. Como alternativa, use o cmdlet **Get-AzResource** do PowerShell.

> [!NOTE]
> Este aplicativo de exemplo adiciona um hub IoT padrão S1 para o qual você é cobrado. Você pode excluir o Hub IoT por meio do [portal do Azure][lnk-azure-portal] ou usando o cmdlet **Remove-AzResource** do PowerShell quando terminar.

## <a name="next-steps"></a>Passos seguintes
Agora que você implantou um hub IoT usando um modelo de C# Azure Resource Manager com um programa, talvez você queira explorar ainda mais:

* Leia sobre os recursos da [API REST do provedor de recursos do Hub IOT][lnk-rest-api].
* Leia [Azure Resource Manager visão geral][lnk-azure-rm-overview] para saber mais sobre os recursos do Azure Resource Manager.
* Para obter a sintaxe JSON e as propriedades a serem usadas em modelos, consulte [tipos de recurso Microsoft. Devices](/azure/templates/microsoft.devices/iothub-allversions).

Para saber mais sobre como desenvolver para o Hub IoT, consulte os seguintes artigos:

* [Introdução ao SDK do C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar ainda mais os recursos do Hub IoT, consulte:

* [Implantando ia em dispositivos de borda com Azure IoT Edge][lnk-iotedge]

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
