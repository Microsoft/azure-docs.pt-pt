---
title: Tutorial - Gerir máquinas virtuais com o CLI
description: Neste tutorial, você aprende a usar o CLI Azure para gerir máquinas virtuais Azure aplicando RBAC, polícias, fechaduras e tags.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 890afadc82acc90ab0324058e07aa5c4d34d04e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87926166"
---
# <a name="tutorial-learn-about-linux-virtual-machine-management-with-azure-cli"></a>Tutorial: Saiba mais sobre a gestão de máquinas virtuais linux com o Azure CLI

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente, este tutorial exige que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="understand-scope"></a>Compreender o âmbito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Neste tutorial, vai aplicar todas as definições de gestão a um grupo de recursos para poder remover facilmente essas definições quando terminar.

Vamos então criar esse grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Atualmente, o grupo de recursos está vazio.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Deve confirmar que os utilizadores na sua organização possuem o nível adequado de acesso a estes recursos. Não vai querer conceder acesso ilimitado aos utilizadores, mas também precisa de confirmar que estes podem fazer o seu trabalho. [O controlo de acesso baseado em funções (Azure RBAC) permite-lhe](../../role-based-access-control/overview.md) gerir quais os utilizadores que têm permissão para completar ações específicas num âmbito.

Para criar e remover atribuições de funções, os utilizadores devem ter acesso `Microsoft.Authorization/roleAssignments/*`. Este acesso é concedido através das funções Proprietário ou Administrador de Acesso dos Utilizadores.

Para gerir soluções de máquina virtual, existem três funções de recursos específicos que fornecem o acesso normalmente necessário:

* [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Contribuidor de Rede](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Contribuidor de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a utilizadores individuais, muitas vezes, é mais fácil utilizar um grupo do Azure Active Directory que tem utilizadores que precisam de realizar ações semelhantes. Em seguida, atribua esse grupo à função adequada. Neste artigo, utilize um grupo existente para gerir a máquina virtual ou utilize o portal para [criar um grupo do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Depois de criar um grupo novo ou encontrar um existente, utilize o comando [az role assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) para atribuir o novo grupo do Azure Active Directory à função Contribuidor de Máquina Virtual do grupo de recursos.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Se receber um erro a indicar que o **Principal\<guid> não existe no diretório**, o novo grupo ainda não foi propagado pelo Azure Active Directory. Tente executar o comando novamente.

Normalmente, pode repetir o processo para o *Contribuidor de Rede* e o *Contribuidor de Conta de Armazenamento* para confirmar que os utilizadores estão atribuídos para gerir os recursos implementados. Neste artigo, pode ignorar esses passos.

## <a name="azure-policy"></a>Azure Policy

O [Azure Policy](../../governance/policy/overview.md) ajuda-o a certificar-se de que todos os recursos na subscrição cumprem os padrões empresariais. A subscrição já tem várias definições de política. Para ver as definições de política disponíveis, utilize o comando [az policy definition list](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Poderá ver as definições de política existentes. O tipo de política é **BuiltIn** ou **Personalizado**. Examine as definições para encontrar aquelas que descrevem uma condição que pretenda atribuir. Neste artigo, pode atribuir políticas para:

* Limitar as localizações de todos os recursos.
* Limitar os SKUs das máquinas virtuais.
* Auditar máquinas virtuais que não utilizam discos geridos.

No exemplo seguinte, obtém três definições de políticas baseadas no nome a apresentar. Utiliza o comando [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) para atribuir essas definições ao grupo de recursos. Para algumas políticas, forneça valores de parâmetros para especificar os valores permitidos.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

O exemplo anterior assume que já conhece os parâmetros de uma política. Se precisar de ver os parâmetros, utilize:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Implementar a máquina virtual

Atribuiu funções e políticas, pelo que está pronto para implementar a sua solução. O tamanho predefinido é Standard_DS1_v2, que é um dos seus SKUs permitidos. O comando cria chaves SSH caso estas não existam numa localização predefinida.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Após a conclusão da implementação, pode aplicar mais definições de gestão à solução.

## <a name="lock-resources"></a>Bloquear recursos

Os [bloqueios de recursos](../../azure-resource-manager/management/lock-resources.md) impedem que os utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos críticos. Ao contrário do controlo de acesso baseado em funções, os bloqueios de recursos aplicam uma restrição a todos os utilizadores e a todas as funções. Pode definir o nível do bloqueio para *CanNotDelete* ou *ReadOnly*.

Para criar ou eliminar bloqueios de gestão, tem de ter acesso a ações `Microsoft.Authorization/locks/*`. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

Para bloquear a máquina virtual e o grupo de segurança de rede, utilize o comando [az lock create](/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-create):

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Para testar os bloqueios, tente executar o seguinte comando:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Verá um erro a indicar que a operação de eliminação não pode ser concluída devido a um bloqueio. O grupo de recursos só poderá ser eliminado se remover especificamente os bloqueios. Este passo é apresentado em [Limpar recursos](#clean-up-resources).

## <a name="tag-resources"></a>Etiquetar recursos

Aplica [etiquetas](../../azure-resource-manager/management/tag-resources.md) nos seus recursos Azure para organizá-las logicamente por categorias. Cada etiqueta é constituída por um nome e um valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Para aplicar etiquetas a uma máquina virtual, utilize o comando [az resource tag](/cli/azure/resource?view=azure-cli-latest#az-resource-list). As etiquetas existentes no recurso não são mantidas.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Localizar recursos por etiqueta

Para localizar recursos com um nome e valor de etiqueta, utilize o comando [az resource list](/cli/azure/resource?view=azure-cli-latest#az-resource-list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Pode utilizar os valores devolvidos das tarefas de gestão, como parar todas as máquinas virtuais com um valor de etiqueta.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Ver custos por valores de etiqueta

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

O grupo de segurança de rede bloqueado não pode ser eliminado até que o bloqueio seja removido. Para remover o bloqueio, obtenha os IDs dos bloqueios e forneça-os ao comando [az lock delete](/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-delete):

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Quando já não é necessário, pode utilizar o comando de eliminação do [grupo az](/cli/azure/group?view=azure-cli-latest#az-group-delete) para remover o grupo de recursos, VM e todos os recursos relacionados. Encerre a sessão SSH da VM e, em seguida, elimine os recursos da seguinte forma:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Atribuir utilizadores a uma função
> * Aplicar políticas que impõem normas
> * Proteger recursos críticos com bloqueios
> * Etiquetar recursos para faturação e gestão

Avance para o próximo tutorial para aprender como identificar alterações e gerir atualizações de pacotes numa máquina virtual.

> [!div class="nextstepaction"]
> [Gerir máquinas virtuais](tutorial-config-management.md)
