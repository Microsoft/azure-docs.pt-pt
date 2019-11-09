---
title: Criar um hub IoT usando CLI do Azure | Microsoft Docs
description: Saiba como usar os comandos de CLI do Azure para criar um grupo de recursos e, em seguida, criar um hub IoT no grupo de recursos. Saiba também como remover o Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889504"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Criar um hub IoT usando o CLI do Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra como criar um hub IoT usando CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este "como", você precisa de uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Entre e defina sua conta do Azure

Se estiver executando CLI do Azure localmente em vez de usar Cloud Shell, você precisará entrar em sua conta do Azure.

No prompt de comando, execute o [comando de logon](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Use o CLI do Azure para criar um grupo de recursos e, em seguida, adicione um hub IoT.

1. Ao criar um hub IoT, você deve criá-lo em um grupo de recursos. Use um grupo de recursos existente ou execute o seguinte [comando para criar um grupo de recursos](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > O exemplo anterior cria o grupo de recursos na localização E.U.A. Oeste. Você pode exibir uma lista de locais disponíveis executando este comando: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Execute o seguinte [comando para criar um hub IOT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) em seu grupo de recursos, usando um nome globalmente exclusivo para o Hub IOT:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


O comando anterior cria um hub IoT no tipo de preço S1 para o qual você é cobrado. Para obter mais informações, consulte [preços do Hub IOT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Remover um hub IoT

Você pode usar CLI do Azure para [excluir um recurso individual](https://docs.microsoft.com/cli/azure/resource), como um hub IOT, ou excluir um grupo de recursos e todos os seus recursos, incluindo os hubs IOT.

Para [excluir um hub IOT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), execute o seguinte comando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Para [excluir um grupo de recursos](https://docs.microsoft.com/cli/azure/group#az-group-delete) e todos os seus recursos, execute o seguinte comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar um hub IoT, confira os seguintes artigos:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Usando o portal do Azure para gerenciar o Hub IoT](iot-hub-create-through-portal.md)
