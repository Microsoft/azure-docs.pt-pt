---
title: Gerenciar IoT Central de Azure PowerShell | Microsoft Docs
description: Este artigo descreve como criar e gerenciar seus aplicativos de IoT Central do Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7c55d0568832fcefee6e0763810c5e1220480270
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278850"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gerir o IoT Central a partir do Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se preferir executar Azure PowerShell em seu computador local, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Ao executar Azure PowerShell localmente, use o cmdlet **Connect-AzAccount** para entrar no Azure antes de tentar os cmdlets neste artigo.

## <a name="install-the-iot-central-module"></a>Instalar o módulo IoT Central

Execute o seguinte comando para verificar se o [módulo IOT central](https://docs.microsoft.com/powershell/module/az.iotcentral/) está instalado em seu ambiente do PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Se a lista de módulos instalados não incluir **AZ. IotCentral**, execute o seguinte comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Criar uma aplicação

Use o cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) para criar um aplicativo IOT central em sua assinatura do Azure. Por exemplo:

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

O script cria primeiro um grupo de recursos no local leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **New-AzIotCentralApp** :

|Parâmetro         |Descrição |
|------------------|------------|
|ResourceGroupName |O grupo de recursos que contém o aplicativo. Este grupo de recursos já deve existir em sua assinatura. |
|Localização |Por padrão, esse cmdlet usa o local do grupo de recursos. No momento, você pode criar um aplicativo IoT Central no **Estados Unidos**, na **austrália**, no **Pacífico Asiático**ou nos locais da **Europa** .  |
|Nome              |O nome do aplicativo no portal do Azure. |
|subdomínio         |O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é https://mysubdomain.azureiotcentral.com. |
|SKU               |Atualmente, o único valor é **S1** (camada Standard). Consulte [preços de IOT central do Azure](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modelo          | O modelo de aplicativo a ser usado. Para obter mais informações, consulte a tabela a seguir: |
|DisplayName       |O nome do aplicativo, conforme exibido na interface do usuário. |

**Modelos de aplicativos**

|Nome do modelo  |Descrição |
|---------------|------------|
|iotc-default@1.0.0 |Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
|iotc-demo@1.0.0    |Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Use este modelo se você for um desenvolvedor de dispositivo experimentando qualquer um desses dispositivos. |

> [!NOTE]
> No momento, o modelo de **aplicativo de visualização** só está disponível nas regiões **Europa setentrional** e **EUA Central** .

## <a name="view-your-iot-central-applications"></a>Exibir seus aplicativos de IoT Central

Use o cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) para listar seus aplicativos IOT central e exibir metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o cmdlet [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) para atualizar os metadados de um aplicativo IOT central. Por exemplo, para alterar o nome de exibição do seu aplicativo:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Use o cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para excluir um aplicativo IOT central. Por exemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do Azure PowerShell, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administre seu aplicativo](howto-administer.md)
