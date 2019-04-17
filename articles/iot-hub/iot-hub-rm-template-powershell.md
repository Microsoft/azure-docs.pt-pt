---
title: Criar um IoT Hub do Azure através de um modelo (PowerShell) | Documentos da Microsoft
description: Como utilizar um modelo Azure Resource Manager para criar um IoT Hub com o Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609183"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Criar um hub IoT com o modelo Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Saiba como utilizar um modelo Azure Resource Manager para criar um IoT Hub e um grupo de consumidores. Os modelos do Resource Manager são ficheiros JSON que definem os recursos que precisa de implementar para a sua solução. Para obter mais informações sobre o desenvolvimento de modelos do Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-iot-hub"></a>Criar um hub IoT

O modelo do Resource Manager utilizado neste início rápido é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Esta é uma cópia do modelo:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

O modelo cria um hub Iot do Azure com três pontos de extremidade (eventhub, cloud-para-dispositivo e de mensagens) e um grupo de consumidores. Para obter mais exemplos de modelos, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Pode encontrar o esquema do modelo de Iot Hub [aqui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Existem vários métodos para implementar um modelo.  Utilizar o Azure PowerShell neste tutorial.

Para executar o script do PowerShell, selecione **experimente** para abrir o Azure Cloud shell. Cole o script, o shell com o botão direito e, em seguida, selecione colar:

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

Como pode ver a partir do script do PowerShell, o modelo utilizado é a partir de modelos de início rápido do Azure. Para utilizar as suas próprias, precisa para primeiro carregar o ficheiro de modelo para o Cloud shell e, em seguida, utilize o `-TemplateFile` comutador para especificar o nome de ficheiro.  Por exemplo, veja [implementar o modelo](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou um hub IoT com um modelo Azure Resource Manager, pode querer explorar ainda mais:

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
