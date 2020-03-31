---
title: Crie um Hub Azure IoT usando um modelo (PowerShell) / Microsoft Docs
description: Como usar um modelo de Gestor de Recursos Azure para criar um Hub IoT com O PowerShell Azure.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976611"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Crie um hub IoT usando o modelo de Gestor de Recursos Azure (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Aprenda a usar um modelo de Gestor de Recursos Azure para criar um Hub IoT e um grupo de consumidores. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para obter mais informações sobre o desenvolvimento de modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos do Azure.](https://docs.microsoft.com/azure/azure-resource-manager/)

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O modelo de Gestor de Recursos utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Aqui está uma cópia do modelo:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

O modelo cria um hub Azure Iot com três pontos finais (eventhub, cloud-to-device e mensagens) e um grupo de consumidores. Para mais amostras de modelo, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). O esquema do modelo Do Hub pode ser encontrado [aqui.](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)

Existem vários métodos para implementar um modelo.  Usa o Azure PowerShell neste tutorial.

Para executar o script PowerShell, selecione **Experimente-o** para abrir a concha Azure Cloud. Para colar o script, clique na concha e, em seguida, selecione Pasta:

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

Como pode ver no script PowerShell, o modelo utilizado é de modelos Azure Quickstart. Para utilizar o seu próprio, tem de carregar primeiro o ficheiro `-TemplateFile` do modelo para a casca cloud e, em seguida, utilizar o interruptor para especificar o nome do ficheiro.  Por exemplo, consulte [A implantação do modelo](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Passos seguintes

Agora implementou um hub IoT usando um modelo de Gestor de Recursos Azure, você pode querer explorar mais:

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
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
