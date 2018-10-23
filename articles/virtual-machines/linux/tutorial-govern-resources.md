---
title: Tutorial – Governar máquinas virtuais do Azure com a CLI do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para gerir máquinas virtuais do Azure através da aplicação de RBAC, políticas, bloqueios e etiquetas
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/12/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 715a8e5bab9e5d16b8c0e54298101df856d51a9a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309864"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>Tutorial: Saiba mais acerca da governação de máquinas virtuais do Linux com a CLI do Azure

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente, este tutorial exige que execute uma versão da CLI do Azure que seja a 2.0.30 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Compreender o âmbito

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Neste tutorial, vai aplicar todas as definições de gestão a um grupo de recursos para poder remover facilmente essas definições quando terminar.

Vamos então criar esse grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Atualmente, o grupo de recursos está vazio.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Deve confirmar que os utilizadores na sua organização possuem o nível adequado de acesso a estes recursos. Não vai querer conceder acesso ilimitado aos utilizadores, mas também precisa de confirmar que estes podem fazer o seu trabalho. O [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md) permite-lhe gerir os utilizadores que têm permissão para concluir ações específicas num âmbito.

Para criar e remover atribuições de funções, os utilizadores devem ter acesso `Microsoft.Authorization/roleAssignments/*`. Este acesso é concedido através das funções Proprietário ou Administrador de Acesso dos Utilizadores.

Para gerir soluções de máquina virtual, existem três funções de recursos específicos que fornecem o acesso normalmente necessário:

* [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Contribuidor de Rede](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Contribuidor de Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a utilizadores individuais, muitas vezes, é mais fácil utilizar um grupo do Azure Active Directory que tem utilizadores que precisam de realizar ações semelhantes. Em seguida, atribua esse grupo à função adequada. Neste artigo, utilize um grupo existente para gerir a máquina virtual ou utilize o portal para [criar um grupo do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Depois de criar um grupo novo ou encontrar um existente, utilize o comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para atribuir o novo grupo do Azure Active Directory à função Contribuidor de Máquina Virtual do grupo de recursos.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Se receber um erro a indicar que o **Principal<guid> não existe no diretório**, o novo grupo ainda não foi propagado pelo Azure Active Directory. Tente executar o comando novamente.

Normalmente, pode repetir o processo para o *Contribuidor de Rede* e o *Contribuidor de Conta de Armazenamento* para confirmar que os utilizadores estão atribuídos para gerir os recursos implementados. Neste artigo, pode ignorar esses passos.

## <a name="azure-policy"></a>Azure Policy

O [Azure Policy](../../azure-policy/azure-policy-introduction.md) ajuda-o a certificar-se de que todos os recursos na subscrição cumprem os padrões empresariais. A subscrição já tem várias definições de política. Para ver as definições de política disponíveis, utilize o comando [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list):

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Poderá ver as definições de política existentes. O tipo de política é **BuiltIn** ou **Personalizado**. Examine as definições para encontrar aquelas que descrevem uma condição que pretenda atribuir. Neste artigo, pode atribuir políticas para:

* Limitar as localizações de todos os recursos.
* Limitar os SKUs das máquinas virtuais.
* Auditar máquinas virtuais que não utilizam discos geridos.

No exemplo seguinte, obtém três definições de políticas baseadas no nome a apresentar. Utiliza o comando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) para atribuir essas definições ao grupo de recursos. Para algumas políticas, forneça valores de parâmetros para especificar os valores permitidos.

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

Os [bloqueios de recursos](../../azure-resource-manager/resource-group-lock-resources.md) impedem que os utilizadores na sua organização eliminem ou modifiquem acidentalmente recursos críticos. Ao contrário do controlo de acesso baseado em funções, os bloqueios de recursos aplicam uma restrição a todos os utilizadores e a todas as funções. Pode definir o nível do bloqueio para *CanNotDelete* ou *ReadOnly*.

Para criar ou eliminar bloqueios de gestão, tem de ter acesso a ações `Microsoft.Authorization/locks/*`. Das funções incorporadas, apenas **Proprietário** e **Administrador de Acesso dos Utilizadores** têm acesso a essas ações.

Para bloquear a máquina virtual e o grupo de segurança de rede, utilize o comando [az lock create](/cli/azure/lock#az_lock_create):

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

O utilizador aplica as [etiquetas](../../azure-resource-manager/resource-group-using-tags.md) aos recursos do Azure para os organizar por categorias. Cada etiqueta é constituída por um nome e um valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Para aplicar etiquetas a uma máquina virtual, utilize o comando [az resource tag](/cli/azure/resource#az_resource_tag). As etiquetas existentes no recurso não são mantidas.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Localizar recursos por etiqueta

Para localizar recursos com um nome e valor de etiqueta, utilize o comando [az resource list](/cli/azure/resource#az_resource_list):

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Pode utilizar os valores devolvidos das tarefas de gestão, como parar todas as máquinas virtuais com um valor de etiqueta.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Ver custos por valores de etiqueta

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpar recursos

O grupo de segurança de rede bloqueado não pode ser eliminado até que o bloqueio seja removido. Para remover o bloqueio, obtenha os IDs dos bloqueios e forneça-os ao comando [az lock delete](/cli/azure/lock#az_lock_delete):

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

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o Grupo de Recursos, a VM e todos os recursos relacionados. Encerre a sessão SSH da VM e, em seguida, elimine os recursos da seguinte forma:

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

Avance para o próximo tutorial, para saber mais sobre máquinas virtuais de elevada disponibilidade.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitoring.md)

