---
title: Como marcar uma máquina virtual Linux do Azure
description: Saiba mais sobre como marcar uma máquina virtual Linux do Azure criada no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 871159a660d03469b67461f5eb1ce8245d4c3611
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369155"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina virtual do Linux no Azure
Este artigo descreve diferentes maneiras de marcar uma máquina virtual do Linux no Azure por meio do modelo de implantação do Resource Manager. As marcas são pares de chave/valor definidos pelo usuário que podem ser colocados diretamente em um recurso ou em um grupo de recursos. Atualmente, o Azure dá suporte a até 15 marcas por recurso e grupo de recursos. As marcas podem ser colocadas em um recurso no momento da criação ou adicionadas a um recurso existente. Observe que as marcas têm suporte apenas para recursos criados por meio do modelo de implantação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com CLI do Azure

Para começar, você precisa das [CLI do Azure](/cli/azure/install-azure-cli) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index#az-login).

Você pode exibir todas as propriedades de uma determinada máquina virtual, incluindo as marcas, usando este comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Para adicionar uma nova marca de VM por meio do CLI do Azure, você pode usar o comando `azure vm update` junto com o parâmetro de marca **--set**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover marcas, você pode usar o parâmetro **--Remove** no comando `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Agora que aplicamos marcas aos nossos recursos CLI do Azure e ao portal, vamos dar uma olhada nos detalhes de uso para ver as marcas no portal de cobrança.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre como marcar os recursos do Azure, confira [Azure Resource Manager visão geral][Azure Resource Manager Overview] e [usando marcas para organizar os recursos do Azure][Using Tags to organize your Azure Resources].
* Para ver como as marcas podem ajudá-lo a gerenciar seu uso de recursos do Azure, consulte [noções básicas sobre sua fatura do Azure][Understanding your Azure Bill] e [obter informações sobre o consumo de recursos Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
