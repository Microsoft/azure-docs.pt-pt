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
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480288"
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

**Modelos de aplicativos com recursos disponíveis para o público geral**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| iotc-demo@1.0.0          | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Use este modelo se você for um desenvolvedor de dispositivo experimentando qualquer um desses dispositivos. |


**Modelos de aplicativos com recursos de visualização pública**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Cria um aplicativo de visualização de plug and Play vazio para popular com seus próprios dispositivos e modelos de dispositivo. |
| iotc-condition@1.0.0     | Cria um aplicativo com um modelo de monitoramento de condição em uma análise na loja. Use este modelo para conectar e monitorar o ambiente de armazenamento. |
| iotc-consumption@1.0.0   | Cria um aplicativo com o modelo de monitoramento de consumo de água. Use este modelo para monitorar e controlar o fluxo de água. |
| iotc-distribution@1.0.0  | Cria um aplicativo com um modelo de distribuição digital. Use este modelo para melhorar a eficiência da saída do depósito ao digitalização de ativos e ações de chave. |
| iotc-inventory@1.0.0     | Cria um aplicativo com um modelo de gerenciamento de inventário inteligente. Use este modelo para automatizar o recebimento, movimentação de produtos, contagem de ciclos e acompanhamento de sensores. |
| iotc-logistics@1.0.0     | Cria um aplicativo com um modelo de logística conectado. Use este modelo para acompanhar sua remessa em tempo real entre ar, água e terreno com monitoramento de local e condição. |
| iotc-meter@1.0.0         | Cria um aplicativo com o modelo de monitoramento do medidor inteligente. Use este modelo para monitorar o consumo de energia, o status da rede e identificar as tendências para melhorar o atendimento ao cliente e o gerenciamento de medidor inteligente.  |
| iotc-patient@1.0.0       | Cria um aplicativo com o modelo de monitoramento contínuo do paciente. Use este modelo para estender o atendimento ao paciente, renovações e gerenciar doenças. |
| iotc-power@1.0.0         | Cria um aplicativo com o modelo de monitoramento de painel solar. Use este modelo para monitorar o status do painel solar, as tendências de geração de energia. |
| iotc-quality@1.0.0       | Cria um aplicativo com o modelo de monitoramento de qualidade de água. Use este modelo para monitorar digitalmente a qualidade da água.|
| iotc-store@1.0.0         | Cria um aplicativo com um modelo de check-out de análise no repositório. Use este modelo para monitorar e gerenciar o fluxo de check-out dentro de seu repositório. |
| iotc-waste@1.0.0         | Cria um aplicativo com um modelo de gerenciamento de resíduos conectado. Use este modelo para monitorar os operadores de lixo e de campo de expedição. |

> [!NOTE]
> Os modelos de aplicativo de visualização atualmente só estão disponíveis nos locais da **Europa** e **Estados Unidos** .

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

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do Azure PowerShell, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administre seu aplicativo](howto-administer.md)
