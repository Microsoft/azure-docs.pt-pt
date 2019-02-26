---
title: Gerir o Centro de IoT do Azure PowerShell | Documentos da Microsoft
description: Gerir o Centro de IoT a partir do Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 450b27d21cf4079bdef6fc20c3f2e83f4e47cbd2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806585"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerir o IoT Central a partir do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações de IoT Central a partir do Centro de IoT [Gerenciador de aplicativos](https://aka.ms/iotcentral) página, pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para gerir as suas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se preferir executar o Azure PowerShell no seu computador local, veja [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Ao executar localmente o Azure PowerShell, utilize o **Connect-AzAccount** cmdlet para iniciar sessão no Azure antes de tentar os cmdlets neste artigo.

## <a name="install-the-iot-central-module"></a>Instalar o módulo de IoT Central

Execute o seguinte comando para verificar a [módulo do IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) é instalado no seu ambiente do PowerShell:

```PowerShell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não inclui **Az.IotCentral**, execute o seguinte comando:

```PowerShell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar uma aplicação

Utilize o [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet para criar uma aplicação IoT Central na sua subscrição do Azure. Por exemplo:

```PowerShell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```PowerShell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

O script cria primeiro um grupo de recursos na região Leste região dos EUA para a aplicação. A tabela seguinte descreve os parâmetros utilizados com o **New-AzIotCentralApp** comando:

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém a aplicação. Este grupo de recursos tem de existir na sua subscrição. |
|Localização |Por predefinição, este cmdlet utiliza a localização do grupo de recursos. Atualmente, pode criar uma aplicação IoT Central no **E.U.A. Leste**, **E.U.A. oeste**, **Europa do Norte**, ou **Europa Ocidental** regiões. |
|Name              |O nome da aplicação no portal do Azure. |
|Subdomínio         |O subdomínio no URL da aplicação. No exemplo, o URL da aplicação é https://mysubdomain.azureiotcentral.com. |
|Sku               |Atualmente, o único valor é **S1** (escalão standard). Ver [preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicação a utilizar. Para obter mais informações, consulte a tabela seguinte: |
|Nome a Apresentar       |O nome da aplicação tal como apresentado na IU. |

**Modelos de aplicativos**

|Nome do modelo  |Descrição |
|---------------|------------|
|iotc-default@1.0.0 |Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
|iotc-demo@1.0.0    |Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Utilize este modelo, se for um desenvolvedor de dispositivo fazer experiências com qualquer um desses dispositivos. |

## <a name="view-your-iot-central-applications"></a>Ver as suas aplicações de IoT Central

Utilize o [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet para listar as suas aplicações de IoT Central e ver os metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome a apresentar da sua aplicação:

```PowerShell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize o [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet para eliminar uma aplicação IoT Central. Por exemplo:

```PowerShell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como gerir aplicações do Azure IoT Central do Azure PowerShell, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
