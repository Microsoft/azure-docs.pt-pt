---
title: Malhas
description: Definição de malhas no âmbito da renderização remota Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: eea43f48abef5e2b258251d46eca1061a2263519
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613837"
---
# <a name="meshes"></a>Malhas

## <a name="mesh-resource"></a>Recurso de malha

As malhas são um [recurso partilhado](../concepts/lifetime.md)imutável, que só pode ser criado através da conversão de [modelos.](../how-tos/conversion/model-conversion.md) As malhas contêm uma ou *múltiplas submechas.* Cada submesh refere um [material](materials.md) com o qual deve ser prestado por defeito. Para colocar uma malha no espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [Entidade.](entities.md)

### <a name="mesh-resource-properties"></a>Propriedades de recursos de malha

As `Mesh` propriedades da classe são:

* **Materiais:** Uma variedade de materiais. Cada material é utilizado por um submesh diferente. Várias entradas na matriz podem fazer referência ao mesmo [material](materials.md). Estes dados não podem ser modificados em tempo de execução.

* **Limites:** Uma caixa de delimitação alinhada com o eixo local (AABB) dos vértices da malha.

## <a name="meshcomponent"></a>MeshComponent

A `MeshComponent` classe é usada para colocar um exemplo de um recurso de malha. Cada MeshComponent refere uma única malha. Pode sobrepor-se aos materiais utilizados para render cada submesh.

### <a name="meshcomponent-properties"></a>Propriedades MeshComponent

* **Malha:** O recurso de malha que é utilizado por este componente.

* **Materiais:** A matriz de materiais especificados no próprio componente da malha. A matriz terá sempre o mesmo comprimento que a matriz *de Materiais* no recurso de malha. Os materiais que não devem ser ultrapassados do predefinido da malha, são definidos como *nulos* nesta matriz.

* **Materiais Usados:** A matriz de materiais realmente usados para cada submesh. Será idêntico aos dados da matriz *de Materiais,* para valores não nulos. Caso contrário, contém o valor da matriz *de materiais* na placa de malha.

## <a name="api-documentation"></a>Documentação da API

* [Classe C# Malha](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# Classe MeshComponent](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [Classe C++ malha](https://docs.microsoft.com/cpp/api/remote-rendering/mesh)
* [Classe C++ MeshComponent](https://docs.microsoft.com/cpp/api/remote-rendering/meshcomponent)

## <a name="next-steps"></a>Próximos passos

* [Materiais](materials.md)
