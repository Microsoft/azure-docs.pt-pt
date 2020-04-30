---
title: Como marcar uma máquina virtual Azure Linux
description: Aprenda a marcar uma máquina virtual Azure Linux criada em Azure utilizando o modelo de implementação do Gestor de Recursos.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: fd4a93f4c0b2f052fe5c9890bee01e5da0dcead2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460958"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina virtual Linux em Azure
Este artigo descreve diferentes formas de marcar uma máquina virtual Linux em Azure através do modelo de implementação do Gestor de Recursos. As etiquetas são pares de chaves/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. O Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Por favor, note que as tags são suportadas apenas para recursos criados apenas através do modelo de implementação do Gestor de Recursos.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com Azure CLI

Para começar, precisa do mais recente [Azure CLI](/cli/azure/install-azure-cli) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index#az-login).

Pode visualizar todas as propriedades para uma determinada Máquina Virtual, incluindo as etiquetas, utilizando este comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Para adicionar uma nova etiqueta VM através do ClI Azure, pode utilizar o `azure vm update` comando juntamente com o parâmetro de etiqueta **-set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover as etiquetas, pode utilizar o `azure vm update` parâmetro **-remover** o comando.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Agora que aplicámos etiquetas aos nossos recursos Azure CLI e portal, vamos dar uma olhada nos detalhes de utilização para ver as etiquetas no portal de faturação.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre a marcação dos seus recursos Azure, consulte a [visão geral do Gestor de Recursos Azure][Azure Resource Manager Overview] e usando [tags para organizar os seus Recursos Azure.][Using Tags to organize your Azure Resources]
* Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [Understanding your Azure Bill][Understanding your Azure Bill] e Gain insights sobre o seu consumo de recursos Microsoft [Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
