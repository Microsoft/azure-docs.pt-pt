---
title: Gerenciar IoT Central de CLI do Azure | Microsoft Docs
description: Gerenciar IoT Central de CLI do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1ec83541c62d93eee91348531797ecdeb8c9fc6e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873472"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerenciar IoT Central de CLI do Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerenciar IoT Central aplicativos no site [do Azure IOT central Application Manager](https://aka.ms/iotcentral) , você pode usar [CLI do Azure](/cli/azure/) para gerenciar seus aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se preferir executar CLI do Azure em seu computador local, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli). Ao executar CLI do Azure localmente, use o comando **AZ login** para entrar no Azure antes de tentar os comandos neste artigo.

## <a name="create-an-application"></a>Criar uma aplicação

Use o comando [AZ iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) para criar um aplicativo IOT central em sua assinatura do Azure. Por exemplo:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Esses comandos primeiro criam um grupo de recursos na região leste dos EUA para o aplicativo. A tabela a seguir descreve os parâmetros usados com o comando **AZ iotcentral app Create** :

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém o aplicativo. Este grupo de recursos já deve existir em sua assinatura. |
| location          | Por padrão, esse comando usa o local do grupo de recursos. No momento, você pode criar um aplicativo IoT Central nas regiões **leste dos EUA**, **oeste dos EUA**, **Europa setentrional**ou **Europa Ocidental** ou na geografia da **Austrália** . |
| name              | O nome do aplicativo no portal do Azure. |
| Subdomínio         | O subdomínio na URL do aplicativo. No exemplo, a URL do aplicativo é https://mysubdomain.azureiotcentral.com. |
| sku               | Atualmente, o único valor é **S1** (camada Standard). Consulte [preços de IOT central do Azure](https://azure.microsoft.com/pricing/details/iot-central/). |
| Modelo          | O modelo de aplicativo a ser usado. Para obter mais informações, consulte a tabela a seguir: |
| nome de exibição      | O nome do aplicativo, conforme exibido na interface do usuário. |

**Modelos de aplicativos**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| iotc-demo@1.0.0          | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Use este modelo se você for um desenvolvedor de dispositivo experimentando qualquer um desses dispositivos. |

> [!NOTE]
> No momento, o modelo de **aplicativo de visualização** só está disponível nas regiões **Europa setentrional** e **EUA Central** .

## <a name="view-your-applications"></a>Ver as suas aplicações

Use o comando [AZ iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) para listar seus aplicativos IOT central e exibir metadados.

## <a name="modify-an-application"></a>Modificar um aplicativo

Use o comando [AZ iotcentral app Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) para atualizar os metadados de um aplicativo IOT central. Por exemplo, para alterar o nome de exibição do seu aplicativo:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Use o comando [AZ iotcentral app Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) para excluir um aplicativo IOT central. Por exemplo:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a gerenciar os aplicativos do Azure IoT Central do CLI do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Administre seu aplicativo](howto-administer.md)
