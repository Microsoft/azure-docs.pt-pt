---
title: Criar um Hub de IoT do Azure com um cmdlet do PowerShell | Documentos da Microsoft
description: Como utilizar um cmdlet do PowerShell para criar um hub IoT.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 6462673f6c3992aacbaee168eafc6bdb1b2fa944
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733475"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Criar um hub IoT com o cmdlet New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introdução

Pode utilizar cmdlets do Azure PowerShell para criar e gerir hubs IoT do Azure. Este tutorial mostra-lhe como criar um hub IoT com o PowerShell.

Para concluir este procedimentos, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

Se estiver a utilizar o Cloud Shell, é tiver sessão iniciada na sua subscrição. Se estiver a executar PowerShell localmente em vez disso, introduza o seguinte comando para iniciar sessão na sua subscrição do Azure:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Precisa de um grupo de recursos para implementar um hub IoT. Pode utilizar um grupo de recursos existente ou crie um novo.

Para criar um grupo de recursos do hub IoT, utilize o [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) comando. Este exemplo cria um grupo de recursos chamado **MyIoTRG1** no **E.U.A. Leste** região:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Para criar um hub IoT no grupo de recursos que criou no passo anterior, utilize o [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) comando. Este exemplo cria um **S1** hub chamado **MyTestIoTHub** no **E.U.A. Leste** região:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

O nome do IoT hub tem de ser globalmente exclusivo.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Pode listar todos os hubs IoT na sua subscrição com o [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) comando:

```azurepowershell-interactive
Get-AzIotHub
```

Este exemplo mostra o S1 Standard do IoT Hub que criou no passo anterior.

Pode eliminar o hub IoT com o [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) comando:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Em alternativa, pode remover um grupo de recursos e todos os recursos nele contidos através da [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) comando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou um hub IoT com um cmdlet do PowerShell, se quiser explorar ainda mais, consulte os artigos seguintes:

* [Cmdlets do PowerShell para trabalhar com o seu hub IoT](https://docs.microsoft.com/powershell/module/az.iothub/).

* [REST API do fornecedor de recursos de IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Para saber mais sobre o desenvolvimento para o IoT Hub, veja os artigos seguintes:

* [Introdução ao C SDK](iot-hub-device-sdk-c-intro.md)

* [SDKs do Azure IoT](iot-hub-devguide-sdks.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
