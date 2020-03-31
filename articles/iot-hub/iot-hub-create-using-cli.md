---
title: Crie um Hub IoT usando o Azure CLI [ Microsoft Docs
description: Aprenda a usar os comandos Azure CLI para criar um grupo de recursos e, em seguida, criar um hub IoT no grupo de recursos. Também aprenda a remover o centro.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284723"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Crie um hub IoT usando o Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra-lhe como criar um hub IoT usando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este how-to, você precisa de uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Inscreva-se e detete a sua conta Azure

Se estiver a executar o Azure CLI localmente em vez de utilizar a Cloud Shell, tem de iniciar sessão na sua conta Azure.

Na linha de comandos, execute o [comando login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Utilize o Azure CLI para criar um grupo de recursos e, em seguida, adicione um hub IoT.

1. Quando se cria um hub IoT, deve criá-lo num grupo de recursos. Utilize um grupo de recursos existente ou execute o comando [ para criar um grupo de recursos](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > O exemplo anterior cria o grupo de recursos na localização E.U.A. Oeste. Pode ver uma lista de locais disponíveis executando este comando: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Execute o seguinte [comando para criar um hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) no seu grupo de recursos, usando um nome globalmente único para o seu hub IoT:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


O comando anterior cria um hub IoT no nível de preços S1 para o qual você é cobrado. Para mais informações, consulte [os preços do Hub Azure IoT.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="remove-an-iot-hub"></a>Remova um hub ioT

Pode utilizar o Azure CLI para [eliminar um recurso individual](https://docs.microsoft.com/cli/azure/resource), como um hub IoT, ou eliminar um grupo de recursos e todos os seus recursos, incluindo quaisquer hubs IoT.

Para [eliminar um hub IoT,](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)executar o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Para [eliminar um grupo](https://docs.microsoft.com/cli/azure/group#az-group-delete) de recursos e todos os seus recursos, executar o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de um hub IoT, consulte os seguintes artigos:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Usando o portal Azure para gerir o IoT Hub](iot-hub-create-through-portal.md)
