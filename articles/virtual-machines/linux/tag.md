---
title: Como Etiquetar uma máquina virtual Linux do Azure | Documentos da Microsoft
description: Saiba mais sobre a marcação de uma máquina de virtual de Linux do Azure criada no Azure com o modelo de implementação do Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 290105b4e5e3ac3337b0be1b7d437601223bdf68
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708735"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como Etiquetar uma máquina virtual Linux no Azure
Este artigo descreve as diferentes formas de etiquetar uma máquina virtual Linux no Azure através do modelo de implementação do Resource Manager. As etiquetas são pares de chave/valor definido pelo utilizador que podem ser colocados diretamente num recurso ou grupo de recursos. Atualmente, o Azure suporta até 15 etiquetas por recurso e grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção, as etiquetas são suportadas para os recursos criados por meio de apenas o modelo de implementação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com a CLI do Azure

Para começar, terá da versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index#az-login).

Pode ver todas as propriedades de uma determinada máquina Virtual, incluindo as etiquetas, utilizando este comando:

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Para adicionar uma nova etiqueta VM através da CLI do Azure, pode utilizar o `azure vm update` comando, juntamente com o parâmetro de marca **– definir**:

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Para remover etiquetas, pode utilizar o **– remover** parâmetro no `azure vm update` comando.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Agora que estamos tiver aplicado etiquetas para os nossos recursos da CLI do Azure e o Portal, vamos dar uma olhada nos detalhes de utilização para ver as etiquetas no portal de faturação.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre marcação seus recursos do Azure, veja [descrição geral do Azure Resource Manager][Azure Resource Manager Overview] and [Using Tags to organize your Azure Resources][Using Tags to organize your Azure Resources].
* Para ver como as etiquetas podem ajudar a gerir a utilização de recursos do Azure, consulte [entender a sua fatura do Azure][Understanding your Azure Bill] and [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
