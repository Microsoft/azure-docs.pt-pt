---
title: Meshes
description: Definição de maças no âmbito da renderização remota azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681769"
---
# <a name="meshes"></a>Meshes

## <a name="mesh-resource"></a>Recurso de malha

As malshes são um [recurso partilhado](../concepts/lifetime.md)imutável, que só pode ser criado através da conversão de [modelos.](../how-tos/conversion/model-conversion.md) As malsheas contêm uma ou *múltiplas submeshes.* Cada submalha refere um [material](materials.md) com o qual deve ser prestado por defeito. Para colocar uma malha no espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [Entidade](entities.md).

### <a name="mesh-resource-properties"></a>Propriedades de recursos de malha

As `Mesh` propriedades da classe são:

* **Materiais:** Uma variedade de materiais. Cada material é usado por uma submalha diferente. Várias entradas na matriz podem fazer referência ao mesmo [material](materials.md). Estes dados não podem ser modificados no prazo de execução.

* **Limites:** Uma caixa de delimitação alinhada com eixos locais (AABB) dos vértices da malha.

## <a name="meshcomponent"></a>Componente de Malha

A `MeshComponent` classe é usada para colocar uma instância de um recurso de malha. Cada MalhaComponent refere uma única malha. Pode sobrepor-se aos materiais utilizados para renderizar cada submesh.

### <a name="meshcomponent-properties"></a>Propriedades meshComponent

* **Malha:** O recurso de malha que é utilizado por este componente.

* **Materiais:** A matriz de materiais especificadas no próprio componente da malha. A matriz terá sempre o mesmo comprimento que a matriz *de Materiais* no recurso de malha. Os materiais que não devem ser ultrapassados da malha predefinida, devem *ser anulados* nesta matriz.

* **Materiais Usados:** A matriz de materiais realmente usados para cada sub-malha. Será idêntico aos dados da matriz *de Materiais,* para valores não nulos. Caso contrário, contém o valor da matriz de *materiais* na instância da malha.

## <a name="next-steps"></a>Passos seguintes

* [Materiais](materials.md)
