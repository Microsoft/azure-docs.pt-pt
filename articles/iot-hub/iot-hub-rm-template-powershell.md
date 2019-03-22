---
title: Criar um IoT Hub do Azure através de um modelo (PowerShell) | Documentos da Microsoft
description: Como utilizar um modelo Azure Resource Manager para criar um IoT Hub com o PowerShell.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c9a72bc435517241cf705660a669e9fb57ae2a1a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103116"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um hub IoT com o modelo Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Pode utilizar o Azure Resource Manager para criar e gerir hubs IoT do Azure através de programação. Este tutorial mostra-lhe como utilizar um modelo Azure Resource Manager para criar um hub IoT com o PowerShell.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [O Azure Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. <br/>Se não tiver uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* [O Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior.

> [!TIP]
> O artigo [utilizar o Azure PowerShell com o Azure Resource Manager] [ lnk-powershell-arm] fornece mais informações sobre como utilizar modelos do Azure Resource Manager e do PowerShell para criar recursos do Azure.

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Numa linha de comandos do PowerShell, introduza o seguinte comando para iniciar sessão na sua subscrição do Azure:

```powershell
Connect-AzAccount
```

Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis que pode utilizar:

```powershell
Get-AzSubscription
```

Utilize o comando seguinte para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

```powershell
Select-AzSubscription `
    -SubscriptionName "{your subscription name}"
```

Pode utilizar os comandos seguintes para descobrir onde pode implementar um hub IoT e as versões de API atualmente suportadas:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Crie um grupo de recursos para conter o seu hub IoT com o seguinte comando em uma das localizações suportadas para o IoT Hub. Este exemplo cria um grupo de recursos chamado **MyIoTRG1**:

```powershell
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Submeter um modelo para criar um hub IoT

Utilize um modelo JSON para criar um hub IoT no grupo de recursos. Também pode utilizar um modelo Azure Resource Manager para efetuar alterações a um hub IoT.

1. Utilize um editor de texto para criar um modelo do Azure Resource Manager chamado **Template** com a seguinte definição de recurso para criar um novo IoT hub padrão. Este exemplo adiciona o IoT Hub na **E.U.A. Leste** região, cria dois grupos de consumidores (**cg1** e **cg2**) no ponto de final compatível com o Event Hub e utiliza o  **2016-02-03** versão de API. Este modelo também espera que transmita o nome do hub IoT como um parâmetro chamado **hubName**. Para obter a lista atual de localizações que suportam o IoT Hub, veja [estado do Azure][lnk-status].

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
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
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

2. Guarde o ficheiro de modelo do Azure Resource Manager no seu computador local. Este exemplo assume que salvá-lo numa pasta chamada **c:\templates**.

3. Execute o seguinte comando para implementar o seu hub IoT novo, passando o nome do seu hub IoT como um parâmetro. Neste exemplo, o nome do IoT hub é `abcmyiothub`. O nome do IoT hub tem de ser globalmente exclusivo:

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. A saída apresenta as chaves para o hub IoT que criou.

5. Para verificar a sua aplicação adicionada novo hub IoT, visite o [portal do Azure] [ lnk-azure-portal] e ver a sua lista de recursos. Em alternativa, utilize o **Get-AzResource** cmdlet do PowerShell.

> [!NOTE]
> Esta aplicação de exemplo adiciona uma S1 Standard do IoT Hub que lhe é cobrada. Pode eliminar o hub IoT através da [portal do Azure] [ lnk-azure-portal] ou utilizando o **Remove-AzResource** cmdlet do PowerShell quando tiver terminado.

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou um hub IoT com um modelo Azure Resource Manager com o PowerShell, pode querer explorar ainda mais:

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
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
