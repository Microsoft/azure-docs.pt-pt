---
title: Gerir a IoT Central da Azure PowerShell | Microsoft Docs
description: Este artigo descreve como criar e gerir as suas aplicações IoT Central a partir da Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: a870b72d1dda04ab29dbb5f056873d47f888b837
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501376"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerir o IoT Central a partir do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar [a Azure PowerShell](/powershell/azure/) para gerir as suas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Se preferir executar o Azure PowerShell na sua máquina local, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Quando executar a Azure PowerShell localmente, utilize o cmdlet **Connect-AzAccount** para iniciar súm na Azure antes de experimentar os cmdlets deste artigo.

> [!TIP]
> Se precisar de executar os seus comandos PowerShell numa subscrição Azure diferente, consulte [alterar a subscrição ativa](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instale o módulo Central IoT

Executar o seguinte comando para verificar se o [módulo Central IoT](/powershell/module/az.iotcentral/) está instalado no seu ambiente PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não incluir **Az.IotCentral,** executar o seguinte comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar uma aplicação

Utilize o cmdlet [New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) para criar uma aplicação IoT Central na sua subscrição Azure. Por exemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

O script cria primeiro um grupo de recursos na região leste dos EUA para a aplicação. A tabela a seguir descreve os parâmetros utilizados com o comando **New-AzIotCentralApp:**

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém a aplicação. Este grupo de recursos já deve existir na sua subscrição. |
|Localização |Por predefinição, este cmdlet utiliza a localização do grupo de recursos. Atualmente, você pode criar uma aplicação IoT Central nas geografias da **Austrália**, **Ásia Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido** e **Japão.** |
|Name              |O nome da aplicação no portal Azure. |
|Subdomínio         |O subdomínio na URL da aplicação. No exemplo, o URL de aplicação é `https://mysubdomain.azureiotcentral.com` . |
|Sku               |Atualmente, pode utilizar **ST1** ou **ST2**. Consulte [os preços da Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicação a utilizar. Para mais informações, consulte a tabela a seguir. |
|DisplayName       |O nome da aplicação, tal como mostrado na UI. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Veja as suas aplicações IoT Central

Utilize o cmdlet [Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) para listar as suas aplicações IoT Central e ver metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o [cmdlet Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome de exibição da sua aplicação:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize o [cmdlet Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para eliminar uma aplicação IoT Central. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações da Azure IoT Central da Azure PowerShell, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)