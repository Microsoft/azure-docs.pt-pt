---
title: Criar um Hub Azure IoT utilizando um cmdlet PowerShell / Microsoft Docs
description: Aprenda a usar os cmdlets PowerShell para criar um grupo de recursos e, em seguida, crie um hub IoT no grupo de recursos. Também aprenda a remover o centro.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73890592"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Criar um hub IoT utilizando o cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar cmdlets Azure PowerShell para criar e gerir os hubs Azure IoT. Este tutorial mostra-lhe como criar um hub IoT com PowerShell.

Para completar este como fazer, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Se estiver a utilizar a Cloud Shell, já está a iniciar sessão na sua subscrição. Se estiver a executar o PowerShell localmente, insira o seguinte comando para iniciar sação na sua subscrição Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Precisa de um grupo de recursos para implantar um hub IoT. Pode utilizar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos para o seu hub IoT, utilize o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) Este exemplo cria um grupo de recursos chamado **MyIoTRG1** na região **leste dos EUA:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Para criar um hub IoT no grupo de recursos que criou no passo anterior, utilize o comando [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) Este exemplo cria um hub **S1** chamado **MyTestIoTHub** na região **leste dos EUA:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do hub IoT deve ser globalmente único.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Pode listar todos os hubs IoT da sua subscrição utilizando o comando [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

Este exemplo mostra o Hub IoT Standard S1 que criou no passo anterior.

Pode eliminar o hub IoT utilizando o comando [Remove-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Em alternativa, pode remover um grupo de recursos e todos os recursos que contém utilizando o comando [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Próximos passos

Agora que implementou um hub IoT usando um cmdlet PowerShell, se quiser explorar mais, confira os seguintes artigos:

* [PowerShell cmdlets para trabalhar com o seu hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Fornecedor de recursos IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os seguintes artigos:

* [Introdução à C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
