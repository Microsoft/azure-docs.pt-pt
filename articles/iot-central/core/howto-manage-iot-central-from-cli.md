---
title: Gerir a IoT Central da Azure CLI | Microsoft Docs
description: Este artigo descreve como criar e gerir a sua aplicação IoT Central utilizando o CLI. Pode visualizar, modificar e remover a aplicação utilizando o CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: d414b86ff81a33f9e818a0a28031e73d88cabec2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202268"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerir a IoT Central a partir de Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar [o Azure CLI](/cli/azure/) para gerir as suas aplicações.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Se precisar de executar os seus comandos CLI numa subscrição Azure diferente, consulte [alterar a subscrição ativa](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

## <a name="create-an-application"></a>Criar uma aplicação

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Utilize a [aplicação central az iot criar](/cli/azure/iot/central/app#az-iot-central-app-create) comando para criar uma aplicação IoT Central na sua subscrição Azure. Por exemplo:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Estes comandos criam primeiro um grupo de recursos na região leste dos EUA para a aplicação. A tabela a seguir descreve os parâmetros utilizados com a **aplicação central az iot criar** comando:

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém a aplicação. Este grupo de recursos já deve existir na sua subscrição. |
| localização          | Por predefinição, este comando utiliza a localização do grupo de recursos. Atualmente, você pode criar uma aplicação IoT Central nas geografias da **Austrália**, **Ásia Pacífico,** **Europa,** **Estados Unidos,** **Reino Unido** e **Japão.** |
| name              | O nome da aplicação no portal Azure. |
| subdomínio         | O subdomínio na URL da aplicação. No exemplo, o URL de aplicação é `https://mysubdomain.azureiotcentral.com` . |
| sku               | Atualmente, pode utilizar **ST1** ou **ST2**. Consulte [os preços da Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| modelo          | O modelo de aplicação a utilizar. Para mais informações, consulte a tabela a seguir. |
| nome de exibição      | O nome da aplicação, tal como mostrado na UI. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Ver as suas aplicações

Utilize o comando [da lista de aplicações az iot central](/cli/azure/iot/central/app#az-iot-central-app-list) para listar as suas aplicações IoT Central e ver metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o comando [de atualização de aplicações az iot central](/cli/azure/iot/central/app#az-iot-central-app-update) para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome de exibição da sua aplicação:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize o comando [de exclusão da aplicação az iot](/cli/azure/iot/central/app#az-iot-central-app-delete) para eliminar uma aplicação IoT Central. Por exemplo:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações da Azure IoT Central da Azure CLI, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
