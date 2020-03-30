---
title: Políticas de marcação de recursos
description: Descreve as Políticas Azure que pode atribuir para garantir o cumprimento da etiqueta.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147021"
---
# <a name="assign-policies-for-tag-compliance"></a>Atribuir políticas para o cumprimento da etiqueta

Usa a [Política Azure](../../governance/policy/overview.md) para impor regras e convenções de marcação. Ao criar uma política, evita o cenário de recursos que estão a ser implantados na sua subscrição que não têm as etiquetas esperadas para a sua organização. Em vez de aplicar manualmente tags ou procurar recursos que não sejam compatíveis, cria-se uma política que aplica automaticamente as etiquetas necessárias durante a implementação. As etiquetas também podem agora ser aplicadas aos recursos existentes com o novo efeito [Modificar](../../governance/policy/concepts/effects.md#modify) e uma tarefa de [reparação.](../../governance/policy/how-to/remediate-resources.md) A secção seguinte mostra políticas de exemplo para tags.

## <a name="policies"></a>Políticas

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a marcação de recursos, consulte [Use tags para organizar os seus recursos Azure.](tag-resources.md)
* Nem todos os tipos de recursos suportam tags. Para determinar se pode aplicar uma etiqueta a um tipo de recurso, consulte o [suporte da Tag para os recursos Azure](tag-support.md).
