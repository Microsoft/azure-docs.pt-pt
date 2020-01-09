---
title: Criar um hub IoT do Azure usando um modelo (PowerShell) | Microsoft Docs
description: Como usar um modelo de Azure Resource Manager para criar um hub IoT com Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 45908557ed40eb249bbac08cfca83bca7ff06ad1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429143"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um hub IoT usando o modelo de Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Saiba como usar um modelo de Azure Resource Manager para criar um hub IoT e um grupo de consumidores. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O modelo do Resource Manager usado neste guia de início rápido é de [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Aqui está uma cópia do modelo:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

O modelo cria um hub IOT do Azure com três pontos de extremidade (eventhub, nuvem para dispositivo e mensagens) e um grupo de consumidores. Para obter mais exemplos de modelo, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). O esquema de modelo do Hub IOT pode ser encontrado [aqui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Há vários métodos para implantar um modelo.  Você usa Azure PowerShell neste tutorial.

Para executar o script do PowerShell, selecione **Experimente-** o para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione colar:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Como você pode ver no script do PowerShell, o modelo usado é dos modelos de início rápido do Azure. Para usar seu próprio, você precisa primeiro carregar o arquivo de modelo para o Cloud Shell e, em seguida, usar a opção `-TemplateFile` para especificar o nome do arquivo.  Para obter um exemplo, consulte [implantar o modelo](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Passos seguintes

Agora que você implantou um hub IoT usando um modelo de Azure Resource Manager, talvez você queira explorar ainda mais:

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
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
