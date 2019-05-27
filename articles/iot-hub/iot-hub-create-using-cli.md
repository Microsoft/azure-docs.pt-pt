---
title: Criar um IoT Hub com a CLI do Azure | Documentos da Microsoft
description: Como criar um hub IoT do Azure com a CLI do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 78ea9071f220b2a78c6d9260d47145f22284d760
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66166284"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Criar um hub IoT com a CLI do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra-lhe como criar um hub IoT com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimentos, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Iniciar sessão e definir a sua conta do Azure

Se estiver a executar o CLI do Azure localmente em vez de utilizar o Cloud Shell, tem de iniciar sessão sua conta do Azure.

Na linha de comandos, execute o [comandos de início de sessão](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Utilize a CLI do Azure para criar um grupo de recursos e, em seguida, adicione um hub IoT.

1. Quando cria um hub IoT, tem de criá-la num grupo de recursos. Utilize um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > O exemplo anterior cria o grupo de recursos na localização E.U.A. Oeste. Pode ver uma lista de localizações disponíveis ao executar este comando: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Execute o seguinte [comando para criar um hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) no seu grupo de recursos, utilizando um nome globalmente exclusivo para o seu hub IoT:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


O comando anterior cria um hub IoT na S1 que lhe é cobrada de escalão de preço. Para obter mais informações, consulte [preços do IoT Hub do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Remover um Hub IoT

Pode usar a CLI do Azure para [eliminar um recurso individual](https://docs.microsoft.com/cli/azure/resource), como um IoT hub ou eliminar um grupo de recursos e todos os respetivos recursos, incluindo qualquer hubs IoT.

Para [eliminar um hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), execute o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Para [eliminar um grupo de recursos](https://docs.microsoft.com/cli/azure/group#az-group-delete) e todos os respetivos recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como utilizar um hub IoT, veja os artigos seguintes:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Utilizar o portal do Azure para gerir o IoT Hub](iot-hub-create-through-portal.md)
