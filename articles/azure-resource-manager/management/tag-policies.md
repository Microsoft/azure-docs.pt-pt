---
title: Políticas de marcação de recursos
description: Descreve as Políticas Azure que pode atribuir para garantir o cumprimento da etiqueta.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89293745"
---
# <a name="assign-policies-for-tag-compliance"></a>Atribuir políticas para o cumprimento da etiqueta

Usa [a Política Azure](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, evita o cenário de recursos que estão a ser implantados na sua subscrição que não têm as etiquetas esperadas para a sua organização. Em vez de aplicar manualmente etiquetas ou procurar recursos que não sejam compatíveis, cria uma política que aplica automaticamente as etiquetas necessárias durante a implementação. As etiquetas também podem agora ser aplicadas aos recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma [tarefa de remediação.](../../governance/policy/how-to/remediate-resources.md) A secção seguinte mostra políticas de exemplo para tags.

## <a name="policies"></a>Políticas

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Passos seguintes

* Para aprender a marcar recursos, consulte [use tags para organizar os seus recursos Azure.](tag-resources.md)
* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte [o suporte da Tag para os recursos do Azure.](tag-support.md)
