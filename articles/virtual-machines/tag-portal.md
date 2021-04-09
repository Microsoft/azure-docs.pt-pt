---
title: Como marcar um VM usando o portal Azure
description: Saiba como marcar uma máquina virtual utilizando o portal Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897413"
---
# <a name="tagging-a-vm-using-the-portal"></a>Marcação de um VM usando o portal

Este artigo descreve como adicionar tags a um VM usando o portal. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente.


1. Navegue para o seu VM no portal.
1. No **Essencial,** **selecione Clique aqui para adicionar tags**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot da secção Essencial da página VM.":::

1. Adicione um valor para **Nome** e **Valor** e, em seguida, selecione **Guardar**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot da página para adicionar um par de valor chave como uma etiqueta.":::

### <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure](../azure-resource-manager/management/overview.md) e [a utilização de Tags para organizar os seus Recursos Azure.](../azure-resource-manager/management/tag-resources.md)
- Para ver como as etiquetas podem ajudá-lo a gerir o seu uso dos recursos Azure, consulte [compreender a sua Conta de Azure.](../cost-management-billing/understand/review-individual-bill.md)
