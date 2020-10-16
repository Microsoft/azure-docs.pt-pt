---
title: Como marcar uma máquina virtual Azure usando o CLI
description: Saiba a marcação de uma máquina virtual utilizando o Azure CLI.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a417e7cff4c7afb601861ddfe09eec171f0cf15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320618"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina virtual Linux em Azure
Este artigo descreve diferentes formas de marcar uma máquina virtual Linux em Azure através do modelo de implementação do Gestor de Recursos. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Por favor, note que as tags são suportadas para recursos criados apenas através do modelo de implementação do Gestor de Recursos.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com Azure CLI

Para começar, precisa do mais recente [Azure CLI](/cli/azure/install-azure-cli) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index#az-login).

Pode visualizar todas as propriedades de uma determinada Máquina Virtual, incluindo as tags, utilizando este comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Para adicionar uma nova etiqueta VM através do CLI Azure, pode utilizar o `azure vm update` comando juntamente com o parâmetro da etiqueta **--conjunto**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover as etiquetas, pode utilizar o parâmetro **de remoção** no `azure vm update` comando.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Agora que aplicámos etiquetas nos nossos recursos Azure CLI e no Portal, vamos dar uma olhada nos detalhes de utilização para ver as etiquetas no portal de faturação.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure][Azure Resource Manager Overview] e [a utilização de Tags para organizar os seus Recursos Azure.][Using Tags to organize your Azure Resources]
* Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [compreender o seu Azure Bill][Understanding your Azure Bill] e obter informações sobre o seu consumo de recursos Microsoft [Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]: ../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../cost-management-billing/manage/usage-rate-card-overview.md
