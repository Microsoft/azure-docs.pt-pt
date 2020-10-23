---
title: Criar um Hub IoT utilizando o Azure CLI Microsoft Docs
description: Aprenda a usar os comandos Azure CLI para criar um grupo de recursos e, em seguida, criar um hub IoT no grupo de recursos. Também aprenda a remover o centro.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 6daed4f5f1871d76da707edec00010cd27dfa8db
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142328"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Criar um hub IoT utilizando o Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Este artigo mostra-lhe como criar um hub IoT usando Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este como fazer, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Inscreva-se e descreva a sua conta Azure

Se estiver a executar o Azure CLI localmente em vez de utilizar a Cloud Shell, tem de iniciar scontabilidade na sua conta Azure.

Na linha de comandos, execute o [comando login](/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

## <a name="create-an-iot-hub"></a>Criar um Hub IoT

Utilize o CLI Azure para criar um grupo de recursos e, em seguida, adicione um hub IoT.

1. Quando se cria um hub IoT, deve criá-lo num grupo de recursos. Utilize um grupo de recursos existente ou execute o comando [ para criar um grupo de recursos](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > O exemplo anterior cria o grupo de recursos na localização E.U.A. Oeste. Pode ver uma lista de locais disponíveis executando este comando: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Executar o seguinte [comando para criar um hub IoT](/cli/azure/iot/hub#az-iot-hub-create) no seu grupo de recursos, usando um nome globalmente único para o seu hub IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


O comando anterior cria um hub IoT no nível de preços S1 para o qual é faturado. Para mais informações, consulte os preços do [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Remover um hub IoT

Pode utilizar o Azure CLI para [eliminar um recurso individual](/cli/azure/resource), como um hub IoT, ou eliminar um grupo de recursos e todos os seus recursos, incluindo quaisquer centros IoT.

Para [eliminar um hub IoT,](/cli/azure/iot/hub#az-iot-hub-delete)executar o seguinte comando:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Para [eliminar um grupo de recursos](/cli/azure/group#az-group-delete) e todos os seus recursos, executar o seguinte comando:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização de um hub IoT, consulte os seguintes artigos:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Usando o portal Azure para gerir o IoT Hub](iot-hub-create-through-portal.md)