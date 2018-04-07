---
title: Como tag de uma máquina virtual do Linux do Azure | Microsoft Docs
description: Saiba mais sobre a marcação de uma máquina virtual de Linux do Azure criada no Azure utilizando o modelo de implementação Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
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
ms.openlocfilehash: 103784d97301313379b2fd336624e5cda591c5a6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como tag de uma máquina virtual Linux no Azure
Este artigo descreve as diferentes formas para marcar uma máquina virtual Linux no Azure através do modelo de implementação Resource Manager. As etiquetas são definidos pelo utilizador pares chave-valor que podem ser colocadas diretamente um recurso ou um grupo de recursos. Atualmente, o Azure suporta até 15 etiquetas por recursos e o grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção, as etiquetas são suportadas para os recursos criados através de apenas o modelo de implementação do Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marcação com a CLI do Azure
Para começar, [instalar e configurar a CLI do Azure](../../xplat-cli-azure-resource-manager.md) e certifique-se de que está no modo Resource Manager (`azure config mode arm`).

Pode ver todas as propriedades para uma determinada máquina Virtual, incluindo as etiquetas, utilizar este comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Para adicionar uma nova etiqueta VM através da CLI do Azure, pode utilizar o `azure vm set` comando juntamente com o parâmetro de tag **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as etiquetas, pode utilizar o **– T** parâmetro o `azure vm set` comando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Agora que tem Aplicamos etiquetas aos nossos recursos CLI do Azure e o Portal, vamos ver os detalhes de utilização para ver as etiquetas no portal de faturação.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre a etiquetagem os recursos do Azure, consulte o artigo [descrição geral do Azure Resource Manager] [ Azure Resource Manager Overview] e [etiquetas a utilizar para organizar os recursos do Azure] [ Using Tags to organize your Azure Resources].
* Para ver como as etiquetas podem ajudá-lo a gerir a utilização de recursos do Azure, consulte [compreender a fatura do Azure] [ Understanding your Azure Bill] e [obter informações acerca do consumo de recursos do Microsoft Azure] [Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
