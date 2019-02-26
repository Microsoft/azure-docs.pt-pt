---
title: Gerir o Centro de IoT a partir da CLI do Azure | Documentos da Microsoft
description: Gerir o Centro de IoT a partir da CLI do Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808625"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerir o Centro de IoT a partir da CLI do Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações de IoT Central a partir do Centro de IoT [Gerenciador de aplicativos](https://aka.ms/iotcentral) página, pode usar [CLI do Azure](/cli/azure/) para gerir as suas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se preferir executar a CLI do Azure no seu computador local, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli). Ao executar localmente o CLI do Azure, utilize o **início de sessão az** comando para iniciar sessão no Azure antes de tentar os comandos neste artigo.

## <a name="create-an-application"></a>Criar uma aplicação

Utilize o [az iotcentral aplicação criar](/cli/azure/iotcentral/app#az-iotcentral-app-create) comando para criar uma aplicação IoT Central na sua subscrição do Azure. Por exemplo:

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

Estes comandos primeiro criam um grupo de recursos na região Leste região dos EUA para a aplicação. A tabela seguinte descreve os parâmetros utilizados com o **az iotcentral aplicação criar** comando:

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém a aplicação. Este grupo de recursos tem de existir na sua subscrição. |
| localização          | Por predefinição, este comando utiliza a localização do grupo de recursos. Atualmente, pode criar uma aplicação IoT Central no **E.U.A. Leste**, **E.U.A. oeste**, **Europa do Norte**, ou **Europa Ocidental** regiões. |
| nome              | O nome da aplicação no portal do Azure. |
| Subdomínio         | O subdomínio no URL da aplicação. No exemplo, o URL da aplicação é https://mysubdomain.azureiotcentral.com. |
| sku               | Atualmente, o único valor é **S1** (escalão standard). Ver [preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| modelo          | O modelo de aplicação a utilizar. Para obter mais informações, consulte a tabela seguinte: |
| display-name      | O nome da aplicação tal como apresentado na IU. |

**Modelos de aplicativos**

| Nome do modelo            | Descrição |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Cria uma aplicação vazia que pode preencher com os seus próprios modelos de dispositivo e dispositivos. |
| iotc-demo@1.0.0          | Cria uma aplicação que inclui um modelo de dispositivo já criado para uma máquina dispensadora de bebidas. Utilize este modelo para começar a explorar o Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Cria uma aplicação com modelos de dispositivo prontos para se poder ligar a dispositivos MXChip ou Raspberry Pi. Utilize este modelo, se for um desenvolvedor de dispositivo fazer experiências com qualquer um desses dispositivos. |

## <a name="view-your-applications"></a>Ver as suas aplicações

Utilize o [lista de aplicações do az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-list) comando para listar as suas aplicações de IoT Central e ver os metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o [atualização da aplicação az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-update) comando para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome a apresentar da sua aplicação:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize o [delete de aplicação de iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-delete) comando para eliminar uma aplicação IoT Central. Por exemplo:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como gerir aplicações do Azure IoT Central a partir da CLI do Azure, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
