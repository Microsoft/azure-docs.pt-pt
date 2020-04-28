---
title: Crie um Hub Azure IoT utilizando um cmdlet PowerShell / Microsoft Docs
description: Aprenda a usar os cmdlets PowerShell para criar um grupo de recursos e, em seguida, criar um hub IoT no grupo de recursos. Também aprenda a remover o centro.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73890592"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Crie um hub IoT usando o cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar cmdlets Azure PowerShell para criar e gerir os hubs Azure IoT. Este tutorial mostra-lhe como criar um hub IoT com powerShell.

Para completar este how-to, você precisa de uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Se estiver a utilizar a Cloud Shell, já está registado na sua subscrição. Se estiver a executar a PowerShell localmente, insira o seguinte comando para iniciar sessão na sua subscrição Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Precisa de um grupo de recursos para implantar um centro de IoT. Você pode usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos para o seu hub IoT, utilize o comando [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) Este exemplo cria um grupo de recursos chamado **MyIoTRG1** na região **leste dos EUA:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Para criar um hub IoT no grupo de recursos que criou no passo anterior, use o comando [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) Este exemplo cria um hub **S1** chamado **MyTestIoTHub** na região **leste dos EUA:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do centro ioT deve ser globalmente único.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Pode listar todos os hubs IoT na sua subscrição utilizando o comando [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

Este exemplo mostra o S1 Standard IoT Hub que criou no passo anterior.

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

## <a name="next-steps"></a>Passos seguintes

Agora implementou um hub IoT usando um cmdlet PowerShell, se quiser explorar mais, confira os seguintes artigos:

* [PowerShell cmdlets para trabalhar com o seu hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [IoT Hub fornecedor](https://docs.microsoft.com/rest/api/iothub/iothubresource)de recursos REST API .

Para saber mais sobre o desenvolvimento para o IoT Hub, consulte os seguintes artigos:

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
