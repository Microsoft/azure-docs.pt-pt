---
title: Manage IoT Central from Azure PowerShell | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480288"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerir o IoT Central a partir do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) to manage your applications.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you prefer to run Azure PowerShell on your local machine, see [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps). When you run Azure PowerShell locally, use the **Connect-AzAccount** cmdlet to sign in to Azure before you try the cmdlets in this article.

## <a name="install-the-iot-central-module"></a>Install the IoT Central module

Run the following command to check the [IoT Central module](https://docs.microsoft.com/powershell/module/az.iotcentral/) is installed in your PowerShell environment:

```powershell
Get-InstalledModule -name Az.I*
```

If the list of installed modules doesn't include **Az.IotCentral**, run the following command:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar uma aplicação

Use the [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet to create an IoT Central application in your Azure subscription. Por exemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

The script first creates a resource group in the east US location for the application. The following table describes the parameters used with the **New-AzIotCentralApp** command:

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |The resource group that contains the application. This resource group must already exist in your subscription. |
|Localização |By default, this cmdlet uses the location from the resource group. Currently, you can create an IoT Central application in the **United States**, **Australia**, **Asia Pacific**, or in the **Europe** locations.  |
|Nome              |The name of the application in the Azure portal. |
|Subdomain         |The subdomain in the URL of the application. In the example, the application URL is https://mysubdomain.azureiotcentral.com. |
|SKU               |Currently, the only value is **S1** (standard tier). See [Azure IoT Central pricing](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | The application template to use. For more information, see the following table: |
|DisplayName       |The name of the application as displayed in the UI. |

**Application templates with generally available features**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| iotc-demo@1.0.0          | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Use this template if you're a device developer experimenting with any of these devices. |


**Application templates with public preview features**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Creates an empty plug and play preview application for you to populate with your own device templates and devices. |
| iotc-condition@1.0.0     | Creates an application with a in-store analytics – condition monitoring template. Use this template to connect and monitor store environment. |
| iotc-consumption@1.0.0   | Creates an application with water consumption monitoring template. Use this template to monitor and control water flow. |
| iotc-distribution@1.0.0  | Creates an application with a Digital distribution template. Use this template to improve warehouse output efficiency by digitalizing key assets and actions. |
| iotc-inventory@1.0.0     | Creates an application with a smart inventory management template. Use this template to automate receiving, product movement, cycle counting, and tracking of sensors. |
| iotc-logistics@1.0.0     | Creates an application with a Connected logistics template. Use this template to track your shipment in real-time across air, water and land with location and condition monitoring. |
| iotc-meter@1.0.0         | Creates an application with smart meter monitoring template. Use this template to monitor energy consumption, network status, and identify trends to improve customer support and smart meter management.  |
| iotc-patient@1.0.0       | Creates an application with continuous patient monitoring template. Use this template to extend patient care, re-admissions, and manage diseases. |
| iotc-power@1.0.0         | Creates an application with solar panel monitoring template. Use this template to monitor solar panel status, energy generation trends. |
| iotc-quality@1.0.0       | Creates an application with water quality monitoring template. Use this template to digitally monitor water quality.|
| iotc-store@1.0.0         | Creates an application with a in-store analytics – checkout template. Use this template to monitor and manage the checkout flow inside your store. |
| iotc-waste@1.0.0         | Creates an application with a Connected waste management template. Use this template to monitor waste bins and dispatch field operators. |

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

## <a name="view-your-iot-central-applications"></a>View your IoT Central applications

Use the [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet to list your IoT Central applications and view metadata.

## <a name="modify-an-application"></a>Modify an application

Use the [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet to update the metadata of an IoT Central application. For example, to change the display name of your application:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Use the [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet to delete an IoT Central application. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passos seguintes

Now that you've learned how to manage Azure IoT Central applications from Azure PowerShell, here is the suggested next step:

> [!div class="nextstepaction"]
> [Administer your application](howto-administer.md)
