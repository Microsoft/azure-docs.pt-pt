---
title: Políticas de marcação de recursos
description: Descreve as Políticas Azure que pode atribuir para garantir o cumprimento da etiqueta.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147855"
---
# <a name="assign-policies-for-tag-compliance"></a>Atribuir políticas para o cumprimento da etiqueta

Usa a [Política Azure](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, evita o cenário de recursos que estão a ser implantados na sua subscrição que não têm as etiquetas esperadas para a sua organização. Em vez de aplicar manualmente tags ou procurar recursos que não sejam compatíveis, cria-se uma política que aplica automaticamente as etiquetas necessárias durante a implementação. As etiquetas também podem agora ser aplicadas aos recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma tarefa de [reparação.](../../governance/policy/how-to/remediate-resources.md) A secção seguinte mostra políticas de exemplo para tags.

## <a name="policies"></a>Políticas

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a marcação de recursos, consulte [Use tags para organizar os seus recursos Azure.](tag-resources.md)
* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
