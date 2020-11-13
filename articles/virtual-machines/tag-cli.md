---
title: Como marcar uma máquina virtual Azure usando o CLI
description: Saiba a marcação de uma máquina virtual utilizando o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595069"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Como marcar um VM usando o CLI

Este artigo descreve como marcar um VM usando o Azure CLI. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Também pode marcar uma máquina virtual utilizando a Azure [PowerShell](tag-powershell.md).


Pode ver todas as propriedades para um determinado VM, incluindo as etiquetas, utilizando `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Para adicionar uma nova etiqueta VM através do CLI Azure, pode utilizar o `azure vm update` comando juntamente com o parâmetro `--set` tag:

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover as etiquetas, pode utilizar o `--remove` parâmetro no `azure vm update` comando.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Agora que aplicámos etiquetas nos nossos recursos Azure CLI e no Portal, vamos dar uma olhada nos detalhes de utilização para ver as etiquetas no portal de faturação.


**Próximos passos**

- Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure](../azure-resource-manager/management/overview.md) e [a utilização de Tags para organizar os seus Recursos Azure.](../azure-resource-manager/management/tag-resources.md)
- Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [compreender o seu Azure Bill](../cost-management-billing/understand/review-individual-bill.md) e obter informações sobre o seu consumo de recursos Microsoft [Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
