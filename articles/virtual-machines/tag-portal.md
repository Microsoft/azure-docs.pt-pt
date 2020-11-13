---
title: Como marcar um VM usando o portal Azure
description: Saiba como marcar uma máquina virtual utilizando o portal Azure.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594974"
---
# <a name="tagging-a-vm-using-the-portal"></a>Marcação de um VM usando o portal

Este artigo descreve como adicionar tags a um VM usando o portal. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente.


1. Navegue para o seu VM no portal.
1. No **Essencial,** **selecione Clique aqui para adicionar tags**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Screenshot da secção Essencial da página VM.":::

1. Adicione um valor para **Nome** e **Valor** e, em seguida, selecione **Guardar**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Screenshot da página para adicionar um par de valor chave como uma etiqueta.":::



**Próximos passos**

- Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure](../azure-resource-manager/management/overview.md) e [a utilização de Tags para organizar os seus Recursos Azure.](../azure-resource-manager/management/tag-resources.md)
- Para ver como as tags podem ajudá-lo a gerir o seu uso de recursos Azure, consulte [compreender o seu Azure Bill](../cost-management-billing/understand/review-individual-bill.md) e obter informações sobre o seu consumo de recursos Microsoft [Azure](../cost-management-billing/manage/usage-rate-card-overview.md).