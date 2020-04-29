---
title: Gerencie a IoT Central da Azure PowerShell [ Microsoft Docs
description: Este artigo descreve como criar e gerir as suas aplicações IoT Central a partir do Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 42d853c9cf53c1c6921fbd1816ec2298c9c3583e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80365542"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerir o IoT Central a partir do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do gestor de [aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para gerir as suas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se preferir executar o Azure PowerShell na sua máquina local, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps). Quando executar o Azure PowerShell localmente, utilize o cmdlet **Connect-AzAccount** para iniciar sessão no Azure antes de experimentar os cmdlets neste artigo.

> [!TIP]
> Se precisar de executar os seus comandos PowerShell numa subscrição Azure diferente, consulte [Alterar a subscrição ativa](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription).

## <a name="install-the-iot-central-module"></a>Instale o módulo IoT Central

Execute o seguinte comando para verificar se o [módulo IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) está instalado no seu ambiente PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não incluir **a Az.IotCentral,** execute o seguinte comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar uma aplicação

Utilize o cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) para criar uma aplicação IoT Central na sua subscrição Azure. Por exemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

O guião cria primeiro um grupo de recursos na região leste dos EUA para a aplicação. A tabela seguinte descreve os parâmetros utilizados com o comando **New-AzIotCentralApp:**

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém a aplicação. Este grupo de recursos já deve existir na sua subscrição. |
|Localização |Por predefinição, este cmdlet utiliza a localização do grupo de recursos. Atualmente, você pode criar uma aplicação IoT Central nas geografias **Da Austrália**, **Ásia-Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido**e **Japão.** |
|Nome              |O nome da aplicação no portal Azure. |
|Subdomínio         |O subdomínio no URL da aplicação. No exemplo, o URL `https://mysubdomain.azureiotcentral.com`da aplicação é . |
|Sku               |Atualmente, pode utilizar **sT1** ou **ST2**. Consulte [os preços centrais azure ioT](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicação a utilizar. Para mais informações, consulte a tabela a seguir. |
|DisplayName       |O nome da aplicação como mostrado na UI. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>Ver as suas aplicações IoT Central

Utilize o cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) para listar as suas aplicações IoT Central e ver metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o [cmdlet Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome de exibição da sua aplicação:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize o cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para eliminar uma aplicação IoT Central. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações Azure IoT Central da Azure PowerShell, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
