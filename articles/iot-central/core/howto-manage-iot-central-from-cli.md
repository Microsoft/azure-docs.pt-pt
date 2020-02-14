---
title: Gerir o IoT Central do Azure CLI  Microsoft Docs
description: Este artigo descreve como criar e gerir a sua aplicação IoT Central usando cli. Pode visualizar, modificar e remover a aplicação utilizando o CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c44b7cd045547d01d1a31f949a42087e78e88b21
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198842"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gerir a IoT Central do Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do gestor de [aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode utilizar o [Azure CLI](/cli/azure/) para gerir as suas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se preferir executar o Azure CLI na sua máquina local, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli). Quando executar o Azure CLI localmente, utilize o comando **de login az** para iniciar sessão no Azure antes de experimentar os comandos neste artigo.

> [!TIP]
> Se precisar de executar os seus comandos CLI numa subscrição Azure diferente, consulte [Alterar a subscrição ativa](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Criar uma aplicação

Utilize a [aplicação az iotcentral criar](/cli/azure/iotcentral/app#az-iotcentral-app-create) comando para criar uma aplicação IoT Central na sua subscrição Azure. Por exemplo:

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
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Estes comandos criam primeiro um grupo de recursos na região leste dos EUA para a aplicação. A tabela seguinte descreve os parâmetros utilizados com a **aplicação az iotcentral criar** comando:

| Parâmetro         | Descrição |
| ----------------- | ----------- |
| resource-group    | O grupo de recursos que contém a aplicação. Este grupo de recursos já deve existir na sua subscrição. |
| localização          | Por predefinição, este comando utiliza a localização do grupo de recursos. Atualmente, você pode criar uma aplicação IoT Central nas geografias **da Austrália**, **Ásia-Pacífico,** **Europa**ou **Estados Unidos.** |
| nome              | O nome da aplicação no portal Azure. |
| subdomínio         | O subdomínio no URL da aplicação. No exemplo, o URL da aplicação é https://mysubdomain.azureiotcentral.com. |
| sku               | Atualmente, pode utilizar **sT1** ou **ST2**. Consulte [os preços centrais azure ioT](https://azure.microsoft.com/pricing/details/iot-central/). |
| modelo          | O modelo de aplicação a utilizar. Para mais informações, consulte a tabela seguinte. |
| nome de exibição      | O nome da aplicação como mostrado na UI. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Ver as suas aplicações

Utilize o comando da lista de [aplicações az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-list) para listar as suas aplicações IoT Central e ver metadados.

## <a name="modify-an-application"></a>Modificar uma aplicação

Utilize o comando de atualização de [aplicações az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-update) para atualizar os metadados de uma aplicação IoT Central. Por exemplo, para alterar o nome de exibição da sua aplicação:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Remover uma aplicação

Utilize a [aplicação az iotcentral eliminar](/cli/azure/iotcentral/app#az-iotcentral-app-delete) o comando para eliminar uma aplicação IoT Central. Por exemplo:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir as aplicações azure IoT Central do Azure CLI, aqui está o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Administrar a sua aplicação](howto-administer.md)
