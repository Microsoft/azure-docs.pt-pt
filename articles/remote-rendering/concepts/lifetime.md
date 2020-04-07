---
title: Vida útil do objeto e do recurso
description: Explica a gestão vitalícia para diferentes tipos
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: d031ff4a6ee86da2843f0f18ac428c50f7cfc121
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681873"
---
# <a name="object-and-resource-lifetime"></a>Vida útil do objeto e do recurso

A renderização remota azure distingue entre dois tipos: **objetos** e **recursos.**

## <a name="object-lifetime"></a>Vida útil do objeto

*Os objetos* são considerados coisas que o utilizador pode criar, modificar e destruir a seu próprio critério. Os objetos podem ser duplicados livremente e cada instância pode sofrer mutações ao longo do tempo. Consequentemente, [entidades](entities.md) e [componentes](components.md) são objetos.

A vida útil dos objetos está totalmente sob o controlo do utilizador. No entanto, não está relacionado com a vida útil da representação do lado do cliente. Aulas `Entity` como `Component` e `Destroy` têm uma função que deve ser chamada para desalojar o objeto no hospedeiro de renderização remota. Além `Entity.Destroy()` disso, destruirá a entidade, os seus filhos e todos os componentes dessa hierarquia.

## <a name="resource-lifetime"></a>Vida útil do recurso

*Os recursos* são coisas cuja vida é inteiramente gerida pelo hospedeiro de renderização remota. Os recursos são contados internamente. São transferidos quando ninguém os faz referência.

A maioria dos recursos só pode ser criado indiretamente, normalmente carregando-os a partir de um ficheiro. Quando o mesmo ficheiro é carregado várias vezes, a Renderização Remota Azure devolverá a mesma referência e não carregará novamente os dados.

Muitos recursos são imutáveis, por exemplo [maltratas](meshes.md) e [texturas.](textures.md) Alguns recursos são mutáveis, porém, por exemplo [materiais.](materials.md) Uma vez que os recursos são frequentemente partilhados, modificar um recurso pode afetar vários objetos. Por exemplo, mudar a cor de um material mudará a cor de todos os objetos que usam maltratas, que por sua vez referenciam esse material.

### <a name="built-in-resources"></a>Recursos incorporados

A Renderização Remota Azure contém alguns recursos incorporados, que podem `builtin://` ser carregados através da pré-aplicação do respetivo identificador durante a chamada para `AzureSession.Actions.LoadXYZAsync()`. Os recursos integrados disponíveis estão listados na documentação para cada característica respetiva. Por exemplo, o [capítulo do céu](../overview/features/sky.md) lista as texturas do céu incorporado.

## <a name="general-lifetime"></a>Vida geral

A vida útil de todos os objetos e recursos está ligada à ligação. Em desconexão tudo é descartado. Ao voltar à mesma sessão, o gráfico de cena estará vazio e todos os recursos são purgados.

Na prática, carregar o mesmo recurso numa sessão, após uma desconexão, é geralmente mais rápido do que a primeira vez. É o caso porque a maioria dos recursos deve ser descarregado do Azure Storage pela primeira vez, o que não é necessário pela segunda vez, poupando bastante tempo.

## <a name="next-steps"></a>Passos seguintes

* [Entidades](entities.md)
* [Componentes](components.md)
* [Modelos](models.md)
