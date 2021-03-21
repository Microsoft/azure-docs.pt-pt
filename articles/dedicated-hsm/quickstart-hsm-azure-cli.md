---
title: 'Quickstart: Criar Azure Dedicado HSM com o Azure CLI'
description: Criar, mostrar, listar, atualizar e eliminar HSMs dedicados Azure utilizando o Azure CLI.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020965"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Quickstart: Criar um HSM dedicado a azure utilizando o CLI Azure

Este artigo descreve como criar e gerir um HSM dedicado Azure utilizando a extensão [Az-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Pode [criar uma conta gratuita](https://azure.microsoft.com/free/) se não tiver uma.
  
  Se tiver mais do que uma subscrição do Azure, desapedaça a subscrição para a faturação com o comando [conjunto de conta](/cli/azure/account#az_account_set) Azure CLI.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- Todos os requisitos são cumpridos para um HSM dedicado, incluindo registo, aprovação e uma rede virtual e máquina virtual para usar para o provisionamento. Para obter mais informações sobre os requisitos e pré-requisitos dedicados do HSM, consulte [Tutorial: Implantação de HSMs numa rede virtual existente utilizando o Azure CLI](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) é um recipiente lógico para implantar e gerir os recursos da Azure como um grupo. Se ainda não tiver um grupo de recursos para o HSM dedicado, crie um utilizando o comando de criação do [grupo az.](/cli/azure/group#az_group_create) O exemplo a seguir cria um grupo de recursos denominado `myRG` na `westus` região de Azure:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Criar um HSM dedicado

Para criar um HSM dedicado, use o comando [az dedicado-hsm criar](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) comando. O exemplo a seguir prevê um HSM dedicado nomeado `hsm1` na `westus` região, `myRG` grupo de recursos, e subscrição especificada, rede virtual e sub-rede. Os parâmetros necessários são `name` `location` , e `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

A colocação leva aproximadamente 25 a 30 minutos para ser concluída.

## <a name="get-a-dedicated-hsm"></a>Obtenha um HSM dedicado

Para obter um HSM dedicado atual, executar o comando [de show az dedicado-hsm.](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) O exemplo a seguir obtém o `hsm1` HSM dedicado no `myRG` grupo de recursos.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Atualize um HSM dedicado

Utilize o comando [de atualização az dedicado ao hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) para atualizar um HSM dedicado. O exemplo a seguir atualiza o `hsm1` HSM dedicado no `myRG` grupo de recursos e as suas etiquetas:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Lista de HSMs dedicados

Executar o comando [da lista az dedicado-hsm](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) para obter informações sobre os HSMs dedicados atuais. O exemplo a seguir enumera os HSMs dedicados no `myRG` grupo de recursos:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Remover um HSM dedicado

Para remover um HSM dedicado, utilize o comando [az dedicado-hsm eliminar.](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) O exemplo a seguir elimina o `hsm1` HSM dedicado do `myRG` grupo de recursos:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

Se já não precisar do grupo de recursos que criou para o HSM dedicado, pode eliminá-lo executando o comando de eliminação do [grupo az.](/cli/azure/group#az_group_delete) Este comando elimina o grupo e todos os recursos nele, incluindo todos os que não estejam relacionados com o HSM dedicado. O exemplo a seguir elimina o `myRG` grupo de recursos e tudo o que nele está:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure Dedicado HSM, consulte [Azure Dedicado HSM](overview.md).
