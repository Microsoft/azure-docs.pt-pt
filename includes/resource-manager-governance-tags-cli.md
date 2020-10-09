---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c1259584e91461865b0c7e7bbbd6aced1781827b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67708455"
---
Para adicionar duas etiquetas a um grupo de recursos, utilize o comando [az group update](/cli/azure/group):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Vamos supor que pretende adicionar uma terceira etiqueta. Execute o comando novamente com a nova etiqueta. Esta é acrescentada às etiquetas existentes.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Os recursos não herdam etiquetas do grupo de recursos. Atualmente, o seu grupo de recursos tem três etiquetas, mas os recursos não têm etiquetas. Para aplicar todas as etiquetas de um grupo de recursos a todos os respetivos recursos e reter as etiquetas existentes nos recursos, utilize o script seguinte:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Em alternativa, pode aplicar etiquetas do grupo de recursos nos recursos, sem manter as etiquetas existentes:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Para combinar vários valores numa única etiqueta, utilize uma cadeia JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Para remover todas as etiquetas num grupo de recursos, utilize:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
