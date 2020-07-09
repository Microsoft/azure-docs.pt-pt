---
title: Substituição do estado hierárquico
description: Explica o conceito de estado hierárquico sobrepõe-se a componentes.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 5ef5af77831c01ae484398c1f2d8905e5e2bc11e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021335"
---
# <a name="hierarchical-state-override"></a>Substituição do estado hierárquico

Em muitos casos, é necessário alterar dinamicamente o aparecimento de partes de um [modelo](../../concepts/models.md), por exemplo, ocultar subgrafias ou trocar peças para renderização transparente. Mudar os materiais de cada parte envolvida não é prático, uma vez que requer iterar ao longo de todo o gráfico de cena, e gerir a clonagem e atribuição de materiais em cada nó.

Para realizar este caso de utilização com o mínimo de despesas possíveis, utilize o `HierarchicalStateOverrideComponent` . Este componente implementa atualizações hierárquicas de estado em ramos arbitrários do gráfico de cena. Isto significa que um estado pode ser definido em qualquer nível no gráfico de cena e escorre pela hierarquia até que seja ultrapassado por um estado novo, ou aplicado a um objeto de folha.

Como exemplo, considere o modelo de um carro e pretende trocar todo o carro para ser transparente, exceto para a parte do motor interior. Este caso de utilização envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó de raiz do modelo e liga renderização transparente para todo o carro.
* O segundo componente é atribuído ao nó raiz do motor e substitui novamente o estado desligando explicitamente o modo de reposição.

## <a name="features"></a>Funcionalidades

O conjunto fixo de estados que podem ser ultrapassados são:

* **`Hidden`**: As respetivas malhas no gráfico de cena são ocultas ou mostradas.
* **`Tint color`**: Um objeto renderizado pode ser colorido com a sua cor de tonalidade individual e peso de tonalidade. A imagem abaixo mostra a cor da borda de uma roda.
  
  ![Cor Tint](./media/color-tint.png)

* **`See-through`**: A geometria é tornada semi-transparente, por exemplo, para revelar as partes interiores de um objeto. A imagem a seguir mostra todo o carro a ser renderizado em modo see-through, com exceção da pinça do travão vermelho:

  ![See-Through](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito see-through só funciona quando o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition* é utilizado.

* **`Selected`**: A geometria é prestada com um [esboço de seleção](outlines.md).

  ![Contorno de seleção](./media/selection-outline.png)

* **`DisableCollision`**: A geometria está isenta de [consultas espaciais.](spatial-queries.md) A **`Hidden`** bandeira não afeta a bandeira do estado de colisão, por isso estas duas bandeiras são muitas vezes colocadas juntas.

## <a name="hierarchical-overrides"></a>Sobreposições hierárquicas

Pode `HierarchicalStateOverrideComponent` ser anexado em vários níveis de uma hierarquia de objetos. Uma vez que só pode haver um componente de cada tipo numa entidade, cada um `HierarchicalStateOverrideComponent` gere os estados para ocultação, ver-ver, selecionada, coloração e colisão.

Portanto, cada estado pode ser definido como um dos:

* `ForceOn`- o estado está habilitado para toda a malha sobre e abaixo deste nó
* `ForceOff`- o estado é deficiente para todas as malhas sobre e abaixo deste nó
* `InheritFromParent`- o Estado não é afetado por este componente de sobreposição

Pode alterar os estados diretamente ou através da `SetState` função:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->HiddenState(HierarchicalEnableState::ForceOn);

// set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Cor de tinta

A `tint color` sobreposição é ligeiramente especial na medida em que há tanto um estado on/off/herdado como uma propriedade de cor tint. A porção alfa da cor da tonalidade define o peso do efeito de coloração: Se definido para 0.0, não é visível nenhuma cor de tonalidade e se definido para 1.0 o objeto será renderizado com cor de tonalidade pura. Para valores intermédios, a cor final será misturada com a cor da tonalidade. A cor da tonalidade pode ser alterada numa base por quadro para obter uma animação de cor.

## <a name="performance-considerations"></a>Considerações de desempenho

Um caso por `HierarchicalStateOverrideComponent` si só não acrescenta muito tempo de sobrecarga. No entanto, é sempre uma boa prática manter o número de componentes ativos baixo. Por exemplo, ao implementar um sistema de seleção que realça o objeto escolhido, recomenda-se apagar o componente quando o destaque for removido. Manter os componentes por perto com características neutras pode rapidamente somar.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que a renderização padrão. Se grandes partes do gráfico de cena forem trocadas para *see-through,* com muitas camadas de geometria visíveis, pode tornar-se um estrangulamento de desempenho. O mesmo é válido para objetos com [contornos de seleção.](../../overview/features/outlines.md#performance)

## <a name="next-steps"></a>Próximos passos

* [Contornos](../../overview/features/outlines.md)
* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)
