---
title: Substituição do estado hierárquico
description: Explica o conceito de estado hierárquico sobrepõe-se a componentes.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445159"
---
# <a name="hierarchical-state-override"></a>Substituição do estado hierárquico

Em muitos casos, é necessário alterar dinamicamente o aparecimento de partes de um [modelo](../../concepts/models.md), por exemplo, ocultar subgramas ou trocar peças para renderização transparente. Mudar os materiais de cada parte envolvida não é prático, uma vez que requer iterar ao longo de todo o gráfico de cena, e gerir a clonagem e atribuição de materiais em cada nó.

Para realizar este caso de utilização com o mínimo de despesas possíveis, utilize o `HierarchicalStateOverrideComponent` . Este componente implementa atualizações hierárquicas de estado em ramos arbitrários do gráfico de cena. Isto significa que um estado pode ser definido em qualquer nível no gráfico de cena e escorre pela hierarquia até que seja ultrapassado por um estado novo, ou aplicado a um objeto de folha.

Como exemplo, considere o modelo de um carro e pretende trocar todo o carro para ser transparente, exceto para a parte do motor interior. Este caso de utilização envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó de raiz do modelo e liga renderização transparente para todo o carro.
* O segundo componente é atribuído ao nó raiz do motor e substitui novamente o estado desligando explicitamente o modo de reposição.

## <a name="features"></a>Funcionalidades

O conjunto fixo de estados que podem ser ultrapassados são:

* **`Hidden`** : As respetivas malhas no gráfico de cena são ocultas ou mostradas.
* **`Tint color`** : Um objeto renderizado pode ser colorido com a sua cor de tonalidade individual e peso de tonalidade. A imagem abaixo mostra a cor da borda de uma roda.
  
  ![Cor de tonalidade usada para tornar um objeto verde](./media/color-tint.png)

* **`See-through`** : A geometria é tornada semi-transparente, por exemplo, para revelar as partes interiores de um objeto. A imagem a seguir mostra todo o carro a ser renderizado em modo see-through, com exceção da pinça do travão vermelho:

  ![Modo transparente utilizado para tornar os objetos selecionados transparentes](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito see-through só funciona quando o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition* é utilizado.

* **`Shell`** : A geometria é transformada como uma concha transparente e dessaturada. Este modo permite desvanecer partes não importantes de uma cena, mantendo ainda uma sensação de forma e posicionamento relativo. Para alterar a aparência da renderização da concha, utilize o estado [ShellRenderingSettings.](shell-effect.md) Veja a seguinte imagem para o modelo do carro inteiramente renderizado, com exceção das molas azuis:

  ![Modo de concha usado para desbotar objetos específicos](./media/shell.png)

  > [!IMPORTANT]
  > O efeito da concha só funciona quando o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition* é utilizado.

* **`Selected`** : A geometria é prestada com um [esboço de seleção](outlines.md).

  ![Opção de contorno usada para destacar uma parte selecionada](./media/selection-outline.png)

* **`DisableCollision`** : A geometria está isenta de [consultas espaciais.](spatial-queries.md) A **`Hidden`** bandeira não afeta a bandeira do estado de colisão, por isso estas duas bandeiras são muitas vezes colocadas juntas.

* **`UseCutPlaneFilterMask`** : Utilize uma máscara de broca de filtro individual para controlar a seleção do plano cortado. Esta bandeira determina se a máscara de filtro individual deve ser usada ou herdada do seu progenitor. A máscara de bit de filtro em si é definida através da `CutPlaneFilterMask` propriedade. Para obter informações detalhadas sobre o funcionamento da filtragem, consulte o [parágrafo dos planos de corte seletivo](cut-planes.md#selective-cut-planes). Veja o exemplo seguinte onde apenas o pneu e a borda são cortados enquanto o resto da cena permanece inalterado.
![Aviões de corte seletivo](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Como alternativa para desligar a visibilidade e as consultas espaciais para um gráfico completo, o `enabled` estado de um objeto de jogo pode ser alternado. Se uma hierarquia for desativada, esta tem preferência sobre qualquer `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Sobreposições hierárquicas

Pode `HierarchicalStateOverrideComponent` ser anexado em vários níveis de uma hierarquia de objetos. Uma vez que só pode haver um componente de cada tipo numa entidade, cada um `HierarchicalStateOverrideComponent` gere os estados para ocultação, ver-ver, selecionada, coloração e colisão.

Portanto, cada estado pode ser definido como um dos:

* `ForceOn` - o estado está habilitado para toda a malha sobre e abaixo deste nó
* `ForceOff` - o estado é deficiente para todas as malhas sobre e abaixo deste nó
* `InheritFromParent` - o Estado não é afetado por este componente de sobreposição

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
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Cor de tinta

A `tint color` sobreposição é ligeiramente especial na medida em que há tanto um estado on/off/herdado como uma propriedade de cor tint. A porção alfa da cor da tonalidade define o peso do efeito de coloração: Se definido para 0.0, não é visível nenhuma cor de tonalidade e se definido para 1.0 o objeto será renderizado com cor de tonalidade pura. Para valores intermédios, a cor final será misturada com a cor da tonalidade. A cor da tonalidade pode ser alterada numa base por quadro para obter uma animação de cor.

## <a name="performance-considerations"></a>Considerações de desempenho

Um caso por `HierarchicalStateOverrideComponent` si só não acrescenta muito tempo de sobrecarga. No entanto, é sempre uma boa prática manter o número de componentes ativos baixo. Por exemplo, ao implementar um sistema de seleção que realça o objeto escolhido, recomenda-se apagar o componente quando o destaque for removido. Manter os componentes por perto com características neutras pode rapidamente somar.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que a renderização padrão. Se grandes partes do gráfico de cena forem trocadas para *see-through,* com muitas camadas de geometria visíveis, pode tornar-se um estrangulamento de desempenho. O mesmo é válido para objetos com [contornos de seleção](../../overview/features/outlines.md#performance) e para [renderização de conchas.](../../overview/features/shell-effect.md#performance) 

## <a name="api-documentation"></a>Documentação da API

* [C# Classe HierarchicalStateOverrideComponent](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ Classe HierarchicalStateOverrideComponent](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Passos seguintes

* [Contornos](../../overview/features/outlines.md)
* [Modos de composição](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)