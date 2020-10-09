---
title: Duração dos objetos e recursos
description: Explica a gestão vitalícia para diferentes tipos
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80681873"
---
# <a name="object-and-resource-lifetime"></a>Duração dos objetos e recursos

A renderização remota Azure distingue dois tipos: **objetos** e **recursos**.

## <a name="object-lifetime"></a>Vida útil do objeto

*Os objetos* são considerados coisas que o utilizador pode criar, modificar e destruir a seu critério. Os objetos podem ser duplicados livremente e cada instância pode sofrer mutações ao longo do tempo. [Consequentemente, entidades](entities.md) e [componentes](components.md) são objetos.

A vida útil dos objetos está totalmente sob o controlo do utilizador. No entanto, não está relacionado com a vida da representação do lado do cliente. Aulas como `Entity` e `Component` têm uma `Destroy` função que deve ser chamada para negociar o objeto no hospedeiro de renderização remota. Além disso, `Entity.Destroy()` destruirá a entidade, os seus filhos e todos os componentes dessa hierarquia.

## <a name="resource-lifetime"></a>Vida útil dos recursos

*Os recursos* são coisas cuja vida útil é inteiramente gerida pelo hospedeiro de renderização remota. Os recursos são contados internamente. São negociados quando ninguém os referencia mais.

A maioria dos recursos só pode ser criada indiretamente, normalmente carregando-os a partir de um ficheiro. Quando o mesmo ficheiro é carregado várias vezes, a Renderização Remota Azure retornará a mesma referência e não carregará novamente os dados.

Muitos recursos são imutáveis, por [exemplo, malhas](meshes.md) e [texturas.](textures.md) Alguns recursos são mutáveis, por exemplo, [materiais.](materials.md) Uma vez que os recursos são frequentemente partilhados, modificar um recurso pode afetar vários objetos. Por exemplo, mudar a cor de um material mudará a cor de todos os objetos que usam malhas, que por sua vez referenciam esse material.

### <a name="built-in-resources"></a>Recursos incorporados

A renderização remota Azure contém alguns recursos incorporados, que podem ser carregados premedindo o respetivo identificador `builtin://` durante a chamada para `AzureSession.Actions.LoadXYZAsync()` . Os recursos incorporados disponíveis estão listados na documentação para cada característica. Por exemplo, o capítulo do [céu](../overview/features/sky.md) lista as texturas do céu incorporadas.

## <a name="general-lifetime"></a>Vida útil geral

A vida útil de todos os objetos e recursos está ligada à ligação. Na desconexão tudo é descartado. Ao voltar a ligar-se à mesma sessão, o gráfico de cena estará vazio e todos os recursos são purgados.

Na prática, carregar o mesmo recurso numa sessão, depois de uma desconexão, é geralmente mais rápido do que a primeira vez. É o caso porque a maioria dos recursos deve ser descarregado do Azure Storage pela primeira vez, o que não é necessário na segunda vez, poupando uma quantidade considerável de tempo.

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Componentes](components.md)
* [Modelos](models.md)
